# Job Options Dumps
* $options.BackupStorageOptions
  * $options.BackupStorageOptions.BackupIsAttached Type [System.Boolean]
  * $options.BackupStorageOptions.CheckRetention Type [System.Boolean]
  * $options.BackupStorageOptions.CompressionLevel Type [System.Int32]
  * $options.BackupStorageOptions.EnableDeduplication Type [System.Boolean]
  * $options.BackupStorageOptions.EnableDeletedVmDataRetention Type [System.Boolean]
  * $options.BackupStorageOptions.EnableFullBackup Type [System.Boolean]
  * $options.BackupStorageOptions.EnableIntegrityChecks Type [System.Boolean]
  * $options.BackupStorageOptions.KeepFirstFullBackup Type [System.Boolean]
  * $options.BackupStorageOptions.RetainCycles Type [System.Int32]
  * $options.BackupStorageOptions.RetainDays Type [System.Int32]
  * $options.BackupStorageOptions.StgBlockSize Type [Veeam.Backup.Common.EKbBlockSize]
  * $options.BackupStorageOptions.StorageEncryptionEnabled Type [System.Boolean]
* $options.BackupTargetOptions
  * $options.BackupTargetOptions.Algorithm Type [Veeam.Backup.Model.EAlgorithm]
  * $options.BackupTargetOptions.FullBackupDays Type [System.DayOfWeek[]]
  * $options.BackupTargetOptions.FullBackupMonthlyScheduleOptions Type [Veeam.Backup.Model.CDomFullBackupMonthlyScheduleOptions]
  * $options.BackupTargetOptions.FullBackupScheduleKind Type [Veeam.Backup.Model.EFullBackupScheduleKind]
  * $options.BackupTargetOptions.TransformFullToSyntethic Type [System.Boolean]
  * $options.BackupTargetOptions.TransformIncrementsToSyntethic Type [System.Boolean]
  * $options.BackupTargetOptions.TransformToSyntethicDays Type [System.DayOfWeek[]]
* $options.CloudReplicaTargetOptions
  * $options.CloudReplicaTargetOptions.CloudConnectHost Type [System.Guid]
  * $options.CloudReplicaTargetOptions.CloudConnectStorage Type [System.Guid]
* $options.FailoverPlanOptions
  * $options.FailoverPlanOptions.PostCommandLine Type [System.String]
  * $options.FailoverPlanOptions.PostEnabled Type [System.Boolean]
  * $options.FailoverPlanOptions.PreCommandLine Type [System.String]
  * $options.FailoverPlanOptions.PreEnabled Type [System.Boolean]
* $options.GenerationPolicy
  * $options.GenerationPolicy.ActualRetentionRestorePoints Type [System.Int32]
  * $options.GenerationPolicy.CompactFullBackupDays Type [System.DayOfWeek[]]
  * $options.GenerationPolicy.CompactFullBackupMonthlyScheduleOptions Type [Veeam.Backup.Model.CDomFullBackupMonthlyScheduleOptions]
  * $options.GenerationPolicy.CompactFullBackupScheduleKind Type [Veeam.Backup.Model.EFullBackupScheduleKind]
  * $options.GenerationPolicy.DeletedVmsDataRetentionPeriodDays Type [System.Int32]
  * $options.GenerationPolicy.EnableCompactFull Type [System.Boolean]
  * $options.GenerationPolicy.EnableCompactFullLastTime Type [$null]
  * $options.GenerationPolicy.EnableDeletedVmDataRetention Type [System.Boolean]
  * $options.GenerationPolicy.EnableRechek Type [System.Boolean]
  * $options.GenerationPolicy.GFSIsReadEntireRestorePoint Type [System.Boolean]
  * $options.GenerationPolicy.GFSMonthlyBackups Type [System.Int32]
  * $options.GenerationPolicy.GFSQuarterlyBackups Type [System.Int32]
  * $options.GenerationPolicy.GFSRecentPoints Type [System.Int32]
  * $options.GenerationPolicy.GFSWeeklyBackups Type [System.Int32]
  * $options.GenerationPolicy.GFSYearlyBackups Type [System.Int32]
  * $options.GenerationPolicy.IsGfsActiveFullEnabled Type [System.Boolean]
  * $options.GenerationPolicy.KeepGfsBackup Type [System.Boolean]
  * $options.GenerationPolicy.MonthlyBackup Type [Veeam.Backup.Model.CDomMonthlyBackupCreationTime]
  * $options.GenerationPolicy.QuarterlyBackup Type [Veeam.Backup.Model.CDomQuarterlyBackupCreationTime]
  * $options.GenerationPolicy.RecheckBackupMonthlyScheduleOptions Type [Veeam.Backup.Model.CDomFullBackupMonthlyScheduleOptions]
  * $options.GenerationPolicy.RecheckDays Type [System.DayOfWeek[]]
  * $options.GenerationPolicy.RecheckScheduleKind Type [Veeam.Backup.Model.EFullBackupScheduleKind]
  * $options.GenerationPolicy.RecoveryPointObjectiveUnit Type [Veeam.Backup.Common.ETimeUnit]
  * $options.GenerationPolicy.RecoveryPointObjectiveValue Type [System.Int32]
  * $options.GenerationPolicy.RetentionPolicyType Type [Veeam.Backup.Model.ERetentionPolicyType]
  * $options.GenerationPolicy.SimpleRetentionRestorePoints Type [System.Int32]
  * $options.GenerationPolicy.SyncIntervalStartTime Type [System.TimeSpan]
  * $options.GenerationPolicy.WeeklyBackupDayOfWeek Type [System.DayOfWeek]
  * $options.GenerationPolicy.WeeklyBackupTime Type [System.TimeSpan]
  * $options.GenerationPolicy.YearlyBackup Type [Veeam.Backup.Model.CDomYearlyBackupCreationTime]
* $options.HvNetworkMappingOptions
  * $options.HvNetworkMappingOptions.NetworkMapping Type [Veeam.Backup.Model.CHvNetworkMappingSpec[]]
* $options.HvReplicaTargetOptions
  * $options.HvReplicaTargetOptions.EnableInitialPass Type [System.Boolean]
  * $options.HvReplicaTargetOptions.InitialPassDir Type [System.String]
  * $options.HvReplicaTargetOptions.InitialSeeding Type [System.Boolean]
  * $options.HvReplicaTargetOptions.ReplicaNameSuffix Type [System.String]
  * $options.HvReplicaTargetOptions.TargetFolder Type [System.String]
  * $options.HvReplicaTargetOptions.UseNetworkMapping Type [System.Boolean]
  * $options.HvReplicaTargetOptions.UseReIP Type [System.Boolean]
  * $options.HvReplicaTargetOptions.UseVmMapping Type [System.Boolean]
* $options.HvSourceOptions
  * $options.HvSourceOptions.CanDoCrashConsistent Type [System.Boolean]
  * $options.HvSourceOptions.DirtyBlocksNullingEnabled Type [System.Boolean]
  * $options.HvSourceOptions.EnableHvQuiescence Type [System.Boolean]
  * $options.HvSourceOptions.ExcludeSwapFile Type [System.Boolean]
  * $options.HvSourceOptions.FailoverToOnHostBackup Type [System.Boolean]
  * $options.HvSourceOptions.GroupSnapshotProcessing Type [System.Boolean]
  * $options.HvSourceOptions.OffHostBackup Type [System.Boolean]
  * $options.HvSourceOptions.UseChangeTracking Type [System.Boolean]
* $options.JobOptions
  * $options.JobOptions.BackupCopyJobCanRunAnyTime Type [System.Boolean]
  * $options.JobOptions.RunManually Type [System.Boolean]
  * $options.JobOptions.SourceProxyAutoDetect Type [System.Boolean]
  * $options.JobOptions.TargetProxyAutoDetect Type [System.Boolean]
  * $options.JobOptions.UseWan Type [System.Boolean]
* $options.JobScriptCommand
  * $options.JobScriptCommand.Days Type [System.DayOfWeek[]]
  * $options.JobScriptCommand.Frequency Type [System.UInt32]
  * $options.JobScriptCommand.Periodicity Type [Veeam.Backup.Model.CDomJobScriptCommand+PeriodicityType]
  * $options.JobScriptCommand.PostCommand Type [Veeam.Backup.Model.CCustomCommand]
  * $options.JobScriptCommand.PostScriptCommandLine Type [System.String]
  * $options.JobScriptCommand.PostScriptEnabled Type [System.Boolean]
  * $options.JobScriptCommand.PreCommand Type [Veeam.Backup.Model.CCustomCommand]
  * $options.JobScriptCommand.PreScriptCommandLine Type [System.String]
  * $options.JobScriptCommand.PreScriptEnabled Type [System.Boolean]
* $options.NotificationOptions
  * $options.NotificationOptions.EmailNotificationAdditionalAddresses Type [System.String]
  * $options.NotificationOptions.EmailNotificationSubject Type [System.String]
  * $options.NotificationOptions.EmailNotifyOnError Type [System.Boolean]
  * $options.NotificationOptions.EmailNotifyOnLastRetryOnly Type [System.Boolean]
  * $options.NotificationOptions.EmailNotifyOnSuccess Type [System.Boolean]
  * $options.NotificationOptions.EmailNotifyOnWaitingTape Type [System.Boolean]
  * $options.NotificationOptions.EmailNotifyOnWarning Type [System.Boolean]
  * $options.NotificationOptions.SendEmailNotification2AdditionalAddresses Type [System.Boolean]
  * $options.NotificationOptions.SnmpNotification Type [System.Boolean]
  * $options.NotificationOptions.UseCustomEmailNotificationOptions Type [System.Boolean]
* $options.Options
  * $options.Options.RootNode Type [System.Xml.XmlElement]
* $options.ReIPRulesOptions
  * $options.ReIPRulesOptions.Rules Type [Veeam.Backup.Model.CDomReIPRulesOptions+<get_Rules>d__0]
* $options.ReplicaSourceOptions
  * $options.ReplicaSourceOptions.Backup2Vi Type [System.Boolean]
* $options.SanIntegrationOptions
  * $options.SanIntegrationOptions.Failover2StorageSnapshotBackup Type [System.Boolean]
  * $options.SanIntegrationOptions.FailoverFromSan Type [System.Boolean]
  * $options.SanIntegrationOptions.MultipleStorageSnapshotEnabled Type [System.Boolean]
  * $options.SanIntegrationOptions.MultipleStorageSnapshotVmsCount Type [System.Int32]
  * $options.SanIntegrationOptions.NimbleSnapshotSourceEnabled Type [System.Boolean]
  * $options.SanIntegrationOptions.NimbleSnapshotSourceRetention Type [System.Int32]
  * $options.SanIntegrationOptions.NimbleSnapshotTransferAsBackupSource Type [System.Boolean]
  * $options.SanIntegrationOptions.NimbleSnapshotTransferEnabled Type [System.Boolean]
  * $options.SanIntegrationOptions.NimbleSnapshotTransferRetention Type [System.Int32]
  * $options.SanIntegrationOptions.SanSnapshotBackupBackupSource Type [System.Boolean]
  * $options.SanIntegrationOptions.SanSnapshotBackupTransferEnabled Type [System.Boolean]
  * $options.SanIntegrationOptions.SanSnapshotBackupTransferRetention Type [System.Int32]
  * $options.SanIntegrationOptions.SanSnapshotReplicaBackupSource Type [System.Boolean]
  * $options.SanIntegrationOptions.SanSnapshotReplicaTransferEnabled Type [System.Boolean]
  * $options.SanIntegrationOptions.SanSnapshotReplicaTransferRetention Type [System.Int32]
  * $options.SanIntegrationOptions.SanSnapshotSourceEnabled Type [System.Boolean]
  * $options.SanIntegrationOptions.SanSnapshotSourceRetention Type [System.Int32]
  * $options.SanIntegrationOptions.UseSanSnapshots Type [System.Boolean]
* $options.SqlLogBackupOptions
  * $options.SqlLogBackupOptions.BackupIntervalUnit Type [Veeam.Backup.Common.ETimeUnit]
  * $options.SqlLogBackupOptions.BackupIntervalValue Type [System.Int32]
  * $options.SqlLogBackupOptions.DailyRetentionDays Type [System.Int32]
  * $options.SqlLogBackupOptions.RetentionType Type [Veeam.Backup.Model.ESqlLogBackupRetentionType]
  * $options.SqlLogBackupOptions.StorageIntervalUnit Type [Veeam.Backup.Common.ETimeUnit]
  * $options.SqlLogBackupOptions.StorageIntervalValue Type [System.Int32]
* $options.ViCloudReplicaTargetOptions
  * $options.ViCloudReplicaTargetOptions.CloudConnectDatastore Type [System.Guid]
  * $options.ViCloudReplicaTargetOptions.CloudConnectHost Type [System.Guid]
  * $options.ViCloudReplicaTargetOptions.Enabled Type [System.Boolean]
* $options.ViNetworkMappingOptions
  * $options.ViNetworkMappingOptions.NetworkMapping Type [Veeam.Backup.Model.CViNetworkMappingSpec[]]
* $options.ViReplicaTargetOptions
  * $options.ViReplicaTargetOptions.ClusterName Type [System.String]
  * $options.ViReplicaTargetOptions.ClusterReference Type [System.String]
  * $options.ViReplicaTargetOptions.DatastoreHDTargetType Type [Veeam.Backup.Model.HDTargetType]
  * $options.ViReplicaTargetOptions.DatastoreName Type [System.String]
  * $options.ViReplicaTargetOptions.DatastoreReference Type [System.String]
  * $options.ViReplicaTargetOptions.DatastoreRootPath Type [System.String]
  * $options.ViReplicaTargetOptions.DiskCreationMode Type [Veeam.Backup.Model.EDiskCreationMode]
  * $options.ViReplicaTargetOptions.EnableDigests Type [System.Boolean]
  * $options.ViReplicaTargetOptions.EnableInitialPass Type [System.Boolean]
  * $options.ViReplicaTargetOptions.HostReference Type [System.String]
  * $options.ViReplicaTargetOptions.InitialPassDir Type [System.String]
  * $options.ViReplicaTargetOptions.InitialSeeding Type [System.Boolean]
  * $options.ViReplicaTargetOptions.PbmProfileId Type [System.String]
  * $options.ViReplicaTargetOptions.ReplicaNamePrefix Type [System.String]
  * $options.ViReplicaTargetOptions.ReplicaNameSuffix Type [System.String]
  * $options.ViReplicaTargetOptions.ReplicaTargetResourcePoolName Type [System.String]
  * $options.ViReplicaTargetOptions.ReplicaTargetResourcePoolRef Type [System.String]
  * $options.ViReplicaTargetOptions.ReplicaTargetVmFolderName Type [System.String]
  * $options.ViReplicaTargetOptions.ReplicaTargetVmFolderRef Type [System.String]
  * $options.ViReplicaTargetOptions.UseNetworkMapping Type [System.Boolean]
  * $options.ViReplicaTargetOptions.UseReIP Type [System.Boolean]
  * $options.ViReplicaTargetOptions.UseVmMapping Type [System.Boolean]
* $options.ViSourceOptions
  * $options.ViSourceOptions.BackupTemplates Type [System.Boolean]
  * $options.ViSourceOptions.BackupTemplatesOnce Type [System.Boolean]
  * $options.ViSourceOptions.DirtyBlocksNullingEnabled Type [System.Boolean]
  * $options.ViSourceOptions.EnableChangeTracking Type [System.Boolean]
  * $options.ViSourceOptions.EncryptLanTraffic Type [System.Boolean]
  * $options.ViSourceOptions.ExcludeSwapFile Type [System.Boolean]
  * $options.ViSourceOptions.FailoverToNetworkMode Type [System.Boolean]
  * $options.ViSourceOptions.SetResultsToVmNotes Type [System.Boolean]
  * $options.ViSourceOptions.UseChangeTracking Type [System.Boolean]
  * $options.ViSourceOptions.VCBMode Type [System.String]
  * $options.ViSourceOptions.VDDKMode Type [System.String]
  * $options.ViSourceOptions.VmAttributeName Type [System.String]
  * $options.ViSourceOptions.VmNotesAppend Type [System.Boolean]
  * $options.ViSourceOptions.VMToolsQuiesce Type [System.Boolean]

This was generated via the following PScode (if you are running on a higher/lower version this could be userful)
```
$options = Get-VBRJobOptions -job @(Get-VBRJob | where { $_.JobType -eq "Backup" })[0]
$members = $options | gm -MemberType Property  | % { $_.Name }
foreach ($member in $members)
{
 Write-host (("* `$options.{0}") -f $member)
 $suboptions = $options."$member"
 $submembers = $suboptions | gm -MemberType Property | % { new-object -type psobject -argumentlist @{n=$_.Name} }
 foreach ($submember in $submembers)
 {
  $submemt = "`$null"
  try {
  $submemt = $options.$member.$($submember.n).GetType().Fullname
  } catch {
  }
  Write-host (("  * `$options.{0}.{1} Type [{2}]") -f $member,$submember.n,$submemt)
 }
}
```

# Schedule Options Dumps
* $schedoptions.BackupAtLock Type [System.Boolean]
* $schedoptions.BackupAtLogoff Type [System.Boolean]
* $schedoptions.BackupAtStartup Type [System.Boolean]
* $schedoptions.BackupAtStorageAttach Type [System.Boolean]
* $schedoptions.BackupCompetitionWaitingPeriodMin
* $schedoptions.BackupCompetitionWaitingUnit
  * $schedoptions.BackupCompetitionWaitingUnit.value__ Type [System.Int32]
* $schedoptions.EndDateTimeLocal
  * $schedoptions.EndDateTimeLocal.Date Type [System.DateTime]
  * $schedoptions.EndDateTimeLocal.Day Type [System.Int32]
  * $schedoptions.EndDateTimeLocal.DayOfWeek Type [System.DayOfWeek]
  * $schedoptions.EndDateTimeLocal.DayOfYear Type [System.Int32]
  * $schedoptions.EndDateTimeLocal.Hour Type [System.Int32]
  * $schedoptions.EndDateTimeLocal.Kind Type [System.DateTimeKind]
  * $schedoptions.EndDateTimeLocal.Millisecond Type [System.Int32]
  * $schedoptions.EndDateTimeLocal.Minute Type [System.Int32]
  * $schedoptions.EndDateTimeLocal.Month Type [System.Int32]
  * $schedoptions.EndDateTimeLocal.Second Type [System.Int32]
  * $schedoptions.EndDateTimeLocal.Ticks Type [System.Int64]
  * $schedoptions.EndDateTimeLocal.TimeOfDay Type [System.TimeSpan]
  * $schedoptions.EndDateTimeLocal.Year Type [System.Int32]
* $schedoptions.EndDateTimeSpecified Type [System.Boolean]
* $schedoptions.FrequencyTimeUnit
  * $schedoptions.FrequencyTimeUnit.value__ Type [System.Int32]
* $schedoptions.IsContinious Type [System.Boolean]
* $schedoptions.IsFakeSchedule Type [System.Boolean]
* $schedoptions.LatestRecheckLocal
  * $schedoptions.LatestRecheckLocal.Date Type [System.DateTime]
  * $schedoptions.LatestRecheckLocal.Day Type [System.Int32]
  * $schedoptions.LatestRecheckLocal.DayOfWeek Type [System.DayOfWeek]
  * $schedoptions.LatestRecheckLocal.DayOfYear Type [System.Int32]
  * $schedoptions.LatestRecheckLocal.Hour Type [System.Int32]
  * $schedoptions.LatestRecheckLocal.Kind Type [System.DateTimeKind]
  * $schedoptions.LatestRecheckLocal.Millisecond Type [System.Int32]
  * $schedoptions.LatestRecheckLocal.Minute Type [System.Int32]
  * $schedoptions.LatestRecheckLocal.Month Type [System.Int32]
  * $schedoptions.LatestRecheckLocal.Second Type [System.Int32]
  * $schedoptions.LatestRecheckLocal.Ticks Type [System.Int64]
  * $schedoptions.LatestRecheckLocal.TimeOfDay Type [System.TimeSpan]
  * $schedoptions.LatestRecheckLocal.Year Type [System.Int32]
* $schedoptions.LatestRunLocal
  * $schedoptions.LatestRunLocal.Date Type [System.DateTime]
  * $schedoptions.LatestRunLocal.Day Type [System.Int32]
  * $schedoptions.LatestRunLocal.DayOfWeek Type [System.DayOfWeek]
  * $schedoptions.LatestRunLocal.DayOfYear Type [System.Int32]
  * $schedoptions.LatestRunLocal.Hour Type [System.Int32]
  * $schedoptions.LatestRunLocal.Kind Type [System.DateTimeKind]
  * $schedoptions.LatestRunLocal.Millisecond Type [System.Int32]
  * $schedoptions.LatestRunLocal.Minute Type [System.Int32]
  * $schedoptions.LatestRunLocal.Month Type [System.Int32]
  * $schedoptions.LatestRunLocal.Second Type [System.Int32]
  * $schedoptions.LatestRunLocal.Ticks Type [System.Int64]
  * $schedoptions.LatestRunLocal.TimeOfDay Type [System.TimeSpan]
  * $schedoptions.LatestRunLocal.Year Type [System.Int32]
* $schedoptions.LimitBackupsFrequency Type [System.Boolean]
* $schedoptions.MaxBackupsFrequency
* $schedoptions.NextRun
  * $schedoptions.NextRun.Length Type [System.Int32]
* $schedoptions.OptionsBackupWindow
  * $schedoptions.OptionsBackupWindow.BackupWindow Type [System.String]
  * $schedoptions.OptionsBackupWindow.IsEnabled Type [System.Boolean]
* $schedoptions.OptionsContinuous
  * $schedoptions.OptionsContinuous.Enabled Type [System.Boolean]
  * $schedoptions.OptionsContinuous.Schedule Type [System.String]
* $schedoptions.OptionsDaily
  * $schedoptions.OptionsDaily.DaysSrv Type [System.DayOfWeek[]]
  * $schedoptions.OptionsDaily.Enabled Type [System.Boolean]
  * $schedoptions.OptionsDaily.Kind Type [Veeam.Backup.Model.DailyOptions+DailyKinds]
  * $schedoptions.OptionsDaily.TimeLocal Type [System.DateTime]
* $schedoptions.OptionsMonthly
  * $schedoptions.OptionsMonthly.DayNumberInMonth Type [Veeam.Backup.Common.EDayNumberInMonth]
  * $schedoptions.OptionsMonthly.DayOfMonth Type [Veeam.Backup.Model.CDayOfMonth]
  * $schedoptions.OptionsMonthly.DayOfWeek Type [System.DayOfWeek]
  * $schedoptions.OptionsMonthly.Enabled Type [System.Boolean]
  * $schedoptions.OptionsMonthly.Months Type [Veeam.Backup.Common.EMonth[]]
  * $schedoptions.OptionsMonthly.TimeLocal Type [System.DateTime]
* $schedoptions.OptionsPeriodically
  * $schedoptions.OptionsPeriodically.Enabled Type [System.Boolean]
  * $schedoptions.OptionsPeriodically.FullPeriod Type [System.Int32]
  * $schedoptions.OptionsPeriodically.HourlyOffset Type [System.Int32]
  * $schedoptions.OptionsPeriodically.Kind Type [Veeam.Backup.Model.PeriodicallyOptions+PeriodicallyKinds]
  * $schedoptions.OptionsPeriodically.Schedule Type [System.String]
* $schedoptions.OptionsScheduleAfterJob
  * $schedoptions.OptionsScheduleAfterJob.IsEnabled Type [System.Boolean]
* $schedoptions.RepeatNumber
* $schedoptions.RepeatSpecified Type [System.Boolean]
* $schedoptions.RepeatTimeUnit
  * $schedoptions.RepeatTimeUnit.Length Type [System.Int32]
* $schedoptions.RepeatTimeUnitMs
* $schedoptions.RetrySpecified
* $schedoptions.RetryTimeout
* $schedoptions.RetryTimes
* $schedoptions.StartDateTimeLocal
  * $schedoptions.StartDateTimeLocal.Date Type [System.DateTime]
  * $schedoptions.StartDateTimeLocal.Day Type [System.Int32]
  * $schedoptions.StartDateTimeLocal.DayOfWeek Type [System.DayOfWeek]
  * $schedoptions.StartDateTimeLocal.DayOfYear Type [System.Int32]
  * $schedoptions.StartDateTimeLocal.Hour Type [System.Int32]
  * $schedoptions.StartDateTimeLocal.Kind Type [System.DateTimeKind]
  * $schedoptions.StartDateTimeLocal.Millisecond Type [System.Int32]
  * $schedoptions.StartDateTimeLocal.Minute Type [System.Int32]
  * $schedoptions.StartDateTimeLocal.Month Type [System.Int32]
  * $schedoptions.StartDateTimeLocal.Second Type [System.Int32]
  * $schedoptions.StartDateTimeLocal.Ticks Type [System.Int64]
  * $schedoptions.StartDateTimeLocal.TimeOfDay Type [System.TimeSpan]
  * $schedoptions.StartDateTimeLocal.Year Type [System.Int32]
* $schedoptions.WaitForBackupCompletion

```
$options = Get-VBRJobScheduleOptions -job @(Get-VBRJob | where { $_.JobType -eq "Backup" })[0]
$members = $options | gm -MemberType Property  | % { $_.Name }
foreach ($member in $members)
{

 $suboptions = $options."$member"
 if ($suboptions) {
     Write-host (("* `$schedoptions.{0}") -f $member)
     $submembers = $suboptions | gm -MemberType Property | % { new-object -type psobject -argumentlist @{n=$_.Name} }
     foreach ($submember in $submembers)
     {
      $submemt = "`$null"
      try {
      $submemt = $options.$member.$($submember.n).GetType().Fullname
      } catch {
      }
      Write-host (("  * `$schedoptions.{0}.{1} Type [{2}]") -f $member,$submember.n,$submemt)
     }
 } else {
    $t = "`$null"
    try {
        $t = $options.$member.GetType().Fullname
    } catch {}
    Write-host (("* `$schedoptions.{0} Type [{1}]") -f $member,$t)
 }
}
```

# VSS Options Dumps
* $vssoptions.AreLinCredsSet Type [System.Boolean]
* $vssoptions.AreWinCredsSet
* $vssoptions.Enabled
* $vssoptions.ExchangeBackupOptions
  * $vssoptions.ExchangeBackupOptions.TransactionLogsProcessing Type [Veeam.Backup.Model.ETransactionLogsProcessing]
* $vssoptions.ExcludedIndexingFolders Type [System.String[]]
* $vssoptions.GuestFSExcludeOptions
  * $vssoptions.GuestFSExcludeOptions.BackupScope Type [Veeam.Backup.Model.EGuestFSBackupScope]
  * $vssoptions.GuestFSExcludeOptions.ExcludeList Type [System.String[]]
  * $vssoptions.GuestFSExcludeOptions.FileExcludeEnabled Type [System.Boolean]
  * $vssoptions.GuestFSExcludeOptions.IncludeList Type [System.String[]]
* $vssoptions.GuestFSIndexingType Type [Veeam.Backup.Model.EGuestFSIndexingType]
* $vssoptions.GuestProxyAutoDetect
* $vssoptions.GuestScriptsOptions
  * $vssoptions.GuestScriptsOptions.IsAtLeastOneScriptSet Type [System.Boolean]
  * $vssoptions.GuestScriptsOptions.LinScriptFiles Type [Veeam.Backup.Model.CScriptFiles]
  * $vssoptions.GuestScriptsOptions.ScriptingMode Type [Veeam.Backup.Model.EScriptingMode]
  * $vssoptions.GuestScriptsOptions.WinScriptFiles Type [Veeam.Backup.Model.CScriptFiles]
* $vssoptions.IgnoreErrors Type [System.Boolean]
* $vssoptions.IncludedIndexingFolders Type [System.String[]]
* $vssoptions.IsFirstUsage
* $vssoptions.LinCredsId
* $vssoptions.LinExcludedIndexingFolders Type [System.String[]]
* $vssoptions.LinGuestFSIndexingOptions
  * $vssoptions.LinGuestFSIndexingOptions.ExcludedFolders Type [System.String[]]
  * $vssoptions.LinGuestFSIndexingOptions.IncludedFolders Type [System.String[]]
  * $vssoptions.LinGuestFSIndexingOptions.IsIndexingRequired Type [System.Boolean]
  * $vssoptions.LinGuestFSIndexingOptions.Type Type [Veeam.Backup.Model.EGuestFSIndexingType]
* $vssoptions.LinGuestFSIndexingType Type [Veeam.Backup.Model.EGuestFSIndexingType]
* $vssoptions.LinIncludedIndexingFolders Type [System.String[]]
* $vssoptions.m_isFirstUsage
* $vssoptions.OracleBackupOptions
  * $vssoptions.OracleBackupOptions.ArchivedLogsMaxAgeHours Type [System.Int32]
  * $vssoptions.OracleBackupOptions.ArchivedLogsMaxSizeMb Type [System.Int32]
  * $vssoptions.OracleBackupOptions.ArchivedLogsTruncation Type [Veeam.Backup.Model.EArchivedLogsTruncation]
  * $vssoptions.OracleBackupOptions.BackupLogsEnabled Type [System.Boolean]
  * $vssoptions.OracleBackupOptions.BackupLogsFrequencyMin Type [System.Int32]
  * $vssoptions.OracleBackupOptions.ProxyAutoSelect Type [System.Boolean]
  * $vssoptions.OracleBackupOptions.RetainDays Type [System.Int32]
  * $vssoptions.OracleBackupOptions.SysdbaCredsId Type [System.Guid]
  * $vssoptions.OracleBackupOptions.TransactionLogsProcessing Type [Veeam.Backup.Model.ETransactionLogsProcessing]
  * $vssoptions.OracleBackupOptions.UseDbBackupRetention Type [System.Boolean]
* $vssoptions.SqlBackupOptions
  * $vssoptions.SqlBackupOptions.BackupLogsEnabled Type [System.Boolean]
  * $vssoptions.SqlBackupOptions.BackupLogsFrequencyMin Type [System.Int32]
  * $vssoptions.SqlBackupOptions.NeverTruncateLogs Type [System.Boolean]
  * $vssoptions.SqlBackupOptions.ProxyAutoSelect Type [System.Boolean]
  * $vssoptions.SqlBackupOptions.RetainDays Type [System.Int32]
  * $vssoptions.SqlBackupOptions.TransactionLogsProcessing Type [Veeam.Backup.Model.ETransactionLogsProcessing]
  * $vssoptions.SqlBackupOptions.UseDbBackupRetention Type [System.Boolean]
* $vssoptions.VssSnapshotOptions
  * $vssoptions.VssSnapshotOptions.Enabled Type [System.Boolean]
  * $vssoptions.VssSnapshotOptions.IgnoreErrors Type [System.Boolean]
  * $vssoptions.VssSnapshotOptions.IsCopyOnly Type [System.Boolean]
* $vssoptions.WinCredsId
* $vssoptions.WinGuestFSIndexingOptions
  * $vssoptions.WinGuestFSIndexingOptions.ExcludedFolders Type [System.String[]]
  * $vssoptions.WinGuestFSIndexingOptions.IncludedFolders Type [System.String[]]
  * $vssoptions.WinGuestFSIndexingOptions.IsIndexingRequired Type [System.Boolean]
  * $vssoptions.WinGuestFSIndexingOptions.Type Type [Veeam.Backup.Model.EGuestFSIndexingType]

This was generated via the following PScode (if you are running on a higher/lower version this could be userful)
```
$options = Get-VBRJobVSSOptions -job @(Get-VBRJob | where { $_.JobType -eq "Backup" })[0]
$members = $options | gm -MemberType Property  | % { $_.Name }
foreach ($member in $members)
{

 $suboptions = $options."$member"
 if ($suboptions) {
     Write-host (("* `$vssoptions.{0}") -f $member)
     $submembers = $suboptions | gm -MemberType Property | % { new-object -type psobject -argumentlist @{n=$_.Name} }
     foreach ($submember in $submembers)
     {
      $submemt = "`$null"
      try {
      $submemt = $options.$member.$($submember.n).GetType().Fullname
      } catch {
      }
      Write-host (("  * `$vssoptions.{0}.{1} Type [{2}]") -f $member,$submember.n,$submemt)
     }
 } else {
    $t = "`$null"
    try {
        $t = $options.$member.GetType().Fullname
    } catch {}
    Write-host (("* `$vssoptions.{0} Type [{1}]") -f $member,$t)
 }
}
```

# Subobject VSS Options

* $vssobjoptions.AreLinCredsSet Type [System.Boolean]
* $vssobjoptions.AreWinCredsSet Type [System.Boolean]
* $vssobjoptions.Enabled
* $vssobjoptions.ExchangeBackupOptions
  * $vssobjoptions.ExchangeBackupOptions.TransactionLogsProcessing Type [Veeam.Backup.Model.ETransactionLogsProcessing]
* $vssobjoptions.ExcludedIndexingFolders
  * $vssobjoptions.ExcludedIndexingFolders.Length Type [System.Int32]
* $vssobjoptions.GuestFSExcludeOptions
  * $vssobjoptions.GuestFSExcludeOptions.BackupScope Type [Veeam.Backup.Model.EGuestFSBackupScope]
  * $vssobjoptions.GuestFSExcludeOptions.ExcludeList Type [System.String[]]
  * $vssobjoptions.GuestFSExcludeOptions.FileExcludeEnabled Type [System.Boolean]
  * $vssobjoptions.GuestFSExcludeOptions.IncludeList Type [System.String[]]
* $vssobjoptions.GuestFSIndexingType
  * $vssobjoptions.GuestFSIndexingType.value__ Type [System.Int32]
* $vssobjoptions.GuestProxyAutoDetect
* $vssobjoptions.GuestScriptsOptions
  * $vssobjoptions.GuestScriptsOptions.IsAtLeastOneScriptSet Type [System.Boolean]
  * $vssobjoptions.GuestScriptsOptions.LinScriptFiles Type [Veeam.Backup.Model.CScriptFiles]
  * $vssobjoptions.GuestScriptsOptions.ScriptingMode Type [Veeam.Backup.Model.EScriptingMode]
  * $vssobjoptions.GuestScriptsOptions.WinScriptFiles Type [Veeam.Backup.Model.CScriptFiles]
* $vssobjoptions.IgnoreErrors Type [System.Boolean]
* $vssobjoptions.IncludedIndexingFolders Type [System.String[]]
* $vssobjoptions.IsFirstUsage
* $vssobjoptions.LinCredsId
* $vssobjoptions.LinExcludedIndexingFolders
  * $vssobjoptions.LinExcludedIndexingFolders.Length Type [System.Int32]
* $vssobjoptions.LinGuestFSIndexingOptions
  * $vssobjoptions.LinGuestFSIndexingOptions.ExcludedFolders Type [System.String[]]
  * $vssobjoptions.LinGuestFSIndexingOptions.IncludedFolders Type [System.String[]]
  * $vssobjoptions.LinGuestFSIndexingOptions.IsIndexingRequired Type [System.Boolean]
  * $vssobjoptions.LinGuestFSIndexingOptions.Type Type [Veeam.Backup.Model.EGuestFSIndexingType]
* $vssobjoptions.LinGuestFSIndexingType
  * $vssobjoptions.LinGuestFSIndexingType.value__ Type [System.Int32]
* $vssobjoptions.LinIncludedIndexingFolders Type [System.String[]]
* $vssobjoptions.m_isFirstUsage
* $vssobjoptions.OracleBackupOptions
  * $vssobjoptions.OracleBackupOptions.ArchivedLogsMaxAgeHours Type [System.Int32]
  * $vssobjoptions.OracleBackupOptions.ArchivedLogsMaxSizeMb Type [System.Int32]
  * $vssobjoptions.OracleBackupOptions.ArchivedLogsTruncation Type [Veeam.Backup.Model.EArchivedLogsTruncation]
  * $vssobjoptions.OracleBackupOptions.BackupLogsEnabled Type [System.Boolean]
  * $vssobjoptions.OracleBackupOptions.BackupLogsFrequencyMin Type [System.Int32]
  * $vssobjoptions.OracleBackupOptions.ProxyAutoSelect Type [System.Boolean]
  * $vssobjoptions.OracleBackupOptions.RetainDays Type [System.Int32]
  * $vssobjoptions.OracleBackupOptions.SysdbaCredsId Type [System.Guid]
  * $vssobjoptions.OracleBackupOptions.TransactionLogsProcessing Type [Veeam.Backup.Model.ETransactionLogsProcessing]
  * $vssobjoptions.OracleBackupOptions.UseDbBackupRetention Type [System.Boolean]
* $vssobjoptions.SqlBackupOptions
  * $vssobjoptions.SqlBackupOptions.BackupLogsEnabled Type [System.Boolean]
  * $vssobjoptions.SqlBackupOptions.BackupLogsFrequencyMin Type [System.Int32]
  * $vssobjoptions.SqlBackupOptions.NeverTruncateLogs Type [System.Boolean]
  * $vssobjoptions.SqlBackupOptions.ProxyAutoSelect Type [System.Boolean]
  * $vssobjoptions.SqlBackupOptions.RetainDays Type [System.Int32]
  * $vssobjoptions.SqlBackupOptions.TransactionLogsProcessing Type [Veeam.Backup.Model.ETransactionLogsProcessing]
  * $vssobjoptions.SqlBackupOptions.UseDbBackupRetention Type [System.Boolean]
* $vssobjoptions.VssSnapshotOptions
  * $vssobjoptions.VssSnapshotOptions.Enabled Type [System.Boolean]
  * $vssobjoptions.VssSnapshotOptions.IgnoreErrors Type [System.Boolean]
  * $vssobjoptions.VssSnapshotOptions.IsCopyOnly Type [System.Boolean]
* $vssobjoptions.WinCredsId
* $vssobjoptions.WinGuestFSIndexingOptions
  * $vssobjoptions.WinGuestFSIndexingOptions.ExcludedFolders Type [System.String[]]
  * $vssobjoptions.WinGuestFSIndexingOptions.IncludedFolders Type [System.String[]]
  * $vssobjoptions.WinGuestFSIndexingOptions.IsIndexingRequired Type [System.Boolean]
  * $vssobjoptions.WinGuestFSIndexingOptions.Type Type [Veeam.Backup.Model.EGuestFSIndexingType]

```
$job = @(Get-VBRJob | where { $_.JobType -eq "Backup" })[0]
$options = Get-VBRJobObjectVssOptions @(Get-VBRJobObject -job $Job)[0]
$members = $options | gm -MemberType Property  | % { $_.Name }
foreach ($member in $members)
{

 $suboptions = $options."$member"
 if ($suboptions) {
     Write-host (("* `$vssobjoptions.{0}") -f $member)
     $submembers = $suboptions | gm -MemberType Property | % { new-object -type psobject -argumentlist @{n=$_.Name} }
     foreach ($submember in $submembers)
     {
      $submemt = "`$null"
      try {
      $submemt = $options.$member.$($submember.n).GetType().Fullname
      } catch {
      }
      Write-host (("  * `$vssobjoptions.{0}.{1} Type [{2}]") -f $member,$submember.n,$submemt)
     }
 } else {
    $t = "`$null"
    try {
        $t = $options.$member.GetType().Fullname
    } catch {}
    Write-host (("* `$vssobjoptions.{0} Type [{1}]") -f $member,$t)
 }
}
```
