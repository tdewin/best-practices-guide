# Application Specific Configuration

## Microsoft SQL Server

When backing up AlwaysOn availability group make sure all cluster nodes are processed by the same backup job for transaction logs processing and restores to work properly.

## Oracle

Refer to the corresponding section of the User Guide (https://helpcenter.veeam.com/backup/vsphere/backup_job_vss_oracle_vm.html) for details on configuring Oracle database backup and transaction logs processing.

Only databases in ARCHIVELOG mode will be backed up online, databases in NOARCHIVELOG mode will be shut down which will cause **database availability disruption**. Before the backup the database (in ARCHIVELOG mode only) is put into backup mode, this has to be taken into consideration when performing restore - restoring database server VM is not enough for restoring the service, database has to be put out of backup mode:

    ALTER DATABASE END BACKUP

**Note:** 32-bit Oracle instances on 64-bit Linux are not supported.

**Tip:** Avoid using aggressive logs truncation settings for databases protected with Data Guard as it may affect logs synchronization to secondary server. Data Guard should have enough time to transport logs remotely before they are truncated thus generally having "Delete logs older than" option less than 24 hours is not recommended.

Logs are stored temporarily on the guest filesystem before they are shipped for processing. This may cause undesired behavior if there is no enough space available in default location and changing temporary location from default is recommended as per [KB 2093](https://www.veeam.com/kb2093).

### Permissions

Certain level of access is expected from the user account configured for performing Oracle backup. Refer to the corresponding section of the User Guide for details (https://helpcenter.veeam.com/backup/explorers/veo_connection_to_source_server.html).

When processing Linux instances, the same user account specified for application
awareness is used to process the Oracle backup. For Windows instances, you may
specify two separate accounts.

**Note:** It is not possible to use different accounts to access different Oracle instances running on the same VM, make sure specified credentials can be used to access all instances on a VM in those cases.

#### Windows OS

User account used to connect to a VM should have local administrator privileges on guest VM and read/write access to database files on filesystem level.

In addition this account or separate Oracle account in case it is different should have SYSDBA rights, this can be achieved by adding it to **ora_dba** local group.

#### Linux OS

Root account or account elevated to root should be used to connect to a VM. Automatic adding to **sudoers** can be enabled for the account but note that **sudoers** file entry will not be removed automatically. Persistent **sudoers** file entry with *NOPASSWD: ALL* option can be added manually, for example:

    oraclebackup ALL=(ALL) NOPASSWD: ALL

This account should be included in the **oinstall**[^1] group to access Oracle database files hierarchy, and to **asmadmin** group (where applies).

In addition this account or separate Oracle account in case it is different should have SYSDBA rights, this can be achieved by adding it to **dba** local group.

### Oracle on Linux backup workflow

1. Coordination component which will perform all the necessary steps is injected into the guest VM. This component is the same as the one used for Linux application-aware image processing in general.
2. Perform application discovery. This is done using native OS methods, coordination component queries /etc/oraInst.loc and reads inventory.xml which is then compared to /etc/oratab information.
3. Status and version of instance(s) is fetched.
4. Disk group information is retrieved for ASM instances.
5. Log mode is identified, this information will later be used for decisions on how exactly the database has to be processed. Database files, CDB (Oracle 12 only) and current DBID information is retrieved.
7. At this step archive log necessary information was collected and Veeam will start doing actual backup, modifying database state - current archive log is archived and all archive log information is retrieved.
8. PFILE backup is created and archived into the backup metadata.
9. Additional information is collected and recorded (current DBID, SCN, Sequence IDs, database unique name, domain, recovery file destination, basic listener information and current archive log).
10. Coordination component is shut down and then restarted again to finalize the backup: database is put into backup mode and database snapshot is created.

### Oracle on Windows backup workflow

Behavior on Windows depends on the state of VSS writer, Oracle version and database type.

| | VSS enabled | VSS disabled | Pluggable database |
| -- | -- | -- | -- |
| Oracle 11 | Oracle VSS writer is engaged, NOARCHIVELOG databases are shut down and excluded from VSS processing | Same worflow as for Linux | N/A |
| Oracle 12 | Oracle VSS writer is engaged, NOARCHIVELOG databases are shut down and excluded from VSS processing | Same worflow as for Linux | Same workflow as for Linux, VSS writer is skipped |

## Other applications

It is possible to ensure data safety and transactional consistency for applications not supported by Veeam Backup and Replication natively using pre-freeze and post-thaw scripts that will execute inside of the virtual machine. Subject application has to provide the way to prepare itself appropriately.

Generally speaking pre-freeze and post-thaw scripts have to (depending on the capabilities of the application):
* Pre-freeze - freeze transactions or create application-level consistent snapshot of its data. Alternatively application services can be shut down but this involved short user service downtime and thus is not desirable.
* Post-thaw - unfreeze transactions or delete snapshot created by pre-freeze (where applies). In case services were shutdown they should be started again.

Certain applications do not require these steps as they include self-healing mechanics or maintain transactional consistency by other means, application documentation has to be checked and/or application vendor has to be contacted for specifics on achieving this.

Note that in addition to configuring application consistency for such applications, restore process has to be properly planned as additional steps would have to be followed to restore them as well.

### Domino

Backup and restore of IBM Lotus Domino is covered in this Veeam webinar: https://www.veeam.com/videos/backing-up-non-vss-aware-applications-ibm-lotus-domino-4867.html

### SAP HANA

Pre-freeze scripts can be used to create HANA snapshot before the backup starts. This snapshot can be used as transactionally consistent state of database after restoring HANA VM.

An example of ensuring database consistency for SAP HANA is described on Veeam community forums: https://forums.veeam.com/veeam-backup-replication-f2/sap-b1-hana-support-t32514.html.

[^1]: Unless installed from rpm/deb, the group name may differ. To find correct values, search this file: `$ORACLE_HOME/rdbms/lib/config.c`.
