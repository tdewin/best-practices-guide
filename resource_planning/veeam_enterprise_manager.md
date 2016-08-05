# Veeam Backup Enterprise Manager

## Whether to Deploy?
Enterprise Manager is intended for centralized reporting
and management of multiple backup servers. It provides
delegated restore and self-service capabilities as well as the
ability for users to request Virtual Labs from backup
administrators. It provides a central management point for multiple
backup servers from a single interface. Enterprise Manager is also a part of
the data encryption and decryption processes implemented in the Veeam
solution and best practice recommend deploying Enterprise Manager in
the following scenarios:

-   It is recommended to deploy Enterprise Manager if you are using
    encryption for backup or backup copy jobs. If you have enabled
    password loss protection (http://helpcenter.veeam.com/backup/em/index.html?em_manage_keys.html)
    for the connected backup servers backup files will be
    encrypted with an additional private key which is unique for each
    instance of Enterprise Manager. This will allow Enterprise Manager
    administrators to unlock backup files using a challenge/response
    mechanism effectively acting as a Private Key Infrastructure (PKI).

-   If an organization has a Remote Office/Branch Office (ROBO)
    deployment then leverage Enterprise Manager to provide site
    administrators with granular restore access via web UI (rather than
    providing access to Backup & Replication console).

-   In enterprise deployments delegation capabilities can be used to
    elevate the 1st line support to perform in-place restores without
    administrative access.

-   For deployments spanning multiple locations with stand-alone
    instances of Enterprise Manager will be
    helpful in managing licenses across these instances to
    ensure compliance.

-   Enterprise Manager is required when automation is essential to
    delivering IT services — to provide access to the Veeam RESTful API.

If the environment includes a single instance of
Backup & Replication you may not need to deploy Enterprise
Manager, especially if you want to avoid additional SQL Server database
activity and server resource consumption (which can be especially
important if using SQL Server Express Edition).

**Note:** If Enterprise Manager is not deployed, password loss
protection will be unavailable.

## Using Enterprise Manager for Restore Operations

### 1-Click File-level Restore
With Enterprise Manager you can restore VM guest files with a single
click. To support this capability the VM restore point must be created
with guest OS indexing enabled. Guest file indexing allows you to search
for VM guest OS files inside VM backups.

**Note:** It is possible to restore VM guest files even when file indexing is disabled. This does require the restore operator to type in guest OS credentials during the restore.

The backup catalog on the Enterprise Manager server will be used to
store indexing data replicated from the backup catalog on Veeam
backup server(s). For more information about the process, refer to the
[Enterprise Manager User Guide](http://helpcenter.veeam.com/backup/em/index.html?veeam_backup_catalog.html).
To learn more about Veeam Backup Catalog sizing refer to the
“[Search Server and Indexing](search_server_and_indexing.md)” section of this document.

### 1-Click Application Item-level Restore
You can restore items from Microsoft Exchange and Microsoft
SQL Server with a single click using Veeam Backup Enterprise Manager.
These capabilities were developed to elevate the 1st line support
engineers, enabling them to recover mail items and other Microsoft
Exchange objects without any direct visibility of the mailbox or
database content. Database administrators are now able to restore
Microsoft SQL Server databases without addressing the backup team.

#### Microsoft Exchange Mailbox Items Restore
The process of restoring an Exchange mailbox is described in the
[Backup and Restore of Microsoft Exchange Items](http://helpcenter.veeam.com/backup/em/em_exchange_items_restore.html)
section of the Veeam Backup Enterprise Manager User Guide.

To create an application-aware image backup of Microsoft Exchange
database VM ensure you back up at least one server holding the Client
Access Server (CAS) role (This can be Exchange Server with the Mailbox
Database role or a dedicated server. Contact the Exchange administrator if
necessary). A server holding the CAS role is used to discover the
mailbox location for the corresponding user. You should supply
credentials for authentication with the CAS server on the
**Configuration** > **Settings** page as described
[here](http://helpcenter.veeam.com/backup/em/em_providing_access_rights_exch.html).

#### Microsoft SQL Server Database Restore
To perform database level restores of SQL Server databases using
Enterprise Manager ensure you enable application-aware image processing
for the corresponding backup job. To use point-in-time recovery enable
log file backups of the Microsoft SQL Server VM. For more details refer
to the [Backup and Restore of Microsoft SQL Server
Databases](http://helpcenter.veeam.com/backup/em/em_sql_db_restore.html)
section of the Veeam Backup Enterprise Manager User Guide.

### Self-Service File Restore
In addition to 1-Click File-Level Restore Backup & Replication
allows VM administrators to restore files or folders from a VM guest OS
using a browser from within the VM guest OS, without creating specific
users or assigning them specific roles at the Veeam Enterprise Manager
level. To do this an administrator of the VM can access the self-service
web portal using the default URL: "https://ENTERPRISE_MANAGER:9443/selfrestore".

**Tip:** This feature is available only for the
Windows-based VMs and requires Veeam Backup & Replication Enterprise
*Plus* license. The VM needs to be in the same domain with the
Enterprise Manager or in a trusted one (for SID resolution)

The process goes as follows:

1.  During the backup of a VM with indexing enabled Veeam detects users
    who have local administrator access rights to that machine and
    stores this information in the Enterprise Manager database.

2.  User enters the self-service web portal URL in the web browser and
    enters the account name and password to access the necessary VM
    guest OS.

3.  After logging in the user is presented with the most recent restore
    point for that VM (the one this user authenticated to) on the
    **Files** tab of the web portal.

For more information on using this feature refer to the [Self-Restore of VM Guest Files](http://helpcenter.veeam.com/backup/em/em_self_restore.html)
section of the Veeam Backup Enterprise Manager User Guide.

## RESTful API Service
The RESTful API service is installed as part of Veeam Backup Enterprise
Manager. To provide access to the API consider that authentication will
take place through Enterprise Manager. Enterprise Manager user role
assignments (**Portal User**, **Restore Operator**, **Portal
Administrator**) and their access scopes access will be inherited by the
RESTful API service. For more information on role assignment see the
[Configuring Security Settings](http://helpcenter.veeam.com/backup/em/configuring_security_settings.html)
section of the Veeam Backup Enterprise Manager User Guide.
