# Deduplication and Compression

### Storage Optimization Overview 

Veeam Backup & Replication takes advantage of multiple techniques for
optimizing the size of stored backups, primarily compression and
deduplication. The main goal of these techniques is to strike the
correct balance between the amount of data read and transferred during
backup as well as what is stored on the backup target while providing
acceptable backup and restore performance. Veeam Backup & Replication
attempts to use reasonable defaults based on various factors but there
can be cases when leveraging settings other than default might be
valuable.

### Deduplication 

#### What does it do?  {#what-does-it-do-3 .pseudo4}

The primary purpose of deduplication is to reduce the amount of data
that has to be stored on disk by detecting redundant data within the
backup and storing it only once. Veeam deduplication is based on
identifying duplicate blocks across multiple VMs in a job. This is
primarily beneficial when VMs are deployed from the same template since
the base image is identical, but is less useful for incremental data.

#### How does it work?  {#how-does-it-work-3 .pseudo4}

Deduplication is performed both by the source proxy (only for virtual
disk currently being processed) and the target repository (for all
virtual disks of all VMs in the job).

Veeam reads data blocks during the backup, calculates a unique hash for
those blocks, and stores all identical blocks into the backup file for
that session only once. By default, Veeam offers 4 different storage
optimization settings that impact the size of read blocks and hash
calculation for deduplication:

-   **Local** – this is the default setting and is recommended when
    using a true disk-based repository. With this setting selected,
    Veeam reads data and calculates hashes in 1 MB chunks.

-   **LAN** – this value is recommended when using a file-based
    repository such as SMB share. With this setting selected, Veeam
    reads data and calculates hashes in 512 KB chunks.

-   **WAN** – this value is recommended when backing up directly over a
    slow link or for replication as it creates the smallest backups
    files at the cost of memory and backup performance. With this
    setting selected, Veeam reads data and calculates hashes in 256
    KB chunks.

-   **Local (&gt;16MB)** – this setting is recommended for large backup
    jobs with more than 16 TB of source data in the job. With this
    setting selected, Veeam reads data hashes and calculates data on 8
    MB blocks.

**Note:** See also Veeam forum at
<http://forums.veeam.com/veeam-backup-replication-f19/veeam-inline-dedupe-t10928.html?hilit=decipher#p47822>
(login required to view this forum board).

#### When to use it?  {#when-to-use-it-3 .pseudo4}

Veeam deduplication should be enabled in almost all cases. However,
there are a few special cases where a user might consider disabling this
option:

-   **Deduplicating storage** – if you use deduplicating storage as a
    repository for storing Veeam backups, it might be desirable to
    disable Veeam deduplication. Veeam deduplication does not work at
    that same level as storage-based deduplication and will not actually
    interfere with the usefulness of such storage appliances. However,
    enabling Veeam deduplication requires storing deduplication metadata
    within the backup file and reading this data during backup and
    restore operations. This can be time-consuming and can lead to
    overall slower backups and restores. This behavior is worse with
    large backup jobs (&gt;1 TB) that have long backup chains. Disabling
    deduplication in such cases may offer significant benefits.

-   **Large compressed or deduplicated source VMs** – when backing up
    VMs, especially large VMs (&gt;1 TB) that contain already compressed
    data (images, video, Windows deduplicated file servers, etc), it may
    be beneficial to simply disable Veeam deduplication since it is
    unlikely to provide much benefit for this type of source data. Note
    that Veeam deduplication is a job-level setting so VMs of the same
    type should be grouped and processed with one job.

#### When do I change the defaults?  {#when-do-i-change-the-defaults .pseudo4}

As a rule, the default settings provided by Veeam are designed to
provide a good balance of backup size vs. backup and restore performance
and resource usage during the backup process. However, given an
abundance of resources or other specifics of the environment, it might
be useful to change the defaults for a particular job.

For example, transactional servers like Microsoft Exchange and Microsoft
SQL commonly make small changes across the disk. If you use the 1 MB
blocks setting, this can lead to a great amount of incremental changes
each day. Using the WAN optimization with a smaller block size of 256 KB
may significantly decrease the size of increments. However, this can
have a very significant impact on the speed and the amount of memory
needed during the backup process on the repository, especially for large
backup jobs. This could also impact the source proxy at the CPU level.

A 2 TB Microsoft Exchange server may need only 2 GB of RAM on the
repository during backup when using default settings of Local/1 MB
blocks, but would potentially need 8 GB of RAM on the repository with
WAN/256 K blocks. Also, transform operations such as synthetic fulls,
forever forward retention/merge and reverse incremental rollback will
perform 4x as much I/O, which can significantly increase total backup
time. All of this must be taken into consideration prior to changing the
defaults.

**Note:** Changing the block size of an existing job will not actually
take effect until the next active full backup.

#### Best P**r**actices {#best-practices-3 .pseudo4}

-   Unless you have a really good understanding of the impact that can
    cause block size changing, stick to the defaults.

-   If you want to change the default block size, be sure to test it
    well and make sure you have planned appropriately for the extra I/O
    and memory requirements on the repository.

-   Maximum recommended job size for WAN block size is 4 TB of
    source data.

-   Maximum recommended job size for LAN block size is 8 TB of
    source data.

-   Maximum recommended job size for Local block size is 16 TB of
    source data.

-   Maximum recommended job size for Local (&gt;16 TB) block size is
    64TB of source data.

-   When using a block size smaller than the default one for a large
    server, it is recommended to use a backup mode that does not perform
    synthetic processing (forward incremental with scheduled
    active full).

**Note:** Block size changes will only become effective after an active
full is created.

### Compression

#### What does it do? {#what-does-it-do-4 .pseudo4}

Similar to deduplication, the primary purpose of compression is to
reduce the amount of data that has to be transferred across the wire and
stored on disk. Veeam Backup & Replication leverages several different
compression algorithms that provide various balances between compression
ratios, throughput and the amount of CPU use on the backup proxy.
Compression provides maximum effect on space savings in a backup job, so
understanding the tradeoffs in these settings can be very important.

#### How does it work?  {#how-does-it-work-4 .pseudo4}

Veeam Backup & Replication performs compression on a per-block basis,
using the block size selected by the storage optimization settings. The
proxy reads each block from the source disk and applies the compression
algorithm to the block before transferring it to the repository. This
saves network bandwidth between the proxy and repository and allows the
repository to store the already compressed block as soon as it receives
it.

There are multiple compression options available:

-   **None** – this option disables compression for the job. The proxy
    reads blocks and sends them uncompressed to the repository where
    they are written to disk as is.

-   **Dedupe-friendly** – this option uses a very simple compression
    algorithm that needs very little CPU. It creates somewhat
    predictable data patterns, which is useful if users want to leverage
    3rd party WAN accelerators with Veeam and/or a deduplication
    appliance (without the ‘decompress before storing’ setting). This
    allows the network stream to be moderately compressed while still
    being effectively cached.

-   **Optimal** – this is the default compression used on Veeam jobs
    that leverages LZ4 compression. It provides typical compression
    ratios around 2:1 with fairly light CPU overhead. This light CPU
    overhead allows for excellent throughput with rates up to 150 MB/s
    per core and even faster decompression rates. This is a most
    commonly used practice that allows achieving excellent balance
    between performance and compression savings.

-   **High** – this option uses lz3 compression tuned for low to
    moderate CPU overhead. This setting provides for around 10% higher
    compression ratios compared to optimal, but uses over 50% more CPU
    horsepower with rates up to 100 MB/core. If proxies are not
    otherwise CPU bound, this extra savings may still be very much worth
    it, especially for larger repositories or if the bandwidth available
    is less than the 100 MB/s limit (i.e., 1 Gb links or less).

-   **Extreme** – this option uses lz3 compression tuned for high
    CPU overhead. This setting uses even more CPU and lowered through
    even further- to around 50 MB/core, with typically only around 3-5%
    additional savings. It is quite rarely used, however, in cases where
    bandwidth between the proxy and repository is limited, for example,
    when you backup directly through WAN links and are not able to
    backup on first side and use backup copy jobs for this.

Best practices is to use **Dedup-friendly** for deduplication storage,
and **Optimal** for all other storage types.

#### When to use it? {#when-to-use-it-4 .pseudo4}

Veeam compression should almost always be enabled. However, when using a
deduplicating storage system as a repository for storing Veeam backups,
it might be desirable to disable Veeam compression at the job level.

If you configure deduplicating storage as a repository, it is
recommended to use the **Decompress backup data blocks before storing**
advanced option so that data should be written to the storage in the
uncompressed format.

**Note:** The job-level compression settings do not change this, as they
control the compression of data between the proxy and repository.

If there are any bandwidth constraints between the proxy and the
repository, it may still be useful to enable compression at the job
level while allowing the repository to decompress the data prior to
writing it to the deduplicating appliance.

#### When do I change the defaults?  {#when-do-i-change-the-defaults-1 .pseudo4}

As a rule, the default settings provided by Veeam are designed to
provide a good balance of backup size vs. backup and restore performance
and resource usage during the backup process. However, given an
abundance of resources or other specifics of the environment, it might
be useful to change the defaults in particular circumstances. For
example, if you know that CPU resources are plentiful, and backups are
unable to make full use of the CPU due to other bottlenecks
(disk/network), it might be worth increasing the compression level.

Compression settings can be changed on the job at any time and any new
backup sessions will write new blocks with the new compression mode.Old
blocks in already stored backups will remain in their existing
compression level.

#### Best Practices {#best-practices-4 .pseudo4}

-   Defaults are good, don’t change values without understanding
    the impact.

-   Use compression levels above optimal only if you have plenty of CPU
    and understand that maximum throughput, especially during full
    backups, will likely be significantly lower, especially if the
    backup proxy CPUs can’t take more load.

-   Test various compression levels and see how the impacts the
    environment but remember the balance. A single backup job with a few
    concurrent streams may seem fine with **Extreme** compression, but
    may overload all available proxy CPUs during production run of
    all jobs.

-   Remember that higher compression ratios may also negatively impact
    restore speeds.
