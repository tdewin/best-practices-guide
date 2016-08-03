# Transport Modes

Job efficiency and time required for its completion are highly dependent
on the data transport mode. Transport mode is a method used by the Veeam
proxy to retrieve VM data from the source host and write VM data to the
target destination.


-   **Direct Storage Access:** in this mode backup proxy server has direct
    storage access to the location on which VMs reside. In this case the backup proxy will retrieve data directly from the storage bypassing the
    ESXi infrastructure.

Depending on the connection the proxy can be deployed as follows:

-   On a physical server with FibreChannel, FCoE, iSCSI or NFS

-   On a VM with iSCSI and NFS

-   Both options can be used for Storage Snapshot Integration which
    uses the Backup from Storage SnapShot (BfSS) feature with a suitable storage array.

-   **Virtual Appliance mode:** To work in this mode the backup proxy
    must be deployed as a VM. For smaller deployments (e.g., several
    branch offices with a single ESXi host per each office) you can
    deploy a virtual backup proxy on a ESXi host that has access to all
    required datastores. When backup or replication takes place and a VM
    snapshot is processed the snapshotted disks are mapped to the proxy
    to read data (at backup) and write data (at restore/replication);
    later they are unmapped.

    **Note:** As the disks are hot-added, you can find the mode’s name
    referred to as `hotadd` in documentation and logs.

-   **Network mode:** another option is to use the VMKernel Interfaces
    of VMware ESXi hosts to read and write VM data. As it needs no special
    configuration working flawlessly, this mode is also used as a
    default fail-over option for other modes. This universal transport
    mode can be used with all storage protocols between primary storage
    and ESXi host as it uses the default VMware Storage Stack for read/write through the VMKernel Interfaces by NFC protocol (via TCP
    port 902). Data Transport by use of NFC is limited by VMware to
    protect management datastreams to around about 40% of the available
    throughput. The Network proxy can be deployed as a physical server or a VM.

    **Note:** This backup mode is also referred to as NBD.


The following sections explain transport modes in detail.
<!--AN2016 22.06.2016-->
