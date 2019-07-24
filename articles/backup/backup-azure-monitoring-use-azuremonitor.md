---
title: 'Azure Backup: Azure Monitor での Azure Backup の監視'
description: Azure Monitor を使用して、Azure Backup ワークロードを監視し、カスタム アラートを作成する
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Azure Backup; アラート; 診断設定; アクション グループ
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 15bb64917fa58ba2d13c6f372640957508ab29c1
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494561"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Azure Monitor を使用した大規模な監視

[組み込みの監視とアラートに関する記事](backup-azure-monitoring-built-in-monitor.md)に、1 つの Recovery Services コンテナー内の監視機能とアラート機能の一覧が示されています。これらは追加の管理インフラストラクチャなしで提供されます。 ただし、組み込みサービスは次のシナリオでは制限されます。

- サブスクリプションにまたがる複数の RS コンテナーのデータの監視
- 電子メールが優先通知チャネルではない場合
- ユーザーがアラートを受け取りたいシナリオが多い場合
- Azure で System Center DPM (SC-DPM) などオンプレミス コンポーネントの情報を表示しようとする場合 (ポータルの[[バックアップ ジョブ]](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) または [[バックアップ アラート]](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) には表示されない)

## <a name="using-log-analytics-workspace"></a>Log Analytics ワークスペースを使用する

> [!NOTE]
> Azure VM バックアップ、MAB エージェント、System Center DPM (SC-DPM)、Azure VM の SQL バックアップのデータは、診断設定によって Log Analytics ワークスペースに送られます。 Azure ファイル共有のバックアップ、Microsoft Azure Backup Server (MABS) のサポートはまもなく提供される予定です。

大規模な監視のためには、**診断設定** (複数の Azure Resource Manager リソースから別のリソースへデータを送信) および **Log Analytics** (LA、カスタム アラートを生成し、アクション グループを使用して他の通知チャネルを定義できる) という 2 つの Azure サービスの機能を活用します。 以下のセクションでは、LA を使用して Azure Backup の大規模な監視を行う方法を詳しく説明します。

### <a name="configuring-diagnostic-settings"></a>診断設定を構成する

Azure Recovery Services コンテナーなどの Azure Resource Manager リソースは、スケジュールされた操作およびユーザーがトリガーした操作に関して得られるすべての情報を診断データとして記録します。 監視セクションで [診断設定] をクリックし、RS コンテナーの診断データの送信先を指定します。

![送信先が LA になっている RS コンテナーの診断設定](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

送信先として別のサブスクリプションの LA ワークスペースを選択できます。 *複数の RS コンテナーに対して同じ LA ワークスペースを選択することで、サブスクリプションにまたがるコンテナーを一箇所で監視できます。* ログとして AzureBackupReport を選択して、すべての Azure Backup 関連情報を LA ワークスペースに送ります。

> [!IMPORTANT]
> 構成を完了した後で、最初のデータ プッシュが完了するまで 24 時間待つ必要があります。 その後は、[頻度に関するセクション](#diagnostic-data-update-frequency)で説明しているようにすべてのイベントがプッシュされます。

### <a name="deploying-solution-to-log-analytics-workspace"></a>Log Analytics ワークスペースにソリューションをデプロイする

データが LA ワークスペースに入ったら、LA に [github テンプレートをデプロイ](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/)してデータを視覚化します。 同じリソース グループ、ワークスペース名、ワークスペースの場所を指定して、ワークスペースを正しく示していることを確認してから、このテンプレートをインストールします。

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Log Analytics (LA) を使用して Azure Backup データを表示する

テンプレートがデプロイされると、Azure Backup を監視するためのソリューションがワークスペースの概要の領域に表示されます。 次のようにして詳細を確認できます。

- [Azure Monitor] から [インサイト] セクションの [詳細] へ移動し、関連するワークスペースを選択します。
- [Log Analytics ワークスペース] で関連するワークスペースを選択し、[全般] セクションの [ワークスペースの概要] に移動します。

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

タイルをクリックすると、デザイナー テンプレートが開いて、Azure Backup の基本的な監視データのための一連のグラフが次のように表示されます。

#### <a name="all-backup-jobs"></a>すべてのバックアップ ジョブ

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>復元ジョブ

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Azure リソースのための組み込み Azure Backup アラート

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>オンプレミス リソースのための組み込み Azure Backup アラート

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>アクティブなデータソース

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>RS コンテナー クラウド ストレージ

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

上記のグラフはテンプレートで提供されており、自由にグラフを編集したり追加したりすることができます。

> [!IMPORTANT]
> テンプレートをデプロイすると、通常は読み取り専用ロックが作成されます。テンプレートを編集して保存するにはこれを削除する必要があります。 ロックの削除は、Log Analytics ワークスペースの [設定] セクションの [ロック] ウィンドウで行います。

### <a name="create-alerts-using-log-analytics"></a>Log Analytics を使用してアラートを作成する

Azure Monitor では、ユーザーが自分のアラートを LA ワークスペースに作成できます。ここでは、*leverage the Azure アクション グループを利用して優先通知メカニズムを選択*できます。 上記のいずれかのグラフをクリックすると、LA ワークスペースの [ログ] セクションが開きます。"***そこでクエリを編集して、それに対してアラートを作成できます***"。

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

上記のように [新しいアラート ルール] をクリックすると、Azure Monitor のアラート作成画面が開きます。

以下でわかるように、リソースが既に LA ワークスペースとしてマークされておりアクション グループの統合が提供されています。

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> このようなクエリの作成に関連した料金への影響は[ここ](https://azure.microsoft.com/pricing/details/monitor/)で説明されていることに注意してください。

#### <a name="alert-condition"></a>アラートの条件

重要な要素はアラートのトリガー条件です。 [条件] をクリックすると、以下に示すように Kusto クエリが [ログ] 画面に自動的に読み込まれます。これをシナリオに合わせて編集できます。 サンプルの Kusto クエリを[この後のセクション](#sample-kusto-queries)でいくつか示します。

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

必要であれば Kusto クエリを編集し、適切なしきい値 (アラートを発生させるタイミングを決定)、適切な期間 (クエリを実行する期間) および頻度を選択します。 次に例を示します。しきい値が 0 より大、期間が 5 分、頻度が 5 分ごとの場合は、「直近の 5 分間に 5 分ごとにクエリを実行して、結果の数が 0 より大きい場合には選択したアクション グループを介して通知する」という意味になります。

#### <a name="action-group-integration"></a>アクション グループの統合

アクション グループによって、ユーザーが使用できる通知チャネルが指定されます。 [アクション グループ] セクションの [新規作成] をクリックすると、使用可能な通知メカニズムの一覧が表示されます。

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

詳しくは、Azure Monitor ドキュメントの [LA ワークスペースのアラート](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)および[アクション グループ](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)に関するページをご覧ください。

このように、LA のみでアラートと監視のすべての要件を 満たすことも、LA を組み込み通知メカニズムを補助する方法として使用することもできます。

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

既定のグラフによって基本的なシナリオ用の Kusto クエリが提供されるため、それに基づいてアラートを作成できます。 それらを変更して、アラートを設定する対象のデータを取得することもできます。 ここではいくつかのサンプルの Kusto クエリを示しますが、これらを [ログ] ウィンドウに貼り付けて、クエリに対するアラートを作成できます。

#### <a name="all-successful-backup-jobs"></a>成功したすべてのバックアップ ジョブ

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>失敗したすべてのバックアップ ジョブ

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>成功したすべての Azure VM バックアップ ジョブ

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-sql-log-backup-jobs"></a>成功したすべての SQL ログ バックアップ ジョブ

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-mab-agent-backup-jobs"></a>成功したすべての MAB エージェント バックアップ ジョブ

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

### <a name="diagnostic-data-update-frequency"></a>診断データの更新頻度

コンテナーの診断データは少し遅れて LA ワークスペースに送られます。 各イベントが LA ワークスペースに届くのは "***RS コンテナーからプッシュされた 20-30 分後です。***"

- バックアップ サービスの組み込みアラート (すべてのソリューションに対する) は、作成されるとすぐにプッシュされます。 つまり、LA ワークスペースには通常 20-30 分遅れで表示されます。
- アドホック バックアップ ジョブと復元ジョブ (すべてのソリューションに対する) は、"***完了***" するとすぐにプッシュされます。
- すべてのソリューションのスケジュール済みバックアップ ジョブ (SQL バックアップ以外)は、"***完了***" するとすぐにプッシュされます。
- SQL バックアップでは、完了したすべてのスケジュール済みバックアプ ジョブについて 15 分ごとにログ バックアップを行うことができるため、ログも含め、情報が 6 時間ごとにバッチ処理されてプッシュされます。
- すべてのソリューションのバックアップ項目、ポリシー、復旧ポイント、ストレージなど、その他すべての情報は、**少なくとも 1 日 1 回**プッシュされます。
- ポリシーの変更や編集など、バックアップ構成を変更すると、関連するすべてのバックアップ情報のプッシュがトリガーされます。

## <a name="using-rs-vaults-activity-logs"></a>RS コンテナーのアクティビティ ログを使用する

アクティビティ ログを使用して、バックアップ成功などのイベントの通知を受け取ることもできます。

> [!CAUTION]
> **これは Azure VM バックアップにのみ適用されることに注意してください。** Azure Backup エージェント、Azure 内での SQL バックアップ、Azure Files など他のソリューションではこれは使用できません。

### <a name="sign-in-into-azure-portal"></a>Azure portal にサインインする

Azure portal にサインインし、関連する Azure Recovery Services コンテナーに進み、プロパティの [アクティビティ ログ] セクションをクリックします。

### <a name="identify-appropriate-log-and-create-alert"></a>適切なログを指定してアラートを作成する

成功したバックアップのアクティビティ ログを受信しているかどうかを確認するため、次の図に示すようにフィルターを適用します。 レコードを表示する期間を適宜変更します。

![Azure VM バックアップのアクティビティ ログ](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

[JSON] セグメントをクリックすると、より詳細な情報を取得して、それをコピーしてテキスト エディターに貼り付けることによって見ることができます。 コンテナーの詳細と、アクティビティ ログをトリガーした項目 (バックアップ項目) が表示されます。

[アクティビティ ログ アラートの追加] をクリックし、このようなすべてのログに対しアラートを生成します。

上に示されている [アクティビティ ログ アラートの追加] をクリックすると、[前述した](#create-alerts-using-log-analytics)アラート作成画面と似たアラート作成画面が開きます。

ここでは、リソースは RS コンテナーそのものです。したがって、アクティビティ ログを介して通知しようとするすべてのコンテナーについて同じ操作を繰り返す必要があります。 これはイベントベースのアラートであるため、しきい値、期間、頻度は条件に含めません。 関連するアクティビティ ログが生成されるとすぐに、アラートが発生します。

## <a name="recommendation"></a>推奨

***アクティビティ ログと LA ワークスペースから作成されるすべてのアラートは、Azure Monitor の左側にある [アラート] ウィンドウに表示されます。***

アクティビティ ログを介した通知を使用できる一方で、"***Azure Backup サービスでは、以下の理由から大規模な監視のためにはアクティビティ ログではなく LA を使用することを強く推奨します***"。

- **シナリオの制限:** Azure VM バックアップにしか適用できず、すべての RS コンテナーで繰り返す必要があります。
- **定義の一致:** スケジュール済みバックアップ アクティビティは、アクティビティ ログの最新定義と一致せず、[診断ログ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-are-azure-monitor-diagnostic-logs)に対応しています。 これにより、以下で説明するアクティビティ ログ チャネルを介したデータ ポンプの変更に伴って、予期しない影響が生じます。
- **アクティビティ ログ チャネルの問題:** Azure Backup からのアクティビティ ログを Recovery Services コンテナーで汲み上げる新しいモデルに移行しました。 残念ながら、Azure ソブリン クラウドでは、この移行によって、アクティビティ ログの生成に影響が生じています。 Azure ソブリン クラウドのユーザーが、アクティビティ ログからのアラートを Azure Monitor 経由で作成または構成した場合、それらのアラートはトリガーされません。 また、すべての Azure パブリック リージョンにおいて、ユーザーが Recovery Services のアクティビティ ログを Log Analytics ワークスペースに収集している場合 ([こちら](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)を参照)、それらのログも表示されません。

このように、Azure Backup で保護されるすべてのワークロードでは、大規模な監視とアラートでは Log Analytic ワークスペースを使用することを強くお勧めします。

## <a name="next-steps"></a>次の手順

- [Log Analytics データ モデル](backup-azure-log-analytics-data-model.md)について学習し、カスタム クエリを作成します。
