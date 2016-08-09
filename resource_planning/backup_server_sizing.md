# Sizing and System Requirements

In this section, we will describe how to configure and size the Veeam backup server.

Sizing with Veeam is cumulative in respect to configurations, if you want to create an all-in-one appliance (Appliance Model) add all the resource requirements together (CPU + Memory) to understand what in total you will need, the same goes if you only wish to have proxy and repository in one host.

## Compute requirements
Recommended Veeam backup server configuration is **1 CPU core (physical or virtual) and 4 GB RAM per 10 concurrently running jobs**. Concurrent jobs include any running backup or replication jobs as well as any job with a continuous schedule such as backup copy jobs and tape jobs.

The minimum recommendation is 2 CPU cores and 8 GB RAM.

It is recommended to group multiple virtual machines into a single job for better efficiency and resource usage. With default configuration it is recommended to configure around 30 VMs per job. The recommendation can be increased by over 10x (300+ VMs) by leveraging additional features such as [per VM backup files](./repository_planning_pervm.md). Please refer to the [Job Configuration](../job_configuration/backup_job.md) section of this guide to learn more about job design.

All configuration and session information is stored in the configuration database. In larger environments the load on the SQL Server hosting the configuration database may be significant and is highly dependent on the amount of concurrently running jobs. For more information please see the [Backup Server Database](resource_planning/backup_server_database.md) section of this guide.

## Operating system
The Veeam backup server requires Microsoft Windows 2008 R2 or later.
The latest supported version of Windows OS is always recommended (currently
Microsoft Windows 2012 R2) as it will also support restoring from virtual
machines with ReFS file systems or Windows Server Deduplication enabled.

For the full list of supported operating systems,
please refer to the corresponding [System Requirements](https://helpcenter.veeam.com/backup/vsphere/system_requirements.html#backup_server)
section of the Veeam User Guide.

## Disk space
This section explains what folders you should plan for when preparing
for installation of the Veeam backup server.

The folders are detailed here as follows:

### Installation folder
Default location is `C:\Program Files\Veeam\Backup and Replication`

Plan for 40 GB. If installing in a virtual machine, thin disks may be used. By default the installer will choose the biggest drive space for the built in backup repository.

### Log files
Default location is `C:\ProgramData\Veeam\Backup`

Log file growth will depend on the number and frequency of jobs and the VM count. Consider that the logging level may also affect the log size, if you need to change the logging level or log file location refer to this Veeam Knowledge Base article: <http://www.veeam.com/kb1825>.

It is recommended to not configure the logging level below 4, as it may complicate troubleshooting. Logging level 6 is very intrusive, and should only be configured for short periods of time when requested by Veeam Support.

Plan for 3 GB log files generated per 100 virtual machines, with a 24 hour RPO. For environments with more than 500 VMs it is recommended to change the default location to a different fast access disk. Many concurrently running jobs may produce a lot of write streams to log files, than can slow down operations for the Veeam Backup Service and Backup Manager processes.

### Veeam Backup Catalog folder
Default location is `C:\VBRCatalog`

This folder is used if VM guest indexing in backup jobs is enabled. For more information, refer to the [Search Server and Indexing](resource_planning/search_server_and_indexing.md) section of this guide. To change the default location, refer to this Veeam Knowledge Base article: <http://www.veeam.com/kb1453>

### vPower NFS folder
Default location is `C:\ProgramData\Veeam\Backup\NfsDatastore`

When booting VMs with Instant VM Recovery or SureBackup, this folder is used by default to store all configuration files and redo logs of the running VM. To offload the changes to a specific production datastore refer to the corresponding page of the Instant VM Recovery wizard.

We recommend installing vPower NFS Services on each Windows-based backup repository. For SMB/CIFS based repositories or deduplication appliances it is recommended to configure vPower NFS on the gateway server. For Linux-based repositories it is recommended to configure vPower NFS on a managed Windows machine as close as possible to the Linux repository (similar to selecting a Gateway Server for SMB/CIFS or deduplication storages).

The vPower NFS server is bound to backup repositories and the folder location is defined per server. To achieve best performance for VMs running off of vPower NFS please configure the fastest possible storage on the backup server or backup repository. To change the folder location  please see the following steps.

1. in the **Backup Infrastructure**, select the **repository** you wish to change.
2. Right click the **repository** and go to **properties**
3. When the wizard opens navigate to the **Mount server** settings
4. using the browser buttons locate the new location for your vPower NFS storage
5. finish the wizard

It is recommended to reserve at least 10 GB space for this folder. If you plan to start a significant number of VMs or run VMs over a longer period  increase the space accordingly to fit the produced/estimated amount of changes generated by the running VMs (conservative average change rate can be defined as 100 GB per 1 TB VM per 24 hours - or 10%). Additional disk space is consumed when using Quick Migration. See more information here > [Veeam Help Center > Performing Instant VM Recovery > Before You Begin](https://helpcenter.veeam.com/backup/vsphere/instant_recovery_before_you_begin.html).

**Important!** Make sure vPower NFS is configured correctly on the Veeam backup server itself as it will be used when deploying Virtual Lab for SureBackup or when performing file-level recovery for Linux-based VMs.

For information on folders required for Enterprise Manager, backup proxy and repository servers (backup targets) and WAN accelerators, as well as for recommendations on their sizing please refer to the corresponding sections of this guide.

## Other software

It is strongly recommended that no highly-transactional and business-critical software is deployed on the same machine as the Veeam backup server. This could be (but not limited to) software such as Active Directory, Exchange Server or other intensive production databases on the SQL server instance.

It is recommended to follow antivirus exclusion guidelines as explained in [Veeam KB 1999](https://www.veeam.com/kb1999).

If it is not possible to connect to a remote SQL staging server for Veeam Explorers you can install Standard or Enterprise versions of SQL (depending on your licensing) locally for staging databases for item-level restores on the backup server. This installation can also be used to store the Veeam backup database if required as long as sufficient resources are assigned to the host machine, however do not run any instances in production from this installation that may affect the operation of the backups or restore processes. SQL express is included in the distribution but is limited to a 10GB database.

**Note:** Remote SQL Server for staging is supported from v9.0

Other software such as Microsoft Outlook (64-bit) for mail export to PST files via Veeam Explorer for Exchange, or a PDF viewer for reading Veeam documentation are considered non-disruptive.

## Installing Veeam Backup & Replication updates

New Veeam releases and updates are installed on the Veeam
Enterprise Manager and Veeam backup servers by the setup wizard or by using
the unattended installation method (also referred to as “silent installation”). For detailed instructions check the latest release notes.

**Note:** Veeam Backup Enterprise Manager must be updated before updating Veeam backup servers.

After installing updates open the Veeam Backup & Replication management console. The **Update** screen will be displayed and will guide
you through updating distributed components on other Veeam managed servers (like proxy and repository servers, vPower NFS servers, WAN accelerators
and tape servers).

**Note:** As Veeam deploys no agents on the virtual machines, you do
not need to update any software (agents) on the VMs.
