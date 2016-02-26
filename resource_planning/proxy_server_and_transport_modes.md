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


### Sizing a Backup Proxy

