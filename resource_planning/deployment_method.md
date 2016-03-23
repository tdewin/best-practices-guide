# Deployment Method
You may deploy Veeam Backup & Replication server as either a physical or
virtual server. It will run on any server with Windows Server 2008 R2 or higher installed. Depending on the environment, here are some guidelines
that may help deciding which deployment type is the best fit.

## Virtual deployment
If installed in a virtual machine, the VM can be replicated to a
secondary location such as a D/R site. If the virtual machine itself
should fail, or in the event of a datacenter
or infrastructure failure, the replicated VM can be powered on via
the VMware vSphere Client.

In most cases, this is the preferred deployment method as it provides high availability for the backup server component via vSphere High Availability or Fault Tolerance. Additionally it provides great flexibility in sizing and scaling as the environment grows.

## Physical deployment
In very large environments, it may be desired to install the Veeam Backup & Replication server on a physical server. This may be due to a large number jobs running simultaneously and thus consuming large amounts of CPU and RAM.

If installed on a physical machine, the Veeam backup server runs independently from the virtual platform. This may also be an ideal solution in case of a disaster in the virtual environment.

Should the physical server itself fail, there are additional steps to take before reestablishing operations:

  1. Install and update the operating system
  2. Install Veeam Backup & Replication
  3. Restore "Configuration Backup"

[FIXME] In an enterprise environment, it is recommended to install an additional
Veeam backup server to speed up the failover process when it is needed
(it can be co-located with one of the Veeam proxy or repository
servers). If the Veeam Backup Service has to be restored independently
of the Veeam backup server, then back up the Veeam Backup & Replication
configuration and keep a copy in the designated location.

