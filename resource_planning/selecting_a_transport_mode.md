# Selecting a Transport Mode

Depending on the size of the environment, there are different recommendations for selecting a transport mode. For simplicity, a couple of definitions will be used in this section:

| Name | Definition |
| -- | -- |
| Very small | Single host with local disks as primary datastores. Typical ROBO configuration. |
| Small | 2-4 hosts with shared storage. Typical ROBO configuration or small datacenter |
| Medium | 4-20 hosts with shared storage|
| Large | 20-100 hosts with shared storage |
| Enterprise | Over 100 hosts |


##  Very small

-   Virtual Appliance (Hot-Add) mode is the recommended option, as it
    gives you the best performance.

-   NBD over 10GbE VMKernel Interfaces link will provide a very stable
    and good performing solution

-   NBD over 1GbE VMKernel Interfaces can be used for failover.

-   Direct SAN Access mode or Veeams storage integration based backups
    cannot be implemented, as the disks are local and thus cannot be
    mounted to a physical proxy server.

## Small

-   If storage integration is possible, the corresponding transport
    modes are preferred.

-   If the primary datastore runs on block protocols like Fiber Channel
    or iSCSI, then Direct SAN Access mode will provide the best
    performance (if all corresponding requirements are met).

For example, you can place a physical server with access to FC data
stores to the local site and perform backups to a local repository
(scenario with a single Veeam backup server). If you use
thin-provisioned disks for the VMs, configuring a dedicated backup proxy
for restore in the Virtual Appliance (Hot-Add) mode can help to increase
restore performance.

-   The Virtual Appliance (Hot-Add) mode is a recommended option if the
    above modes cannot be used.

**Tip:** If you are using NFS primary data stores, consider setting up a
backup proxy on same host (as described in the previous scenario).

-   NBD over 10 GbE VMKernel Interfaces link will provide a very stable
    and good performing solution.

-   NBD over 1 GbE VMKernel Interfaces can be used for failover and for
    situations where you do not have to transport much data, or if you
    cannot use the Direct SAN Access mode but look for the
    best reliability.

## Medium

-   Use storage integration for all VMs with a high change rate. Veeam
    ONE “VM Change Rate Estimation” report can help to identify them.
    For VMs with low change rate, you can decrease the overhead of
    storage snapshot processing (faster as less data needs to be moved).

-   If the primary datastore is run on block protocols like Fiber
    Channel or iSCSi, then Direct SAN Access mode will provide the best
    performance (if all corresponding requirements are met).

For example, you can place one or multiple physical servers with access
to the Fibre Channel data store to the local site and perform backups to
a local or centralized repository (scenario with multiple proxies backup
to single repository server). If you use thin-provisioned disks for the
VMs, configuring several proxy servers for restore in the Virtual
Appliance (Hot-Add) mode can help to increase restore performance in
that scenario.

-   NBD over 10 GbE VMKernel Interfaces link will provide a very stable
    and good performing solution; it can be recommended for virtual only
    or NFS data stores.

-   The Virtual Appliance (Hot-Add) mode is a recommended option if the
    above modes cannot be used. A Proof of Concept is recommended after
    thorough planning to implement this mode.

-   NBD over 1GbE VMKernel Interfaces can be used for failover.

## Large

-   Use storage integration for all VMs with a high change rate. Veeam
    ONE Change Rate Estimation report can help to identify them (faster
    as less data needs to be moved).

-   If the primary datastore is run on block protocols like Fiber
    Channel or iSCSi, then Direct SAN Access mode will provide the best
    performance (if all corresponding requirements are met). For
    example, you can place one or multiple physical servers with access
    to the Fiber Channel datastore at the local site and perform backups
    to multiple repositories. If you use thin-provisioned disks for the
    VMs, configuring several proxy servers for restore in the Virtual
    Appliance (Hot-Add) mode – per cluster - can help to increase
    restore performance in that scenario.

-   NBD over 10 GbE VMKernel Interfaces link will provide a very stable
    and good performing solution; it can be recommended for virtual only
    or NFS data stores.

-   The Virtual Appliance (Hot-Add) mode implementation should be
    planned carefully, regarding vCenter connection count and general
    overhead on VMware side for disk mount and un-mount processes.

-   NBD over 1 GbE VMKernel Interfaces can be used for failover for some
    VMs; however a 10GbE network on VMKernel interface is
    highly recommended.

## Enterprise

-   Use the recommendations for Large Sites scenario above.

-   In large scale environments, the deployment of Veeam components,
    configuration and job creation is typically automated using the
    Veeam PowerShell integration.

-   To balance the management load, it is recommended to use multiple
    Veeam backup servers for at least every 5000 VMs, together with a
    central reporting and administration by Veeam Backup Enterprise
    Manager and Veeam ONE.

-   If you run a central Veeam backup server and multiple branches with
    it, a separate Veeam Backup & Replication management instance is
    recommended for at least every 200 branches. Consider using Veeam
    Backup Enterprise Manager for central administration.