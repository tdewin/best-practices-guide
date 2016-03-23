# Data Flow at Restore

**[v9]** This section will be updated to include improvements from the Mount Server and stand-alone console.

### Windows File-Level Recovery 

To perform file-level restores for a Windows-based VM, Veeam mounts all
VM disk files from the backup files (stored on the repository server) to
the Veeam backup server. This can produce a significant data flow
between those components (50 - 400MB) – consider this factor if there is
a WAN link here.

When the backup is mounted, the files selected for recovery are
transported from the repository server through the Veeam backup server
to the VM itself.

To optimize file-level and item-level restores from backup repositories
in remote locations, you can deploy a backup server and import backups
manually as mentioned in the “Disaster Recovery Optimization” section
above.

### Veeam Explorers

Veeam Explorers are installed together with the Veeam backup server and
run the Windows file-level restore process in the background. So, if you
plan to use these tools, refer to the previous section for server
deployment considerations.

### Restore via Enterprise Manager 

Veeam Enterprise Manager is a self-service portals where administrators or service desk representatives can initiate restores for VMs, files, e-mail items and SQL databases.

The Self-Service restore process uses Veeam backup servers to mount the
backup file from the repository-based primary backups to itself or to
the Linux FLR appliance (for Linux file restore only). Thus, it is
recommended to place a Veeam backup server on the same site as the
repository where the backup files that will be used for restore are
stored.
