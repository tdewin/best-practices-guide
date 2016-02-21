Protecting Veeam Backup & Replication Configuration
---------------------------------------------------

### Protecting Job Settings

As recommended by best practice for disaster recovery, you can place
Veeam Backup & Replication installation on a virtual machine and protect
it with backups or replicas. Out-of-the box, Veeam automatically creates
configuration backups on the default backup repository. These
configuration backups contain all the information about Veeam Backup &
Replication jobs (passwords are not stored by default) and can be used
to restore all jobs and their metadata (you will be asked for all
required passwords during the restore process). Please refer to the
Veeam Backup & Replication User Guide for further details:
<http://helpcenter.veeam.com/backup/80/free/vbr_config.html>

**Tip:** If encryption is enabled for configuration backup, passwords
are also stored in the configuration backup files.

### Planning for Disaster Recovery of Veeam Backup Server

If possible, it is recommended to replicate Veeam backup server VM to
the secondary site (verify network and IP mapping settings before you
begin; refer to
<http://helpcenter.veeam.com/backup/80/vsphere/index.html?replica_job.html>
for details). If the server is replicated successfully, then in case of
a disaster recovery situation, you may start its replica in the
secondary location without having to reinstall Veeam Backup &
Replication. This will help to reduce overall Recovery Time Objective
(RTO).

If performing a configuration backup, it is also recommended to place
one copy of the backup on the secondary site. You can configure another
repository for that purpose; use Veeam’s File Copy Job to place a copy
of the configuration backup at the DR site.

**Note:** Backup copy jobs do not process configuration backups.
Remember that configuration backups are not processed with backup to
tape jobs; if you want to store configuration backups on tape, use file
to tape jobs instead.

Again, since all data required for a restore is directly placed within
the backup file (which VMs are in the backup file, as well as
deduplication and encryption information), even in the event that
configuration database is lost or damaged, you can set up a new Veeam
backup server and import the backup files there, or even use the
stand-alone “Extract” utility (both a command line and a graphical
version are provided). Then you will be able to restore VMs, files and
application data without restoring the configuration database.

### Antivirus on Veeam Servers

Antivirus software monitors all 'write' operations on the operating
systems, and this also extends to Veeam backup files. Data that is
processed by a backup proxy and repository can overload the antivirus
system so that it blocks the backup files. This can slow down the backup
process or even lead to backup file corruption. To avoid this, it is
recommended to add the following items to the list of antivirus
exclusions on all Veeam servers (including Veeam backup server, proxy
server, repository server, WAN accelerator server, tape server, and
others).

#### Folders {#folders .pseudo4}

-   *C:\\Program Files\\Veeam *

-   *C:\\Program Files(x86)\\Veeam *

-   *C:\\Program Files\\Common Files\\Veeam *

-   *C:\\Program Files(x86)\\Common Files\\Veeam *

-   *VBRCatalog* (\[**HKLM\\SOFTWARE\\Veeam\\Veeam Backup Catalog**\]
    **CatalogPath** value)

-   *NFS* (Configured in each repository, stored in
    **\[HKLM\\SOFTWARE\\Wow6432Node\\Veeam\\Veeam NFS\]**
    **RootFolder** value)

-   *C:\\VeeamFLR\\\* *

-   All Veeam repository folders

-   All Veeam WAN accelerator folders

#### Folder on VM Guest OS (if VSS is used) {#folder-on-vm-guest-os-if-vss-is-used .pseudo4}

*C:\\Windows\\VeeamVssSupport *

#### Files {#files .pseudo4}

-   *VeeamAgent.exe *

-   *VeeamAgent64.exe *

-   *\*.vmdk *

Some additional exclusions may be also needed.

**Tip:** If the antivirus has a logging or history system, you can
review its logs to detect whether it has taken any actions that affected
Veeam Backup & Replication operations.

Also, consider that another service or process may be using ports
configured for the Veeam vPower NFS Service. To avoid possible issues,
it is recommended to stop the Veeam vPower NFS Service if you do not
plan to use it; make sure that none of the NFS ports are used by other
software (including antivirus systems). For more information, refer to
this Veeam Knowledge Base article: <http://www.veeam.com/kb1055>.
