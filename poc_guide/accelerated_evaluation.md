# Accelerated Evaluation

Many customers decide to do a small scale Proof of Concept (POC) after seeing their first live demonstration and presentation meetings with partners or Veeam System Engineers. The idea is to get started with the interface of Veeam Backup & Replication and to test if everything works as expected/presented within the customer’s environment.

As enterprise environments are sometimes very complicated from the firewall and storage perspective, in most cases customers decide to do a POC in very flat test environments. Typically, a test environment includes:

* ESXi hosts, vCenter Server, Veeam Backup & Replication server
* 10-20 VMs running various business applications

It is possible to carry out a Veeam Backup & Replication POC in such environment with only a single Veeam backup server on a VM with 4 cores and 6-8 GB of RAM. (Since this test is focused on the user interface experience, no special preparation is needed from the performance perspective.)

Customers often drive this POC themselves. To assist customers with this task, Veeam has published a good Evaluator's Guide that includes configuration screenshots with minimal required background information.

See Veeam Helpcenter for Evaluator's Guide:
* [VMware vSphere environments](http://helpcenter.veeam.com/evaluation/backup/vsphere/en/)
* [Microsoft Hyper-V environments](http://helpcenter.veeam.com/evaluation/backup/hyperv/en/)
