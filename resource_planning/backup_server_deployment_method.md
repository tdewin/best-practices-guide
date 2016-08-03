# Deployment Method
You may deploy the Veeam Backup & Replication server as either a physical or virtual server. It will run on any server with Windows Server 2008 R2 or higher installed. Depending on the environment here are some guidelines that may help in deciding which deployment type is the best fit.

## Virtual deployment
If installed in a virtual machine the VM can be replicated to a
secondary location such as a DR site. If the virtual machine itself
should fail or in the event of a datacenter/infrastructure failure, the
replicated VM can be powered on via the VMware vSphere Client. Best practice in a two site environment is to install the Backup server in  the DR site, in the event of a disaster it is already available to start recovery processes.

For most cases virtual is the recommended deployment as it provides high
availability for the backup server component via vSphere High Availability or Fault Tolerance. Additionally it provides great flexibility in sizing and scaling as the environment grows.

## Physical deployment
In small-medium environments (up to 500 VMs) it is common to see an all-in-one physical server running the Backup & Replication server, backup proxy and backup repository components. This is also referred to as "Appliance Model" deployment.

In large environments (over 2,500 VMs) installing Backup & Replication
services on separate servers either virtual or physical may provide better performance. When running a large number of jobs simultaneously, consuming large amounts of CPU and RAM,
scaling up the virtual Backup & Replication server to satisfy the system requirements may become impractical.

If installed on a physical machine, the Veeam backup server runs independently from the virtual platform. This may also be an ideal solution in case of disasters in the virtual environment.

Should the physical server itself fail, there are additional steps to take
before reestablishing operations:

  1. Install and update the operating system on a new server
  2. Install Veeam Backup & Replication
  3. Restore the configuration backup

In an enterprise environment you may choose to install an additional
backup server to speed up the recovery process during a disaster. You may re-use existing infrastructure such as a proxy or repository server for the standby Backup & Replication server. During a disaster the configuration backup can easily be restored to this server. It is recommended to store the configuration backup using a file copy job in a location that is always available to this standby Backup & Replication server.
