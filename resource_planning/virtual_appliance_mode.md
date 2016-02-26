# Virtual Appliance Mode

Veeam’s Virtual Appliance (or VMware’s Hot-add) mode has become quite
widespread, as it is the default setting with Veeam Backup & Replication
all-in-one deployment in a virtual machine (for details, see the
[Deployment
Scenarios](http://helpcenter.veeam.com/backup/80/vsphere/deployment_scenarios.html)
section of the User Guide). It is often used when Veeam is deployed in
branch office configurations (ROBO).

This mode supports a 100% virtual deployment, using direct storage
access through VMware ESXi storage I/O stack, thus having a very little
overhead. For example, in the case of a backup, the disk files from a
VMware snapshot are mapped by VMware Disk Hot-Add to a virtual backup
proxy server and later unmapped after backup/restore.

**Note:** For more information on how it works, refer to the section
“[Data Retrieval and Restore in Virtual Appliance
Mode](http://helpcenter.veeam.com/backup/80/vsphere/virtual_appliance_hiw.html)”
of Veeam User Guide.

Virtual Appliance mode can be recommended for proxies in highly dynamic
environments where it can be difficult to maintain access to newly
created datastores for Direct SAN Access. In such scenarios, using
Virtual Appliance mode for data transport will significantly reduce
administrative overhead due to leveraging VMware Hot-Add.

Virtual Appliance mode should be used when it is impossible to leverage
Direct SAN, for example, in the case of local datastores or NFS shares -
then using VMware Hot-Add will likely provide the optimal throughput.

This mode is also a good choice for VMware VSAN configurations, as
Veeam’s built-in intelligent load balancing will have VM disk placement
awareness.

**Note:** For details on using this mode for NFS datastores, see the
“Interaction with vSphere: Considerations for NFS Datastores ” section
of this guide.

-   If using the Virtual Appliance data transport mode with shared
    storage, it is necessary to deploy at least one proxy for each
    environment where you add a specific datastore. In most cases,
    datastores are added on a per-vSphere cluster base, so deploy at
    least one Hot-Add proxy there.

-   When backing up from local datastores, it is necessary to deploy one
    proxy per host (local disks in the ESXi are used for VM data),
    otherwise the proxy servers will fail back to Network mode
    (VMware’s NBD).

When planning for the Virtual Appliance mode for a backup proxy,
consider the time required for actual hot-add operations (such as adding
and removing VM disks from the source virtual machine) – they can add up
to 1-2 minutes per VM. Therefore, for a backup job containing 1000
virtual machines, this could result in more than two hours of adding and
removing disks with no actual data processing. To mitigate the issue,
enable parallel processing and process multiple disks from the same
virtual machine simultaneously (using this transport mode).

**Tip:** It is recommended to benchmark how such operations affect the
backup window - by monitoring a test job in the vSphere console.

## Pros

-   Using the Virtual Appliance mode for proxy servers enables a fully
    virtual deployment.

-   As the proxy will perform source-side data deduplication and
    compression, this mode will provide very good performance in
    environments running 1 GbE configurations.

## Cons

-   If working in this mode, backup proxy will occupy the virtual
    infrastructure resources, impacting consolidation ratio. This could
    ultimately require additional physical ESXi hosts and licensing.

-   This mode requires additional planning and configuration in the
    enterprise environments because of the additional large disk Hot-Add
    processes in VMware vSphere.

## Considerations and Limitations

Additional load is put on the vCenter Server and ESXi hosts as each disk
is mapped and unmapped (disk hot-add) at the backup proxies.

**Note**: For more information, see vCenter Server connection overview
in the "Veeam Backup & Replication Server" section of this guide.

It may occur that VMware API reports that unmap and snapshot commit were
done correctly, but a snapshot file still remains on disk. These
"orphaned snapshots" will grow over time and can fill up the datastore,
leading to downtimes (such a situation is most possible on NFS-based
storage). To overcome this issue, Veeam offers the following methods:

-   Veeam Snapshot Hunter – this feature introduced in v8 supports
    automatic clean-up of orphaned snapshots after each backup process.
    It can also repair the orphaned snapshots that were left over by
    other software (including VMware).

-   Bypassing VDDK processing to overcome some limitations and
    performance challenges, in particular:

<!-- -->

-   Veeam can back up multiple disks of VM in parallel on same proxy
    (default number is 4).

-   Typical "I/O bursts" do not happen at Hot-Add restore or replica
    target Hot-Add processing.

<!-- -->

-   To avoid some VMware issues related to NFS datastore and Hot-Add
    processing (described at
    <http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2010953>
    ), enable a specific setting that will process VM backups only on
    backup proxies that run on the same host. For details, see
    <http://www.veeam.com/kb1681> .

**Note:** For additional tips, refer to the “Impact of Snapshot
Operations” section of this guide.

## Recommendations {#recommendations-2 .pseudo4}

-   You will need at least one type of SCSI controller on each proxy in
    the infrastructure.

-   Add an extra SCSI controller to allow for more VM disks processing
    in parallel (check the corresponding Veeam proxy settings – default
    value is 4).

-   Specific client OS limitations for Hot-Add processing documented in
    Veeam Backup & Replication Release Notes at
    <http://www.veeam.com/veeam_backup_8_0_release_notes_rn.pdf> and in
    the KB article at <http://www.veeam.com/kb1054> . To test whether
    Hot-Add data transport mode is possible, refer to
    <http://www.veeam.com/kb1184>.

-   Disk Hot-Add is supported in all VMware vSphere editions, starting
    from a standalone ESXi host with a license and including vSphere
    Standard and vSphere Essentials (unlike the CPU and RAM Hot-Add
    Feature that is only available in higher VMware vSphere editions ).

-   When deploying hot-add backup proxies, try to avoid cloning existing
    VMs, as this may lead to identical UUIDs in the vSphere environments
    and cause hot-add operations to fail.

-   You may re-use any existing Windows server VM (to save
    on licensing). The Veeam data mover process runs with ‘below normal’
    priority by default.

**Note:** Changed block tracking (CBT) will be disabled for these
hot-add proxies. Consider that it may impact the backup window in case
the said virtual machines should be included in backup or replication
jobs.