# Direct SAN

The Direct SAN Access mode uses a direct data path (a Fibre Channel (FC)
or iSCSI connection) between the VMFS datastore and the backup proxy for data transfer.

## Pros

-   Direct SAN Access mode provides the fastest, the most reliable and
    predictable backup performance (typically, using 8 Gb Fiber Channel
    or 10 GbE for iSCSI).

-   Produces zero impact on VMware vSphere hosts and
    production networks.

-   Starting with Veeam Backup & Replication v8, it is also possible to
    perform full VM restore using Direct SAN Access mode: if backup
    proxies that can use the Direct SAN Access mode are available in the
    backup infrastructure, and the VM disks are thick-provisioned, this
    mode will be used automatically.

-   Can be used at replica target for the initial replication (with
    thick-provisioned disk).

-   This is the fastest method to restore thick-provisioned disk files.

## Cons

-   This mode requires FC, iSCSI, Infiniband or sharedSAS-based storage.
    NFS-based storage is currently only supported with NetApp storage
    systems when Backup from Storage Snapshot feature is enabled
    and licensed.

-   Typically, Direct SAN Access requires a physical server (in-guest
    iSCSI mapping is an exception to this rule, but it rarely
    makes sense).

-   Direct SAN-based restore is possible only for thick-provisioned
    VM disks.

-   This mode is the most difficult backup mode to configure, as it
    involves reconfiguring storage and SANs (FibreChannel Zoning, LUN
    masking, or reconfiguration of iSCSI targets) to provide the
    physical proxy server(s) with direct access to the production
    VMFS datastores. When such configuration has been implemented, it is
    extremely important to ensure that HBAs and NIC drivers and firmware
    are up-to-date, and that multipathing driver software (e.g. MPIO) is
    properly configured.

For more information about configuring Direct SAN Access, refer to FAQ
at [Veeam Community Forums: Direct SAN Access
Mode](http://forums.veeam.com/vmware-vsphere-f24/vmware-frequently-asked-questions-t9329.html#p39948)

## Example

If operating datastores and vRDMs on a block storage that can share the
LUNs to different systems (SharedStorage) like FibreChannel, FCoE, iSCSI
and SharedSAS, you can add a backup proxy as a member to that shared
storage using LUN masking. This will allow for accessing the storage
system for backup and restore.

Remember to ensure that a connection between the storage and backup
proxy can be established – for that, verify FC HBAs, zoning, multipath
driver software, and iSCSI configuration including network changes, or
SAS HBA and its configuration.

## Recommendations

-   Use the multipath driver software of the storage vendors choice
    (preferred integration into Microsoft MPIO) to avoid disk or cluster
    failovers at storage level. This will also prevent the whole storage
    system from being affected by possible failovers if wrong data paths
    are used.

-   If you attach a great number of volumes to the backup proxy,
    consider that logging and search for the correct volume at the job
    run can require extra processing time per VM disk (as well as for
    overall volume count). To avoid that, you can disable logging by
    setting the registry value to **VDDKLogLevel = 0 (DWORD)** in **the
    HKLM\\SOFTWARE\\Veeam\\Veeam Backup and** **Replication**
    registry key.

**Note**: As this reduces the amount of information in debug logs,
remember to enable it again when working with Veeam support (to
facilitate debugging of the Direct SAN Access-related challenges).

-   To achieve the best performance vs. costs, use fewer proxies with
    more CPU cores available. This will help to fully utilize the HBA or
    NIC capacity of each proxy server.

**Tip:** It is highly recommended to contact the storage vendor for
optimal settings.

## Security Considerations {#security-considerations .pseudo4}

While the datastores and vRDMs are mapped to the backup proxy server,
during deployment of the proxy role to a Windows VM, Veeam Backup &
Replication uses the following security mechanisms to protect them:

-   Changes the Windows SAN Policy to "Offline (shared)". This prevents
    Windows from bringing the attached volumes online, and also prevents
    Windows write operations to the volumes.

-   Deploys VMware's VDDK Kit into the backup proxy server – in most
    cases, this VDDK Kit coordinates ‘read’ and possible ‘write’ process
    (Direct SAN restore) with VMware vSphere.

If necessary, you can take additional measures, as follows:

-   Disable Disk Management snap-in with **Group Policy**\
     **User Configuration &gt; Administrative Templates &gt; Window
    Components &gt; Microsoft Management Console &gt;
    Restricted/Permitted snap-ins &gt; Disk Management**.

-   Avoid providing excessive administrative access to the account used
    to run Veeam proxy servers.

-   Present LUNs as read-only to the backup proxy server. This
    capability is supported by most modern storages. For that, implement
    read-only LUN masking on the storage system, or use ReadOnly Zoning
    on the FibreChannel Switches (possible on most SAN directors) for
    the Veeam proxy servers.

**Note**: Consider that read-only LUNs will prevent Direct SAN restore.

If a VMFS data store was brought online, including Windows volume
re-signaturing, you can contact VMware Support. For more information on
Windows re-signaturing process and VMware datastores, please refer to
[VMware KB1002168: Unable to access the VMware virtual machine file
system datastore when the partition is missing or is not set to type
fb](http://kb.vmware.com/kb/1002168) 