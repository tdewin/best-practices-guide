# Backup Copy Job

Instead of just copying backup files to a second destination, Veeam uses
a more intelligent and more secure way of bringing restore points to a
second backup target. Backup copy jobs read specific VM restore points
from backup files and store them as a new backup file chain on the
destionation. The second chain is independent from the first chain and
adds therefor an additional level of protection. You can store VMs from
multiple jobs at the backup copy job target. As an option, select one or
several VMs from a bigger backup job as source (if you do not want to
backup all VMs to the backup copy job desitionation).

Every backup copy job creates its own folder on the target backup
repository and stores all copied restore points to that location. The
folder has the same name as the backup copy job.

**Tip:** Though a backup copy job cannot be targeted at the same
repository where the source backup job places its backup files, it is
possible to process backup copy jobs between 2 repositories on the same
repository server.

Once created, a backup copy job will immediately start processing the
latest existing restore point for all VMs included in the job (as long
as it has been created less than one synchronization interval before the
start of the backup copy job).

By default, Veeam Backup & Replication keeps 7 restore points on the
target backup repository in case of simple retention policy (see the
“[Simple Retention
Policy](http://helpcenter.veeam.com/backup/80/vsphere/index.html?backup_copy_simple_retention.html)”
section of the User Guide for details). If you plan to use
Grandfather-Father-Son (GFS) retention, refer to the “[GFS Retention
Policy](http://helpcenter.veeam.com/backup/80/vsphere/index.html?backup_copy_gfs.html)”
section for details.

Even if a backup copy job processes several VMs, it creates one backup
file on the target backup repository and stores to it data for all VMs
processed by the job.

If a backup copy job cannot process all requested VMs during its
synchronization interval, the job will still create a backup file on the
target backup repository, but some VMs will be left inconsistent or
unprotected. This might be caused by precedence of the backup task over
the backup copy task.

Limitations of backup copy jobs are described in Veeam Backup &
Replication User Guide at
<http://helpcenter.veeam.com/backup/80/vsphere/index.html?backup_copy_select_point.html>.

### Backup Copy Job Scheduling 

By design, a backup copy job is a continuous process that runs
permanently. This process includes several stages.

A backup job restarts itself at the defined **Copy every: …** interval
setting (default is 12:00 AM every day). and looking for new restore
points of the selected VMs. On the **Scheduler** tab, it is possible to
separately schedule the data transfer itself. Veeam global traffic rules
can be used to limit bandwidth over all Veeam jobs at a specific
connection (IP addresses or ranges).

A single backup copy job can also monitor multiple backup jobs so the
concept of the "interval" is to define two things: how often it should
be looking for new points, and for a daily interval, what time it should
start looking for points. If you set an interval for 1 day, that's
telling the backup copy job that once a day, starting at the selected
time, it should begin looking for new restore points and, as soon as it
finds one, copy it. However, once a single point is copied, another
point for that VM will not be copied until the next interval starts.

The idea behind this is that you may run backups locally more often (for
example, hourly), but you may only want to copy offsite once a day or
weekly, thus you can set the backup copy "interval" independently of the
schedule of the backup jobs it is monitoring. Think of it almost like
about setting an SLA.

The backup copy job has the following phases:

1.  **Data transfer (synchronization) phase** — during this phase, the
    backup copy job checks for a new restore point on source, creates a
    file for a new restore point on target and starts copying the most
    recent restore point of each processed VM to the target repository.
    The data transfer (synchronization) phase starts at specific time
    configured in the job properties (see
    <http://helpcenter.veeam.com/backup/80/vsphere/index.html?backup_copy_sync_interval.html>).
    You can define any interval needed in minutes, hours or days.
    Moreover, you can specify the time slot during which data can and
    cannot be transferred over the network, thus regulating network
    usage (see
    <http://helpcenter.veeam.com/backup/80/vsphere/index.html?backup_copy_window.html>).

2.  **Transform phase** — you can configure Veeam Backup & Replication
    to perform a number of additional transform operations on the target
    backup repository: transforming a backup chain, removing deleted VMs
    from restore points, compacting a full backup file. The transform
    phase begins after all restore points are copied to target.

**Note:** Consider that the transform process itself puts additional
pressure on the target repository. In larger environments with
deduplication storage appliances used as backup repositories or with
backup copy jobs processing a huge number of VMs or VMs of big size, the
transform process can take significant amount of time. In the backup
copy job chain, for each block in the first incremental point there will
be 1x read and 1x write I/O. For example, for 8 disk NAS in RAID5, 100
GB of daily change in the worst case may take around three hours.

In the properties of the backup copy job, you can select to perform
post-job activities, such as execution of custom scripts or sending job
results by email. Post-job activities are performed after all transform
operations are completed.

3.  **Idle phase** — for the most time, the backup copy job remains in
    the *Idle* state, waiting for a new restore point to appear on the
    source backup repository. When a new synchronization interval
    starts, as specified by the “Copy every” setting of the job, steps
    1-2 are repeated.\
    For more information, refer to the Backup copy job section of the
    Veeam Backup & Replication User Guide at
    <http://helpcenter.veeam.com/backup/80/vsphere/index.html?backup_copy_job_task.html>.

**Note:** The synchronization interval defines how frequently the backup
copy job must copy the VM restore point from the source backup
repository to the target backup repository. For instance, if you select
to copy a restore point every 7 days, there will be one restore point on
target per week created by the backup copy job.

### Job Layout and Object Selection

#### Source Object Container {#source-object-container .pseudo4}

-   **Select from infrastructure**: this method of selection allows you
    to select a specific VM or container from the
    infrastructure perspective. In this case, the scheduler will look
    backward its starting time for the most recent restore point
    containing the VM(s) within the synchronization interval. The
    scheduler will look for restore points in all backups, no matter
    which job has generated the restore point. If the restore point is
    locked (the backup job is not ended yet), the backup copy job will
    wait for the restore point to be unlocked (the end of the
    backup job) and then start copying the VM(s) restore point according
    to its defined schedule.

<!-- -->

-   **Select from job**: this method of selection is very useful if you
    have multiple backups protecting the same VMs. In this case, you can
    bind the backup copy job to a specific restore point you want
    to copy. This kind of container selection will dynamically protect
    all VMs in the specified job(s), and only in this job.

<!-- -->

-   **Select from backup**: this method is equivalent to the **Select
    from job** method but allows for selecting specific VMs inside
    any job.

#### Backup Copy and Tags {#backup-copy-and-tags .pseudo4}

As you can select any VM to be copied from multiple backups, you can
consider quality of service-based configuration.

For instance, you may not want to apply GFS retention over some VMs like
web servers, DHCP, etc. In this situation, you can use VMware tags to
simplify the management of backup copy process. Tags can be easily
defined according to the desired backup copy configuration, using VMware
vSphere or Veeam ONE Business View.

### Backup Method

Since backup copy process reads VM data from backup files, the RPO and
the backup job method will be tied.

For instance, if you create a backup copy job based on a reverse
incremental backup job, every day when the backup job initiates its
transform phase, the backup copy job will be interrupted (if still
running), leaving inconsistent copies on the target backup repository.

**Tip:** If the first backup copy (full) cycle is expected to be very
long and if you cannot perform a seeding task (to learn more about
seeding, see:
<http://helpcenter.veeam.com/backup/80/vsphere/backup_copy_mapping_auxiliary.html>),
consider creating a forward incremental job, which will provide a backup
copy job with the requested time to complete. Also, make sure that the
backup copy job synchronization interval is long enough.\
Refer to this section of Veeam Backup User Guide for details about
backup copy issues :
<http://helpcenter.veeam.com/backup/80/vsphere/backup_copy_issues.html>

### Common Use: Backup Copy for a Whole Job 

Knowing all this, the most common way to configure a backup copy job is
to use a daily backup job as a source container and to schedule the
backup copy job to start a few minutes after the backup job it is tied
to.

Since the backup copy job requires some connections to the vCenter
Server, starting it a bit earlier than the backup jobs will lower the
number of simultaneous connections against the vCenter Server. As a
result, the backup copy will start copying data as soon as the backup
job ends and the source backup file on the backup repository gets
unlocked.

**Tip:** This is a common RO/BO use case of backup copy job.

### Additional Options

#### Restore Point Lookup

By default, after a restart of the backup copy job interval (the **Copy
after:** setting) it will analyse the VMs and the last transferred
restore points of it. If there was no restore point transferred in the
previous run of a VM, the backup copy job starts transferring the last
restore point immediately. If you do not want the backup copy job to
look backward at the starting time, use the **LookForward** registry key
to change the “looking direction” to only look for newer restore points.
This option is available since Veeam Backup & Replication 8.0.

The following forum thread provides a very good explanation of the
backup copy scheduler and the LookForward registration key:
<http://forums.veeam.com/veeam-backup-replication-f2/backup-copy-intervals-t24238.html>

According to the What's New documentation for Veeam Backup & Replication
v8, the registry key **"BackupCopyLookForward (DWORD) = 1"** should be
created **in "HKLM\\SOFTWARE\\Veeam\\Veeam Backup and Replication"**.

#### Backup Copy from Backup Copy

Since v8, it is possible to use a backup copy job as a source for data
transfer and to generate a third copy of VMs. For this, select the VMs
from infrastructure and specify the backup repository holding the
primary backup copy restore points as the source.

#### Job Seeding

Usually, a backup copy is sending data remotely. If it is necessary to
send data offsite over a slow WAN link, seed the backup copy job by
taking the following steps:

1.  Create a "local" backup copy job and target it at a removable device
    (for example, USB HDD) used as a backup repository. Run the created
    backup copy job to create a full backup file on this
    removable device.

2.  Once the backup copy job is over, delete the local backup copy job
    from the Veeam console.

3.  Transport the removable device with created backup files to the
    destination site.

4.  Copy backup files to the target backup repository.

5.  Create a definitive backup copy job on the Veeam console. On the
    **Target** step of the **Backup copy job** wizard, click the **Map
    backup** link and select the copied backup — this backup will be
    used as a “seed”.

If you are using a WAN accelerated transfer, refer to the WAN
Accelerator section for proper cache population procedure:
<http://helpcenter.veeam.com/backup/80/vsphere/index.html?wan_populate_cache.html>.

**Tip:** Sometimes VeeamZIP is used to create the actual backup for
seeding. VeeamZIP does not create the needed VBM metadata file. Instead
of creating a new backup and shipping it to the seeding target side
again, contact Veeam support to get a PowerShell script that can create
the VBM metadata file.

**Note:** Only the initial first run of a reverse incremental chain can
be used with seeding (but any forward incremental chain can be used).

#### Target Repository for Forever-Incremental Chain

The backup copy process is forever incremental. This means that only the
first transfer of VM data will be a full copy, and every subsequent
transfer will be incremental.

This will generate read I/O on the target repository at the end of the
job — Veeam Backup & Replication needs to transform the full backup and
optionally create GFS full copies of the VMs for archival purposes.
Knowing this, you may find a good balance between cost and performance
for the backup copy target repository storage. To get more information
about the backup repository planning, see the “Repository Server”
section above.

**Tip:** Once you are aware of the sequential nature of the backup copy
job, try to enhance performance if the target-to-source link is not the
bottleneck. For example, you can schedule more backup copy jobs at the
same time to get a better parallelization effect.
