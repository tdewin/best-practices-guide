# Transport Modes

Job efficiency and time required for its completion are highly dependent
on the data transport mode. Transport mode is a method used by the Veeam
proxy to retrieve VM data from the source host and write VM data to the
target destination.

The primary role of the backup proxy is to provide an optimal path for
backup traffic and to enable efficient data transfer. Therefore, to
deploy a backup proxy, you need to analyze the connection between the
backup proxy server and storage it will be accessing. Depending on the
type of connection, the backup proxy can be configured in one of the
following modes (preferred mode is Direct SAN, if possible; all modes
will be explained later in more details):

-   **Direct SAN mode:** in this mode, backup proxy server has direct
    access to the storage on which VMs reside. In this case, the backup
    proxy will retrieve data directly from the storage, bypassing the
    ESXi infrastructure.\
    Depending on the connection, the proxy can be deployed as follows:

    -   On a physical server(FC/SAS/FCoE/iSCSI and, in cases, NFS on
        NetApp - when Backup from Storage Snapshot feature is enabled
        and licensed.)

    -   On a VM (iSCSI and, for the special case, NFS on NetApp when
        Backup from Storage - Snapshot feature is enabled and licensed).

-   **Network mode:** another option is to use the VMKernel Interfaces
    of VMware to read and write VM data. As it needs no special
    configuration and works flawlessly, this mode is also used as a
    default fail-over option for other modes. This universal transport
    mode can be used with all storage protocols between primary storage
    and ESXi host , as it uses the default VMware Storage Stack for read
    and write through the VMKernel Interfaces by NFC protocol (via TCP
    port 902).\
    This proxy can be deployed as a physical server or a VM.

**Note:** This backup mode is also known as NBD.

-   **Virtual Appliance mode** - to work in this mode, the backup proxy
    should be deployed as a VM. For smaller deployments (e.g., several
    branch offices with a single ESXi host per each office), you can
    deploy a virtual backup proxy on a ESXi host that has access to all
    required datastores. When backup or replication takes place and a VM
    snapshot is processed, the snapshotted disks are mapped to the proxy
    to read data (at backup) and write data (at restore/replication);
    later they are unmapped.

**Note:** As the disks are hot-added, you can find the mode’s name
referred to as `hotadd` in documentation and logs.

The following sections explain transport modes in detail.