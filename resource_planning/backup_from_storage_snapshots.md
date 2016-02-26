# Backup from Storage Snapshots

Storage integration implemented with Veeam Backup & Replication supports
HP StoreVirtual, HP StoreServ and NetApp Data ONTAP storage types.
Backup proxy servers are utilized for storage discovery, reading and
writing data and for leveraging storage snapshots, which minimizes the
VMware Snapshot commit load.

**Note**: To read more about storage integration, refer to Veeam Backup
& Replication User Guide and corresponding sections of this guide.

Veeam Backup & Replication uses storage snapshots to minimize VMware VM
snapshot lifetime and to reduce load on VMware ESXi hosts and storage
systems.

The Storage integration feature utilizes proxy servers to read data
directly from these snapshots (without adding them to a proxy ESXi
host), to transport data and to scan the storage systems for possible
restore points in storage snapshots. Based on the supported storage
protocol, this feature requires direct network connections or SAN
connections (like iSCSI/Fiber Channel).

**Note**: For more information, refer to the corresponding sections of
this guide and Veeam Backup & Replication User guide.