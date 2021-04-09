---
title: Azure Backup 診断イベントのデータ モデル
description: このデータ モデルは、Log Analytics (LA) に診断イベントを送信するリソース固有モードを参照しています。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 0d75af6d2b41aad0b5f821dd1f6409b30f7ca531
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499596"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Azure Backup 診断イベントのデータ モデル

> [!NOTE]
>
> カスタムのレポート ビューを作成する場合は、次に示す未加工のテーブルを操作するのではなく、[Azure Monitor ログでシステム関数](backup-reports-system-functions.md)を使用することをお勧めします。

## <a name="coreazurebackup"></a>CoreAzureBackup

次の表に、コンテナーやバックアップ項目などの主要なバックアップ項目に関する情報を示します。

| **フィールド**                         | **[データ型]** | **説明**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Text          | 収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID。 |
| OperationName                     | Text          | このフィールドは現在の操作の名前 (BackupItem、BackupItemAssociation、または ProtectedContainer) を表します。 |
| カテゴリ                          | Text          | このフィールドは Azure Monitor ログにプッシュされた診断データのカテゴリを表します。 たとえば、CoreAzureBackup のようになります。 |
| AgentVersion                      | Text          | エージェント バックアップまたは保護エージェント (SC DPM および MABS の場合) のバージョン番号 |
| AzureBackupAgentVersion           | Text          | バックアップ管理サーバーの Azure Backup エージェントのバージョン |
| AzureDataCenter                   | Text          | コンテナーが配置されるデータ センター                       |
| BackupItemAppVersion              | Text          | バックアップ項目のアプリケーションのバージョン                       |
| BackupItemFriendlyName            | Text          | バックアップ項目のフレンドリ名                             |
| BackupItemName                    | Text          | バックアップ項目の名前                                      |
| BackupItemProtectionState         | Text          | バックアップ項目の保護状態                          |
| BackupItemFrontEndSize            | Text          | バックアップ項目のフロント エンドのサイズ                            |
| BackupItemType                    | Text          | バックアップ項目の種類。 次に例を示します。VM、FileFolder             |
| BackupItemUniqueId                | Text          | バックアップ項目の一意の識別子                         |
| BackupManagementServerType        | Text          | バックアップ管理サーバーの種類 (MABS、SC DPM)     |
| BackupManagementServerUniqueId    | Text          | バックアップ管理サーバーを一意に識別するフィールド      |
| BackupManagementType              | Text          | バックアップ ジョブを実行しているサーバーのプロバイダーの種類。 たとえば、IaaSVM や FileFolder |
| BackupManagementServerName        | Text          | バックアップ管理サーバーの名前                         |
| BackupManagementServerOSVersion   | Text          | バックアップ管理サーバーの OS のバージョン                   |
| BackupManagementServerVersion     | Text          | バックアップ管理サーバーのバージョン                      |
| LatestRecoveryPointLocation       | Text          | バックアップ項目の最新の復旧ポイントの場所    |
| LatestRecoveryPointTime           | DateTime      | バックアップ項目の最新の復旧ポイントの日時   |
| OldestRecoveryPointLocation       | Text          | バックアップ項目の最も古い復旧ポイントの場所    |
| OldestRecoveryPointTime           | DateTime      | バックアップ項目の最新の復旧ポイントの日時   |
| PolicyUniqueId                    | Text          | ポリシーを識別する一意の ID                             |
| ProtectedContainerFriendlyName    | Text          | 保護されるサーバーのフレンドリ名                        |
| ProtectedContainerLocation        | Text          | 保護されたコンテナーの配置場所 (オンプレミスまたは Azure 内) |
| ProtectedContainerName            | Text          | 保護されたコンテナーの名前                            |
| ProtectedContainerOSType          | Text          | 保護されたコンテナーの OS の種類                          |
| ProtectedContainerOSVersion       | Text          | 保護されたコンテナーの OS のバージョン                        |
| ProtectedContainerProtectionState | Text          | 保護されたコンテナーの保護の状態                  |
| ProtectedContainerType            | Text          | 保護されたコンテナーがサーバーかコンテナーか  |
| ProtectedContainerUniqueId        | Text          | DPM、MABS を使用してバックアップされた VM 以外のすべてに対して保護されたコンテナーの識別に使用される一意 ID |
| ProtectedContainerWorkloadType    | Text          | 保護されたコンテナーのバックアップの種類。 たとえば、IaaSVMContainer |
| ProtectionGroupName               | Text          | 該当する場合、SC DPM、および MABS で、バックアップ項目が保護されている保護グループの名前 |
| ResourceGroupName                 | Text          | 収集されるデータのリソースのリソース グループ (例: Recovery Services コンテナー) |
| SchemaVersion                     | Text          | このフィールドは、スキーマの現在のバージョンを表します。 ここでは **V2** です |
| SecondaryBackupProtectionState    | Text          | バックアップ項目の二次的な保護が有効になっているかどうか  |
| State                             | Text          | バックアップ項目オブジェクトの状態。 たとえば、アクティブ、削除済み |
| StorageReplicationType            | Text          | コンテナーのストレージ レプリケーションの種類。 例: GeoRedundant |
| SubscriptionId                    | Text          | データが収集されるリソースのサブスクリプション識別子 (例: Recovery Services コンテナー名) |
| VaultName                         | Text          | コンテナーの名前                                            |
| VaultTags                         | Text          | コンテナー リソースに関連付けられているタグ                    |
| VaultUniqueId                     | Text          | コンテナーの一意の識別子                             |
| SourceSystem                      | Text          | 現在のデータのソース システム (Azure)                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

次の表は、アラートに関連するフィールドの詳細を示しています。

| **フィールド**                      | **[データ型]** | **説明**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | データの収集対象のリソースの一意の識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| OperationName                  | Text          | 現在の操作の名前。 例: アラート            |
| カテゴリ                       | Text          | Azure Monitor ログにプッシュされた診断データのカテゴリ (AddonAzureBackupAlerts) |
| AlertCode                      | Text          | アラートの種類を一意に識別するコード                     |
| AlertConsolidationStatus       | Text          | アラートが統合されたアラートかどうかを識別します         |
| AlertOccurrenceDateTime        | DateTime      | アラートが作成された日付と時刻                     |
| AlertRaisedOn                  | Text          | アラートが発生したエンティティの種類                        |
| AlertSeverity                  | Text          | アラートの重大度。 例: 重大                 |
| AlertStatus                    | Text          | アラートの状態。 例: アクティブ                     |
| AlertTimeToResolveInMinutes    | Number        | アラートの解決に要した時間。 アクティブなアラートの場合は空白。     |
| AlertType                      | Text          | アラートの種類。 例: バックアップ                           |
| AlertUniqueId                  | Text          | 生成されたアラートの一意の識別子                    |
| BackupItemUniqueId             | Text          | アラートに関連付けられているバックアップ項目の一意の識別子 |
| BackupManagementServerUniqueId | Text          | 該当する場合、バックアップ項目の保護に使用されるバックアップ管理サーバーを一意に識別するフィールド |
| BackupManagementType           | Text          | バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| CountOfAlertsConsolidated      | Number        | 統合されたアラートである場合の、統合されたアラートの数  |
| ProtectedContainerUniqueId     | Text          | アラートに関連付けられている保護されるサーバーの一意の識別子 |
| RecommendedAction              | Text          | アラートを解決するために推奨されているアクション                      |
| SchemaVersion                  | Text          | スキーマの現在のバージョン (例: **V2**)            |
| State                          | Text          | アラート オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| StorageUniqueId                | Text          | ストレージ エントリの識別に使用される一意 ID                |
| VaultUniqueId                  | Text          | アラートに関連するコンテナーの識別に使用される一意の ID    |
| SourceSystem                   | Text          | 現在のデータのソース システム (Azure)                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

次の表は、保護されたインスタンスに関連する基本フィールドを示しています。

| **フィールド**                      | **[データ型]** | **説明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | データの収集対象のリソースの一意の識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| OperationName                  | Text          | 操作の名前 (例: ProtectedInstance)         |
| カテゴリ                       | Text          | Azure Monitor ログにプッシュされた診断データのカテゴリ - AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Text          | バックアップ項目の一意の ID                                 |
| BackupManagementServerUniqueId | Text          | 該当する場合、バックアップ項目の保護に使用されるバックアップ管理サーバーを一意に識別するフィールド |
| BackupManagementType           | Text          | バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| ProtectedContainerUniqueId     | Text          | ジョブが実行される保護されたコンテナーを識別する、一意の ID |
| ProtectedInstanceCount         | Text          | 関連付けられているバックアップ項目または該当の日付/時刻で保護されたコンテナーの、保護されたインスタンスの数 |
| SchemaVersion                  | Text          | スキーマの現在のバージョン (例: **V2**)            |
| State                          | Text          | バックアップ項目オブジェクトの状態 (例: アクティブ、削除済み) |
| VaultUniqueId                  | Text          | 保護されたインスタンスに関連付けられている保護されるコンテナーの一意の識別子 |
| SourceSystem                   | Text          | 現在のデータのソース システム (Azure)                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

次の表は、ジョブに関連するフィールドの詳細を示しています。

| **フィールド**                      | **[データ型]** | **説明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| OperationName                  | Text          | このフィールドは現在の操作の名前 (Job) を表します。    |
| カテゴリ                       | Text          | このフィールドは Azure Monitor ログにプッシュされた診断データのカテゴリ (AddonAzureBackupJobs) を表します。 |
| AdhocOrScheduledJob            | Text          | ジョブがアドホックかスケジュール済みかを指定するフィールド           |
| BackupItemUniqueId             | Text          | ストレージ エントリに関連するバックアップ項目の識別に使用される一意 ID |
| BackupManagementServerUniqueId | Text          | ストレージ エントリに関連するバックアップ管理サーバーの識別に使用される一意 ID |
| BackupManagementType           | Text          | バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このジョブが属する FileFolder) |
| DataTransferredInMB            | Number        | このジョブで転送されたデータ (MB)                          |
| JobDurationInSecs              | Number        | 合計ジョブ期間 (秒単位)                                |
| JobFailureCode                 | Text          | ジョブ エラーが発生したことによるエラー コードの文字列    |
| JobOperation                   | Text          | ジョブを実行する対象の操作 (例: バックアップ、復元、バックアップの構成) |
| JobOperationSubType            | Text          | ジョブ操作のサブタイプ。 たとえば、ログ バックアップ ジョブの場合の "Log" |
| JobStartDateTime               | DateTime      | ジョブの実行を開始した日付と時刻                       |
| JobStatus                      | Text          | 完了したジョブの状態 (例: 完了、失敗)   |
| JobUniqueId                    | Text          | ジョブを識別する一意の ID                                |
| ProtectedContainerUniqueId     | Text          | ジョブに関連付けられている保護されるサーバーの一意の識別子 |
| RecoveryJobDestination         | Text          | 回復ジョブの宛先 (データが回復される場所)   |
| RecoveryJobRPDateTime          | DateTime      | 回復する復旧ポイントが作成された日付、時刻 |
| RecoveryJobLocation            | Text          | 回復する復旧ポイントが作成された場所 |
| RecoveryLocationType           | Text          | 回復する場所の種類                                |
| SchemaVersion                  | Text          | スキーマの現在のバージョン (例: **V2**)            |
| State                          | Text          | ジョブ オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| VaultUniqueId                  | Text          | ジョブに関連付けられている保護されるコンテナーの一意識別子 |
| SourceSystem                   | Text          | 現在のデータのソース システム (Azure)                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

次の表は、ポリシーに関連するフィールドの詳細を示しています。

| **フィールド**                       | **[データ型]**  | **説明**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Text           | データの収集対象のリソースの一意の識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| OperationName                   | Text           | 操作の名前 (Policy、PolicyAssociation など) |
| カテゴリ                        | Text           | Azure Monitor ログにプッシュされた診断データのカテゴリ - AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Text           | バックアップがスケジュールされている曜日            |
| BackupFrequency                 | Text           | バックアップを実行する頻度。 例: 毎日、毎週 |
| BackupManagementType            | Text           | バックアップ ジョブを実行しているサーバーのプロバイダーの種類。 例: IaaSVM、FileFolder |
| BackupManagementServerUniqueId  | Text           | 該当する場合、バックアップ項目の保護に使用されるバックアップ管理サーバーを一意に識別するフィールド |
| BackupTimes                     | Text           | バックアップがスケジュールされている日付と時刻                     |
| DailyRetentionDuration          | 整数   | 構成されたバックアップに使用される合計リテンション期間 (日数)      |
| DailyRetentionTimes             | Text           | 毎日のリテンション期間が構成された日付と時刻            |
| DiffBackupDaysOfTheWeek         | Text           | Azure VM バックアップの SQL の差分バックアップの曜日 |
| DiffBackupFormat                | Text           | Azure VM バックアップの SQL の差分バックアップの形式   |
| DiffBackupRetentionDuration     | 10 進数 | Azure VM バックアップの SQL の差分バックアップのリテンション期間 |
| DiffBackupTime                  | Time           | Azure VM バックアップの SQL の差分バックアップの時刻     |
| LogBackupFrequency              | 10 進数 | SQL のログ バックアップの頻度                            |
| LogBackupRetentionDuration      | 10 進数 | Azure VM バックアップの SQL のログ バックアップのリテンション期間 |
| MonthlyRetentionDaysOfTheMonth  | Text           | 毎月のリテンション期間が構成されたときの月の週  例: 最初、最後 |
| MonthlyRetentionDaysOfTheWeek   | Text           | 毎月のリテンション期間に選択された曜日              |
| MonthlyRetentionDuration        | Text           | 構成されたバックアップに使用される合計のリテンション期間 (月単位)    |
| MonthlyRetentionFormat          | Text           | 毎月のリテンション期間に対する構成の種類。 例: 日単位の毎日、週単位の毎週 |
| MonthlyRetentionTimes           | Text           | 毎月のリテンション期間が構成される日付と時刻           |
| MonthlyRetentionWeeksOfTheMonth | Text           | 毎月のリテンション期間が構成されたときの月の週   例: 最初、最後 |
| PolicyName                      | Text           | 定義されたポリシーの名前                                   |
| PolicyUniqueId                  | Text           | ポリシーを識別する一意の ID                             |
| PolicyTimeZone                  | Text           | ポリシー時間フィールドがログで指定されているタイムゾーン |
| RetentionDuration               | Text           | 構成されたバックアップに使用されるリテンション期間                    |
| RetentionType                   | Text           | リテンションの種類                                            |
| SchemaVersion                   | Text           | このフィールドは、スキーマの現在のバージョン (**V2**) を表します |
| State                           | Text           | ポリシー オブジェクトの現在の状態。 例: アクティブ、削除済み |
| SynchronisationFrequencyPerDay  | 整数   | SC DPM および MABS でファイルのバックアップが 1 日に同期される回数 |
| VaultUniqueId                   | Text           | ポリシーが属しているコンテナーの一意の ID          |
| WeeklyRetentionDaysOfTheWeek    | Text           | 毎週のリテンション期間に選択された曜日               |
| WeeklyRetentionDuration         | 10 進数 | 構成されたバックアップの毎週の合計リテンション期間 (週単位) |
| WeeklyRetentionTimes            | Text           | 毎週のリテンション期間が構成される日付と時刻            |
| YearlyRetentionDaysOfTheMonth   | Text           | 毎年のリテンション期間に選択された月の日数             |
| YearlyRetentionDaysOfTheWeek    | Text           | 毎年のリテンション期間に選択された曜日               |
| YearlyRetentionDuration         | 10 進数 | 構成されたバックアップに使用される合計リテンション期間 (年単位)     |
| YearlyRetentionFormat           | Text           | 毎年のリテンション期間に使用する構成の種類 (例: 日単位の毎日、週単位の毎週) |
| YearlyRetentionMonthsOfTheYear  | Text           | 毎年のリテンション期間に選択された月             |
| YearlyRetentionTimes            | Text           | 毎年のリテンション期間が構成される日付と時刻            |
| YearlyRetentionWeeksOfTheMonth  | Text           | 毎年のリテンション期間に選択された月の週数             |
| SourceSystem                    | Text           | 現在のデータのソース システム (Azure)                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

次の表は、ストレージに関連するフィールドの詳細を示しています。

| **フィールド**                      | **[データ型]** | **説明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| OperationName                  | Text          | このフィールドは現在の操作の名前 (Storage または StorageAssociation) を表します |
| カテゴリ                       | Text          | このフィールドは Azure Monitor ログにプッシュされた診断データのカテゴリ (AddonAzureBackupStorage) を表します |
| BackupItemUniqueId             | Text          | DPM、MABS を使用してバックアップされた VM のバックアップ項目の識別に使用される一意の ID |
| BackupManagementServerUniqueId | Text          | 該当する場合、バックアップ項目の保護に使用されるバックアップ管理サーバーを一意に識別するフィールド |
| BackupManagementType           | Text          | バックアップ ジョブを実行しているサーバーのプロバイダーの種類。 例: IaaSVM、FileFolder |
| PreferredWorkloadOnVolume      | Text          | このボリュームが優先ストレージとなるワークロード      |
| ProtectedContainerUniqueId     | Text          | バックアップ項目に関連付けられている保護されるコンテナーの一意の識別子 |
| SchemaVersion                  | Text          | スキーマのバージョン。 例: **V2**                   |
| State                          | Text          | バックアップ項目オブジェクトの状態。 例: アクティブ、削除済み |
| StorageAllocatedInMBs          | Number        | 種類が Disk の対応するストレージ内の対応するバックアップ項目によって割り当てられたストレージのサイズ |
| StorageConsumedInMBs           | Number        | 対応するストレージ内の対応するバックアップ項目によって使用されるストレージのサイズ |
| StorageName                    | Text          | ストレージ エンティティの名前。 例: E:\                      |
| StorageTotalSizeInGBs          | Text          | ストレージ エンティティによって消費されたストレージの合計サイズ (GB 単位)     |
| StorageType                    | Text          | ストレージの種類 (クラウド、ボリューム、ディスクなど)             |
| StorageUniqueId                | Text          | ストレージ エントリの識別に使用される一意 ID                |
| VaultUniqueId                  | Text          | ストレージ エントリに関連するコンテナーの識別に使用される一意の ID |
| VolumeFriendlyName             | Text          | ストレージ ボリュームのフレンドリ名                          |
| SourceSystem                   | Text          | 現在のデータのソース システム (Azure)                    |

## <a name="valid-operation-names-for-each-table"></a>各テーブルの有効な操作名

上記のテーブルの各レコードには、関連付けられている **操作名** があります。 操作名は、レコードの種類を表します (および、そのレコードに対して設定されるテーブル内のフィールドを示します)。 各テーブル (カテゴリ) は、1 つまたは複数の個別の操作名をサポートします。 次に、上記の各テーブルでサポートされている操作名の概要を示します。

| **テーブル名/カテゴリ**                   | **サポートされる操作名** | **説明**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | BackupItem | ID、名前、種類など、特定のバックアップ項目のすべての詳細を含むレコードを表します。 |
| CoreAzureBackup | BackupItemAssociation | バックアップ項目とそれに関連付けられている保護されたコンテナー (該当する場合) との間のマッピングを表します。 |
| CoreAzureBackup | BackupItemFrontEndSizeConsumption | バックアップ項目とそのフロント エンド サイズとの間のマッピングを表します。 |
| CoreAzureBackup | ProtectedContainer | ID、名前、種類など、特定の保護されたコンテナーのすべての詳細を含むレコードを表します。 |
| CoreAzureBackup | ProtectedContainerAssociation | 保護されたコンテナーとそのバックアップに使用されるコンテナーとの間のマッピングを表します。 |
| CoreAzureBackup | コンテナー | 指定されたコンテナーのすべての詳細を含むレコードを表します。たとえば、 ID、名前、タグ、場所などです。 |
| CoreAzureBackup | RecoveryPoint | 特定のバックアップ項目の最古および最新の回復ポイントを含むレコードを表します。 |
| AddonAzureBackupJobs | ジョブ |  指定されたジョブのすべての詳細を含むレコードを表します。 たとえば、ジョブ操作、開始時刻、状態などです。 |
| AddonAzureBackupAlerts | アラート: | 指定されたアラートのすべての詳細を含むレコードを表します。 たとえば、アラートの作成時刻、重要度、状態などです。  |
| AddonAzureBackupStorage | ストレージ | 指定されたストレージ エンティティのすべての詳細を含むレコードを表します。 たとえば、ストレージ名、種類などです。 |
| AddonAzureBackupStorage | StorageAssociation | バックアップ項目と、バックアップ項目によって消費されたクラウド ストレージの合計との間のマッピングを表します。 |
| AddonAzureBackupProtectedInstance | ProtectedInstance | 各コンテナーまたはバックアップ項目の保護されたインスタンス数を含むレコードを表します。 Azure VM バックアップの場合、保護されたインスタンス数はバックアップ項目レベルで利用できます。他のワークロードの場合は、保護されたコンテナー レベルで使用できます。 |
| AddonAzureBackupPolicy | ポリシー |  バックアップと保持ポリシーのすべての詳細を含むレコードを表します。 たとえば、ID、名前、保持設定などです。 |
| AddonAzureBackupPolicy | PolicyAssociation | バックアップ項目と、それに適用されるバックアップ ポリシーとの間のマッピングを表します。 |   

場合によっては、分析に必要なすべてのフィールドを取得するために、異なるテーブル間、および同じテーブル (操作名によって区別される) の一部であるレコードの異なるセット間の結合を実行する必要があります。 開始するには、[サンプル クエリ](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)を参照してください。 

## <a name="next-steps"></a>次のステップ

- [Log Analytics に診断データを送信する方法について学習してください](./backup-azure-diagnostic-events.md)
- [リソース固有のテーブルにクエリを記述する方法について説明します](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)