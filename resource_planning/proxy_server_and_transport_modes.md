# Proxy Server

With backup proxies, you can easily scale Veeam backup infrastructure
based on the organization demands:

-   In a basic installation (simple deployment scenario for smaller
    environments or Proof of Concepts), the backup proxy is
    automatically installed on the Veeam backup server as part of the
    Veeam Backup & Replication installation process.

-   In other deployment scenarios, the backup proxy role is usually
    assigned to several Windows servers (64-bit). This approach allows
    for offloading the Veeam backup server, achieving better performance
    and a minimized backup window. Backup proxies can be deployed both
    in the primary site and in remote sites on any managed Microsoft
    Windows server in the infrastructures. Depending on the data
    transport mode you plan to use, a backup proxy can be installed on a
    physical server or on a VM, as explained later in this section.

A backup proxy handles data traffic between the VMware vSphere
infrastructure and Veeam Backup & Replication during backup,
replication, VM copy, VM migration jobs or VM restore.

Backup proxy operations include the following:

-   Retrieving VM data from production storage, compressing and sending
    it to the backup repository (for a backup job) or another backup
    proxy (for a replication job).

-   A backup proxy is also part of Veeam's deduplication engine; plus,
    it performs encryption if the corresponding option is selected in
    the data transportation settings.

Technically, a backup proxy runs a light-weight transport service that
takes a few seconds to deploy. When you add a Windows-based server to
Veeam backup management console and assign proxy role to it, Veeam
Backup & Replication installs the necessary components and starts the
required services on that server. When a job is started, the Veeam
backup server becomes the “point of control” for dispatching tasks to
proxy servers, using its built-in load balancing algorithm.

### Advanced Veeam Technologies for Data Processing

To specify the threshold for proxy load, an administrator uses the **Max
concurrent tasks** proxy setting (where a task stands for a single VM
disk), and Veeam Backup & Replication uses a unique load balancing
algorithm to automatically spread the load across multiple proxies. This
feature allows you to increase backup performance, minimize backup time
window and optimize data flow. By default, Veeam Backup & Replication
analyzes the backup proxy configuration, determines the datastores it
can access, and automatically selects the best transport mode depending
on the type of connection between the backup proxy and datastore:

First, Veeam Backup & Replication checks if data processing can be
assigned to a backup proxy with the Direct SAN Access, then it checks
whether a Hot-Add proxy can be used, and then looks for a Network (NBD)
proxy. For more details, see the “Transport Modes” section of this
guide.

After the algorithm identifies all existing backup proxies, it spreads
the load across them in an optimal way:

1.  It discovers the number of tasks being processed at the moment by
    each proxy, and looks for the server with the lowest load and the
    best connection.

2.  All tasks are standing in a “VM to process” queue, and when a
    proxy’s task slot becomes free, Veeam Backup & Replication will
    automatically fill it up with the next task.

3.  Note that priority goes to the disk that belongs to an already
    processed VM; also, short-term scheduled jobs take priority over
    long-term scheduled jobs (like daily or weekly jobs).

**Tip:** Default recommended value is **1** task per Core/vCPU. To
optimize the backup window, you can slightly overbook the **Max
concurrent tasks** count.

Starting with v7, Veeam Backup & Replication supports parallel
processing of VMs/VM disks:

-   It can process multiple VMs (each with a single disk)
    simultaneously, increasing data processing efficiency.

-   If a VM was created with multiple disks, Veeam will try to process
    these disks simultaneously to reduce VM snapshot lifetime.

**Note:** Parallel processing is a global setting that is turned on by default.

### Backup Proxy Services and Components

Veeam backup proxy uses the following services and components:

-   **Veeam Installer Service** - a service that is installed and
    started on the Windows server once it is added to the list of
    managed servers in the Veeam Backup & Replication console. This
    service analyses the system, installs and upgrades necessary
    components and services.

-   **Veeam Transport Service** – a service responsible for deploying
    and coordinating executable modules that act as "data movers" and
    perform main job activities on behalf of Veeam Backup & Replication
    (communicating with VMware Tools, copying VM files, performing data
    deduplication and compression, and so on).

-   **VeeamAgent.exe process** - a data mover, which can be started
    multiple times (on demand) for each data stream. These processes
    work in read-and-write pairs, being started for reading data on a
    source backup proxy, and for writing data - on a target backup proxy
    (replication), gateway proxy (for a CIFS repository), or on a
    repository itself. At restore, the dataflow is in the
    opposite direction.

### Data Transport Modes for Backup Proxies 




### Veeam Storage Integration and Processing Modes for Backing Up from Storage Snapshots




### Sizing a Backup Proxy

#### Processing Resources 

As per system requirements, a proxy requires 2 GB RAM plus 200MB for
each concurrent task (VM disk to be processed).

As described above, you can define the max concurrent tasks value in the
backup proxy settings. On average, a task consumes 1 Core or 1 vCPU Core
(for compression and encryption). Depending on the job setup and other
settings, these numbers can vary. A PoC in the specific environment can
help to estimate resource usage, especially if you need to fit into an
existing budget.

-   If you double the proxy task count, that will, in general, decrease
    backup time window up to 50%.

-   It is recommended to plan for some additional resources – for
    further growth and possible new features: for example, the RAM usage
    for one proxy processing task should not be lower than 2 GB - to be
    prepared for upcoming features.

As a rule of thumb, a proxy will need 1 CPU+2 GB RAM for each 30 VMs
(with average change rate of 2-3 % at the block level) to fit into a
8-hour backup window.

#### Example: Calculating Overall Task Count  {#example-calculating-overall-task-count .pseudo4}

Sample infrastructure has the following configuration:

-   480 VMs

-   48 TB used data

-   Backup window - 8 hours

-   Daily change rate - 3%

For that, the following calculation can be used as a starting point:

30 VMs per CPU core (physical core or vCPU core) for a proxy. =&gt; 1
proxy task slot (1 core, 2 GB RAM). =&gt; 16 cores for 480 VMs =&gt; 1
server with 2x 8 core 32 GB RAM

If you need to achieve a 4 hour backup window, then double the resources
=&gt; 2 server with 2x 8 cores 32GB RAM.

The same counts if you have two times bigger amount of data (with 3%
change rate).

**Note:** Overall performance largely depends on the underlying storage
and network infrastructure.

Required processing resources may seem too high if compared with
traditional agent-based solutions. However, consider that instead of
using all VMs as processing power for all backup operations (including
data transport, source deduplication and compression), Veeam Backup &
Replication uses its central proxy and repository resources. Overall,
required CPU and RAM resources are normally below 5% (and in many cases
below 3%) of all virtualization resources utilized by backup and
replication jobs.

#### How Many VMs per Job?

Best practice is to add 20-50 VMs to a job (30 VMs at about 1000-2000
VMs; 50 VMs at 5000 VMs).

Also, remember that the number of running backup jobs should not exceed
100 jobs concurrently running (not overall). Veeam can handle more, but
a “sweet spot” for database load, load balancing and overall processing
is about 80-100 concurrently running jobs.

#### How Many Tasks per Proxy? 

Typically, in a virtual environment, proxy servers use 4, 6 or 8 vCPUs,
while in physical environments you can use a server with a single quad
core CPU for small sites, while more powerful systems (dual 16 core CPU)
are typically deployed at the main datacenter with the Direct SAN Access
mode processing.

**Note**: Parallel processing may also be limited by max concurrent
tasks at the repository level.

So, in a virtual-only environment you will have slightly more proxies
with less proxy task slot count, while in physical infrastructure with
good storage connection you will have a very high parallel proxy task
count per proxy.

The “sweet spot” in a physical environment is about 20 processing tasks
with 2x 16 Gbps FC cards for read.

Depending on the primary storage system and backup target storage
system, any of the following methods can be recommended to reach the
best backup performance:

-   Running fewer proxy tasks with a higher throughput per current proxy
    task

-   Running higher proxy task count with less throughput per task

As performance depends on multiple factors like storage load,
connection, firmware level, raid configuration, access methods and
others, it is recommended to do a Proof of Concept to define optimal
configuration and the best possible processing mode.

#### Considerations and Limitations

Remember that several factors can negatively affect backup resource
consumption and speed:

-   **Compression level**: It is not recommended to set it up to *High*
    (as it needs 2 CPU Cores per proxy task) or to *Extreme* (which
    needs much CPU power but provides only 2-10% additional
    space saving).

-   **Block Size**: the smaller the blocks size is, the more RAM is
    needed for deduplication. For example, you will see a RAM increase
    when using LAN mode if compared to Local target, and even greater
    (2-4 times) when using WAN. Best practice for most environments is
    to use default job settings (*Local* for backup jobs and *LAN* for
    replication jobs) where another is not mentioned in the
    documentation or this guide for specific cases.

-   **Antivirus Scanner** - see the corresponding section of
    this document.

-   **3rd party applications** – it is not recommended to use an
    application server as a backup proxy.
