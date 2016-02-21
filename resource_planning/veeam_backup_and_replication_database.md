# Veeam Backup & Replication Database

Veeam Backup & Replication stores all information about backup
infrastructure, jobs settings, job history, sessions, files and other
configuration data in its SQL Server database (default database name is
*VeeamBackup*).

When planning for Veeam Backup & Replication deployment, examine a
number of options offered for the configuration database setup. To make
an appropriate choice, follow the recommendations provided in this
section.

### SQL Server: Express Edition or Standard/Enterprise Edition?

Microsoft SQL Server 2012 Express Edition is included in the Veeam
Backup & Replication setup, which is a convenient option; on the other
hand, it has several limitations:

-   Uses 1 GB of RAM for the SQL Express Server see
    <https://msdn.microsoft.com/en-us/library/cc645993(v=SQL.110).aspx#CrossBoxScale>

-   SQL Express uses only the first CPU. If possible, configure 4 Cores
    for the CPU. (see
    <https://msdn.microsoft.com/en-us/library/cc645993(v=SQL.110).aspx#CrossBoxScale>)

-   Database size cannot exceed 10GB. That is enough to handle the
    environments with 400 – 500 VMs, as the configuration database will
    likely to be below 10 GB. However, if you plan to back up more VMs,
    it is recommended that you use Standard or Enterprise Edition of
    Microsoft SQL Server, otherwise, the Veeam Backup & Replication
    console and job processing will work slower than needed (due to
    memory and CPU limitations).

Another consideration relates to disaster recovery plans. By default,
Veeam Explorer for Microsoft SharePoint and Veeam Explorer for Microsoft
SQL will use a local SQL Server instance as a staging system for
database restore (for details, see
<http://helpcenter.veeam.com/backup/80/explorers/>) – and SQL databases
in production environment are likely to exceed 10 GB, so Express Edition
will not be able to support them.

Thus, it is necessary to use Standard or Enterprise Edition if you plan
to do any of the following:

-   Process more than 4000 VMs (also recommended for 400 – 4000 VMs)

-   Use Veeam Explorer for Microsoft SQL Server or Veeam Explorer for
    Microsoft SharePoint with databases bigger than 10 GB. If using any
    special features of Microsoft SQL Server or Microsoft Sharepoint
    that are only available with Standard or Enterprise SQL version, you
    have to use the corresponding version with Veeam.

In other deployment cases, accepting the limitations, it is possible to
use Microsoft SQL Server Express Edition.

**Tip:** To improve performance of Microsoft SQL Express version, it is
recommended that you add multiple cores to the first CPU.\
Veeam Backup & Replication leverages Microsoft SQL Server 2014
enhancements, so it is recommended, where possible, to update the
database server to Microsoft SQL Server 2014 or Microsoft SQL Server
Express 2014.

### SQL Server: Local or Remote? 

If you plan to use Veeam Explorer for Microsoft SQL and/or Veeam
Explorer for Microsoft SharePoint, you may need a local Microsoft SQL
Server instance as a staging system. However, the Veeam Backup &
Replication configuration database can be placed on another Microsoft
SQL Server instance. Such deployment scenarios have the following
benefits:

-   It can help to increase the products performance if the Veeam backup
    server is expected to handle a heavy load (for example, if the
    backup server is used as a backup proxy and/or repository).

-   SQL Clustering and AlwaysOn Availability Group on external SQL
    Servers can be used for high availability.

-   If the virtual environment includes certain ESXi servers or VMware
    clusters licensed for Microsoft SQL Server, you can place
    the database(s) on those VMs to reduce overall TCO.

### Reaching Optimal Performance: Fast Disks or more RAM/CPU? 

Veeam Backup & Replication may consume high amounts of CPU and RAM while
processing backup or replication jobs. Therefore, to achieve better
performance and load balancing, it is necessary to provide sufficient
RAM and CPU resources to Veeam components. If possible, follow these
guidelines:

  Number of concurrent running jobs (including enabled backup copy jobs)   Min CPU cores   RAM
  ------------------------------------------------------------------------ --------------- -----
  Up to 25                                                                 2               4GB
  Up to 50                                                                 4               6GB
  Up to 100                                                                8               8GB

If you plan to run more than 100 jobs at the same time, add more
ressources.

It is also recommended to place the configuration database on on a fast
disk storage subsystem, such as 10, 000 rpm or faster SAS/FC disks.
While disk I/O is not critical, lower latency may improve overall UI
experience.

### SQL Server Configuration Settings

Veeam Backup & Replication does not require any specific Microsoft SQL
Server configuration settings. However, if you plan to use Veeam
Explorer for Microsoft SharePoint or Veeam Explorer for SQL Server,
consider that since they utilize the local SQL Server as a staging
system, the corresponding requirements should be met:

-   For Veeam Explorer for Microsoft SharePoint:
    <http://helpcenter.veeam.com/backup/80/explorers/index.html?vesp_staging_microsoft_sql_server.html>

-   For Veeam Explorer for Microsoft SQL Server:
    <http://helpcenter.veeam.com/backup/80/explorers/index.html?vesql_systemreqs.html>

Enable and configure all features used by the production database.
Consider using the highest license level and latest version/patch level
installed in the whole production environment. 

If you plan to restore encrypted databases with Veeam Explorer for
Microsoft SQL Server or Veeam Explorer for SharePoint, you will need a
valid encryption certificate on the local Microosft SQL Server used as a
staging system. For details, see this Veeam Knowledge Base article:
<http://www.veeam.com/kb2006>.

Some installations of Microsoft SQL Server 2012 (and later) do not set
the rights for log file processing by default, so make sure that the
**sysadmin** server role is enabled for the Microsoft SQL Server. For
details, see this Veeam Knowledge Base article:
<http://www.veeam.com/kb1978>\
Other prerequisites for SQL Server utilized by Veeam backup server can
be found at
<http://helpcenter.veeam.com/backup/80/vsphere/index.html?system_requirements.html>.

**Tip** Follow Microsoft general recommendations for optimal SQL
performance. For example, place the SQL tempdb on the fastest disks for
best performance:
<http://blogs.msdn.com/b/cindygross/archive/2009/11/20/compilation-of-sql-server-tempdb-io-best-practices.aspx>

### Modifying Database Connection Settings

To modify database connection settings or connect to another Veeam
configuration database, use the **DBConfig** utility, as described in
the product documentation at
<http://helpcenter.veeam.com/backup/80/vsphere/index.html?dbconfig_utility.html>.

-   If using SQL authentication, consider that all Veeam UI and Veeam
    PowerShell changes are communicated using this authentication.

-   If using Windows Authentication for the Microsoft SQL Server
    connection, ensure the actual user of the Veeam UI or Veeam
    PowerShell has proper rights to the database, as described in the
    Veeam Knowledge Base article: <http://www.veeam.com/kb1853>.

### Migrating Veeam Database to Another SQL Server

To migrate Veeam configuration database to another SQL Server, follow
the recommendations provided in these Veeam Knowledge Base articles:

-   <http://www.veeam.com/kb1448>

-   <http://www.veeam.com/kb1250>
