# Direct Storage Access

Under "Direct Storage Access" mode selection Veeam summarize the VMware own
"Direct SAN Access" and the Veeam own  "Direct NFS Access" modes.

The Direct Storage Access Mode uses a direct data path (a Fibre Channel or iSCSI) between the VMFS datastore and the backup proxy for
data transfer. The Proxy need at least read access to the datastores so
Fibre Channel Zoning, Networking and LUN masking on the Storage need to
reflect this.

To use Direct NFS backup mode the Proxies need access to the NFS network
and need to be member of the NFS Storage System "export policy" for read-
write access.

## Pros

-   Direct SAN Access mode provides very fast and the most reliable
    predictable backup performance (typically, using 8 Gb Fibre Channel
    or 10 GbE for iSCSI and NFS).

-   Produces zero impact on VMware vSphere hosts and (VM) production networks for backup data transport.

-   It is also possible to perform full VM restore using Direct SAN Access mode if backup proxies that can use the Direct SAN Access mode are available in the backup infrastructure and the VM disks are thick-provisioned. This mode will be used automatically.

-	Direct NFS is the fastest restore mode for NFS datastores. It uses multithreaded read and writes.

- 	For NFS Datastore based VMs this mode provides optimized STUN times
	and maximum backup speed without the negative effects that are known
    for NFS based datastore when HotAdd or NBD mode is used. It is very
    fast compared with the other backup methods as it uses multithreaded
	reads.

-   Direct SAN can be used at replica target for the initial replication (with thick-provisioned disk). Direct NFS can be used for initial and incremental replication at target. Both can be used at Replica source for all read data streams.

## Cons

-   Typically Direct SAN Access requires a physical server for an Fibre
	channel or iSCSI connection. A virtual only configuration for iSCSI
	based datastores are possible but would transport the data through the
	ESXi hosts.

-   For Fibre Channel or iSCSI Direct SAN-based restore is possible only
	for thick-provisioned VM disks. At restore the datastream needs to be
    coordinated in the background with vCenter or an ESXi host which can slow down the restore speed. Consider adding additional HotAdd Proxys for restore (FC/iSCSI only).

-   Direct SAN mode (FC/iSCSI only) is the most difficult backup mode to
	configure as it involves reconfiguring not only the storage but also the SAN, (FibreChannel Zoning, LUN masking, or reconfiguration of iSCSI targets) to provide the physical proxy server(s) with direct access to the production VMFS datastores. When such configuration has been implemented it is extremely important to ensure that HBAs, NIC drivers, firmware
    is up-to-date and that multipathing driver software (e.g. MPIO) is
    properly configured.

For more information about configuring Direct SAN Access refer to FAQ
at [Veeam Community Forums: Direct SAN Access
Mode](http://forums.veeam.com/vmware-vsphere-f24/vmware-frequently-asked-questions-t9329.html#p39948)

## Example

If operating datastores and vRDMs on a block storage that can share the
LUNs to different Shared Storage systems like Fibre Channel, FCoE and iSCSI. You can add a backup proxy as a member to that shared storage using LUN masking. This will allow for accessing the storage system for backup and restore.

Ensure that a connection between the storage and backup proxy can be established. Verify FC HBAs, zoning, multipath, driver software and iSCSI configurations including any network changes. To test the connection it is best practices to count the volumes at windows disk manager adding one disk per storage system at a time. Later add all the others. This will streamline the search for errors.

## Recommendations

-   Use the multipath driver software of the storage vendors choice
    (preferred integration into Microsoft MPIO) to avoid disk or cluster
    failovers at storage level. This will also prevent the whole storage
    system from being affected by possible failovers if wrong data paths
    are used. It is highly recommended to contact the storage vendor for
	  optimal settings.

-   If you attach a great number of volumes to the backup proxy consider that logging and search for the correct volume at the job run can require extra processing time per VM disk (as well as for overall volume count). To avoid this, the Veeam logging of the particular process can become the bottleneck you can disable this with the registry setting

  -   Path: `HKEY_LOCAL_MACHINE\SOFTWARE\Veeam\Veeam Backup and Replication`
  -   Key: `VDDKLogLevel`
  -   Type: REG_DWORD
  -   Value: 0
  -   Default: 1
  
  **Note**: As this reduces the amount of information in debug logs
	remember to enable it again when working with Veeam support (to
	facilitate debugging of the Direct SAN Access-related challenges).

-   To achieve performance versus cost use fewer proxies with
    more CPU cores available. This will help to fully utilize the HBA or
    NIC capacity of each proxy server. A 2 CPU System with 2x 12 cores is seen as a good balanced configuration between throughput and costs.



## Security Considerations for Direct SAN mode with FibreChannel or iSCSI

During deployment of the proxy role to a Windows VM, Backup &
Replication uses the following security mechanisms to protect them:

-   Changes the Windows SAN Policy to "Offline (shared)". This prevents
    Windows from bringing the attached volumes online and also prevents
    Windows write operations to the volumes. However if you want to be
    able to restore thick VM disks by this mode you have to disable `automount` and enable the volumes. If you set the disks read only Veeam will choose another restore mode and it will failover to NBD mode through the same proxy.

-   Veeam deploys VMware's VDDK Kit into the backup proxy server, in most
    cases this VDDK Kit coordinates read and write processes (Direct SAN restore) with VMware vSphere allowing VMware's Software to control
    the read and write streams in a reliable manner.

If necessary you can take additional measures as follows:

- 	Disable automount. Open a command box and type "diskpart". Then disable
	automaount with "automount disable".

-   Disable Disk Management snap-in with:

    **Group Policy\User Configuration > Administrative Templates > Window >  Components > Microsoft Management Console > Restricted/Permitted snap-ins > Disk Management**.

-   Avoid providing excessive administrative access to the account used
    to run Veeam proxy servers.

-   Present LUNs as read-only to the backup proxy server. This
    capability is supported by most modern storage. When possible implement
    read-only LUN masking on the storage system or read-only zoning on the
    Fibre Channel switches (possible on most Brocade variants).

If a VMFS datastore is manually brought online in Windows Disk Management by
mistake, and disk resignaturing is initiated, the datastore will become unavailable,
and VMs will stop. Please contact VMware Support for assistance with recreating
the VMFS disk signature. For more information on Windows re-signaturing process
and VMware datastores please refer to [VMware KB1002168: Unable to access the VMware virtual machine file system datastore when the partition is missing or is not set to type fb](http://kb.vmware.com/kb/1002168)


## Summary

Use Direct Storage Access whenever you have the chance to do so for fast backup speed and reduced load on the ESXi host. For fibre channel and iSCSI based datastore it processes backup data reliably and very fast. Consider using HotAdd proxies as these restores are much faster than Direct SAN restores. Direct SAN uses VDDK which can cause excessive metadata updates while hot-add restore bypasses VDDK.

For NFS datastores Veeam's Direct NFS mode is the mode you should choose for backup and restore as it delivers top speed without any negative side effects. You can use it for virtual and physical proxy deployments.

<!-- AN2016 21.06.2016 -->
