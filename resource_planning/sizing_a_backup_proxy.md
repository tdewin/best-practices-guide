# Sizing a Backup Proxy

## Processing Resources 

As per system requirements, a proxy requires 2 GB RAM plus 200MB for
each concurrent task (VM disk to be processed).

As described above, you can define the max concurrent tasks value in the
backup proxy settings. On average, a task consumes 1 Core or 1 vCPU Core
(for compression and encryption). Depending on the job setup and other
settings, these numbers can vary. A PoC in the specific environment can
help to estimate resource usage, especially if you need to fit into an
existing budget.

-   If you double the proxy task count, that will, in general, decrease
    backup time window up to 50%.

-   It is recommended to plan for some additional resources – for
    further growth and possible new features: for example, the RAM usage
    for one proxy processing task should not be lower than 2 GB - to be
    prepared for upcoming features.

As a rule of thumb, a proxy will need 1 CPU+2 GB RAM for each 30 VMs
(with average change rate of 2-3 % at the block level) to fit into a
8-hour backup window.

#### Example: Calculating Overall Task Count  {#example-calculating-overall-task-count .pseudo4}

Sample infrastructure has the following configuration:

-   480 VMs

-   48 TB used data

-   Backup window - 8 hours

-   Daily change rate - 3%

For that, the following calculation can be used as a starting point:

30 VMs per CPU core (physical core or vCPU core) for a proxy. =&gt; 1
proxy task slot (1 core, 2 GB RAM). =&gt; 16 cores for 480 VMs =&gt; 1
server with 2x 8 core 32 GB RAM

If you need to achieve a 4 hour backup window, then double the resources
=&gt; 2 server with 2x 8 cores 32GB RAM.

The same counts if you have two times bigger amount of data (with 3%
change rate).

**Note:** Overall performance largely depends on the underlying storage
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
VMs; 50 VMs at 5000 VMs).

Also, remember that the number of running backup jobs should not exceed
100 jobs concurrently running (not overall). Veeam can handle more, but
a “sweet spot” for database load, load balancing and overall processing
is about 80-100 concurrently running jobs.

## How Many Tasks per Proxy? 

Typically, in a virtual environment, proxy servers use 4, 6 or 8 vCPUs,
while in physical environments you can use a server with a single quad
core CPU for small sites, while more powerful systems (dual 16 core CPU)
are typically deployed at the main datacenter with the Direct SAN Access
mode processing.

**Note**: Parallel processing may also be limited by max concurrent
tasks at the repository level.

So, in a virtual-only environment you will have slightly more proxies
with less proxy task slot count, while in physical infrastructure with
good storage connection you will have a very high parallel proxy task
count per proxy.

The “sweet spot” in a physical environment is about 20 processing tasks
with 2x 16 Gbps FC cards for read.

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
    space saving).

-   **Block Size**: the smaller the blocks size is, the more RAM is
    needed for deduplication. For example, you will see a RAM increase
    when using LAN mode if compared to Local target, and even greater
    (2-4 times) when using WAN. Best practice for most environments is
    to use default job settings (*Local* for backup jobs and *LAN* for
    replication jobs) where another is not mentioned in the
    documentation or this guide for specific cases.

-   **Antivirus Scanner** - see the corresponding section of
    this document.

-   **3rd party applications** – it is not recommended to use an
    application server as a backup proxy.
