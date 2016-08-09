# Search Server and Indexing

### Indexing and Search Overview

Veeam Backup & Replication performs backups at the image-level using
APIs available from the underlying hypervisor. It has no direct
visibility of the file structure after backup is finished. It
is possible to Use File Level Recovery (FLR) wizard or Enterprise Manager to mount VMs from
within a backup file and access/restore VM guest files.
If a user wants to perform file restore from the central Enterprise
Manager it is not possible within an acceptable timeframe to mount all
backup files and VMs in it to find a file that the Enterprise Manager
user wants to restore.
To support advanced file-level restore scenarios Veeam offers the
capability to index files on VMs being backed up. Indexing is available
for both Windows & Linux VMs allowing users of Enterprise Manager to browse and search for the necessary files and to perform one-click file restores.

The sections below will outline some specific use cases for indexing and describe best practices and
guidelines for sizing.


### When to Use Indexing?

File-level indexing should be enabled only if you plan to utilize
advanced file search and one-click file level restore capabilities of
Enterprise Manager (including delegated restore). While
indexing is a job-level setting you can use filters to index only a
subset of files. It is possible to exclude specific VMs from
indexing as described for example in [this
section](http://helpcenter.veeam.com/backup/em/index.html?em_indexing_options.html)
of the Veeam Backup Enterprise Manager User Guide

### How Veeam Indexing Works

Veeam indexing creates a separate index file in the catalog for each restore point.
These index files are used by Veeam Enterprise Manager to support
file browsing or searching without a need to mount the restore point to
the mount server. Users can quickly search for files across
multiple restore points viewing the required file history when looking for
a specific version of a document. They can also select a specific VM and
browse the file system to restore guest files.

Enterprise Manager allows for file-level restore
functions to be delegated to a subset of users by leveraging the
role-based access control.

During the VM backup job run the following operations are performed If configured:

1.  Veeam accesses the guest OS (using credentials specified in the
    job settings) and injects a small run-time process to collect the
    list of files.

    -   For Microsoft Windows-based VMs the process gathers file
        metadata by reading the MFT data of the supported file system
        (NTFS and ReFS).

    -   For Linux-based VMs the process leverages the existing “locate”
        database that is commonly installed on most Linux distributions. Veeam uses the following software packages for it: mlocate, gzip and tar

These operations take place in parallel with the backup and do not
increase the duration of the process. For more details on the indexing
process refer to the [Veeam Backup Enterprise Manager User
Guide](http://helpcenter.veeam.com/backup/em/index.html?indexing_hiw.html).

1.  Veeam Backup & Replication creates a catalog (index) of the VM guest
    OS files and stores index files on the Veeam backup server in the
    `C:\VBRCatalog\Index\Machines\{vm_name}` folder. Creation of the index
    is extremely fast and has minimal impact on network and
    VMware environment.

2.  Once the index is created and stored on Veeam backup servers, the
    indexing service on Veeam Backup Enterprise Manager performs index
    copy — it aggregates index data for all VM image backups from
    multiple backup servers to the Enterprise Manager database while the original index files in the backup servers are deleted to conserve space.
    The consolidated indexes are stored on the Enterprise Manager server in the `C:\VBRCatalog\Index\Catalog` and are used for search queries .


**Important To Note!**
- To search within the index catalog it is necessary to
deploy Veeam Backup Enterprise Manager, this component is in charge
of catalog data replication and retention (see [this section](http://helpcenter.veeam.com/backup/em/index.html?veeam_backup_catalog.html) of the User Guide for more details).
- If you enable indexing without configuring Enterprise Manager the indexes in the *VBRCatalog* folder of the backup server will never be collected or deleted and will eventually fill up the disk drive.


### Temporary VM Disk Usage

During the indexing process indexing information is
temporarily stored on the local VM guest requiring additional free
space on the system drive.

#### Windows VM
Temporary space required on the first drive in the VM (`С:\` drive):

**100 MB per one million files**

This was tested with one million
files with 20 characters long filenames in one directory. Depending on the saved
metadata and folder structure of the files, the value can be lower or
higher.

#### Linux VM
Temporary space required in `/tmp`:

**50 MB per one million files**

Linux indexes require
around 50% less space because `mlocate` does not index
metadata such as timestamps and ownership.

### Sizing Enterprise Manager Catalog

The Veeam Catalog Service is responsible for maintaining index data.
When running on the backup server this catalog service will
maintain index data for all jobs that run on that specific server as
long as the backup data remains on disk. When running on the Enterprise
Manager server the service will move index data from all managed
backup servers into the Enterprise Manager local catalog deleting the files from the originating backup server catalog. So it should be sized
appropriately to hold all data from the remote Veeam servers.

-   When using a *Standard* license, Enterprise Manager will only keep
    index data for restore points still in repositories.

-   For *Enterprise* and *Enterprise Plus* licenses, you can configure
    Enterprise Manager to keep indexes even longer, with the default
    being 3 months. This can significantly increase the amount of space
    required for the catalog.

Estimated used space of the final index file after compression is
approximately 2 MB per 1,000,000 files for a single VM restore point
on the Enterprise Manager server. The indexes are also stored
in the backup files and temporary folders on the backup server.

#### Example
Below is an example that summarizes the information above. The example
is given _per_ indexed VM containing 10,000,000 files.

`2 MB * 10 million files * 60 restore
points per month * 3 months index retention = 3.5 GB`

### Recommended Settings

Follow these recommendations when setting up Veeam indexing:

-   Place the catalog on a dedicated volume of high performance disk. To
    change the default Veeam Catalog folder location refer to this
    Veeam Knowledge Base article: <http://www.veeam.com/kb1453.>

-   You can enable NTFS compression on the catalog folder. This can reduce the space requirements by well
    over 50%. For very large catalogs (with 100s of VMs and
    10's of millions of files) it can be more beneficial to use a
    Windows 2012 R2 volume with Data Deduplication enabled. This volume
    should be dedicated to index files and configured to run
    deduplication functions outside of the normal backup window.

-   It is recommended to enable indexing only on VMs where the advanced
    search capabilities are necessary. Use filters to exclude
    unnecessary files from indexing (Windows system folder, Program
    Files and other system directories are excluded by default). For the
    Linux systems to be indexed, make sure they have **mlocate** or
    another compatible **locate** package installed.

-   Configure index retention in Veeam Backup Enterprise Manager to the
    minimum necessary to meet the IT policy requirements. Index
    retention setting is available in the Enterprise Manager web console
    under **Configuration &gt; Settings &gt; Guest File System
    Catalog**.



-   To enhance search performance, SSDs can be used. If you plan to
    index a very large number of VMs it is recommended to limit the
    search scope at restore to a single VM before you click the search
    button, this will bring faster results.

    **Notes:**

    To take advantage of indexing on SUSE Linux Enterprise Server (SLES) you must be running version 12 or above. In lower versions that do not contain by default the mlocate package you may try this OpenSUSE package
    http://software.opensuse.org/package/mlocate

    Veeam Backup Enterprise Manager SQL database
    (*VeeamBackupReporting*) will not grow much while using indexing
    functions, as this database will only store the corresponding metadata.


### Using Veeam Backup Search (Optional Component)

In its early versions Veeam did not have its own indexing engine,
instead it used the Veeam Backup Search component to connect to the
Microsoft Search Server 2010 that provided search capabilities. Now Veeam has its own built in indexing engine developed specifically for this purpose.

 It is no longer a requirement to have a Veeam Backup Search configured as Veeam Integrated indexing engine can be more performant.

If you need to use that Veeam Backup Search component (and Microsoft Search Server) for indexing consider the following notes:

-   Microsoft Search Server Express Edition can be used as it has no
    limitations for the number of indexed files.

-   Other editions of Microsoft Search Server deliver higher
    scalability because Search Server components can be separately
    installed on multiple servers. If you are using Enterprise Manager  consider that it can spread the load between multiple Microsoft Search Servers Express automatically.

-   Microsoft Search Server functionality is used to scan content in the
    shared *VBRCatalog* folder on the Veeam Backup Enterprise Manager
    server and to create a content index on the Search Server; this
    content index is used to process search queries. For more details,
    refer to the [Veeam Backup
    Search](http://helpcenter.veeam.com/backup/80/em/index.html?installing_veeam_backup_search.html)
    section of the User Guide.

**Note**: Though using content index streamlines the search process the
index itself can require significant space on disk in
`C:\VBRCatalog\Journal\[YYYY_MM]\[search-server]`.

-   Search Server requires an SQL database for its operation. Consider
    that Microsoft SQL Server Express Edition leverages only one CPU
    which limits the Search Server performance. The database
    size supported by this edition is also limited (in particular, 10 GB
    for Microsoft SQL Server 2008 R2 Express Edition or later).
