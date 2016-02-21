## Veeam Backup Server

### Planning for Data Streams to Be Processed 

The placement of Veeam backup server is likely to depend on the several
data streams it will process, in particular:

1.  Host discovery and storage discovery

2.  Data flow at restore, including:

-   Windows file-level restore process

-   Application item restore (via Veeam Explorer for Microsoft Active
    Directory, Veeam Explorer for Microsoft Exchange, Veeam Explorer for
    Microsoft SharePoint, Veeam Explorer for Microsoft SQL)

-   Self-Service Restore of files and application objects (via Veeam
    Backup Enterprise Manager)

3.  Disaster recovery optimization for replica failover and/or for
    possible WAN link downtime

![](../media/image8.png)

### Host and Storage Discovery 

The Veeam backup server periodically scans all managed hosts and storage
systems in order to detect new datastores and newly added VMware cluster
members (ESXi hosts). Host scan runs every 4 hours, storage scan runs
daily. As a result of the scans, Veeam updates VMware metadata stored in
the Veeam Backup & Replication configuration database.

 **Note**: If there is a single Veeam backup server deployed on the same
site with vCenter Servers and VMware hosts, there are no special
considerations on data flow. However, if there are multiple Veeam backup
servers or if the deployment involves WAN links, you should plan
appropriately for network traffic so that it does not affect the
deployment.

When adding a vCenter Server to Veeam Backup & Replication management
console, consider that all its managed ESXi hosts will be scanned
automatically every 4 hours. So, in case of a RO/BO deployment with 100
branches, each with its own Veeam backup server, adding a central
(global) vCenter Server can produce a significant traffic over the WAN
links due to these periodic scans. To avoid that, the following can be
suggested:

-   Create a vCenter Server user account that can only access the ESXi
    hosts of a specific branch office. This account can be used by the
    local Veeam backup server to limit traffic to the local ESXi hosts
    when scanning.

-   If a site comprises standalone ESXi hosts, they can be added to
    Veeam Backup & Replication management console separately, as managed
    hosts (instead of adding the vCenter Server).

**Note:** Avoid adding individual hosts to the backup infrastructure if
using shared storage in VMware vSphere.

### Disaster Recovery Optimization

Best practices recommend to deploy a Veeam backup server on the target
site where replicated VMs will reside. Therefore, if you plan to perform
VM replication with Veeam, consider placing a Veeam backup server at the
replica target site. A backup server deployed in the DR site guarantees
correct one-click failover during disaster recovery event. If it is not
possible to deploy a Veeam backup server in the DR site, install Veeam
Backup & Replication on a VM and replicate this VM to DR site. Then in
the case of a disaster recovery event, you will be able to boot this
replica VM using VMware vSphere Web Client and perform the necessary
failover operations.

![](../media/image9.png)

#### Example 1: Enterprise Deployment for 50 Remote Offices, with Central Job Management

An organization comprises 50 branches, with ESXi hosts on each site, and
a vCenter Server in the central location. IT require a central
management point for the Veeam backup infrastructure, administration and
job scheduling. They require local backups to be created at branch
sites, and a backup copy job (with WAN acceleration) to consolidate
these at Global HQ. An administrator can implement this example scenario
in the following way:

1.  Install Veeam Backup & Replication in the branch office (ROBO) and
    the datacenter (HQ), either on a virtual or physical server. (Do not
    configure WAN accelerators).

2.  Add the branch office (ROBO) ESXi hosts to the corresponding
    (branch office) Veeam backup servers to provide fast local restore.

3.  Install Veeam Backup & Replication at Global datacenter HQ and add
    all branch office (ROBO) Veeam backup servers as managed servers
    with proxy, repository and WAN accelerator.

**Note**: Remember to add appropriate resources so that all 3 roles can
run on this server.

1.  Configure one or more repository servers and WAN accelerators at
    Global datacenter (HQ) as described in the documentation.

2.  Add the vCenter Server to the Veeam Backup & Replication console at
    Global datacenter (HQ).

3.  Configure backup and backup copy jobs on Veeam backup server at
    Global datacenter (HQ) for all branch offices (ROBO).

This deployment scenario facilitates centralized administration via
Veeam backup console at HQ.

#### Constraints

However, the following considerations should be taken into account:

-   Use local Veeam backup servers to rescan/import backups for
    file-level restore and item-level restore via Veeam Explorers.

-   Enterprise Manager can be used with all of its functionality, but
    Enterprise Manager Self-Service file restore functions and object
    restore cannot be used, as it would mount the whole backup file to
    the central Veeam backup server through the WAN link and also
    transport the restore data stream twice through the WAN link. This
    does not affect 1-Click VM Restore/failover and job administration
    by Enterprise Manager.

-   If a WAN link fails, no backup job will run, as the backup server
    will not be able to connect to the branch office Veeam and VMware
    components to start required tasks.

-   As components are managed by multiple backup servers, always ensure
    that the same patch/update/version level is used for the entire
    Veeam backup infrastructure.

#### Example 2: Enterprise Deployment for 50 Remote Offices, with Independent Job Scheduling and Self-Services

An organization features 50 branches, with ESXi hosts on each site, and
a vCenter Server in the central location. They require VM backups and
Self-Service Restore to function without data flowing through a WAN link
for restore, as the WAN bandwidth is very low. They require local
backups to be created at branch sites, and a backup copy job (with WAN
acceleration to save bandwidth) to consolidate these at Global HQ.

An administrator can implement this example scenario, as follows:

1.  Install an Enterprise Manager on the Global HQ and add all Backup &
    Replication Servers later.

2.  Install Veeam Backup & Replication on each site, either on a virtual
    or physical server.

3.  Configure WAN accelerators as described to the documentation.

4.  Add the branch office ESXi hosts to the corresponding (local) Veeam
    backup servers to provide fast local restore.

5.  Install Veeam Backup & Replication at Global HQ for import and
    restore of backups that are copied to the Global HQ using the backup
    copy jobs.

6.  Configure backup and backup copy jobs on Veeam backup servers in the
    branch offices.

This deployment scenario facilitates VM backups at local sites, as well
as Enterprise Manager-based Self-Service Restore. Windows VM Restore
File Level Recovery and application item restore via Veeam Explorers can
be done without a need to rescan/import the backups directly at the
branch office Backup & Replication servers. You can use multiple Veeam
Backup & Replication management consoles for independent job scheduling.

**Note**: As components are managed by multiple backup servers, always
ensure that the same patch/update/version level is used for the entire
Veeam backup infrastructure.

### Data Flow at Restore

#### Windows File-Level Recovery 

To perform file-level restores for a Windows-based VM, Veeam mounts all
VM disk files from the backup files (stored on the repository server) to
the Veeam backup server. This can produce a significant data flow
between those components (50 - 400MB) – consider this factor if there is
a WAN link here.

When the backup is mounted, the files selected for recovery are
transported from the repository server through the Veeam backup server
to the VM itself.

To optimize file-level and item-level restores from backup repositories
in remote locations, you can deploy a backup server and import backups
manually as mentioned in the “Disaster Recovery Optimization” section
above.

#### Veeam Explorers

Veeam Explorers are installed together with the Veeam backup server and
run the Windows file-level restore process in the background. So, if you
plan to use these tools, refer to the previous section for server
deployment considerations.

#### Self-Service File, Item and Database Restore via Enterprise Manager 

The Self-Service restore process uses Veeam backup servers to mount the
backup file from the repository-based primary backups to itself or to
the Linux FLR appliance (for Linux file restore only). Thus, it is
recommended to place a Veeam backup server on the same site as the
repository where the backup files that will be used for restore are
stored.

### Physical or Virtual? 

Veeam Backup & Replication server can be deployed on a physical or
virtual server.

-   In most cases, when the Veeam backup server runs on a VM, it is
    replicated to a secondary location or a DR site by leveraging a
    separate VMware environment. In the case of a datacenter
    availability failure/event, this VM can be powered on via the VMware
    vSphere Client to ensure proper 1-click failover.

-   If installed on a physical machine, the Veeam backup server runs
    independently from the VMware platform, which may also be an ideal
    solution in case of a disaster even within VMware environment.

In an enterprise environment, it is recommended to install an additional
Veeam backup server to speed up the failover process when it is needed
(it can be co-located with one of the Veeam proxy or repository
servers). If the Veeam Backup Service has to be restored independently
of the Veeam backup server, then back up the Veeam Backup & Replication
configuration and keep a copy in the designated location.

### Veeam Backup Server Sizing

#### CPU **an**d Memory

Recommended Veeam backup server configuration should have 1 Core or 1
vCPU and 4 GB RAM per 10 concurrent running jobs, including any started
backup or replication jobs, as well as backup copy and tape jobs running
in the background.

The minimum requirement is 2 cores or 2 vCPUs.

**Note:** Best practice is to add multiple servers to a single job. For
more information, refer to the job sizing recommendations later in this
guide.**\
**Additional resources will be required for the Microsoft SQL Server
database, backup proxy, backup repository, WAN accelerator, Enterprise
Manager and other roles. For their requirements, see the corresponding
sections of the Veeam Backup & Replication User Guide and this document.

#### Operating System 

Veeam backup server requires Microsoft Windows 2008 R2 or later. The
latest version of Windows OS is recommended (currently, Microsoft
Windows 2012R2 ) as it will support additional options at restore (e.g.,
Windows file-level restore from virtual machines with Windows Server
Deduplication enabled).

For the full list of supported OS, refer to Veeam Backup & Replication
Release Notes.

#### Disk Space 

This section explains what folders you should plan for when preparing
for installation of the Veeam backup server.

The folders are detailed here as follows:

1.  Installation folder - default location is *C:\\Program
    Files\\Veeam\\Backup and Replication\\*. Plan for 40 GB (if
    installing on a VM, thin disks can be used).

2.  Application log files – default location is
    *%allusersprofile%\\Veeam\\Backup\\*, which in most cases is located
    at *C:\\ProgramData\\Veeam\\Backup\\.*

> Log file size mainly depends on the number and frequency of jobs and
> on the VM count. Also, consider that the logging level may also affect
> the log size. If you need to change the logging level or log file
> location, refer to this Veeam Knowledge Base article:
> <http://www.veeam.com/kb1825>. In enterprise environments, it is
> recommended to change the default location to a separated disk.

1.  Veeam Backup Catalog folder – default location is
    *C:\\VBRCatalog\\*.

> This folder is used if VM guest indexing in backup jobs is enabled.
> For more information, refer to the “Indexing” section of this guide.
> To change the default location, refer to this Veeam Knowledge Base
> article: <http://www.veeam.com/kb1453>.

1.  Veeam vPower NFS folder for NFS Service – default location is
    *C:\\ProgramData\\Veeam\\Backup\\NfsDatastore*.

> If you start a VM with Instant VM Recovery, this folder is used by
> default to store all configuration files and data change of the
> running VM. To offload the storing of these changes to a specific
> datastore, use the Instant VM Recovery wizard. Best practices
> recommend to implement vPower NFS Services on each Windows-based
> backup repository. For CIFS shares, it is recommended to locate it on
> a proxy gateway, and for Linux repositories — on a Windows machine
> next to the Linux repository (remember to use fast and low-latency
> network connections).
>
> It is recommended to reserve at least 10 GB space for this folder. If
> you plan to start a significant number of VMs or run VMs over a long
> time, increase the space according to the produced/estimated
> block-level changes of the VMs.

The vPower NFS server is bound to backup repositories, and the folder
location is defined per server. To change the folder location, go to a
repository where you want to use this vPower NFS Server, and modify its
properties:

1.  In the **Backup Infrastructure** view, click **Backup Repository**.

2.  Then right-click the necessary repository, select **Properties** and
    navigate to the **vPower NFS** step of the wizard.

**Important!** Make sure the vPower NFS Service is configured correctly
on the Veeam backup server itself, as it will be used (if selected) on a
repository server. Otherwise, you will be unable to leverage Veeam
background functionalities like distribution of Virtual Lab FLR
appliances or disk mapping for the Linux FLR appliance.

For information on folders required for Enterprise Manager, backup proxy
and repository servers (backup targets) and WAN accelerators, as well as
for recommendations on their sizing, please refer to the corresponding
sections of this guide.

There are no special sizing considerations for PowerShell SDK and tape
proxy server.

#### Other Software 

It is strongly recommended that no highly-transactional and
business-critical software (like Microsoft Active Directory Domain
Controller, Exchange server or production databases on the SQL server
instance) are deployed on the same machine as the Veeam backup server.

However, you can install the dependancies, like staging Microsoft SQL
Server for Veeam Explorer for SQL or for Sharepoint, or Microsoft
Outlook (64-bit) for mail export to PST files via Veeam Explorer for
Exchange, or Acrobat Reader for reading Veeam documentation.

#### Installing Veeam Backup & Replication Updates

New Veeam releases and updates can be installed on the Veeam Backup
Enterprise Manager and Veeam backup servers by the setup wizard or by an
installation script (so called “silent installation”). For detailed
instructions, check the latest release notes.

**Note:** In most cases, Veeam Backup Enterprise Manager should be
updated before updating Veeam backup servers.

After installing the update, open the Veeam Backup & Replication
management console. The **Update** screen will be displayed, then you
can update distributed components on other Veeam managed servers (like
proxy and repository servers, Veeam vPower NFS servers, WAN accelerators
and tape proxy servers).

**Note:** As Veeam deploys no agents on the virtual machines, you will
not need to update any software (agents) on the VMs.
