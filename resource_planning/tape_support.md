# Tape Support

### Overview

The diagram below illustrates the main components and processes within
the backup infrastructure when tape support is implemented within Veeam
Backup & Replication deployment:

![](./media/image25.png)

### Tape Device Connection 

All connection types require driver installation.

**Note**: You can use generic drivers from Microsoft Windows, but they
may not provide as high performance as the vendor’s.

Separate drivers for tape drives and for tape media libraries should be
installed.

  Connection Type                                                               Compatibility
  ----------------------------------------------------------------------------- ------------------------------------------------------------------------
  FC/SAS/SCSI/FCoE/InfiniBand/ other block technology to physical tape server   Works (as long as Windows drivers are available)
  FC/SAS redirect to VM                                                         Not supported by VMware and Veeam
  iSCSI                                                                         Works (limited for physical tape libraries, mostly used for VTL)
  1 GbE FC/SAS to iSCSI converter                                               Works but the throughput is limited to 1GbE
  10 GbE FC/SAS to ISCSI converter                                              Works but very expensive
  StarWind Tape Redirector                                                      Works but not needed with v8, as Veeam introduced the tape server role

### What Tapes are Supported?

**Supported:**

-   LTO- 3 and higher.

-   Unofficial tape compatibility list:
    <http://forums.veeam.com/tape-f29/unofficial-tape-library-compatibility-list-t17488.html>

-   For VTLs, see the unofficial list above to check compatibility. In
    most cases VTLs can also be added as disk repositories (Linux
    or share) to Veeam Backup & Replication (which is likely more
    efficient and compatible with all VTLs).

**Not supported:**

-   IBM "Jaguar" Enterprise tape drive

-   DLT or AIT tape drives

### Special Settings for Drivers 

-   **IBM drivers**: use “non-exclusive” driver setup and start it with
    admininstrative rights.

-   **HP drivers**: these are not installable with **.exe** file on a VM
    (for example, to use with VTL). As a solution, run **.exe** and
    choose **Extract. Use Device Manager –&gt; Update driver** and
    select the drivers for tape drives and (if you use HP/HP emulation
    tape library) for media changer.

### MediaPool – MediaSet - Vault 

![](./media/image26.png)

#### Media Pool

A media pool simply defines a group of tapes managed by Veeam Veeam
Backup & Replication. Media pools are of two kinds:

-   **Service media pools** that are created and managed automatically.
    You cannot create them manually or modify their settings.

<!-- -->

-   Empty media starts out in the **Free pool** indicating it’s
    available for use in other pools.

-   Unknown media will be placed to the **Unrecognized pool** so that it
    is not overwritten.

-   Media with some content is placed into the **Imported pool**. You
    can view the contents and place them into the **Free**
    pool (overwrite) or leave in the **Imported pool** and use the data.

<!-- -->

-   Exhausted or broken tapes are placed into the **Retired pool** and
    are not used further.

<!-- -->

-   **Custom media pools** that are created by the user. These are
    groups of media to which backup data can be written.

    -   You can create as many custom media pools as needed.

    -   Media can be assigned to a pool manually, or configured to be
        automatically assigned from the free pool.

    -   Configure each pool settings according to the purpose of the
        pool, such as the overwrite protection period that is applied to
        all media within the pool.

#### Media Set

A media set is a subset of a media pool that contains at least one
backup. A new media set can be created for every backup, or on a time
based schedule (i.e. weekly). If a media set contains at least one full
backup, it is a self-sufficient restore point. It means that if you have
all tapes from the media set at hand, you can be sure that restore will
be successful.

#### Media Vault 

A media vault is used to organize offline media. For example, you have a
service organization that transports the tapes to a safe at a bunker.
You can name the vault accordingly and add some useful information in
the description (phone number, place, etc.). When you need to transport
physical tapes to the safe, add these tapes to the vault manually or set
automatic export of offline tapes to a vault in the tape jobs or media
pools properties.

### Veeam Backup Job and Tape

When you archive forward incremental or reverse incremental backups, a
backup-to-tape job does not transform backups but transports them to
tape as a copy.

-   If you archive reverse incremental backups, the tape job will always
    copy the full backup.

-   If you archive forward incremental backups, with or without active
    or synthetic full scheduled, the backup chain on tape will be simply
    a copy of the backup chain on disk.

-   To archive forever forward incremental backup chains, schedule
    virtual full in the tape job. The tape job creates the full backup
    on the fly from a full and subsequent increment backups on disk.

When creating a backup-to-tape job, you will be asked what you want to
copy to tape during the first run: only the last backup chain or all
existing restore points.

In small environments, you are more likely to create a single media pool
and a single tape job that uses the whole repository as source. Refer to
Veeam Backup & Replication User Guide and Evaluators Guide for more
examples.

### Sizing in Bigger Environments 

Install Windows 2012 R2 on the tape server for best performance. Use the
latest Veeam version and patch level as they often contain tape speed
optimizations.

Perform a PoC to test speed (tape/disk). If you have no opportunity to
test speed, assume that the lowest speed for backup to tape jobs with
LTO5/6 is 40MB/s.

For each running backup-to-tape or file-to-tape job, one tape drive is
used. You can use this for planning multitask tape scenarios, as such
jobs can run simultaneously.

-   To utilize multiple drive installations most efficiently, create one
    tape job per each drive. The tape job must use the backup repository
    as the source. Set up as many repositories as tape jobs/drives.

-   Size repository so that the tape jobs have enough time to archive
    the new data within the backup window.

### Backup Copy Job and Tape 

When the backup to tape job archives backup copy chains, it extracts
data blocks and builds new backup files on tape.

The tape server implemented in v8 allows you to archive data to remote
tape devices. If you locate a tape server on the backup copy job target
side, you will be able to transport the data locally to tape. However
keep in mind that the backup copy job and the tape job must be defined
and run from the same Veeam backup server.

If you use a separate Veeam backup server to transport the data to tape,
set up a file-to-tape job together with a PowerShell script, as
described in these forum topics:

-   <http://forums.veeam.com/tape-f29/v7-stop-bcj-start-file2tape-startbcj-t23048.html?hilit=Tape>

-   <http://forums.veeam.com/vmware-vsphere-f24/hp-data-protector-and-veeam-only-backup-latest-vbk-file-t17204.html>

### 3rd Party Backup-to-Tape Software 

As Veeam Backup & Replication tracks and orchestrates all backups
written to tape, Veeam can recommend use of the native Veeam tape
features (backup-to-tape and file-to-tape jobs).

However, in some situations you may want to use an existing library with
non-LTO tapes, or you need to integrate Veeam Backup & Replication into
an existing backup-to-tape software. Veeam backup files contain all
information needed for restore (e.g. deduplication information, VM
metadata, etc.), and you can use the existing backup-to-tape solution to
bring the Veeam backup files on tape.

Veeam can import and read tapes that use the native Microsoft Tape
Format.

### Tape Encryption

Veeam uses hardware encryption if it is provided by the tape device and
enabled in Veeam Backup & Replication. Tape library should work in the
application-managed encryption mode.

If the hardware encryption is not supported by the tape device, then 256
AES software encryption is used.

When archiving data, Veeam generates a user key which is stored with
data on tape. If you restore data using another Veeam backup server,
provide the password or utilize the password loss prevention
functionality supported by Veeam Backup Enterprise Manager. See the
Veeam Backup & Replication User Guide for more information.

If the hardware encryption option is used, and you archive to tape Veeam
backups that are already encrypted on disk, they will be encrypted
twice. If you restore such backups with double encryption on the same
Veeam backup server they will be decrypted automatically. To decrypt on
another Veeam backup server, you will need to enter the two passwords
accordingly.