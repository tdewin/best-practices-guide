# Application-Aware Image Processing

When configuring Veeam backup and replication jobs, you can specify how the transactionally-consistent backup images of VMware VMs should be created. Two methods are available for bringing VM file system and applications into consistent state: VMware Tools quiescence and Veeam's proprietary application-aware image processing (using Microsoft VSS or Linux scripts). Key features of both methods are illustrated by the following table:

| Feature | VMware Tools Quiescence | Application-Aware Image Processing |
| -- | -- | -- |
| Support for consistent backup on Windows guest | Yes | Yes |
| Sync driver for Linux guest | Yes | No |
| Support for application-aware backup | Limited | Yes |
| Pre-VSS preparation for specific applications (e.g. Oracle) | No | Yes |
| Support for application log truncation (Microsoft SQL Server and Exchange Server) | No | Yes |
| Support for scripts | Yes (need to be placed on VM guest) | Yes (can be centrally distributed) |
| Interaction with user via UI | Not needed | Not needed |
| Error reporting | Within VM guest OS | Centralized, on Veeam backup server |

## How Veeam Guest OS Processing Works

1.  First, Veeam Backup & Replication performs guest OS inventory to find out if there is a VSS-aware application running inside a VM.
2.  Veeam Backup & Replication runs pre-freeze script (if any) for the Microsoft Windows/Linux guest OS with applications that utilize other means of VM quiescence.
3.  Then VSS quiescence of the VM is performed, including restore awareness settings.
4.  VM snapshot is created.
5.  VSS unfreeze (“thaw”) is performed.
6.  Veeam Backup & Replication runs post-thaw script (if any) for the Microsoft Windows/Linux guest OS.
7.  Backup data transfer and snapshot commit is performed.
8.  Finally, log file truncation is performed with VSS (for Microsoft SQL Server and Exchange Server) or using native Oracle commands (for Oracle databases on Linux).

## Selecting Guest Processing Options

When on the **Guest Processing** step of the job wizard, you are presented with the variety of options (as described in detail in the User Guide (https://helpcenter.veeam.com/backup/vsphere/backup_job_vss_vm.html).

Note that you can use pre- and post-job scripting to automate job global settings from the Veeam Backup & Replication server itself. It is recommended to use the VM guest processing options for interaction with VMs.

To select the necessary options, refer to the table below.

| VM guest OS type | Linux (with applications and known user for Guest OS processing)   | Windows and VMware VSS-supported applications (without known user for Guest OS processing) | Windows with VSS-aware applications  | Windows (no VSS-aware applications) | Linux with applications | Linux server (no applications) |
| -- | -- | -- | -- | -- | -- | -- |
| Guest OS processing is applicable | Y | Y | Y | Y | Y | N |
| Use VMware Tools quiescence | N | Y | N | N | N | N |
| VMware Tools quiescence with VMware Script processing | Y | N | N | N | N | N |
| Enable Veeam Application-Aware Image Processing | N | N | Y | N | N | N |
| Enable Veeam Application-Aware Image Processing and InGuest Scripts | N | N | N | Y | N | N |
| Disable Veeam Application-Aware Image Processing | N | N | N | N | Y | N |

To coordinate proper VSS and indexing activities, Veeam Backup & Replication deploys a small executable component inside a VM. It is installed only during VSS quiescence procedure and removed immediately after the processing is finished, producing very low impact on VM performance and stability. As for connection method for accessing VM guest OS, Veeam first tries to connect to the VM over network using RPC and then by VMware VIX channel through VMware Tools (for Windows guest only).

## Guest Interaction Proxy

Depending on the guest VM operating system and/or Veeam Backup and Replication Edition different servers may be selected to perform guest processing step and initiate connection to a VM as per the table below.

| Edition | Windows | Linux |
| -- | -- | -- |
| Standard | Backup server | Backup server |
| Enterprise | Guest interaction proxy | Backup server |
| Enterprise Plus | Guest interaction proxy | Backup server |

Any Windows server managed by Veeam Backup and Replication can be selected to act as guest interaction proxy but the preference would be given to the server that has IP address in the same subnet as subject VM. This functionality allows for having only small limited range of ports to allow through the firewalls in restricted environments and for that reason it is recommended to have guest interaction proxies in all VM subnets that are not supposed to be directly accessible from the network where Veeam backup server resides.

For details on network configuration refer to the section "Required ports" below.

**Tip:** If the backup server has no network connection to the VMs and deploying additional guest interaction proxies is not practical/possible (for example, service provider environments), order in which backup server or guest interaction proxy tries to communicate to a VM can be changed using the following registry key:

-   Path: `HKEY_LOCAL_MACHINE\SOFTWARE\Veeam\Veeam Backup and Replication`
-   Key: `InverseVssProtocolOrder`
-   Type: REG_DWORD
-   Value: **0** - try connection through RPC, failover to VIX (default)
-   Value: **1** - try connection through VIX, failover to RPC

RPC connections means injecting the file via the "ADMIN$" share on the target VM. See Veeam Knowledge Base article at <http://www.veeam.com/kb1230> for more information. Consider that this is a global setting that will be applied on the Veeam backup server level and affects all jobs with application-aware image processing.

## Guest Access Credentials

Depending on the VM guest OS processing options selected (enabled or disabled application-aware image processing) and on the guest access method, you may need to supply access credentials for the guest OS, as described in the tables below.

**Tip:** To verify the credentials you supplied on the Guest Processing step of the job wizard, click **Test Now** button.

### Windows OS

| Application-Aware Image Processing (AAIP) | VMware Tools Quiescence | Veeam via VIX | Veeam via RPC | Disabled (crash-consistent) |
| -- | -- | -- | -- | -- | -- |
| Membership in the local Administrators group | User account not needed | No | Yes | Not needed |
| Enter username as *&lt;servername&gt;\\ Administrator* or *&lt;domain&gt;\\Administrator* | No | Yes[^1] | No | No |
| UAC can be enabled | Yes | Yes[^2] | Yes | Yes |
| VMware Tools must be installed and up to date | Yes | Yes | Yes | No |

### Linux OS

| Linux guest OS processing | VMware Tools Quiescence | Veeam via SSH | Disabled (crash-consistent) |
| -- | -- | -- | -- |
| Root user account | No | Yes | No |
| User requires `sudo` rights | No | Yes | No |
| Certificate-based authentication available | No | Yes | No |
| VMware Tools must be installed and up to date | Yes | Yes | No |


## Required Ports

The following ports should be open between the Veeam backup server and VM for guest OS processing:

-   For Windows VMs - remote RPC ports, including Dynamic Port Range (TCP ports 1025 to 5000 - for Microsoft Windows 2003, 49152-65535 - for Microsoft Windows 2008 and newer); TCP\\UDP ports 135, 137-139, 445.
-   For Linux VMs – SSH port (default is TCP port 22)

For details, refer to the Veeam Backup & Replication User Guide (https://helpcenter.veeam.com/backup/vsphere/used_ports.html).

## Sizing

Since guest processing produces very low impact on VM performance, no special considerations on sizing are required. If you use VSS processing with VMware Tools quiescence or Veeam in-guest processing, you need free space on each drive of the VM for the software VSS snapshot. Please check Microsoft requirements for more information.

## File exclusions

Another operation Veeam Backup can do on guest OS level (NTFS only) is excluding certain files or folders from the backup. Alternatively the job can be configured to include only specified files or folders in the backup.

This functionality operates very similarly and shares a lot of characteristics with excluding Windows page file and deleted file blocks. It may help reduce size of the backup files or implement additional data protection strategies for specific data. Backups for which this option was enabled remain image-level and hypervisor APIs are used to retrieve VM data. File exclusion feature uses a combination of NTFS MFT data and guest file system indexes collected by in-guest coordination process to determine which virtual disk blocks belong to the excluded files and thus should not be included in the backup.

Full file/folder paths, environment variables or file masks can be used to define exclusions. For more details on configuring exclusions and its limitations refer to the [corresponding User Guide section](https://helpcenter.veeam.com/backup/vsphere/guest_file_exclusion.html).

**Note:** Generic file exclusions (defined for high level folders) are most effective. File masks exclusions require guest file system indexes and generating indexes may put additional stress on guest VM and will increase backup time. For this reason it is recommended to avoid using file system masks especially on fileservers with large number (thousands) of small files and use high level folder exclusions instead. When using include filters, file exclusions are
created for everything else and can take significant time.

### How file exclusion works

For each VM in a job that has exclusions enabled Veeam Backup and Replication performs the following operations:
1. Virtual machine NTFS MFT is read into the memory cache on the backup proxy, data blocks that store excluded files are marked as deleted.
2. When sending data blocks to target repository data is read both from the VM snapshot and memory cache on the backup proxy. Target repository reconstructs VM disks without excluded VM blocks.
3. Virtual machine NTFS is modified using the data in the cache on the proxy and information about excluded data blocks is saved in the backup file or replica metadata. This information is necessary as CBT is not aware of which blocks were excluded and is used to determine which blocks should be processed during the next backup session.


[^1] Only this account is able to
bypass the UAC prompt for launching processes
with administrative privileges. If not applicable,
see [^2].

[^2] When performing application-aware image processing on
Windows via VIX, UAC must be entirely disabled,
unless the user account is the local administrator account
(SID S-...-500).
