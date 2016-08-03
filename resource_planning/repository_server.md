# Backup Repository

Before you start planning for the repository, go through Veeam Backup & Replication online documentation at <https://www.veeam.com/documentation-guides-datasheets.html> to get basic understanding of repositories.

A backup repository is a storage location used by Veeam Backup & Replication jobs to store backup files, copies of VMs and metadata for replicated VMs. Technically, a backup repository is a server that runs the Veeam Transport Service and provides a destination folder on the backup storage. Each job can use only one repository as its destination storage, but one repository can be used by multiple jobs.

You can balance the load across the backup infrastructure by setting up several repositories in the environment and limiting the number of concurrent jobs for each repository, or if you have a proper license you can leverage Scale-out Backup Repository as explained later on in this section.

## The 3-2-1 rule
The 3-2-1 rule states that an environment, in order to be properly protected, has to have **3 copies of data, stored on 2 different media, with at least 1 copy in a different location**. Each of the parts of the rule involves the use of a storage device, that's why a Backup Repository is such a key component in each Veeam deployment.

The 3-2-1 rule however is a data protection strategy, whereas **availability** requires the different storage implemented in this strategy to support additional capabilities like:
-	Instant VM recovery
-	File transforms
-	Distant copies
-	Item restoration
-	Sure Backup

This is the reason why v9.0 introduced two major new features for Veeam backup repositories : **Scale-out Backup Repository** and **Per-VM Backup chains**.
