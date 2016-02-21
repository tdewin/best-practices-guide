Veeam Backup Enterprise Manager
-------------------------------

### Whether to Deploy? 

Veeam Backup Enterprise Manager is intended for centralized reporting
and management of multiple Veeam backup servers; it also provides
delegated restore and self-service restore capabilities, as well as the
ability for users to request Virtual Labs from Veeam backup
administrators. Starting with v8, Enterprise Manager is also a part of
the data encryption and decryption processes implemented in the Veeam
solution. Thus, best practices recommend deploying Enterprise Manager in
the following scenarios :

-   It is recommended to deploy Enterprise Manager if you are using
    encryption for backup or backup copy jobs. If you have [enabled
    password loss
    protection](http://helpcenter.veeam.com/backup/80/em/index.html?em_manage_keys.html)
    for the connected Veeam backup servers, backup files will be
    encrypted with an additional private key which is unique for each
    instance of Enterprise Manager. This will allow Enterprise Manager
    administrators to unlock backup files using a challenge/response
    mechanism, effectively acting as a Private Key Infrastructure (PKI).

-   If an organization has a Remote Office/Branch Office (ROBO)
    deployment, leverage Enterprise Manager to provide site
    administrators with granular restore access via web UI (rather than
    providing access to Veeam Backup & Replication console).

-   In enterprise deployments, delegation capabilities can be used to
    elevate the 1st line support to perform in-place restores without
    administrative access.

-   For deployments spanning multiple locations with stand-alone
    instances of Veeam Backup & Replication, Enterprise Manager will be
    helpful in managing licenses across these instances to
    ensure compliance.

-   Enterprise Manager is required when automation is essential to
    delivering IT services — to provide access to the Veeam RESTful API.

Remember, however, that if the environment includes a single instance of
Veeam Backup & Replication, you may not need to deploy Enterprise
Manager, especially if you want to avoid additional SQL Server database
activity and server resource consumption (which can be especially
important if using SQL Server Express Edition).

**Note:** If Enterprise Manager is not deployed, password loss
protection will be unavailable.

### Using Enterprise Manager for Restore Operations

#### 1-Click File-level Restore 

With Enterprise Manager, you can restore VM guest OS files with a single
click. To support this capability, the VM restore point must be created
with guest OS indexing enabled; guest file indexing allows you to search
for VM guest OS files inside VM backups.

Veeam Backup Catalog on the Enterprise Manager server will be used to
store indexing data replicated from the Veeam Backup Catalog on Veeam
backup server(s). For more information about the process, refer to the
[Enterprise Manager User
Guide](http://helpcenter.veeam.com/backup/80/em/index.html?veeam_backup_catalog.html).
To learn more about Veeam Backup Catalog sizing, refer to the “Indexing
and Search” section of this document.

#### 1-Click Application Item-level Restore 

Currently, you can restore items from Microsoft Exchange and Microsoft
SQL Server with a single click using Veeam Backup Enterprise Manager.
These capabilities were developed to elevate the 1st level support
engineers, enabling them to recover mail items and other Microsoft
Exchange objects, without any direct visibility of the mailbox or
database content. Database administrators are now able to restore
Microsoft SQL Server databases without addressing the backup team.

#### Microsoft Exchange Mailbox Items Restore

The process of restoring an Exchange mailbox is described in the [Backup
and Restore of Microsoft Exchange
Items](http://helpcenter.veeam.com/backup/80/em/index.html?em_exchange_items_restore.html)
section of the Veeam Backup Enterprise Manager User Guide.

To create an application-aware image backup of Microsoft Exchange
database VM, ensure you back up at least one server holding the Client
Access Server (CAS) role (this can be Exchange Server with the Mailbox
Database role, or a dedicated server; contact Exchange administrator if
necessary). A server holding the CAS role is used to discover the
mailbox location for the corresponding user. You should supply
credentials for authentication with the CAS server on the
**Configuration** &gt; **Settings** page, as described
[here](http://helpcenter.veeam.com/backup/80/em/index.html?em_providing_access_rights_exch.html).

#### Microsoft SQL Server Database Restore

To perform database-level restores of SQL Server databases using
Enterprise Manager, ensure you enable application-aware image processing
for the corresponding backup job. To use point-in-time recovery, enable
log file backups of the Microsoft SQL Server VM. For more details, refer
to the [Backup and Restore of Microsoft SQL Server
Databases](http://helpcenter.veeam.com/backup/80/em/index.html?em_sql_db_restore.html)
section of the Veeam Backup Enterprise Manager User Guide.

#### Self-Service File Restore 

In addition to 1-Click File-Level Restore, Veeam Backup & Replication v8
allows VM administrators to restore files or folders from a VM guest OS,
using a browser from within the VM guest OS, without creating specific
users or assigning them specific roles at the Veeam Enterprise Manager
level. For that, an administrator of the VM can access the self-service
web portal using the default URL:
https://&lt;enterprise\_manager\_server\_name&gt;:9443/selfrestore.

**Note:** Currently, this feature is available only for the
Windows-based VMs and requires Veeam Backup & Replication Enterprise
*Plus* license. Also, the VM should be in the same domain with the
Enterprise Manager, or in a trusted one (for SID resolution)

The process goes as follows:

1.  During the backup of a VM with indexing enabled, Veeam detects users
    who have local administrator access rights to that machine, and
    stores this information in the Enterprise Manager database.

2.  User enters the self-service web portal URL in the web browser and
    enters the account name and password to access the necessary VM
    guest OS.

3.  After logging in, the user is presented with the most recent restore
    point for that VM (the one this user authenticated to) on the
    **Files** tab of the web portal.

For more information on using this feature, refer to the [Self-Restore
of VM Guest
Files](http://helpcenter.veeam.com/backup/80/em/index.html?em_self_restore.html)
section of the Veeam Backup Enterprise Manager User Guide.

### RESTful API Service 

RESTful API service is installed as part of Veeam Backup Enterprise
Manager. To provide access to the API, consider that authentication will
take place through Enterprise Manager; Enterprise Manager user role
assignments (**Portal User**, **Restore Operator**, **Portal
Administrator**) and their access scopes access will be inherited by the
RESTful API service. For more information on role assignment, see the
[Configuring Security
Settings](http://helpcenter.veeam.com/backup/80/em/index.html?configuring_security_settings.html)
section of the Veeam Backup Enterprise Manager User Guide.
