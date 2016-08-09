# Proxy Server

With backup proxies you can easily scale Veeam backup infrastructure
based on the organization demands:

-   In a basic installation (simple deployment scenario for smaller
    environments or Proof of Concepts) the backup proxy is
    automatically installed on the Veeam backup server as part of the
    Veeam Backup & Replication installation process.

-   In advanced deployment scenarios, the backup proxy role is usually
    assigned to between one or several Windows servers (recommend using  64-bit). This approach allows for offloading the Veeam backup server, achieving better performance and a minimized backup window. Backup proxies can be deployed both
    in the primary site and in remote sites on any managed Microsoft
    Windows server in the infrastructure. Depending on the data
    transport mode you plan to use a backup proxy can be installed on a
    physical server or on a VM as explained later in this section.

A backup proxy handles data traffic between the VMware vSphere
infrastructure and Backup & Replication during backup,
replication (at source and target), VM copy, VM migration jobs or VM restore.
They are also used to detect and scan snapshots to enable Veeam
Explorer for Storage Snapshot features if a compatible storage system
was added to the Backup & Replication Server.

Backup proxy operations include the following:

-   Retrieving VM data from production storage, compressing and sending
    it to the backup repository (for a backup job) or another backup
    proxy (for a replication job).

-   BitLooker: Applies to VMs running Windows OS and using NTFS. For more information, see the corresponding section of this guide > [Deduplication and Compression - BitLooker](../job_configuration/deduplication_and_compression.md#bitlooker)

-   Inline source side data deduplication to optimize information received by vSphere Change Block Tracking (CBT)

- 	Inline compression

- 	AES256 encryption if the corresponding option is selected in
    the data transportation or backup data settings.

Technically a backup proxy runs a light-weight transport service that
takes a few seconds to deploy. When you add a Windows-based server to
Veeam backup management console assigning the proxy role to it
Backup & Replication installs the necessary components starting the
required services on that server. When a job is started the Veeam
Backup & Replication server becomes the point of control for dispatching
tasks to proxy servers using its built-in load balancing algorithm.

Like any backup vendor using VMware VADP, Backup & Replication integrates
VMware VDDK in the Veeam Transport Service. This is necessary for management
interaction with vCenter and ESXi hosts, while in some scenarios, VDDK is bypassed to
optimize performance.

Backup data VDDK based transport modes underlay some limitations so Veeam developed it´s own more advanced communication protocols to address
them. For example Veeam can backup multiple disks of the same VM at same time with HotAdd mode or can read data with Direct NFS mode directly out of
NFS based storage systems.

### Intelligent Load Balancing

To specify the threshold for proxy load an administrator uses the **Max
concurrent tasks** proxy setting (where a task stands for a single VM
disk), Backup & Replication uses a unique load balancing
algorithm to automatically spread the load across multiple proxies. This
feature allows you to increase backup performance, minimize backup time
window and optimize data flow.

The default proxy server is configured for 2 simultaneous tasks at installation,
whereas subsequently added proxy servers analyze the CPU configuration. The proxy
server automatically proposes configuring 1 task per CPU core. During deployment,
it is determined which datastores the proxy can access. This information is stored
in the configuration database, and is used at backup time to automatically select
the best transport mode depending on the type of connection between the backup proxy and datastore.

First Backup & Replication checks if data processing can be
assigned to a backup proxy with the Direct Storage mode (which includes
Direct SAN Access and Veeam's own special Direct NFS Access), then it checks
whether a Virtual Appliance or Hot-Add proxy can be used. Then it looks
for a Network Mode(NBD) proxy. For more details, see the “Transport Modes”
section of this guide.

After the algorithm identifies all existing backup proxies it spreads
the load across them in an optimal way:

1.  It discovers the number of tasks being processed at the moment by
    each proxy and looks for the server with the lowest load and the
    best connection.

2.  All tasks are standing in a “VM to process” queue,  when a
    proxy’s task slot becomes free Backup & Replication will
    automatically fill it up with the next VM disk backup task.

3.  Priority goes to the disk that belongs to an already
    processed VM, after that VMs of already running jobs have next higher
	priority. Short-term scheduled jobs take priority over
    long-term scheduled jobs (like daily or weekly jobs).


**Tip:** At the repository which writes the backup data, only one
    write stream is started to the backup storage per job. Job priority together with jobs to be run with a large amount of
    VMs can lead to the situation that only 1-3 target write streams
    can become the bootleneck in the backup processing. Consider
	enabling "Per VM backup chain" at the repository to address this.

**Tip:** Default recommended value is **1** task per Core/vCPU, with no
less than 2 cores as minimum. To
optimize the backup window, you can cautiously oversubscribe the **Max
concurrent tasks** count, but monitor CPU and RAM usage carefully.

**Veeam Backup & Replication supports parallel processing of VMs/VM disks:**

-   It can process multiple VMs within a Job simultaneously increasing
	data processing efficiency.

-   If a VM was created with multiple disks Veeam will try to process
    these disks simultaneously to reduce VM backup time to minimize
	VMware snapshot lifetime.

- 	Priority goes to already running parallel processes for VM disks backups. After all these disks are backed up, VMs from existing running
	jobs take priority. As well as highly frequent scheduled jobs which are prioritized higher.

To achieve the best backup window it is recommended to slightly oversubscribe the tasks slots and start more jobs. This allow Veeam to leverage the maximum of the task slots and lead into an optimal backup window.

**Note:** Parallel processing is a global setting that is turned on by default.
If you had upgraded from older versions please check and enable this setting.

### Backup Proxy Services and Components

Veeam backup proxy uses the following services and components:

-   **Veeam Installer Service** - A service that is installed and
    started on the Windows server once it is added to the list of
    managed servers in the Veeam Backup & Replication console. This
    service analyses the system, installs and upgrades necessary
    components and services.

-   **Veeam Transport Service** – A service responsible for deploying
    and coordinating executable modules that act as "data movers". It
    performs main job activities on behalf of Veeam Backup & Replication
    (communicating with VMware Tools, copying VM files, performing data
    deduplication and compression, and so on).

-   **VeeamAgent.exe process** - a data mover which can be started
    multiple times (on demand) for each data stream on the proxy.
    These processes can operate in either read or write mode. When used on a
    proxy server for backup, they are only performing read operations, while
    "write" mode is used for writing data on a target backup proxy
    (replication). Veeam agents in write mode are also used on all repository
    types, but will not be discussed in this chapter.
