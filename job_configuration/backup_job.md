# Backup Job

### Job Layout and Object Selection 

Veeam Backup and Replication allows you to flexibly select objects to
add to the job. At the **Virtual Machines** step of the job wizard, the
**Add Objects** screen offers various “views” into the vCenter
architecture that match the views provided by the vSphere client. You
can switch between the **Hosts and Clusters**, **VMs and Templates**,
**Datastores and VMs** or **Tags** views by pressing the appropriate
button on the backup object selection screen.

This screen also provides an advanced object exclusion tool that allows
you to select a parent object and then exclude child objects, or even
individual disks within a VM.

More guidelines on object selection are listed below.

### Keeping Amount of Data Under Control 

It is recommended to use the following settings for deduplication:

-   **Local Target** as default for standard backup jobs

-   **Local Target 16TB** in case full backup file is bigger than 8TB

-   **LAN Target** as default setting for standard replication jobs

These values are based on the backup architecture.

Consider that there are good reasons to keep amount of data processed by
a job even smaller: small backup files are easier to manage or move to
new storage, they need less time and space when running a full backup,
and they require smaller staging areas for restores from tape.

### Increasing Deduplication Rate 

VMs created from the same or similar templates will increase
deduplication rate, but keep in mind the backup windows and the size of
the job for manageability.

### Adding Containers to Jobs

Adding resource pools, folders, datastores, or vSphere Tags (vSphere 5.5
and higher) to backup jobs makes backup management easier. New machines
that become members of these groups are automatically included in the
backup job.

With this approach, you need to monitor the amount of data in job to
make sure there is enough datastore space.

If you add a number of datastores (or a mix of resource pools), make
sure that you do not overlap, as VMs disks may reside on multiple
datastores: a virtual machine residing on several datastores included in
more than one backup job will be entirely backed-up in each job.

Limit the number of exclusions in backup jobs and exclude objects
carefully. While exclusions can be very useful, virtual infrastructure
is dynamic and changes over time. It’s quite possible that a VM gets
moved to a folder or resource pool that is excluded which makes it
unprotected.

A word about tags:

-   Tags might be very convenient for VM selection; however, you need to
    monitor the number of VM that will be automatically added to
    the job.

-   Using tags, you can classify VMs by service levels, using different
    backup jobs for different service levels.

-   Veeam ONE Business View is a very convenient tool to handle tags,
    allowing you to create classification rules and write tags on the
    vCenter - for example, according to CPU number, RAM quantity, names
    of VMs, folders, resource pools, datastores, and so. Veeam ONE
    Business View can also import VM/host/datastore descriptions from a
    CSV file. This feature can be useful when refreshing VMware tags,
    for example, to update the change management database information.

### Setting Deduplication and Compression Level 

Detailed description of these settings and their influence on the backup
infrastructure is provided in the “Deduplication and Compression
“section above in this document. In almost all cases deduplication
should be left enabled. Veeam Backup & Replication uses source side
deduplication which decreases the amount of data that must be
transferred to the target repository.

**Tip:** If you are using a deduplication storage appliance, you have to
disable inline deduplication to get better performance on incremental
runs, as Veeams own inline deduplication increases random reads from the
target significant and therefore the jobs process longer as needed. It
as well optimizes (disable) Metadata Caching accordingly to again
increase performance with deduplication appliances without a special
Veeam integration (like DDBoost protocol).

### Setting Target Optimization 

When choosing target mode, follow the guidelines described in the
“Deduplication” and “Compression” sections above.

For very high change rate VMs such as Exchange and SQL servers, choosing
WAN target mode (256K block size) may be more efficient even for local
backups. However, WAN target mode takes high CPU load. Tests showed a
50% or more reduction of incremental backup data amount when using WAN
target mode for incremental backups of highly transactional servers.

For compression, the **Optimal** level is almost always the best.
However, if the target is a hardware deduplication appliance, you will
generally achieve the best compression and deduplication ratios by
writing the data to the storage uncompressed, or by using
**Dedupe-friendly** compression level. Since the target data mover (that
is, Veeam Transport service) is available to decompress data prior to
writing it to the target, this option is the recommended one for such
cases.

You can also use 4K blocks data alignment, which can be helpful for
deduplication storages with fixed block size. If necessary, use the
**Align backup file data blocks** setting to change the block size.

### Encryption 

Detailed description of encryption settings and their influence on the
backup infrastructure is provided in the “ Encryption “section above in
this document.

Job encryption might cause some proxy resources consumption, since Veeam
Backup & Replication encrypts data on the proxy and then sends it to the
repository. You might need to add a bit more computing resources on the
proxy to compensate the additional load.

**Tip:** By default, Veeam Backup & Replication will encrypt data if one
of the Proxy/Repository interfaces (if used or not) holds a public IP
address. Please refer to the “Encryption” section of this guide for more
details.

For general guidelines for encryption configuration, refer to the Veeam
User Guide:
<http://helpcenter.veeam.com/backup/80/vsphere/encryption_keys.html?zoom_highlightsub=encryption>.

### Backup Jobs Chaining 

Chaining backup jobs is convenient in certain circumstances, but should
be used with caution. For example, if a job in such chain fails or stops
responding, the whole job chain delivers poor backup success rate.

A most common way to handle multiple jobs is to let Veeam Backup
&Replication Scheduler handle the proxy/repository ressources by
starting more jobs (overbooking) than available proxy/repository
resources. This will allow Veeam Backup & Replication to optimize the
backup time window with its load balancing algorithm.

### Load Balancing 

When planning jobs schedule, you should think of balancing the load on
source and target disks. Too many jobs accessing the same disk will load
the storage significantly; this makes the job run slower or may have a
negative impact on the VMs performance. To mitigate this problem, you
can utilize the Backup I/O Control settings (see the “ Backup I/O
Control“ section above).

Veeam also employs a load balancing method that automatically allocates
a proxy, making a choice between all proxies managed by Veeam Backup &
Replication that are available at the moment.

For more details on load balancing, refer to the Veeam Backup &
Replication User Guide at
<http://helpcenter.veeam.com/backup/80/vsphere/index.html?resource_scheduling.html>.

### Binding Jobs to Specific Proxies 

Refer to the User Guide in order to examine the advanced deployment
scenario with multiple proxies:
<http://helpcenter.veeam.com/backup/80/vsphere/advanced.html>

While configuring a backup job, you can disable the automatic proxy
selection. Instead, you can select particular proxies from the list of
proxies managed by Veeam backup server, and appoint them to the job.
This is a very good way to manage distributed infrastructures; also it
helps you to keep performance under control.

For example, you can back up a cluster residing on multiple blade
chassis. In this case, if you use virtual proxies, keep the proxies load
well-balanced and optimize the network traffic.

Dedicated proxies can be also very helpful if you use a stretched
cluster and do not want proxy traffic to go across inter-switch link.

See the illustration below as a good starting point to reach and keep
control on high backup throughput. In this example, administrator wants
to keep network traffic as much as possible inside the chassis; only the
proxy-to-repository traffic goes via an external link.

![](../media/image34.png)

**Tip:** To optimize load balancing in a distributed environment where
backup proxies are rolled out to multiple sites, it is recommended to
select all proxies from the same site in the job.
