# Automation
The bigger is the environment, the more automation is needed to reduce the administration effort. For example, if operating 40 branch offices with independent Veeam installations, you may want to roll out and configure backup servers with scripts and automatically create jobs there as well. Another example is automatic job creation for 2,000-3,000 VMs with exactly the same configurations, which can limit user-caused misconfiguration.

## PowerShell
Operations in Veeam Backup & Replication can be automated with Veeam PowerShell snap-in in the following areas:

* Installation - [Link to Help Center](https://helpcenter.veeam.com/backup/vsphere/silent_mode.html)
* Updates - [Link to Help Center](https://helpcenter.veeam.com/backup/vsphere/update_unattended.html)
* Configuration
* Job creation/job editing
* Working with external schedulers (UC4/TWS and other) to start Veeam jobs
* Restores
* Reporting
* Datacenter migration (quick migration or replication)

The PowerShell plugin is available with all commercial versions of the product.

**Note:**	PowerShell plugin is also available with Veeam Backup FREE, although limited: http://www.veeam.com/blog/veeam-backup-free-edition-now-with-powershell.html

Our customers and partners use this functionality to scale out backup infrastructure environments to nearly 100 000 VMs under a single Veeam Backup Enterprise Manager instance with multiple backup servers located in different datacenters.

The best starting point to get in touch with the Veeam PowerShell plugin is to read the Veeam PowerShell User Guide > [Veeam Help Center - PowerShell Reference](https://helpcenter.veeam.com/backup/powershell/getting_started.html).

You can find help for the scripts in the [Veeam Community Forums - PowerShell](http://forums.veeam.com/powershell-f26/) section. If you need some examples, refer to the following thread: [Getting Started and Code Examples](https://forums.veeam.com/powershell-f26/getting-started-and-code-examples-t13372.html)

## RESTful API
In the Veeam Enterprise Manager, there is as well RESTful API that allows you to create workflows in orchestration tools or to integrate Veeam Backup Enterprise Manager (self-services) in your own “cloud” portal. Specifically, this is an option that comes with Enterprise Plus Editions and is focused on the hosting business.

Here is a list of external resources:
* [Veeam Help Center - RESTful API Reference ](https://helpcenter.veeam.com/backup/rest/overview.html)
* [Veeam Community Forums](http://forums.veeam.com/restful-api-f30/)
* [Veeam Help Center - Beginner Example](https://helpcenter.veeam.com/backup/rest/beginner_example.html)
