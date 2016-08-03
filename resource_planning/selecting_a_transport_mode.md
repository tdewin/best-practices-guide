# Selecting a Transport Mode

Depending on the size of the environment, there are different recommendations for selecting a transport mode. For simplicity, a couple of definitions will be used in this section:

| Name | Definition |
| -- | -- |
| Very small | Single host with local disks as primary datastores. Typical ROBO configuration. |
| Small | 2-4 hosts with shared storage. Typical ROBO configuration or small datacenter |
| Medium | 4-20 hosts with shared storage|
| Large | 20-100 hosts with shared storage |
| Enterprise | Over 100 hosts |

Keep in mind that within larger datacenters, multiple definitions may apply. As an example, it is possible that a separate management or DMZ cluster without shared storage could benefit from using the "Very small" or "Small" recommendations, while the main production environment is leveraging recommendations based on "Medium" to "Enterprise" datacenter size.

##  Very small

-   Virtual Appliance (Hot-Add) mode is the recommended option, as it
    gives you the best performance.

-   NBD over 10GbE VMKernel interfaces link will provide a very stable
    and good performing solution without any special configuration needed.

-   NBD over 1GbE VMKernel interfaces can be used for failover.

-   Direct Storage Access mode or Backup from Storage Snapshots
  modes are typically unavailable, as the disks of the host
	are local and thus cannot be mounted to an external proxy server.

## Small and Medium

-   If storage integration is available, use Backup from Storage Snapshots (BfSS)[^1]

- 	For NFS based Storage, use Direct Storage Access

- 	For shared storage connected via FC or iSCSI, you can choose one of the
	following two modes:
	 - **Physical proxy:**
	  Direct Storage Access will provide the best
    backup performance. For example, you can configure a physical server
    with access to FC datastores on the local site and perform backups to a
    local repository. If you use
  	thin-provisioned disks for the VMs, configuring a dedicated backup proxy
  	for restoring via Virtual Appliance (hot-add) mode can help to increasing
  	restore performance.

    - **Virtual proxy:**
  	The Virtual Appliance (hot-add) mode is a good an fast backup mode.
    Avoid to backing up VMs on NFS datastores using hot-add. Use Direct Storage
  	Access or NBD backup modes instead.

-   NBD over 10 GbE VMKernel Interfaces link will provide a very stable
    and good performing solution.

-   NBD over 1 GbE VMKernel Interfaces can be used for failover and for
    situations where you do not have to transport much data.

-   When using NBD, check the [Network Mode](./network_mode.md) chapter for tuning tips.

## Large
In addition to the above considerations for Small and Medium, please see the
following guidelines:

- 	When Direct Storage Access, or Backup from Storage Snapshots are unavailable,
    and when virtual proxy servers are disallowed, Network Mode (NBD) is the
    only choice. In such cases, 10GbE interfaces are a must.

- 	For virtual only deployments (virtual proxies only) in environments with many
    isolated clusters, using network mode (NBD) may be ideal. As hot-add requires
    at least one proxy within each cluster, it may require many more proxy servers
    compared to using network mode.

-   A combination of hot-add mode for large clusters and NBD mode for smaller
    clusters may be ideal.

## Enterprise
In addition to the above considerations for Large, please see the
following guidelines:

-   In large enterprise scale environments, the deployment of Veeam components,
    configuration and job creation is typically automated using the
    Veeam PowerShell SDK.

-   To balance the management load, it is recommended to use multiple
    Veeam backup servers for at least every 5,000 VMs and federate them
    for central reporting and administration by using either Veeam Enterprise
    Manager, Veeam Managed Backup Portal, Veeam Management Pack for Microsoft System
    Center Operations Manager or Veeam ONE.

-   When running a central backup server and with multiple branches connected to
    it, a dedicated backup server is
    recommended for at least every 200 branches. Consider using Veeam
    Enterprise Manager for federation.

[^1]: In case storage integration is used with Backup from Storage Snapshots (BfSS), the overhead of mapping blocks from VMware CBT and the storage snapshot can increase processing time and lead to longer backup windows. To mitigate, consider the majority if the VMs can be backed up with one of the other transport modes and use BfSS only for the largest VMs or high change rates (typically 10% of VMs). Veeam ONE Change Rate Estimation report can help to identify such VMs.
