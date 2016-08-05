# Sizing a Backup Proxy

Getting the right amount of processing power is essential to achieving the RTPO defined by the business. In this section, we will outline the recommendations to follow for appropriate sizing.

## Processing Resources

As described above, you may define the max concurrent tasks value in the
backup proxy settings. It is best practices to plan for 1 physical core or 1 vCPU
and 2 GB of RAM for each of the tasks. A task processes 1 VM disk at a time and CPU/RAM
resources are used for inline data deduplication, compression, encryption and other features that are
running on the proxy itself.

In the User Guide it is stated that proxy servers required 2 GB RAM + 512 MB per task.
Please consider these values as minimum requirements. Using the above
mentioned recommendations allow for growth and additional inline processing features
or other special job settings that increase RAM consumption.

If the proxy is used for other roles like Gateway Server for SMB shares, EMC
DataDomain DDBoost, HPE StoreOnce Catalyst or if you run the backup repository
on the server, remember stacking system requirements for all the different components.
Please see related chapters for each components for further details.

**Tip:** Doubling the proxy server task count will - in general - reduce the backup window by 2x.

##Total needed Task Slot Numbers
A general sizing rule of thumb is, to use 1 physical CPU core or vCPU and 2 GB RAM
for each 30 VMs within an 8 hour backup window. Depending on the infrastructure and mainly the storage
performance, these number can turn out to be too conservative, we recommend to do a POC and find out the
specific numbers for the environment.

## Calculating Overall Task Count Examples

Sample infrastructure has the following configuration:

-   480 VMs
-   48 TB used data
-   Backup window: 8 hours
-   Change rate: 5%

For that, the following calculation can be used as a starting point.

Using the "30 VMs per CPU core" rule, we get following result:

* 480 VMs / 30 VMs per core = 16 CPU cores

Each CPU core must have 2 GB RAM:

* 16 CPU cores x 2 GB RAM = 32 GB RAM.

Result:
**16 CPU cores and 32 GB RAM**.
- For a physical server, it is recommended to install dual CPUs with 8 cores each.
- For virtual proxy servers, it is recommended to configure multiple proxies with maximum 8 vCPUs to avoid co-stop scheduling issues.

If you need to achieve a 2x smaller backup window (4 hours), then you may double the resources for a total of **32 CPU cores and 64 GB RAM** - 2x the amount of compute power (possibly split across multiple servers).

The same rule applies if the change rate is 2x higher (10% change rate). To process a 2x increase in amount of changed data, it is also required to double the proxy resources.

**Note:** Performance largely depends on the underlying storage
and network infrastructure.

Required processing resources may seem too high if compared with
traditional agent-based solutions. However, consider that instead of
using all VMs as processing power for all backup operations (including
data transport, source deduplication and compression), Veeam Backup &
Replication uses its central proxy and repository resources. Overall,
required CPU and RAM resources are normally below 5% (and in many cases
below 3%) of all virtualization resources utilized by backup and
replication jobs.

## How Many VMs per Job?

Best practice is to add 20-50 VMs to a job (30 VMs at about 1000-2000
VMs; 50 VMs at 5000 VMs). With enabled per VM chains larger jobs can be
considered. But think about that some of the tasks within a job are still
sequential processes. For example a merge process that write the oldest
incremental file into the full file is started after the last VM finishes
backup processing. If you split the VMs into multiple jobs these background
processes are parallelized and overall backup window can be lower.
Be as well carefull with big jobs when you use Storage Snapshots at Backup
from Storage Snapshots. Guest processing and Scheduling of jobs that contain
multiple snapshots can lead into difficult scheduling situation and Jobs
that spend time waiting for (free) ressources. A good size for Jobs that
write to per VM chain enabled repositories is 50-200 VMs per Job.

Also, remember that the number of running backup jobs should not exceed
100 jobs concurrently running (not overall). Veeam can handle more, but
a “sweet spot” for database load, load balancing and overall processing
is about 80-100 concurrently running jobs.

## How Many Tasks per Proxy?

Typically, in a virtual environment, proxy servers use 4, 6 or 8 vCPUs,
while in physical environments you can use a server with a single quad
core CPU for small sites, while more powerful systems (dual 10-16 core CPU)
are typically deployed at the main datacenter with the Direct SAN Access
mode processing.

**Note**: Parallel processing may also be limited by max concurrent
tasks at the repository level.

So, in a virtual-only environment you will have slightly more proxies
with less proxy task slot count, while in physical infrastructure with
good storage connection you will have a very high parallel proxy task
count per proxy.

The “sweet spot” in a physical environment is about 20 processing tasks
2x10 Core CPU with 48GB RAM and 2x 16 Gbps FC cards for read + 1-2 10GbE
Network cards.

Depending on the primary storage system and backup target storage
system, any of the following methods can be recommended to reach the
best backup performance:

-   Running fewer proxy tasks with a higher throughput per current proxy
    task

-   Running higher proxy task count with less throughput per task

As performance depends on multiple factors like storage load,
connection, firmware level, raid configuration, access methods and
others, it is recommended to do a Proof of Concept to define optimal
configuration and the best possible processing mode.

## Considerations and Limitations

Remember that several factors can negatively affect backup resource
consumption and speed:

-   **Compression level**: It is not recommended to set it up to *High*
    (as it needs 2 CPU Cores per proxy task) or to *Extreme* (which
    needs much CPU power but provides only 2-10% additional
    space saving). However if you have a lot of free CPU ressources
    at the backup time window, you can consider to use *High* compression
    mode.

-   **Block Size**: the smaller the blocks size is, the more RAM is
    needed for deduplication. For example, you will see a RAM increase
    when using LAN mode if compared to Local target, and even greater
    (2-4 times) when using WAN. Best practice for most environments is
    to use default job settings (*Local* for backup jobs and *LAN* for
    replication jobs) where another is not mentioned in the
    documentation or this guide for specific cases.

-   **Antivirus** - see the corresponding section of
    this document.

-   **3rd party applications** – it is not recommended to use an
    application server as a backup proxy.
<!-- AN2016 21.06.2016 -->
