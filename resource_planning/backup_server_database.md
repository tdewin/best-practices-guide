# Veeam Backup & Replication Database

Veeam Availability Suite which includes Veeam Backup & Replication and Enterprise Manager stores all information about backup infrastructure, jobs settings, job history, sessions and other
configuration data in an instance of SQL Server.

When planning the Veeam Backup & Replication deployment you must choose the
placement of the configuration database. It may be either a local or remote SQL Server with several licensing options available. Please see the
following recommendations to ensure your Backup & Replication setup will
scale to the size of your infrastructure.

## SQL Server Edition

Microsoft SQL Server 2012 Express Edition is included in the Veeam
Backup & Replication setup which is a convenient option for most smaller deployments. It does however have several limitations[^1] which may affect performance:

-   Each instance uses only up to 1 GB of RAM
-   Each instance uses only up to 4 cores of the first CPU
-   Database size cannot exceed 10 GB

It is recommended to install Standard or Enterprise Edition if any of the following apply:

-   **When protecting more than 500 VMs**. It is recommended to use Standard or Enterprise versions of Microsoft SQL Server. The max database size allowed by Express Edition is usually sufficient, so do not consider this a constraint. Veeam Backup & Replication console and job processing may however slow down as a result of CPU and RAM constraints on the SQL Server Express instance.
-   **When using Files to Tape jobs extensively**, the database may grow significantly, and the 10 GB limitation may be exceeded quickly.
-   **When unable to configure an external staging server**. For Veeam Explorer for Microsoft SQL Server or Veeam Explorer for Microsoft SharePoint. When working with databases larger than 10 GB, SQL Server Express cannot mount the databases.
-   **When databases are using advanced features of Microsoft SQL Server**. Such as encryption or table partitioning, the licensing level of the staging server (local or remote) must match the level of the original instance.

If none of the above apply it is recommended to use Microsoft SQL Server Express Edition for the sake of simplicity.

**Tip:** Veeam Backup & Replication leverages Microsoft SQL Server 2014 enhancements (cardinality estimator has proved to show significant improvements for large queries), it is recommended where possible to update the database server to Microsoft SQL Server 2014 or Microsoft SQL Server
Express 2014.

## Database Placement
It is possible to leverage a remote SQL Server as staging server during restores
in Veeam Explorer products. There are no specific edition requirements for neither SQL Express,  Standard or Enterprise instance of SQL Server installed locally on the backup server. It is still recommended to run the SQL Server locally (when resource and planning allow) on the backup server for lowest latency and highest performance.

There may still be scenarios where a remote SQL Server is the better choice:

-   SQL Clustering and AlwaysOn Availability Group on external SQL
    Servers can be used for configuration database high availability

-   Failover to a standby backup server can be simplified by connecting to
    the configuration database directly without the need for restoring from
    a configuration backup

-   Some enterprises have dedicated virtual clusters for SQL Server due
    to licensing constraints. In such cases, you may place the Veeam
    configuration database on existing instances to lower the overall TCO

## Sizing
Veeam Backup & Replication may consume high amounts of CPU and RAM while
processing backup or replication jobs. To achieve better performance and load balancing it is necessary to provide sufficient RAM and CPU resources to Veeam components.  Remember to add additional resources, if the backup server is responsible for multiple roles, such as repository or backup proxy.

If possible follow these guidelines:

| Number of concurrently running jobs | CPU | RAM   |
| ------------------------------------|---- | ------|
| Up to 25                            |  2  | 4 GB  |
| Up to 50                            |  4  | 8 GB  |
| Up to 100                           |  8  | 16 GB |

**Note:** Concurrently running jobs include any job type with a continuous schedule such as Backup Copy Jobs.

When running more than 100 jobs concurrently increase compute
resources in line with the table above to meet the resource need of the workload.

It is recommended to place the configuration database on fast, resilient
storage subsystem. Performant storage for backing the configuration database will result in overall increased processing performance. Jobs with a lot of metadata such as very large SharePoint farms with thousands of sites,
SQL Server instances with many databases or Files to Tape jobs may increase
the I/O requirements for the configuration database.

## SQL Server Configuration Tips
Veeam Backup & Replication does not require any specific settings[^2] on the SQL Server in order to utilize the capabilities of Veeam Explorer for SharePoint or SQL. Both local and remote SQL Servers can be used for staging purposes, the corresponding requirements are detailed on
[Veeam Helpcenter](https://helpcenter.veeam.com) should be met:

-   [Veeam Explorer for Microsoft SharePoint](https://helpcenter.veeam.com/backup/explorers/vesp_staging_microsoft_sql_server.html)
-   [Veeam Explorer for Microsoft SQL Server](https://helpcenter.veeam.com/backup/explorers/vesql_systemreqs.html)

**Tip:**
- Enable and configure all features used by production databases.
- When possible use the highest license level and latest version and cumulative update level installed in any VM.
- Using an older version of SQL Server for the configuration database than running in a protected VM may result in warnings in job session logs when such VMs are processed.

If you plan to restore encrypted databases with Veeam Explorer for
Microsoft SQL Server or SharePoint you will need a
valid encryption certificate on the staging Microsoft SQL Server[^3].

Follow Microsoft general recommendations for optimal SQL performance, for example, place the SQL `tempdb` on the fastest disks for best performance[^5].

## Modifying Database Connection Settings
To modify database connection settings or connect to another Veeam
configuration database use the `DBConfig` utility as described in
the product documentation at
<https://helpcenter.veeam.com/backup/vsphere/dbconfig_utility.html>.

If using SQL authentication consider that all Veeam UI and Veeam
PowerShell changes are communicated using this authentication.

## Migrating Veeam Database
To migrate Veeam configuration database to another SQL Server follow
the recommendations provided in these Veeam Knowledge Base articles:

-   <http://www.veeam.com/kb1250>
-   <http://www.veeam.com/kb1448>


[^1]: Features Supported by the Editions of SQL Server 2012 <https://msdn.microsoft.com/en-us/library/cc645993(v=SQL.110).aspx#CrossBoxScale>

[^2]: Generic requirements for SQL Server can be found here: <https://helpcenter.veeam.com/backup/vsphere/system_requirements.html#backup_server>

[^3]: For restoring encrypted databases, please see: <http://www.veeam.com/kb2006>

[^5]: SQL Server `tempdb` Best Practices: <http://blogs.msdn.com/b/cindygross/archive/2009/11/20/compilation-of-sql-server-tempdb-io-best-practices.aspx>
