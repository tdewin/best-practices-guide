# Application-Aware Image Processing

When configuring Veeam backup and replication jobs, you can specify how
the transactionally-consistent backup images of VMware VMs should be
created. Two methods are available for bringing VM file system and
applications into consistent state: VMware Tools quiescence and Veeam
Application-Aware Image Processing (utilizing Windows VSS). Key features
of both methods are illustrated by the following table:

  Feature 
  
  VMware Tools Quiescence               Veeam Application-Aware Image Processing
  ----------------------------------------------------------------------------------- ------------------------------------- ------------------------------------------
  Support for consistent backup on Windows guest                                      Yes                                   Yes
  **Sync** driver for Linux guest                                                     Yes                                   No
  Support for application-aware backup                                                Limited                               Yes
  Pre-VSS preparation for specific applications (e.g. Oracle)                         No                                    Yes
  Support for application log truncation (Microsoft SQL Server and Exchange Server)   No                                    Yes
  Support for scripts                                                                 Yes (need to be placed on VM guest)   Yes (can be centrally distributed)
  Interaction with user via UI                                                        Not needed                            Not needed
  Error reporting                                                                     Within VM guest OS                    Centralized, on Veeam backup server
  Stability                                                                           Good                                  Very good

### How Veeam Guest OS Processing Works

1.  First, Veeam Backup & Replication performs guest OS inventory to
    find out if there is a VSS-aware application running inside a VM.

2.  Veeam Backup & Replication runs pre-freeze script (if any) for the
    Microsoft Windows/Linux guest OS with applications that utilize
    other means of VM quiescence.

3.  Then VSS quiescence of the VM is performed, including restore
    awareness settings.

4.  VM snapshot is created.

5.  VSS unfreeze (“thaw”) is performed.

6.  Veeam Backup & Replication runs post-thaw script (if any) for the
    Microsoft Windows/Linux guest OS.

7.  Backup data transfer and snapshot commit is performed.

8.  Finally, log file truncation is performed with VSS (for Microsoft
    SQL Server and Exchange Server).

### Selecting Guest Processing Options 

When on the **Guest Processing** step of the job wizard, you are
presented with the variety of options (as described in detail in the
User Guide
(<http://helpcenter.veeam.com/backup/80/vsphere/index.html?backup_job_vss_vm.html>).

Note that you can use pre- and post-job scripting to automate job global
settings from the Veeam Backup & Replication server itself. It is
recommended to use the VM guest processing options for interaction with
VMs.

To select the necessary options, refer to the table below.

  VM guest OS type                                                                             Guest OS processing is applicable   Use VMware Tools quiescence   VMware Tools quiescence with VMware Script processing   Use Veeam Application-Aware Processing   Use Veeam Application-Aware Processing and InGuest Scripts   Use Veeam InGuest Script Processing (Application Aware Processing disabled)
  -------------------------------------------------------------------------------------------- ----------------------------------- ----------------------------- ------------------------------------------------------- ---------------------------------------- ------------------------------------------------------------ -----------------------------------------------------------------------------
  Linux server (no applications)                                                               No                                  No                            No                                                      No                                       No                                                           No
  Linux with applications                                                                      Yes                                 No                            No                                                      No                                       No                                                           Yes
  Windows (no VSS-aware applications)                                                          Yes                                 No                            No                                                      No                                       Yes                                                          No
  Windows with VSS-aware applications                                                          Yes                                 No                            No                                                      Yes                                      No                                                           No
  Windows and VMware VSS-supported applications (without known user for Guest OS processing)   Yes                                 Yes                           No                                                      No                                       No                                                           No
  Linux (with applications and known user for Guest OS processing)                             Yes                                 No                            Yes                                                     No                                       No                                                           No

To coordinate proper VSS and indexing activities, Veeam Backup &
Replication deploys a small executable component inside a VM. It is
installed only during VSS quiescence procedure and removed immediately
after the processing is finished, producing very low impact on VM
performance and stability. As for connection method for accessing VM
guest OS, Veeam first tries to connect to the VM over network using RPC
and then by VMware VIX channel through VMware Tools (for Windows guest
only).

**Tip:** If the backup server has no network connection to the VMs (for
example, service providers), this order can be changed using the
**InverseVssProtocolOrder (REG\_DWORD)** registry value under
**HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Veeam\\Veeam Backup and
Replication**:\
Value = **1** – try connection through VIX first\
Value = **0** – try network connection through
[\\\\admin\$](file:///\\admin$) first\
See Veeam Knowledge Base article at <http://www.veeam.com/kb1230>\
Consider that this is a global setting that will be applied on the Veeam
backup server level (to all jobs with application-aware processing
configured on it).

Depending on the VM guest OS processing options selected (enabled or
disabled application-aware image processing) and on the guest access
method, you may need to supply access credentials for the guest OS, as
described in the tables below.

#### Guest Access Credentials for Windows OS {#guest-access-credentials-for-windows-os .pseudo4}

  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Application-Aware Image Processing (AAIP)       Membership in the local Administrators group   Enter username as\                                                        UAC can be enabled   VMware Tools must be installed and up to date
                                                                                                 *&lt;servername&gt;\\ Administrator* or *&lt;domain&gt;\\Administrator*                        
  ----------------------------------------------- ---------------------------------------------- ------------------------------------------------------------------------- -------------------- -----------------------------------------------
  Disabled (crash-consistent backup is created)   Not needed                                     N/A                                                                       Yes                  No

  Using Veeam (guest access by Network)           Yes                                            No                                                                        Yes                  Yes

  Using Veeam (guest access by VIX)               No                                             Yes                                                                       Yes                  Yes

  Using Veeam (guest access by VIX)               Yes                                            No                                                                        No                   Yes

  Using VMware Tools Quiescence                   User account not needed                        No                                                                        Yes                  Yes
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#### Guest Access Credentials for Linux OS {#guest-access-credentials-for-linux-os .pseudo4}

  Linux guest OS processing                       Root user account         User requires sudoer rights   Certificate-based authentication is possible   VMware Tools must be installed and up to date
  ----------------------------------------------- ------------------------- ----------------------------- ---------------------------------------------- -----------------------------------------------
  Disabled (crash-consistent backup is created)   Not needed                No                            No                                             No
  Using Veeam (guest access by Network)           Yes                       Yes                           Yes                                            Yes
  Using VMware Tools Quiescence                   User account not needed   No                            No                                             Yes

**Tip:** To verify the credentials you supplied on the Guest Processing
step of the job wizard, click **Test Now** button.

### Required Ports

The following ports should be open between the Veeam backup server and
VM for guest OS processing:

-   For Windows VMs - remote RPC ports, including Dynamic Port Range
    (TCP ports 1025 to 5000 - for Microsoft Windows 2003, 49152-65535 -
    for Microsoft Windows 2008 and newer); TCP\\UDP ports 135,
    137-139, 445.

-   For Linux VMs – SSH port (default is TCP port 22)

For details, refer to the Veeam Backup & Replication User Guide
(<http://helpcenter.veeam.com/backup/80/vsphere/index.html?used_ports.html>).

### Sizing

Since guest processing produces very low impact on VM performance, no
special considerations on sizing are required. If you use VSS processing
with VMware Tools quiescence or Veeam in-guest processing, you need free
space on each drive of the VM for the software VSS snapshot. Please
check Microsoft requirements for more information.

**Note:** If you plan to use snapshot-only backups with NetApp storage
system (see
<http://helpcenter.veeam.com/backup/80/vsphere/index.html?netapp_integration.html>
for details), consider that default number of VM guest systems processed
in parallel is by default set to **20**.
