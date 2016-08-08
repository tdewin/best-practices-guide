# Windows Server 2012 Deduplication

Follow the recommendations provided in the configuration guidelines above; here is the summary:

1.  Use **Windows 2012 R2** and apply all patches (some roll-ups contain improvements to deduplication).
2.  Format the disk using the command line "**/L**" option (for "large size file records") and **64KB** cluster size (use parameters `/Q /L /A:64K`)
3.  Follow [compression and deduplication guidelines](./repository_type_dedupe.md#best-practices) for non-integrated deduplication storage in previous chapter.
4.  Modify garbage collection schedule to run daily rather than weekly.
5.  Use backup jobs configured to perform Active full with incrementals.
6.  If possible, spread active full backups over the entire week.
7.  Try to keep the .VBK files **below 1TB** in size (there is no official support from Microsoft for files bigger than this; see <https://msdn.microsoft.com/en-us/library/hh769303(v=vs.85).aspx>). Large files take a long time to deduplicate and will have to be fully reprocessed if the process is interrupted.
8.  Where possible, use multiple volumes. Windows deduplication can process multiple volumes using multi-core CPU – one CPU core per volume; see <http://blogs.technet.com/b/filecab/archive/2014/12/04/sizing-volumes-for-data-deduplication-in-windows-server.aspx> for details.)
9.  Configure deduplication process to run once a day, and for as long as possible.

More information can be found here: <http://forums.veeam.com/veeam-backup-replication-f2/best-practice-for-ms-server-2012-dedup-repo-t14002-135.html>.
