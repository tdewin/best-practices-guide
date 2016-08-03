# Repository Sizing
In mid-sized or enterprise environments, the recommended amount of CPU for a repository is 1 core per concurrent job that processes data on a repository server. At least 2 cores allow for the Operating System to be more responsive.

It is recommended to configure 4 GB RAM per core. The same amount of resources are needed for SMB gateway servers. Also, consider that VM recovery processes (Instant Recovery, FLR and others) require sufficient resources (as described [here](https://helpcenter.veeam.com/backup/70/bp_vsphere/index.html?recovery_performance.html)).

## Estimating Repository Capacity
When estimating the amount of required disk space, you should know the following:
-   Total size of VMs being backed up
-   Frequency of backups
-   Retention period for backups
-   Will jobs use forward or reverse incremental

Also, when testing is not possible beforehand, you should make assumptions on compression and deduplication ratios, change rates, and other factors. The following figures are typical for most deployments; however, it is important to understand the specific environment to figure out possible exceptions:
-   Data reduction thanks to Compression and Deduplication is usually 2:1 or more; it's common to see  3:1 or better, but you should always be conservative when estimating required space.
-   Typical daily change rate is between 2 and 5% in a mid-size or enterprise environment; this can greatly vary among servers; some servers show much higher values. If possible, run monitoring tools like Veeam ONE to have a better understanding of the real change rate values.
-   Include additional space for one-off full backups.
-   Include additional space for backup chain transformation (forward forever incremental, reverse incremental) – at least the size of a full backup multiplied by 1.25x.

**Note**: When using deduplication appliances, please contact the vendor for sizing guidelines.

Using the numbers above, you can estimate required disk space for any job. Besides, always leave plenty of extra headroom for future growth, additional full backups, moving VMs, restoring VMs from tape.

A repository sizing tool that can be used for estimation is available at <http://vee.am/rps>. Note that this tool is not officially supported by Veeam, and it should be used "as is", but it's nonetheless heavily used by Veeam Architects and regularly updated.

**Tip:** With Veeam Availability Suite, you can use Veeam ONE together with Veeam Backup & Replication. Among the many reports, Veeam ONE has the [VM Change Rate Estimation](https://helpcenter.veeam.com/one/reporter/vm_change_rate_history.html) report from the “Infrastructure Assessment” report pack; this can be used as an indicative pre-deployment assessment of the potential amount of space that should be available on the backup repositories. This report is built measuring the number of VM virtual disk write operations supplied by VMware vSphere.

It is also recommended to periodically run the “Capacity Planning for Backup Repositories” report from the “Veeam Backup & Replication Reports” pack to analyze the amount of free space on backup repositories and estimate the projected growth and consequent space consumption. The report provides recommendations for adjusting the allocated storage resources in order to meet the future demand for backup storage. Furthermore, it calculates the amount of additional space that needs to be provisioned to accommodate the necessary restore points.

For more information on Veeam Availability Suite, please refer to its Reviewer's Guide at <https://www.veeam.com/documentation-guides-datasheets.html>

### Examples

The examples below explain the impact of backup method and retention policy on the estimated repository size, assuming the environment is the same in all three cases.

Environment: 10 VMs, 100GB each, 80GB avg/used

2:1 Estimated Compression/Deduplication, 5% daily change

**Example 1**

Backup: Reverse Incremental, Daily Backup, 30 Day Retention
-   Estimated Full Backup Size: 10 \* 80GB (Used space) \* 50% (2:1 Compression) = 400GB
-   Estimated Reverse Incremental Size: 10 \* 80GB \* 50% (2:1 Comp) \*     5% (Change Rate) \* 29 (reverse incremental restore points) = 580GB
-   Spare : 500 GB
-   Estimated total Backup Size: 400GB + 580GB + 500 = 1480 GB

**Example 2**

Backup: Forward Incremental, Daily Backup, 30 Day Retention, Weekly Full
-   Estimated Full Backup Size: 10 \* 80GB (Used space) \* 50% (2:1 Compression) = 400GB
-   Estimated space for 6 Weekly Fulls (Max required for 30 Day Retention): 400GB \* 6 = 2400GB
-   Estimated Forward Incremental Size Max: 10 \* 80GB \* 50% \* 5% \* 32 = 640GB
-   Estimated total Backup Size: 2400GB + 640GB = 3,040GB (\~3TB)

**Example 3**

Backup: Forward Incremental, Daily Backup, 30 Day Retention, Monthly Full
-   Estimated Full Backup Size: 10 \* 80GB (Used space) \* 50% (2:1 Compression) = 400GB
-   Estimated space for 3 Monthly Fulls (Max req for 30 Day Retention): 400GB \* 3 = 1200GB
-   Estimated Forward Incremental Size Max: 10 \* 80GB \* 50% \* 5% \* 60 = 1200GB
-   Estimated total Backup Size: 1200GB + 1200GB = 2,400GB (\~2.4TB)

To summarize, when estimating the size of the repositories, use the following best practices:

-   Be conservative when estimating compression and deduplication ratios if actual ratios and disk content are unknown.
-   Use higher estimates for change rate if a significant number of servers are transactional such as Microsoft SQL and Microsoft Exchange.
-   Include enough free space to take at least one and a quarter extra full backup for each transformation job.
