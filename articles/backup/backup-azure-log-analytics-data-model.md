---
title: Azure Backup の Azure Monitor ログのデータ モデル
description: この記事では、Azure Backup データに使用する Azure Monitor ログのデータ モデルの詳細について説明します。
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: 6563eefffee0ed8d9ce94c3e0a1e24b0d32314f0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466151"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure Backup データの Log Analytics データ モデル

Log Analytics からカスタム アラートを作成するには、Log Analytics データ モデルを使用します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Azure Backup データ モデルを使用する

データ モデルの一部として提供される次のフィールドを使用して、ビジュアル、カスタム クエリ、ダッシュボードを要件に合わせて作成できます。

### <a name="alert"></a>アラート:

次の表は、アラートに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| AlertUniqueId_s |Text |生成されたアラートの一意の識別子 |
| AlertType_s |Text |アラートの種類 (例: バックアップ) |
| AlertStatus_s |Text |アラートの状態 (例: アクティブ) |
| AlertOccurrenceDateTime_s |日付/時刻 |アラートが作成された日付と時刻 |
| AlertSeverity_s |Text |アラートの重大度 (例: 重大) |
|AlertTimeToResolveInMinutes_s    | Number        |アラートの解決に要した時間。 アクティブなアラートの場合は空白。         |
|AlertConsolidationStatus_s   |Text         |アラートが統合されたアラートかどうかを識別します         |
|CountOfAlertsConsolidated_s     |Number         |統合されたアラートである場合の、統合されたアラートの数          |
|AlertRaisedOn_s     |Text         |アラートが発生したエンティティの種類         |
|AlertCode_s     |Text         |アラートの種類を一意に識別するコード         |
|RecommendedAction_s   |Text         |アラートを解決するために推奨されているアクション         |
| EventName_s |Text |イベントの名前。 常に AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |アラートに関連付けられているバックアップ項目の一意の識別子 |
| SchemaVersion_s |Text |スキーマの現在のバージョン (例: **V2**) |
| State_s |Text |アラート オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |Text |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このアラートが属する FileFolder) |
| OperationName |Text |現在の操作の名前 (例: アラート) |
| Category |Text |Azure Monitor ログにプッシュされた診断データのカテゴリ。 常に AzureBackupReport |
| Resource |Text |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedContainerUniqueId_s |Text |アラートに関連付けられている保護されるサーバーの一意の識別子 (V1 では ProtectedServerUniqueId_s でした)|
| VaultUniqueId_s |Text |アラートに関連付けられている保護されるコンテナーの一意の識別子 |
| SourceSystem |Text |現在のデータのソース システム (Azure) |
| ResourceId |Text |データの収集対象のリソースの一意の識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceProvider |Text |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |Text |データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="backupitem"></a>BackupItem

次の表は、バックアップ項目に関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |Text |イベントの名前。 常に AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |バックアップ項目の一意の識別子 |
| BackupItemId_s |Text |バックアップ項目の識別子 (このフィールドは v1 スキーマ専用です) |
| BackupItemName_s |Text |バックアップ項目の名前 |
| BackupItemFriendlyName_s |Text |バックアップ項目のフレンドリ名 |
| BackupItemType_s |テキスト |バックアップ項目の種類 (例: VM、FileFolder) |
| BackupItemProtectionState_s |Text |バックアップ項目の保護状態 |
| BackupItemAppVersion_s |Text |バックアップ項目のアプリケーションのバージョン |
| ProtectionState_s |Text |バックアップ項目の現在の保護状態 (例: 保護済み、保護停止) |
| ProtectionGroupName_s |Text | 該当する場合、SC DPM、および MABS で、バックアップ項目が保護されている保護グループの名前|
| SecondaryBackupProtectionState_s |Text |バックアップ項目の二次的な保護が有効になっているかどうか|
| SchemaVersion_s |Text |スキーマのバージョン (例: **V2**) |
| State_s |Text |バックアップ項目オブジェクトの状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |Text |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このバックアップ項目が属する FileFolder) |
| OperationName |Text |操作の名前 (例: BackupItem) |
| Category |Text |Azure Monitor ログにプッシュされた診断データのカテゴリ。 常に AzureBackupReport |
| Resource |Text |データの収集対象のリソース (例: Recovery Services コンテナー名) |
| SourceSystem |Text |現在のデータのソース システム (Azure) |
| ResourceId |Text |収集されるデータのリソース ID (例: Recovery Services コンテナーのリソース ID) |
| SubscriptionId |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のリソース グループ |
| ResourceGroup |Text |収集されるデータのリソース (例: Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |Text |収集されるデータのリソース プロバイダー (例: Microsoft.RecoveryServices) |
| ResourceType |Text |収集されるデータのリソースの種類 (例: コンテナー) |

### <a name="backupitemassociation"></a>BackupItemAssociation

次の表は、バックアップ項目 アソシエーションとさまざまなエンティティの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |Text |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |  
| BackupItemUniqueId_s |Text |バックアップ項目の一意の ID |
| SchemaVersion_s |Text |このフィールドは、スキーマの現在のバージョン (**V2**) を表します |
| State_s |Text |バックアップ項目アソシエーション オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |Text |バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| BackupItemSourceSize_s |Text | バックアップ項目のフロント エンドのサイズ |
| BackupManagementServerUniqueId_s |Text | 該当する場合、バックアップ項目の保護に使用されるバックアップ管理サーバーを一意に識別するフィールド |
| Category |Text |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| OperationName |Text |このフィールドは現在の操作の名前 (BackupItemAssociation) を表します。 |
| Resource |Text |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedContainerUniqueId_s |Text |バックアップ項目に関連付けられている保護されるサーバーの一意の識別子 (V1 では ProtectedServerUniqueId_s でした) |
| VaultUniqueId_s |Text |バックアップ項目を含むコンテナーの一意の識別子 |
| SourceSystem |Text |現在のデータのソース システム (Azure) |
| ResourceId |Text |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のリソース グループ |
| ResourceGroup |Text |収集されるデータのリソース (例: Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |Text |収集されるデータのリソース プロバイダー (例: Microsoft.RecoveryServices) |
| ResourceType |Text |収集されるデータのリソースの種類 (例: コンテナー) |

### <a name="backupmanagementserver"></a>BackupManagementServer

次の表は、バックアップ項目 アソシエーションとさまざまなエンティティの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |バックアップ管理サーバーの名前        |
|AzureBackupAgentVersion_s     |Text         |バックアップ管理サーバーの Azure Backup エージェントのバージョン          |
|BackupManagementServerVersion_s     |Text         |バックアップ管理サーバーのバージョン|
|BackupManagementServerOSVersion_s    |Text            |バックアップ管理サーバーの OS のバージョン|
|BackupManagementServerType_s     |Text         |バックアップ管理サーバーの種類 (MABS、SC DPM)|
|BackupManagementServerUniqueId_s     |Text         |バックアップ管理サーバーを一意に識別するフィールド       |
| SourceSystem |Text |現在のデータのソース システム (Azure) |
| ResourceId |Text |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のリソース グループ |
| ResourceGroup |Text |収集されるデータのリソース (例: Recovery Services コンテナー) のリソース グループ |
| ResourceProvider |Text |収集されるデータのリソース プロバイダー (例: Microsoft.RecoveryServices) |
| ResourceType |Text |収集されるデータのリソースの種類 (例: コンテナー) |

### <a name="job"></a>ジョブ

次の表は、ジョブに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |Text |イベントの名前。 常に AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |バックアップ項目の一意の識別子 |
| SchemaVersion_s |Text |スキーマのバージョン (例: **V2**) |
| State_s |Text |ジョブ オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |Text |バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |Text |このフィールドは現在の操作の名前 (Job) を表します。 |
| Category |Text |このフィールドは Azure Monitor ログにプッシュされた診断データのカテゴリ (AzureBackupReport) を表します |
| Resource |Text |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |Text |ジョブに関連付けられている保護されるサーバーの一意の識別子 |
| ProtectedContainerUniqueId_s |Text | ジョブが実行される保護されたコンテナーを識別する、一意の ID |
| VaultUniqueId_s |Text |保護されるコンテナーの一意の識別子 |
| JobOperation_s |Text |ジョブを実行する対象の操作 (例: バックアップ、復元、バックアップの構成) |
| JobStatus_s |Text |完了したジョブの状態 (例: 完了、失敗) |
| JobFailureCode_s |Text |ジョブ エラーが発生したことによるエラー コードの文字列 |
| JobStartDateTime_s |日付/時刻 |ジョブの実行を開始した日付と時刻 |
| BackupStorageDestination_s |Text |バックアップ ストレージの保存先 (例: クラウド、ディスク)  |
| AdHocOrScheduledJob_s |Text | ジョブがアドホックかスケジュール済みかを指定するフィールド |
| JobDurationInSecs_s | Number |合計ジョブ期間 (秒単位) |
| DataTransferredInMB_s | Number |このジョブで転送されたデータ (MB)|
| JobUniqueId_g |Text |ジョブを識別する一意の ID |
| RecoveryJobDestination_s |Text | 回復ジョブの宛先 (データが回復される場所) |
| RecoveryJobRPDateTime_s |DateTime | 回復する復旧ポイントが作成された日付、時刻 |
| RecoveryJobRPLocation_s |Text | 回復する復旧ポイントが作成された場所|
| SourceSystem |Text |現在のデータのソース システム (Azure) |
| ResourceId |Text |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID|
| SubscriptionId |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceProvider |Text |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |Text |データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="policy"></a>ポリシー

次の表は、ポリシーに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 適用可能なバージョン | 説明 |
| --- | --- | --- | --- |
| EventName_s |Text ||このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |Text ||このフィールドは、スキーマの現在のバージョン (**V2**) を表します |
| State_s |Text ||ポリシー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |Text ||バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |Text ||このフィールドは現在の操作の名前 (Policy) を表します。 |
| Category |Text ||このフィールドは Azure Monitor ログにプッシュされた診断データのカテゴリ (AzureBackupReport) を表します |
| Resource |Text ||これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| PolicyUniqueId_g |Text ||ポリシーを識別する一意の ID |
| PolicyName_s |Text ||定義されたポリシーの名前 |
| BackupFrequency_s |Text ||バックアップが実行される頻度 (例: 毎日、毎週) |
| BackupTimes_s |Text ||バックアップがスケジュールされている日付と時刻 |
| BackupDaysOfTheWeek_s |Text ||バックアップがスケジュールされている曜日 |
| RetentionDuration_s |整数 ||構成されたバックアップに使用されるリテンション期間 |
| DailyRetentionDuration_s |整数 ||構成されたバックアップに使用される合計リテンション期間 (日数) |
| DailyRetentionTimes_s |Text ||毎日のリテンション期間が構成された日付と時刻 |
| WeeklyRetentionDuration_s |10 進数 ||構成されたバックアップの毎週の合計リテンション期間 (週単位) |
| WeeklyRetentionTimes_s |Text ||毎週のリテンション期間が構成される日付と時刻 |
| WeeklyRetentionDaysOfTheWeek_s |Text ||毎週のリテンション期間に選択された曜日 |
| MonthlyRetentionDuration_s |10 進数 ||構成されたバックアップに使用される合計のリテンション期間 (月単位) |
| MonthlyRetentionTimes_s |Text ||毎月のリテンション期間が構成される日付と時刻 |
| MonthlyRetentionFormat_s |Text ||毎月のリテンション期間に使用する構成の種類 (例: 日単位の毎日、週単位の毎週) |
| MonthlyRetentionDaysOfTheWeek_s |Text ||毎月のリテンション期間に選択された曜日 |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||毎月のリテンション期間が構成されたときの月の週 (例: 最初、最後など) |
| YearlyRetentionDuration_s |10 進数 ||構成されたバックアップに使用される合計リテンション期間 (年単位) |
| YearlyRetentionTimes_s |Text ||毎年のリテンション期間が構成される日付と時刻 |
| YearlyRetentionMonthsOfTheYear_s |Text ||毎年のリテンション期間に選択された月 |
| YearlyRetentionFormat_s |Text ||毎年のリテンション期間に使用する構成の種類 (例: 日単位の毎日、週単位の毎週) | |
| YearlyRetentionDaysOfTheMonth_s |Text ||毎年のリテンション期間に選択された月の日数 |
| SynchronisationFrequencyPerDay_s |整数 |v2|SC DPM および MABS でファイルのバックアップが 1 日に同期される回数 |
| DiffBackupFormat_s |Text |v2|Azure VM バックアップの SQL の差分バックアップの形式 |
| DiffBackupTime_s |Time |v2|Azure VM バックアップの SQL の差分バックアップの時刻|
| DiffBackupRetentionDuration_s |10 進数 |v2|Azure VM バックアップの SQL の差分バックアップのリテンション期間|
| LogBackupFrequency_s |10 進数 |v2|SQL のログ バックアップの頻度|
| LogBackupRetentionDuration_s |10 進数 |v2|Azure VM バックアップの SQL のログ バックアップのリテンション期間|
| DiffBackupDaysofTheWeek_s |Text |v2|Azure VM バックアップの SQL の差分バックアップの曜日|
| SourceSystem |Text ||現在のデータのソース システム (Azure) |
| ResourceId |Text ||収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |Text ||データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |Text ||データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceProvider |Text ||データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |Text ||データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="policyassociation"></a>PolicyAssociation

次の表は、ポリシー アソシエーションとさまざまなエンティティの詳細を示しています。

| フィールド | データ型 | 適用可能なバージョン | 説明 |
| --- | --- | --- | --- |
| EventName_s |Text ||このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |Text ||このフィールドは、スキーマの現在のバージョン (**V2**) を表します |
| State_s |Text ||ポリシー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |Text ||バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |Text ||このフィールドは現在の操作の名前 (PolicyAssociation) を表します。 |
| Category |Text ||このフィールドは Azure Monitor ログにプッシュされた診断データのカテゴリ (AzureBackupReport) を表します |
| Resource |Text ||これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| PolicyUniqueId_g |Text ||ポリシーを識別する一意の ID |
| VaultUniqueId_s |Text ||ポリシーが属しているコンテナーの一意の ID |
| BackupManagementServerUniqueId_s |Text |v2 |該当する場合、バックアップ項目の保護に使用されるバックアップ管理サーバーを一意に識別するフィールド        |
| SourceSystem |Text ||現在のデータのソース システム (Azure) |
| ResourceId |Text ||収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |Text ||データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |Text ||データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceProvider |Text ||データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |Text ||データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="protected-container"></a>保護されたコンテナー

次の表は、保護されたコンテナーに関する基本フィールドを示しています。 (v1 では ProtectedServer でした)

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | 保護されたコンテナーを一意に識別するフィールド |
| ProtectedContainerOSType_s |Text |保護されたコンテナーの OS の種類 |
| ProtectedContainerOSVersion_s |Text |保護されたコンテナーの OS のバージョン |
| AgentVersion_s |Text |エージェント バックアップまたは保護エージェント (SC DPM および MABS の場合) のバージョン番号 |
| BackupManagementType_s |Text |バックアップを実行するためのプロバイダーの種類。 たとえば、IaaSVM や FileFolder |
| EntityState_s |Text |保護されるサーバー オブジェクトの現在の状態。 たとえば、アクティブ、削除済み |
| ProtectedContainerFriendlyName_s |Text |保護されるサーバーのフレンドリ名 |
| ProtectedContainerName_s |Text |保護されたコンテナーの名前 |
| ProtectedContainerWorkloadType_s |Text |保護されたコンテナーのバックアップの種類。 たとえば、IaaSVMContainer |
| ProtectedContainerLocation_s |Text |保護されたコンテナーの配置場所 (オンプレミスまたは Azure 内) |
| ProtectedContainerType_s |Text |保護されたコンテナーがサーバーかコンテナーか |
| ProtectedContainerProtectionState_s’  |Text |保護されたコンテナーの保護の状態 |

### <a name="storage"></a>Storage

次の表は、ストレージに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| CloudStorageInBytes_s |10 進数 |最新の値に基づいて計算された、バックアップによって使用されるクラウド バックアップ ストレージ (このフィールドは v1 スキーマ専用です)|
| ProtectedInstances_s |10 進数 |最新の値に基づいて計算された、課金されるフロントエンド ストレージの計算に使用する保護されるインスタンスの数 |
| EventName_s |Text |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |Text |このフィールドは、スキーマの現在のバージョン (**V2**) を表します |
| State_s |Text |ストレージ オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |Text |バックアップ ジョブを行うサーバーのプロバイダーの種類 (例: IaaSVM、FileFolder) |
| OperationName |Text |このフィールドは現在の操作の名前 (Storage) を表します。 |
| Category |Text |このフィールドは Azure Monitor ログにプッシュされた診断データのカテゴリ (AzureBackupReport) を表します |
| Resource |Text |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |Text |ストレージを計算するために使用する保護されるサーバーの一意の ID |
| VaultUniqueId_s |Text |ストレージを計算するために使用するコンテナーの一意の ID |
| SourceSystem |Text |現在のデータのソース システム (Azure) |
| ResourceId |Text |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceProvider |Text |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |Text |データの収集対象のリソースの種類。 たとえば、コンテナー |
| StorageUniqueId_s |Text |ストレージ エントリの識別に使用される一意 ID |
| StorageType_s |Text |ストレージの種類 (クラウド、ボリューム、ディスクなど) |
| StorageName_s |Text |ストレージ エンティティの名前 (E:\ など) |
| StorageTotalSizeInGBs_s |Text |ストレージ エンティティによって消費されたストレージの合計サイズ (GB 単位)|

### <a name="storageassociation"></a>StorageAssociation

次の表は、ストレージを他のエンティティに接続する、基本的なストレージ関連のフィールドを示しています。

| フィールド | データ型 | 説明 |
| --- | --- |  --- |
| StorageUniqueId_s |Text |ストレージ エントリの識別に使用される一意 ID |
| SchemaVersion_s |Text |このフィールドは、スキーマの現在のバージョン (**V2**) を表します |
| BackupItemUniqueId_s |Text |ストレージ エントリに関連するバックアップ項目の識別に使用される一意 ID |
| BackupManagementServerUniqueId_s |Text |ストレージ エントリに関連するバックアップ管理サーバーの識別に使用される一意 ID|
| VaultUniqueId_s |Text |ストレージ エントリに関連するコンテナーの識別に使用される一意 ID|
| StorageConsumedInMBs_s |Number|対応するストレージ内の対応するバックアップ項目によって使用されるストレージのサイズ |
| StorageAllocatedInMBs_s |Number |種類がディスクの対応するストレージ内の対応するバックアップ項目によって割り当てられたストレージのサイズ|

### <a name="vault"></a>コンテナー

次の表は、コンテナーに関連するフィールドの詳細を示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| EventName_s |Text |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |Text |このフィールドは、スキーマの現在のバージョン (**V2**) を表します |
| State_s |Text |コンテナー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| OperationName |Text |このフィールドは現在の操作の名前 (Vault) を表します。 |
| Category |Text |このフィールドは Azure Monitor ログにプッシュされた診断データのカテゴリ (AzureBackupReport) を表します |
| Resource |Text |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| VaultUniqueId_s |Text |コンテナーの一意の ID |
| VaultName_s |Text |コンテナーの名前 |
| AzureDataCenter_s |Text |コンテナーが配置されるデータ センター |
| StorageReplicationType_s |Text |コンテナーのストレージ レプリケーションの種類 (例: GeoRedundant) |
| SourceSystem |Text |現在のデータのソース システム (Azure) |
| ResourceId |Text |収集されるデータのリソース識別子。 たとえば、Recovery Services コンテナーのリソース ID |
| SubscriptionId |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceGroup |Text |データの収集対象のリソース (例: Recovery Services コンテナー) のサブスクリプション識別子 |
| ResourceProvider |Text |データの収集対象のリソース プロバイダー。 たとえば、Microsoft.RecoveryServices |
| ResourceType |Text |データの収集対象のリソースの種類。 たとえば、コンテナー |

### <a name="backup-management-server"></a>バックアップ管理サーバー

次の表は、バックアップ管理サーバーに関する基本フィールドを示しています。

|フィールド  |データ型  | 説明  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |バックアップ管理サーバーの名前        |
|AzureBackupAgentVersion_s     |Text         |バックアップ管理サーバーの Azure Backup エージェントのバージョン          |
|BackupManagmentServerVersion_s     |Text         |バックアップ管理サーバーのバージョン|
|BackupManagmentServerOSVersion_s     |Text            |バックアップ管理サーバーの OS のバージョン|
|BackupManagementServerType_s     |Text         |バックアップ管理サーバーの種類 (MABS、SC DPM)|
|BackupManagmentServerUniqueId_s     |Text         |バックアップ管理サーバーを一意に識別するフィールド       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

次の表は、ボリュームが関連付けられているワークロードを示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| StorageUniqueId_s |Text |ストレージ エントリの識別に使用される一意 ID |
| BackupItemType_s |Text |このボリュームが優先ストレージとなるワークロード|

### <a name="protectedinstance"></a>ProtectedInstance

次の表は、保護されたインスタンスに関連する基本フィールドを示しています。

| フィールド | データ型 |適用可能なバージョン | 説明 |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|DPM、MABS を使用してバックアップされた VM のバックアップ項目の識別に使用される一意 ID|
| ProtectedContainerUniqueId_s |Text |v2|DPM、MABS を使用してバックアップされた VM 以外のすべてに対して保護されたコンテナーの識別に使用される一意 ID|
| ProtectedInstanceCount_s |Text |v2|関連付けられているバックアップ項目または該当の日付/時刻で保護されたコンテナーの、保護されたインスタンスの数|

### <a name="recoverypoint"></a>RecoveryPoint

次の表は、復旧ポイントに関連する基本フィールドを示しています。

| フィールド | データ型 | 説明 |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |DPM、MABS を使用してバックアップされた VM のバックアップ項目の識別に使用される一意 ID|
| OldestRecoveryPointTime_s |Text |バックアップ項目の最も古い復旧ポイントの日時|
| OldestRecoveryPointLocation_s |Text |バックアップ項目の最も古い復旧ポイントの場所|
| LatestRecoveryPointTime_s |Text |バックアップ項目の最新の復旧ポイントの日時|
| LatestRecoveryPointLocation_s |Text |バックアップ項目の最新の復旧ポイントの場所|

## <a name="next-steps"></a>次の手順

データ モデルを確認した後、Azure Monitor ログで[カスタム クエリの作成](../azure-monitor/learn/tutorial-logs-dashboards.md)を開始して、独自のダッシュボードを構築することができます。
