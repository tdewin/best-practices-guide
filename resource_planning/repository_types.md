# Repository Type
Being storage-agnostic, Veeam Backup & Replication supports a wide range of repository types, each offering its own set of specific capabilities. So when deciding on repository storage, you might consider the following:
-   Capacity
-   Write performance
-   Read performance
-   Data density
-   Security
-   Backup file utilization

As a basic guideline, a repository should be highly resilient, since it is hosting customers data. It also needs to be scalable, allowing the backup to grow as needed.

Organization policies may require different storages for backups with different
retention. In such scenario, you may configure two backup repositories: 
-   A high-performance repository hosting several recent retention points for instant restores and other quick operations
-   A repository with more capacity, but using a cheaper and slower storage, storing long-term retention points

You can consume both layers by setting up a backup copy job from the first to the second repository, or leverage Scale-out Backup Repository, if licensed.

## Server-Based Repository: DAS or SAN?

### Direct-Attached Storage
This is a cheap, easy-to-use solution that can be very efficient in terms of performance; however, if not used as part of a Scale-out Backup Repository, it is less manageable due to non-transportable volumes, capacity growth, and so on.
-   Since a DAS storage can be fully dedicated to backup operations, this type of repository is considered to offer a good balance between “performance” and “cost” factors.
-   A strong benefit of a DAS repository is that it supports the features offered by Veeam Backup & Replication in a very flexible way. In particular, it provides good read and write performance, sufficient for Veeam vPower-based features (such as Instant VM Recovery, SureBackup, and others). As it typically provides good random I/O performance, it will be the optimal solution when using I/O intensive backup modes such as reverse incremental or forever forward incremental (also used in backup copy job).

However, consider that though DAS is a valuable option in many cases, its scalability may not meet an organization’s requirements.

**Tip:** To achieve optimal performance, it is often required to install a battery module to the server’s controller card in order to enable write-back mode for the internal cache. A DAS is a shelf with disks, and all the intelligence of the solution is delegated to the controller installed in the connected server.

| Pros        | Cons                    |
|:------------|:------------------------|
| Cost        | Manageability           |
| Performance | Single point of failure |
| Simplicity  | Monolithic              |

### SAN Storage

This is a more advanced and manageable solution that offers the same advantages as DAS, and adds more advantages like higher availability and resiliency.

The volume size and quantity are easily adjustable over time, thus offering a truly scalable capacity.

**Tip**: You can configure multiple backup repositories on the SAN storage to increase repository throughput to the storage system.

| Pros                   | Cons       |
|:-----------------------|:-----------|
| Reliability            | Complexity |
| Performance            | Cost       |
| Technical capabilities | ** **      |

## Windows or Linux?
The main difference between Windows and Linux in regards to Veeam repositories is the way they handle NAS shares – this can be summarized as a choice between NFS and SMB. Generally, a Linux-based repository can handle a higher throughput than a Windows-based repository with same CPU/RAM/Disk resources. However, if you deploy Veeam in a small-sized infrastructure, you may want to keep the configuration "all-in-one" on a single Windows server, so deploying a Linux server as a repository could add extra complexity to the solution. Other possible concerns relate to cost and administrative burden.

## Physical or Virtual?
You can use a virtual machine as a repository server, however, keep in mind that the storage and associated transport media will be heavily occupied.

If you are using a SAN storage, it can be accessed through software iSCSI initiators, or directly (as a VMDK or RDM mounted to the Repository VM).

Best practice is to avoid using the same storage technology that is used for the virtualized infrastructure, as the loss of this single system would lead to the loss of both copies of the data, the production ones and their backups.

In general we recommend whenever possible to use physical machines as repositories, in order to maximize performance and have a clear separation between the production environment that needs to be protected and the backup storage.
