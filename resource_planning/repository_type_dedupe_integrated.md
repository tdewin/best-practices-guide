# Deduplication integration specifics

## EMC DataDomain

Selecting DataDomain as a repository will automatically recommend job and repository settings according to best practices. For more information, refer to vendor guidelines.

DDBoost allows for the following capabilities:

-   Source side deduplication between the Veeam gateway server and DataDomain appliance. This will reduce the amount of data sent over the network to the appliance
-   Better LAN parallelization, since DDBoost manages its own network load balancing algorithms which are considered more efficient than standard network links aggregation
-   Seamless Veeam files transformations like synthetic full or forever forward incremental
-   DDBoost can be used through Fibre Channel SAN, providing a totally LAN-free backup solution

For more details, refer to the DDBoost configuration guide by Rick Vanover:  [Configuring EMC Data Domain Boost with Veeam Availability Suite](https://www.veeam.com/wp-configuring-emc-data-domain-boost-with-veeam-availability-suite-v8.html) (still applicable for version 9).

### Chain Length Limitation

Consider that DataDomain can support only up to 60 incremental restore points for a single full backup. For details, refer to the Veeam Backup & Replication User Guide: [Limitations for EMC Data Domain](https://helpcenter.veeam.com/backup/vsphere/emc_limitations.html)

## ExaGrid

ExaGrid appliances run an integrated Veeam data mover similar to a Linux based backup repository. With ExaGrid, there is no requirement for a Windows based gateway server.

See [Using Veeam Backup and Replication Software with an ExaGrid System](http://go.veeam.com/rs/veeam/images/Best_Practices_and_Deployment_Veeam_and_ExaGrid.pdf) for more information.

ExaGrid recommends configuring 1 job per repository. Thus, if you want to achieve parallel processing, create several repositories and setup 1 job per repository.

As a rule of thumb, the "landing zone" (which is the zone that will hold most recent set of data waiting to be deduplicated) should have sufficient capacity for an uncompressed full backup so that each backup can fully be written there and processed. This ensures SureBackup, Instant VM Recovery and item-level restores will be usable for the latest restore point without rehydration overhead.

## HPE StoreOnce

Selecting StoreOnce appliance as a repository will automatically recommend job and repository settings according to best practices. For more information, refer to vendor guidelines.

When using HPE Catalyst, consider the following recommendations:

If the Catalyst Store is configured as **High Bandwidth** on the appliance, Low Bandwidth mode can be forced using the following registry value (ideally, work around the issue by configuring both Primary and Secondary modes to "Low"):

-   Path: `HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Veeam\Veeam Backup Transport`
-   Key: `UseLowBandwithMode`
-   Type: REG_DWORD
-   Value: 1 (default: 0)

If the Catalyst Store is configured as **Low Bandwidth**, additional payload verification is introduced. Over high latency connections, disabling the verification may improve performance. However, the defaults should be left for local connections.

See the following registry keys:

-   Path: `HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Veeam\Veeam Backup Transport`
-   Key: `PayloadChecksumsDisabled`
-   Type: REG_DWORD
-   Value: 1 (default: 0)

and

-   Path: `HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Veeam\Veeam Backup Transport`
-   Key: `BodyPayloadCompressionDisabled`
-   Type: REG_DWORD
-   Value: 1 (default: 0)

### Chain Length Limitation
Consider that HPE StoreOnce can support only up to 7 restore points. For details, refer to the Veeam Backup & Replication User Guide: [Limitations for HPE StoreOnce](https://helpcenter.veeam.com/backup/vsphere/deduplicating_appliance_storeonce_limitations.html)
