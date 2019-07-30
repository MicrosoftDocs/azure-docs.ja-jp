---
title: Azure Backup のデータ モデル
description: この記事では、Azure Backup レポートに使用する Power BI データ モデルの詳細について説明します。
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: adigan
ms.openlocfilehash: 66245a25af000d999ded87c58e3ba225b373b101
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465588"
---
# <a name="data-model-for-azure-backup-reports"></a>Azure Backup レポートのデータ モデル
この記事では、Azure Backup レポートの作成に使用する Power BI データ モデルについて説明します。 このデータ モデルを使用すると、関連するフィールドに基づく既存のレポートをフィルター処理し、さらに重要なことに、モデルのテーブルとフィールドを使用して独自のレポートを作成できます。 

## <a name="creating-new-reports-in-power-bi"></a>Power BI で新しいレポートを作成する
Power BI には、[データ モデルを使用してレポートを作成できる](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)カスタマイズ機能が備わっています。

## <a name="using-azure-backup-data-model"></a>Azure Backup データ モデルを使用する
レポートを作成したり、既存のレポートをカスタマイズしたりするには、データ モデルの一部として提供される次のフィールドを使用できます。

### <a name="alert"></a>アラート:
次の表は、さまざまなアラートに関連するフィールドの基本フィールドと集計を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| #AlertsCreatedInPeriod |整数 |選択した期間に作成されるアラートの数 |
| %ActiveAlertsCreatedInPeriod |割合 |選択した期間のアクティブなアラートの割合 |
| %CriticalAlertsCreatedInPeriod |割合 |選択した期間の重大なアラートの割合 |
| AlertOccurrenceDate |Date |アラートが作成された日付 |
| AlertSeverity |Text |アラートの重要度 (例: 重大) |
| AlertStatus |Text |アラートの状態 (例: アクティブ) |
| AlertType |Text |生成されたアラートの種類 (例: バックアップ) |
| AlertUniqueId |Text |生成されたアラートの一意の ID |
| AsOnDateTime |日付/時刻 |選択した行の最新の更新時刻 |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |10 進数 |選択した期間のアラートを解決するための平均時間 (分) |
| EntityState |Text |アラート オブジェクトの現在の状態 (例: アクティブ、削除済み) |

### <a name="backup-item"></a>バックアップ項目
次の表は、さまざまなバックアップ項目に関連するフィールドの基本フィールドと集計を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| #BackupItems |整数 |バックアップ項目の数 |
| #UnprotectedBackupItems |整数 |保護のために停止されたか、バックアップを構成されているが、バックアップが開始されていないバックアップ項目の数|
| AsOnDateTime |日付/時刻 |選択した行の最新の更新時刻 |
| BackupItemFriendlyName |Text |バックアップ項目のフレンドリ名 |
| BackupItemId |Text |バックアップ項目の ID |
| BackupItemName |Text |バックアップ項目の名前 |
| BackupItemType |Text |バックアップ項目の種類 (例: VM、FileFolder) |
| EntityState |Text |バックアップ項目オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| LastBackupDateTime |日付/時刻 |選択したバックアップ項目の最後のバックアップの時刻 |
| LastBackupState |Text |選択したバックアップ項目の最後のバックアップの状態 (例: 成功、失敗) |
| LastSuccessfulBackupDateTime |日付/時刻 |選択したバックアップ項目の最後に成功したバックアップの時刻 |
| ProtectionState |Text |バックアップ項目の現在の保護状態 (例: 保護済み、保護停止) |

### <a name="calendar"></a>カレンダー
次の表は、カレンダーに関連するフィールドを示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| Date |Date |データをフィルター処理するために選択した日付 |
| DateKey |Text |各日付項目の一意のキー |
| DayDiff |10 進数 |データのフィルター処理に使用する日単位の差。たとえば、0 は現在の日付のデータを示し、-1 は前日のデータを示し、0 および-1 は現在の日付と前日のデータを示します  |
| 月 |Text |データをフィルター処理するために選択した月。月は 1 日から始まり、31 日で終わります |
| MonthDate | Date |データをフィルター処理するために選択した、月が終わる日付 |
| MonthDiff |10 進数 |データのフィルター処理に使用する月単位の差。たとえば、0 は現在の月のデータを示し、-1 は前月のデータを示し、0 および-1 は現在の月と前月のデータを示します |
| 週 |Text |データをフィルター処理するために選択した週。週は日曜日から始まり、土曜日で終わります |
| WeekDate |Date |データをフィルター処理するために選択した、週が終わる曜日 |
| WeekDiff |10 進数 |データのフィルター処理に使用する週単位の差。たとえば、0 は現在の週のデータを示し、-1 は前の週のデータを示し、0 および-1 は現在と前の週のデータを示します |
| 年 |Text |データをフィルター処理するために選択したカレンダーの年 |
| YearDate |Date |データをフィルター処理するために選択した、年が終わる日付 |

### <a name="job"></a>ジョブ
次の表は、さまざまなジョブに関連するフィールドの基本フィールドと集計を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| #JobsCreatedInPeriod |整数 |選択した期間に作成されるジョブの数 |
| %FailuresForJobsCreatedInPeriod |割合 |選択された期間の全体的なジョブ エラーの割合 |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |10 進数 |選択した期間に作成された**バックアップ** ジョブに対して転送されるデータの80 パーセンタイル値 (MB) |
| AsOnDateTime |日付/時刻 |選択した行の最新の更新時刻 |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |10 進数 |選択した期間に作成された**完了したバックアップ** ジョブの平均時間 (分) |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |10 進数 |選択した期間に作成された**完了した復元**ジョブの平均時間 (分) |
| BackupStorageDestination |Text |バックアップ ストレージの保存先 (例: クラウド、ディスク)  |
| EntityState |Text |ジョブ オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| JobFailureCode |Text |ジョブ エラーが発生したことによるエラー コードの文字列 |
| JobOperation |Text |ジョブを実行する対象の操作 (例: バックアップ、復元、バックアップの構成) |
| JobStartDate |Date |ジョブの実行開始日 |
| JobStartTime |Time |ジョブの実行開始時刻 |
| JobStatus |Text |完了したジョブの状態 (例: 完了、失敗) |
| JobUniqueId |Text |ジョブを識別する一意の ID |

### <a name="policy"></a>ポリシー
次の表は、さまざまなポリシーに関連するフィールドの基本フィールドと集計を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| #Policies |整数 |システム内に存在するバックアップ ポリシーの数 |
| #PoliciesInUse |整数 |現在、バックアップを構成するために使用されているポリシーの数 |
| AsOnDateTime |日付/時刻 |選択した行の最新の更新時刻 |
| BackupDaysOfTheWeek |Text |バックアップがスケジュールされている曜日 |
| BackupFrequency |Text |バックアップが実行される頻度 (例: 毎日、毎週) |
| BackupTimes |Text |バックアップがスケジュールされている日付と時刻 |
| DailyRetentionDuration |整数 |構成されたバックアップに使用される合計リテンション期間 (日数) |
| DailyRetentionTimes |Text |毎日のリテンション期間が構成された日付と時刻 |
| EntityState |Text |ポリシー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| MonthlyRetentionDaysOfTheMonth |Text |毎月のリテンション期間に選択された月の日数 |
| MonthlyRetentionDaysOfTheWeek |Text |毎月のリテンション期間に選択された曜日 |
| MonthlyRetentionDuration |10 進数 |構成されたバックアップに使用される合計のリテンション期間 (月単位) |
| MonthlyRetentionFormat |Text |毎月のリテンション期間に使用する構成の種類 (例: 日単位の毎日、週単位の毎週) |
| MonthlyRetentionTimes |Text |毎月のリテンション期間が構成される日付と時刻 |
| MonthlyRetentionWeeksOfTheMonth |Text |毎月のリテンション期間が構成されたときの月の週 (例: 最初、最後など) |
| PolicyName |Text |定義されたポリシーの名前 |
| PolicyUniqueId |Text |ポリシーを識別する一意の ID |
| RetentionType |Text |リテンション ポリシーの種類 (例: 毎日、毎週、毎月、毎年) |
| WeeklyRetentionDaysOfTheWeek |Text |毎週のリテンション期間に選択された曜日 |
| WeeklyRetentionDuration |10 進数 |構成されたバックアップの毎週の合計リテンション期間 (週単位) |
| WeeklyRetentionTimes |Text |毎週のリテンション期間が構成される日付と時刻 |
| YearlyRetentionDaysOfTheMonth |Text |毎年のリテンション期間に選択された月の日数 |
| YearlyRetentionDaysOfTheWeek |Text |毎年のリテンション期間に選択された曜日 |
| YearlyRetentionDuration |10 進数 |構成されたバックアップに使用される合計リテンション期間 (年単位) |
| YearlyRetentionFormat |Text |毎年のリテンション期間に使用する構成の種類 (例: 日単位の毎日、週単位の毎週) |
| YearlyRetentionMonthsOfTheYear |Text |毎年のリテンション期間に選択された月 |
| YearlyRetentionTimes |Text |毎年のリテンション期間が構成される日付と時刻 |
| YearlyRetentionWeeksOfTheMonth |Text |毎年のリテンション期間が構成される場合の月の週 (例: 最初、最後など) |

### <a name="protected-server"></a>保護されるサーバー
次の表は、さまざまな保護されるサーバーに関連するフィールドの基本フィールドと集計を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| #ProtectedServers |整数 |保護されるサーバーの数 |
| AsOnDateTime |日付/時刻 |選択した行の最新の更新時刻 |
| AzureBackupAgentOSType |Text |Azure Backup エージェントの OS の種類 |
| AzureBackupAgentOSVersion |Text |Azure Backup エージェントの OS バージョン |
| AzureBackupAgentUpdateDate |Text |Azure Backup エージェントが更新された日付 |
| AzureBackupAgentVersion |Text |エージェント バックアップ バージョンのバージョン番号 |
| BackupManagementType |Text |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| EntityState |Text |保護されるサーバー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| ProtectedServerFriendlyName |Text |保護されるサーバーのフレンドリ名 |
| ProtectedServerName |Text |保護されるサーバーの名前 |
| ProtectedServerType |Text |バックアップされた保護されるサーバーの種類 (例: IaaSVMContainer) |
| ProtectedServerName |Text |バックアップ項目が属している保護されるサーバーの名前 |
| RegisteredContainerId |Text |バックアップ用に登録されたコンテナーの ID |

### <a name="storage"></a>Storage
次の表は、さまざまなストレージに関連するフィールドの基本フィールドと集計を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| #ProtectedInstances |10 進数 |選択した時刻の最新の値に基づいて計算された、課金されるフロントエンド ストレージの計算に使用する保護されるインスタンスの数 |
| AsOnDateTime |日付/時刻 |選択した行の最新の更新時刻 |
| CloudStorageInMB |10 進数 |選択した時刻の最新の値に基づいて計算された、バックアップによって使用されるクラウド バックアップ ストレージ |
| EntityState |Text |オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| LastUpdatedDate |Date |選択した行が最後に更新された日付 |

### <a name="time"></a>Time
次の表は、時刻に関連するフィールドを示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| Hour |Time |時刻 (例: 1:00:00 PM) |
| HourNumber |10 進数 |1 日の時間 (例: 13.00) |
| [分] |10 進数 |分 |
| PeriodOfTheDay |Text |1 日の時間帯スロット (例: 12-3 AM) |
| Time |Time |1 日の時刻 (例 12:00:01 AM) |
| TimeKey |Text |時刻を表すキー値 |

### <a name="vault"></a>コンテナー
次の表は、さまざまなコンテナーに関連するフィールドの基本フィールドと集計を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| #Vaults |整数 |コンテナーの数 |
| AsOnDateTime |日付/時刻 |選択した行の最新の更新時刻 |
| AzureDataCenter |Text |コンテナーが配置されるデータ センター |
| EntityState |Text |コンテナー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| StorageReplicationType |Text |コンテナーのストレージ レプリケーションの種類 (例: GeoRedundant) |
| SubscriptionId |Text |レポートを生成するために選択された顧客のサブスクリプション ID |
| VaultName |Text |コンテナーの名前 |
| VaultTags |Text |コンテナーに関連付けられるタグ |

## <a name="next-steps"></a>次の手順
Azure Backup のレポートを作成するためのデータ モデルを確認した時点で、Power BI レポートの作成および表示に関する詳細について、次の記事を参照してください。

* [Power BI でレポートを作成する](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Power BI でレポートをフィルター処理する](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
