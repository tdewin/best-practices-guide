# Assessment

Before starting a project, it is very important to understand customers’ needs, vision and the IT environment. While the first two can be the outcome of an initial project meeting, the IT environment can be analyzed with Veeam ONE, which is a part of the Veeam Availability Suite.

The following information is very important and can help to streamline the project and proactively prevent situations that impact the environment:

## Veeam ONE Monitor
### Alerts tab
Check in the Alerts tab of Veeam ONE Monitor if there are specific errors that need to be addressed before you bring extra load to the environment with backup processing that can cause business critical situations.

## Veeam ONE Reporter
### Storage Latency

![Veeam ONE Reporter - Storage Latency](../media/image40.png)

This report will help you identify storage systems that are under heavy pressure or at its maximum load. Let Veeam ONE run at least 24 hours and check if there are high latency situations.   

### Change Rate Estimation

![Veeam ONE Reporter - Change Rate Estimation](../media/image41.png)

This report will help you identify VMs with a high change rate at the block level (relevant for incremental backups). You can later configure the backup or replication job to process them at the beginning of the backup window, to address the longer job runtimes. In general, this report will give you numbers for backup target storage planning.

### VM Configuration Assessment

![Veeam ONE Reporter - VM Configuration Assessment](../media/image42.png)

This report will help you assess VMs readiness for performing backup with Veeam Backup & Replication. It analyzes configuration of VMs in the virtual environment and shows potential issues and possible limitations that can cause the backup process to fail or prevent VMs from being properly backed up.

### Infrastructure Overview

![Veeam ONE Reporter - Infrastructure Overview](../media/image43.png)

### Active Snapshots

![Veeam ONE Reporter - Active Snapshots](../media/image44.png)

VMware snapshots are often done to save a specific state of the VM for some time. While they are created very easily, administrators forget to delete them over time. Together with administrators, you can release all snapshots that are not needed anymore. This will help prevent datastore downtimes because of snapshots filling up the whole physical storage.

### Orphaned Snapshots

![Veeam ONE Reporter - Orphaned Snapshots](../media/image45.png)

This report detects VM snapshots that are still active on datastores but do not show up in the VMware Snapshot Manager. Veeam Backup & Replication and its Snapshot Hunter will correct this situation by consolidating these snapshots, which can bring extra load at the first backup POC. We strongly recommend that you tune the VMware environment and consolidate all orphaned snapshots before you start a Backup & Replication project.

### Infrastructure Changes by User

![Veeam ONE Reporter - Infrastructure Changes by User](../media/image46.png)

In the later POC phase, create a separate account for a VMware user and use this account for all authentication operations in Veeam Backup & Replication. With the Infrastructure Changes by User report, you can track and document all changes done by this user.  

### Inventory

![Veeam ONE Reporter - Inventory](../media/image47.png)

This report provides the most complete and up-to-date configuration information on all objects in the virtual environment. It can be used offline at the planning phase to address any virtual infrastructure-related questions.

There are many additional interesting reports in the Veeam Availability Suite.

Check out the [VMware Optimization](https://helpcenter.veeam.com/one/reporter/vmware_optimization.html) or [Hyper-V Optimization](https://helpcenter.veeam.com/one/reporter/hyperv_optimization.html) sections of Veeam ONE Reporter. A good example is the Garbage Files Report that can identify possible wasted space on datastores. In some cases, it helped to free up 10 TB+ of space on the tier 1 storage.
