---
title: "Azure Backup の Log Analytics データ モデル"
description: "この記事では、Azure Backup データに使用する Log Analytics データ モデルの詳細について説明します。"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: dfd5c73d-0d34-4d48-959e-1936986f9fc0
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041a8835a1dd185739b23d4073fd5811bb4490b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure Backup データの Log Analytics データ モデル
この記事では、Log Analytics にレポート データをプッシュするのに使用するデータ モデルについて説明します。 このデータ モデルを使用すると、カスタム クエリ、ダッシュボードを作成し、OMS で活用することができます。 

## <a name="using-azure-backup-data-model"></a>Azure Backup データ モデルを使用する
データ モデルの一部として提供される次のフィールドを使用して、ビジュアル、カスタム クエリ、ダッシュボードを要件に合わせて作成できます。

### <a name="alert"></a>アラート:
次の表は、アラートに関連するフィールドの詳細を示しています。

| フィールド | データ型 | Description |
| --- | --- | --- |
| AlertUniqueId_s |テキスト |生成されたアラートの一意の ID |
| AlertType_s |テキスト |生成されたアラートの種類 (例: バックアップ) |
| AlertStatus_s |テキスト |アラートの状態 (例: アクティブ) |
| AlertOccurenceDateTime_s |日付/時刻 |アラートが作成された日付と時刻 |
| AlertSeverity_s |テキスト |アラートの重大度 (例: 重大) |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| BackupItemUniqueId_s |テキスト |このアラートが属しているバックアップ項目の一意の ID |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |アラート オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このアラートが属する FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (Alert) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |テキスト |このアラートが属している保護対象の一意の ID |
| VaultUniqueId_s |テキスト |このアラートが属している保護対象の一意の ID |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |このフィールドはデータの収集対象のリソース ID を表し、Recovery Services コンテナーのリソース ID を示します。 |
| SubscriptionId |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のサブスクリプション ID を表します。 |
| ResourceGroup |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のリソース グループを表します。 |
| ResourceProvider |テキスト |このフィールドはデータの収集対象のリソース プロバイダー (Microsoft.RecoveryServices) を表します。 |
| ResourceType |テキスト |このフィールドはデータの収集対象のリソースの種類 (コンテナー) を表します。 |

### <a name="backupitem"></a>BackupItem
次の表は、バックアップ項目に関連するフィールドの詳細を示しています。

| フィールド | データ型 | Description |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |  
| BackupItemUniqueId_s |テキスト |バックアップ項目の一意の ID |
| BackupItemId_s |テキスト |バックアップ項目の ID |
| BackupItemName_s |テキスト |バックアップ項目の名前 |
| BackupItemFriendlyName_s |テキスト |バックアップ項目のフレンドリ名 |
| BackupItemType_s |テキスト |バックアップ項目の種類 (例: VM、FileFolder) |
| ProtectedServerName_s |テキスト |バックアップ項目が属している保護されるサーバーの名前 |
| ProtectionState_s |テキスト |バックアップ項目の現在の保護状態 (例: 保護済み、保護停止) |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |バックアップ項目オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このバックアップ項目が属する FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (BackupItem) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |このフィールドはデータの収集対象のリソース ID を表し、Recovery Services コンテナーのリソース ID を示します。 |
| SubscriptionId |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のサブスクリプション ID を表します。 |
| ResourceGroup |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のリソース グループを表します。 |
| ResourceProvider |テキスト |このフィールドはデータの収集対象のリソース プロバイダー (Microsoft.RecoveryServices) を表します。 |
| ResourceType |テキスト |このフィールドはデータの収集対象のリソースの種類 (コンテナー) を表します。 |

### <a name="backupitemassociation"></a>BackupItemAssociation
次の表は、バックアップ項目 アソシエーションとさまざまなエンティティの詳細を示しています。

| フィールド | データ型 | Description |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |  
| BackupItemUniqueId_s |テキスト |バックアップ項目の一意の ID |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |バックアップ項目アソシエーション オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このバックアップ項目が属する FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (BackupItemAssociation) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| PolicyUniqueId_g |テキスト |バックアップ項目が関連付けられているポリシーを識別する一意の ID |
| ProtectedServerUniqueId_s |テキスト |バックアップ項目が属している保護されるサーバーの一意の ID |
| VaultUniqueId_s |テキスト |バックアップ項目が属しているコンテナーの一意の ID |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |このフィールドはデータの収集対象のリソース ID を表し、Recovery Services コンテナーのリソース ID を示します。 |
| SubscriptionId |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のサブスクリプション ID を表します。 |
| ResourceGroup |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のリソース グループを表します。 |
| ResourceProvider |テキスト |このフィールドはデータの収集対象のリソース プロバイダー (Microsoft.RecoveryServices) を表します。 |
| ResourceType |テキスト |このフィールドはデータの収集対象のリソースの種類 (コンテナー) を表します。 |

### <a name="job"></a>ジョブ
次の表は、ジョブに関連するフィールドの詳細を示しています。

| フィールド | データ型 | Description |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| BackupItemUniqueId_s |テキスト |このジョブが属しているバックアップ項目の一意の ID |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |ジョブ オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このジョブが属する FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (Job) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |テキスト |このジョブが属している保護対象の一意の ID |
| VaultUniqueId_s |テキスト |このジョブが属している保護対象の一意の ID |
| JobOperation_s |テキスト |ジョブを実行する対象の操作 (例: バックアップ、復元、バックアップの構成) |
| JobStatus_s |テキスト |完了したジョブの状態 (例: 完了、失敗) |
| JobFailureCode_s |テキスト |ジョブ エラーが発生したことによるエラー コードの文字列 |
| JobStartDateTime_s |日付/時刻 |ジョブの実行を開始した日付と時刻 |
| BackupStorageDestination_s |テキスト |バックアップ ストレージの保存先 (例: クラウド、ディスク)  |
| JobDurationInSecs_s | Number |合計ジョブ期間 (秒単位) |
| DataTransferredInMB_s | Number |このジョブで転送されたデータ (MB)|
| JobUniqueId_g |テキスト |ジョブを識別する一意の ID |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |このフィールドはデータの収集対象のリソース ID を表し、Recovery Services コンテナーのリソース ID を示します。 |
| SubscriptionId |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のサブスクリプション ID を表します。 |
| ResourceGroup |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のリソース グループを表します。 |
| ResourceProvider |テキスト |このフィールドはデータの収集対象のリソース プロバイダー (Microsoft.RecoveryServices) を表します。 |
| ResourceType |テキスト |このフィールドはデータの収集対象のリソースの種類 (コンテナー) を表します。 |

### <a name="policy"></a>[ポリシー]
次の表は、ポリシーに関連するフィールドの詳細を示しています。

| フィールド | データ型 | Description |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |ポリシー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このポリシーが属する FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (Policy) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| PolicyUniqueId_g |テキスト |ポリシーを識別する一意の ID |
| PolicyName_s |テキスト |定義されたポリシーの名前 |
| BackupFrequency_s |テキスト |バックアップが実行される頻度 (例: 毎日、毎週) |
| BackupTimes_s |テキスト |バックアップがスケジュールされている日付と時刻 |
| BackupDaysOfTheWeek_s |テキスト |バックアップがスケジュールされている曜日 |
| RetentionDuration_s |整数 |構成されたバックアップに使用されるリテンション期間 |
| DailyRetentionDuration_s |整数 |構成されたバックアップに使用される合計リテンション期間 (日数) |
| DailyRetentionTimes_s |テキスト |毎日のリテンション期間が構成された日付と時刻 |
| WeeklyRetentionDuration_s |10 進数 |構成されたバックアップの毎週の合計リテンション期間 (週単位) |
| WeeklyRetentionTimes_s |テキスト |毎週のリテンション期間が構成される日付と時刻 |
| WeeklyRetentionDaysOfTheWeek_s |テキスト |毎週のリテンション期間に選択された曜日 |
| MonthlyRetentionDuration_s |10 進数 |構成されたバックアップに使用される合計のリテンション期間 (月単位) |
| MonthlyRetentionTimes_s |テキスト |毎月のリテンション期間が構成される日付と時刻 |
| MonthlyRetentionFormat_s |テキスト |毎月のリテンション期間に使用する構成の種類 (例: 日単位の毎日、週単位の毎週) |
| MonthlyRetentionDaysOfTheWeek_s |テキスト |毎月のリテンション期間に選択された曜日 |
| MonthlyRetentionWeeksOfTheMonth_s |テキスト |毎月のリテンション期間が構成されたときの月の週 (例: 最初、最後など) |
| YearlyRetentionDuration_s |10 進数 |構成されたバックアップに使用される合計リテンション期間 (年単位) |
| YearlyRetentionTimes_s |テキスト |毎年のリテンション期間が構成される日付と時刻 |
| YearlyRetentionMonthsOfTheYear_s |テキスト |毎年のリテンション期間に選択された月 |
| YearlyRetentionFormat_s |テキスト |毎年のリテンション期間に使用する構成の種類 (例: 日単位の毎日、週単位の毎週) |
| YearlyRetentionDaysOfTheMonth_s |テキスト |毎年のリテンション期間に選択された月の日数 |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |このフィールドはデータの収集対象のリソース ID を表し、Recovery Services コンテナーのリソース ID を示します。 |
| SubscriptionId |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のサブスクリプション ID を表します。 |
| ResourceGroup |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のリソース グループを表します。 |
| ResourceProvider |テキスト |このフィールドはデータの収集対象のリソース プロバイダー (Microsoft.RecoveryServices) を表します。 |
| ResourceType |テキスト |このフィールドはデータの収集対象のリソースの種類 (コンテナー) を表します。 |

### <a name="policyassociation"></a>PolicyAssociation
次の表は、ポリシー アソシエーションとさまざまなエンティティの詳細を示しています。

| フィールド | データ型 | Description |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |ポリシー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このポリシーが属する FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (PolicyAssociation) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| PolicyUniqueId_g |テキスト |ポリシーを識別する一意の ID |
| VaultUniqueId_s |テキスト |ポリシーが属しているコンテナーの一意の ID |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |このフィールドはデータの収集対象のリソース ID を表し、Recovery Services コンテナーのリソース ID を示します。 |
| SubscriptionId |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のサブスクリプション ID を表します。 |
| ResourceGroup |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のリソース グループを表します。 |
| ResourceProvider |テキスト |このフィールドはデータの収集対象のリソース プロバイダー (Microsoft.RecoveryServices) を表します。 |
| ResourceType |テキスト |このフィールドはデータの収集対象のリソースの種類 (コンテナー) を表します。 |

### <a name="protectedserver"></a>ProtectedServer
次の表は、保護されるサーバーに関連するフィールドの詳細を示しています。

| フィールド | データ型 | Description |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| ProtectedServerName_s |テキスト |保護されるサーバーの名前 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |保護されるサーバー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、この保護されるサーバーが属する FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (ProtectedServer) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |テキスト |保護されるサーバーの一意の ID |
| RegisteredContainerId_s |テキスト |バックアップ用に登録されたコンテナーの ID |
| ProtectedServerType_s |テキスト |バックアップされた保護されるサーバーの種類 (例: Windows) |
| ProtectedServerFriendlyName_s |テキスト |保護されるサーバーのフレンドリ名 |
| AzureBackupAgentVersion_s |テキスト |エージェント バックアップ バージョンのバージョン番号 |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |このフィールドはデータの収集対象のリソース ID を表し、Recovery Services コンテナーのリソース ID を示します。 |
| SubscriptionId |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のサブスクリプション ID を表します。 |
| ResourceGroup |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のリソース グループを表します。 |
| ResourceProvider |テキスト |このフィールドはデータの収集対象のリソース プロバイダー (Microsoft.RecoveryServices) を表します。 |
| ResourceType |テキスト |このフィールドはデータの収集対象のリソースの種類 (コンテナー) を表します。 |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
次の表は、保護されるサーバー アソシエーションとさまざまなエンティティの詳細を示しています。

| フィールド | データ型 | Description |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |保護されるサーバー アソシエーション オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、この保護されるサーバーが属する FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (ProtectedServerAssociation) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |テキスト |保護されるサーバーの一意の ID |
| VaultUniqueId_s |テキスト |この保護されるサーバーが属しているコンテナーの一意の ID |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |このフィールドはデータの収集対象のリソース ID を表し、Recovery Services コンテナーのリソース ID を示します。 |
| SubscriptionId |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のサブスクリプション ID を表します。 |
| ResourceGroup |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のリソース グループを表します。 |
| ResourceProvider |テキスト |このフィールドはデータの収集対象のリソース プロバイダー (Microsoft.RecoveryServices) を表します。 |
| ResourceType |テキスト |このフィールドはデータの収集対象のリソースの種類 (コンテナー) を表します。 |

### <a name="storage"></a>ストレージ
次の表は、ストレージに関連するフィールドの詳細を示しています。

| フィールド | データ型 | Description |
| --- | --- | --- |
| CloudStorageInBytes_s |10 進数 |最新の値に基づいて計算された、バックアップによって使用されるクラウド バックアップ ストレージ |
| ProtectedInstances_s |10 進数 |最新の値に基づいて計算された、課金されるフロントエンド ストレージの計算に使用する保護されるインスタンスの数 |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |ストレージ オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| BackupManagementType_s |テキスト |バックアップを実行するためのプロバイダーの種類 (例: IaaSVM、このストレージが属する FileFolder) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (Storage) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| ProtectedServerUniqueId_s |テキスト |ストレージを計算するために使用する保護されるサーバーの一意の ID |
| VaultUniqueId_s |テキスト |ストレージを計算するために使用するコンテナーの一意の ID |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |このフィールドはデータの収集対象のリソース ID を表し、Recovery Services コンテナーのリソース ID を示します。 |
| SubscriptionId |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のサブスクリプション ID を表します。 |
| ResourceGroup |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のリソース グループを表します。 |
| ResourceProvider |テキスト |このフィールドはデータの収集対象のリソース プロバイダー (Microsoft.RecoveryServices) を表します。 |
| ResourceType |テキスト |このフィールドはデータの収集対象のリソースの種類 (コンテナー) を表します。 |

### <a name="vault"></a>コンテナー
次の表は、コンテナーに関連するフィールドの詳細を示しています。

| フィールド | データ型 | Description |
| --- | --- | --- |
| EventName_s |テキスト |このフィールドはこのイベントの名前 (常に AzureBackupCentralReport) を表します。 |
| SchemaVersion_s |テキスト |このフィールドは、スキーマの現在のバージョン (**V1**) を表します。 |
| State_s |テキスト |コンテナー オブジェクトの現在の状態 (例: アクティブ、削除済み) |
| OperationName |テキスト |このフィールドは現在の操作の名前 (Vault) を表します。 |
| カテゴリ |テキスト |このフィールドは Log Analytics にプッシュされた診断データのカテゴリ (AzureBackupReport) を表します。 |
| リソース |テキスト |これはデータの収集対象のリソースであり、Recovery Services コンテナー名を示します。 |
| VaultUniqueId_s |テキスト |コンテナーの一意の ID |
| VaultName_s |テキスト |コンテナーの名前 |
| AzureDataCenter_s |テキスト |コンテナーが配置されるデータ センター |
| StorageReplicationType_s |テキスト |コンテナーのストレージ レプリケーションの種類 (例: GeoRedundant) |
| SourceSystem |テキスト |現在のデータのソース システム (Azure) |
| ResourceId |テキスト |このフィールドはデータの収集対象のリソース ID を表し、Recovery Services コンテナーのリソース ID を示します。 |
| SubscriptionId |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のサブスクリプション ID を表します。 |
| ResourceGroup |テキスト |このフィールドはデータの収集対象のリソース (RS コンテナー) のリソース グループを表します。 |
| ResourceProvider |テキスト |このフィールドはデータの収集対象のリソース プロバイダー (Microsoft.RecoveryServices) を表します。 |
| ResourceType |テキスト |このフィールドはデータの収集対象のリソースの種類 (コンテナー) を表します。 |

## <a name="next-steps"></a>次のステップ
Azure Backup のレポートを作成するためのデータ モデルを確認したら、Log Analytics および OMSで[ダッシュボードの作成](../log-analytics/log-analytics-dashboards.md)を開始できます。
