# SMB Repository

While an SMB repository (also called CIFS, even if this term is technically wrong) is often considered to provide less performance than direct attached storage, it still can provide very good results as a repository due to leveraging Veeam’s load-balancing technology for write operations, as explained in the next sections.

## Gateway Server

When you set up an SMB share as a repository, the following options are available:
-   Automatic selection of the server as the SMB gateway proxy (that is, the server that will host the target-side transport component and thus perform the role of “data writer” towards the SMB share itself).
-   Specify a specific server (among the available managed Windows servers in Veeam Backup & Replication) as a SMB gateway proxy.

The second option is very helpful in situations where the SMB share is located on a remote location, since it avoids that the automatic selection uses a server that is not local to the SMB share, thus having all synthetic operations or backup copy jobs occuring over the WAN link (which is usually slower than the local link). It's always recommended to use an SMB gateway server as close as possible to the SMB storage. By specifying the SMB gateway you have a better chance of keeping the data flow under control and avoid data crossing the WAN links unnecessarily.

As single stream performance for SMB repositories may be suboptimal,
you can potentially increase performance of your SMB storage by configuring
several repositories pointing to the same folder using different gateway servers.
With multiple proxies, the automatic SMB gateway may be a good option and can
be configured by selecting **Automatic** from the drop-down list.

**Tip**: Gateway servers must be properly sized as regular Windows repositories.
If you are using Automatic mode, remember that the same machine could be elected
backup proxy and gateway server simultaneously. Apply sizing it accordingly.

Another option for increasing the number of streams is using per VM backup
files. Please see the corresponding section of this guide for more information >
[Per VM backup files](./repository_planning_pervm.md)

### Load Balancing (with Automatic Selection)
Even when multiple proxies are used to process a given backup job, only **one*** Windows server (called “gateway server") per backup chain will be used to write data to the SMB share. In **Automatic** mode the first selected proxy in the running job will become the gateway server. If per-vm backup files are enabled, this applies to each per-vm chain, thus multiple gateway servers may be started concurrently.

Here are some recommendations for managing backup proxies used as gateway servers:
-   The networking between the multiple proxies should be sized correctly to allow data to flow from each proxy to the gateway server.
-   As the first backup proxy of a job is used as the gateway server, it may happen that all the gateway server instances of different jobs (or per-vm backup file chains) are started on the same proxy. This requires proper sizing of CPU and RAM; ensure resource monitoring is in place.

**Note:** Consider that increasing the number of jobs also increases the number of threads to the NAS storage.

![](../media/image12.png)

**NOTE for Creative: rework the image with SMB instead of CIFS NAS**

Scaling out using this approach will allow for processing larger amounts of data and optimize the throughput of the SMB shares. Best practice for large scale environments is to use at least a mid range or enterprise NAS storage system that provides good I/O performance. Low end NAS devices often have non official implementations of the SMB protocol that may improve performance test results, but may also corrupt backup files. For these devices it is discouraged to use SMB.
