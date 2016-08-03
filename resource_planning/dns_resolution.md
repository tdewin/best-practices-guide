# DNS Resolution

Domain Name System (DNS) resolution is critical for Veeam Backup & Replication deployment (VBR)
and configuration. VMware components must be accessible via both forward (A) and reverse (PTR)
lookups. If not some Veeam components may not work correctly.

If DNS resolution is not available you may add VMware vCenter, ESXi and managed Veeam servers to the local `hosts` file on _all_ managed Veeam servers. When using this workaround it is recommended to add both short name and fully qualified domain name in the `hosts` file. DNS should be the preferred option.

When ESXi hosts are added to vCenter it is recommended to use FQDN. When using Network Backup mode the FQDN
is returned via VMware API for Data Protection (VADP) so the backup proxy server must be able to
resolve the FQDN via DNS. Using the `hosts` file the data transport path can be altered for Network Backup mode transfers.

Please see the below example.

## Example `hosts` file

	10.0.4.10	vcenter	vcenter.example.com

	# 10.0.4.21	esx1	esx1.example.com # commented out management interface
	# 10.0.4.22	esx2	esx2.example.com # commented out management interface

	10.255.4.21	esx1	esx1.example.com # dedicated 10 GbE backup network
	10.255.4.22	esx2	esx2.example.com # dedicated 10 GbE backup network

To explicitly alter the data transport path, the `hosts` file must be deployed on all backup proxy servers. For easier management, please see the [Carbon module](http://get-carbon.org) and [`Set-HostsEntry`](http://get-carbon.org/Set-HostsEntry.html) by Aaron Jensen.
