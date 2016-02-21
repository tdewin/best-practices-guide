## DNS and Name Resolution

Name resolution is critical for Veeam Backup & Replication deployment
and configuration. Make sure that all VMware components are accessible by both forward and reverse domain name resolution, otherwise some Veeam components may not be able to work correctly.

If the name resolution system is not available, you can add VMware
servers to the local hosts file on all Veeam component servers. Add
short and fully qualified domain names in order to increase reliability.

Make sure that fully qualified domain names (FQDN) and domain name
servers (DNS) on ESXi hosts are configured correctly. You may alter host
file on Veeam proxy serves for ESXi host name resolution to bind Veeam
datastreams for NBD mode to the required VMKernel interfaces.