# Sizing and System Requirements

## CPU and RAM

Recommended Veeam backup server configuration should have 1 CPU core (physical or virtual) and 4 GB RAM per 10 concurrent running jobs. Concurrent jobs include any
running backup or replication jobs, as well as any job with a continuous schedule such as backup copy jobs and tape jobs.

The minimum recommendation is 2 CPU cores and 8 GB RAM.

**Note:** Best practice is to add multiple servers to a single job. For
more information, refer to the job sizing recommendations later in this
guide.

Additional resources will be required for the Microsoft SQL Server
database, backup proxy, backup repository, WAN accelerator, Enterprise
Manager and other roles. For their requirements, see the corresponding
sections of the Veeam Backup & Replication User Guide and this document.

## Operating System 

Veeam backup server requires Microsoft Windows 2008 R2 or later. The
latest version of Windows OS is recommended (currently, Microsoft
Windows 2012R2 ) as it will support additional options at restore (e.g.,
Windows file-level restore from virtual machines with Windows Server
Deduplication enabled).

For the full list of supported OS, refer to Veeam Backup & Replication
Release Notes.

## Disk Space 

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

## Other Software 

It is strongly recommended that no highly-transactional and
business-critical software (like Microsoft Active Directory Domain
Controller, Exchange server or production databases on the SQL server
instance) are deployed on the same machine as the Veeam backup server.

However, you can install the dependencies, like staging Microsoft SQL
Server for Veeam Explorer for SQL or for Sharepoint, or Microsoft
Outlook (64-bit) for mail export to PST files via Veeam Explorer for
Exchange, or Acrobat Reader for reading Veeam documentation.

## Installing Veeam Backup & Replication Updates

New Veeam releases and updates are installed on the Veeam
Enterprise Manager and Veeam backup servers by the setup wizard or by using
the unattended installation method (also referred to as “silent installation”). For detailed instructions, check the latest release notes.

**Note:** Veeam Backup Enterprise Manager must be updated before updating Veeam backup servers.

After installing updates, open the Veeam Backup & Replication
management console. The **Update** screen will be displayed, and will guide
you through updating distributed components on other Veeam managed servers (like
proxy and repository servers, vPower NFS servers, WAN accelerators
and tape servers).

**Note:** As Veeam deploys no agents on the virtual machines, you do
not need to update any software (agents) on the VMs.