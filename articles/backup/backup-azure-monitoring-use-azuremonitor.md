---
title: 'Azure Backup: Azure Monitor での Azure Backup の監視'
description: Azure Monitor を使用して、Azure Backup ワークロードを監視し、カスタム アラートを作成します。
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Azure Backup; アラート; 診断設定; アクション グループ
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786313"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Azure Monitor を使用した大規模な監視

Azure Backup では、Recovery Services コンテナーに[組み込みの監視とアラートの機能](backup-azure-monitoring-built-in-monitor.md)が提供されています。 これらの機能は、管理インフラストラクチャを追加しなくても使用できます。 ただし、この組み込みサービスは次のシナリオでは制限されます。

- サブスクリプションにまたがる複数の Recovery Services コンテナーのデータを監視する場合
- 優先通知チャネルが "*電子メールではない*" 場合
- ユーザーがより多くのシナリオでのアラートを希望する場合
- Azure で System Center Data Protection Manager などのオンプレミス コンポーネントの情報を表示しようとする場合 (ポータルの [ **[バックアップ ジョブ]** ](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) または [ **[バックアップ アラート]** ](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) には表示されない)

## <a name="using-log-analytics-workspace"></a>Log Analytics ワークスペースを使用する

> [!NOTE]
> Azure VM バックアップ、Azure Backup エージェント、System Center Data Protection Manager、Azure VM の SQL バックアップ、および Azure Files 共有のバックアップのデータは、診断設定によって Log Analytics ワークスペースに送られます。 

大規模な監視のためには、2 つの Azure サービスの機能が必要です。 "*診断設定*" では、複数の Azure Resource Manager リソースから別のリソースにデータを送信します。 "*Log Analytics*" では、アクション グループを使用して他の通知チャネルを定義できるカスタム アラートが生成されます。 

以下のセクションでは、Log Analytics を使用して Azure Backup の大規模な監視を行う方法を詳しく説明します。

### <a name="configure-diagnostic-settings"></a>診断設定を構成する

Recovery Services コンテナーなどの Azure Resource Manager リソースは、スケジュールされた操作およびユーザーがトリガーした操作に関する情報を診断データとして記録します。 

監視セクションで **[診断設定]** を選択し、Recovery Services コンテナーの診断データの送信先を指定します。

![Log Analytics を送信先にした Recovery Services コンテナーの診断設定](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

別のサブスクリプションの Log Analytics ワークスペースを送信先にすることができます。 サブスクリプションにまたがるコンテナーを 1 か所で監視するには、複数の Recovery Services コンテナーに対して同じ Log Analytics ワークスペースを選択します。 Azure Backup に関連するすべての情報を Log Analytics ワークスペースに送るには、ログとして **[AzureBackupReport]** を選択します。

> [!IMPORTANT]
> 構成が完了したら、最初のデータ プッシュが終了するまで 24 時間待機する必要があります。 この最初のデータ プッシュの後、この記事の[頻度に関するセクション](#diagnostic-data-update-frequency)で後述するように、すべてのイベントがプッシュされます。

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Log Analytics ワークスペースにソリューションをデプロイする

データが Log Analytics ワークスペース内に入れられたら、データを可視化するために、Log Analytics に [GitHub テンプレートをデプロイ](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/)します。 ワークスペースを正しく識別するために、必ず同じリソース グループ、ワークスペース名、およびワークスペースの場所を指定してください。 次に、このテンプレートをワークスペースにインストールします。

> [!NOTE]
> Log Analytics ワークスペースにアラート、バックアップ ジョブ、または復元ジョブがない場合、ポータルに "BadArgumentError" というエラー コードが表示されることがあります。 このエラーを無視し、ソリューションの使用を続けます。 関連するデータ型がワークスペースに流入し始めた後は、視覚化によって同じ内容が反映されて、このエラーは表示されなくなります。

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Log Analytics を使用して Azure Backup データを表示する

テンプレートがデプロイされた後、Azure Backup を監視するためのソリューションがワークスペースの概要の領域に表示されます。 概要に移動するには、次のいずれかのパスに従います。

- **Azure Monitor**: **[分析情報]** セクションで **[その他]** を選択し、関連するワークスペースを選択します。
- **Log Analytics ワークスペース**: 関連するワークスペースを選択し、 **[全般]** の **[ワークスペースの概要]** を選択します。

![Log Analytics の監視タイル](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

監視タイルを選択すると、デザイナー テンプレートが開いて、Azure Backup の基本的な監視データについての一連のグラフが表示されます。 表示されるグラフの一部を次に示します。

* すべてのバックアップ ジョブ

   ![バックアップ ジョブの Log Analytics グラフ](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* 復元ジョブ

   ![復元ジョブの Log Analytics グラフ](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* Azure リソースのための組み込み Azure Backup アラート

   ![Azure リソースのための組み込み Azure Backup アラートの Log Analytics グラフ](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* オンプレミス リソースのための組み込み Azure Backup アラート

   ![オンプレミス リソースのための組み込み Azure Backup アラートの Log Analytics グラフ](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* アクティブなデータ ソース

   ![アクティブなバックアップ エンティティの Log Analytics グラフ](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* Recovery Services コンテナーのクラウド ストレージ

   ![Recovery Services コンテナーのクラウド ストレージの Log Analytics グラフ](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

これらのグラフは、テンプレートで提供されています。 必要に応じて、グラフを編集したり、グラフを追加したりできます。

> [!IMPORTANT]
> テンプレートをデプロイすると、基本的に読み取り専用ロックが作成されます。 テンプレートを編集して保存するには、ロックを解除する必要があります。 ロックの解除は、Log Analytics ワークスペースの **[設定]** セクションの **[ロック]** ウィンドウで行うことができます。

### <a name="create-alerts-by-using-log-analytics"></a>Log Analytics を使用してアラートを作成する

Azure Monitor では、Log Analytics ワークスペースで独自のアラートを作成できます。 ワークスペースで、"*Azure アクション グループ*" を使用して、優先する通知メカニズムを選択します。 

> [!IMPORTANT]
> このクエリの作成コストについては、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

いずれかのグラフを選択して、Log Analytics ワークスペースの **[ログ]** セクションを開きます。 **[ログ]** セクションで、クエリを編集し、それらに対するアラートを作成します。

![Log Analytics ワークスペースでアラートを作成する](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

**[新しいアラート ルール]** を選択すると、次の図に示すように、Azure Monitor のアラート作成ページが開きます。 ここではリソースが既に Log Analytics ワークスペースとしてマークされており、アクション グループの統合が提供されています。

![Log Analytics のアラート作成ページ](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>アラートの条件

アラートの決定的な特徴は、そのトリガー条件です。 **[条件]** を選択すると、次の図に示すように、 **[ログ]** ページに Kusto クエリが自動的に読み込まれます。 ここでは、ニーズに合わせて条件を編集できます。 詳細については、「[サンプル Kusto クエリ](#sample-kusto-queries)」を参照してください。

![アラート条件を設定する](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

必要に応じて、Kusto クエリを編集できます。 しきい値、期間、頻度を選択します。 どのような場合にアラートが発生するかは、しきい値によって決まります。 期間は、クエリが実行される時間枠です。 たとえば、しきい値が 0 より大きく、期間が 5 分で頻度が 5 分の場合、そのルールでは、5 分ごとにクエリを実行し、前の 5 分間を確認します。 結果の件数が 0 より大きい場合は、選択したアクション グループを使用して通知されます。

#### <a name="alert-action-groups"></a>アラートのアクション グループ

アクション グループは、通知チャネルを指定するために使用します。 使用可能な通知メカニズムを確認するには、 **[アクション グループ]** で **[新規作成]** を選択します。

![[アクション グループの追加] ウィンドウの使用可能な通知メカニズム](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

アラートと監視のすべての要件を Log Analytics 単独で満たすことも、Log Analytics を使用して組み込みの通知を補うこともできます。

詳細については、「[Azure Monitor を使用してログ アラートを作成、表示、管理する](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)」と「[Azure Portal でのアクション グループの作成および管理](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)」を参照してください。

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

既定のグラフによって基本的なシナリオ用の Kusto クエリが提供されるため、それに基づいてアラートを作成できます。 クエリを変更して、アラートを設定する対象のデータを取得することもできます。 次のサンプル Kusto クエリを **[ログ]** ページに貼り付け、次のクエリに対してアラートを作成します。

* 成功したすべてのバックアップ ジョブ

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* 失敗したすべてのバックアップ ジョブ

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* 成功したすべての Azure VM バックアップ ジョブ

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

* 成功したすべての SQL ログ バックアップ ジョブ

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

* 成功したすべての Azure Backup エージェント ジョブ

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

コンテナーの診断データは少し遅れて Log Analytics ワークスペースに送られます。 すべてのイベントは、Recovery Services コンテナーからプッシュされてから "*20 分から 30 分*" 後に Log Analytics ワークスペースに到着します。 ここでは、その遅れの詳細について説明します。

- すべてのソリューションについて、バックアップ サービスの組み込みアラートは、作成されるとすぐにプッシュされます。 そのため、通常は 20 分から 30 分後に Log Analytics ワークスペースに表示されます。
- すべてのソリューションについて、アドホック バックアップ ジョブと復元ジョブは、"*終了*" するとすぐにプッシュされます。
- すべてのソリューション (SQL バックアップ以外) について、スケジュール済みバックアップ ジョブは、"*終了*" するとすぐにプッシュされます。
- SQL バックアップでは、15 分ごとにログ バックアップを行うことができるため、完了したすべてのスケジュール済みバックアップ ジョブの情報は、ログも含めて 6 時間ごとにバッチ処理されてプッシュされます。
- すべてのソリューションについて、バックアップ項目、ポリシー、復旧ポイント、ストレージなどのその他の情報は、少なくとも "*1 日 1 回*" プッシュされます。
- バックアップ構成を変更すると (ポリシーの変更や編集など)、関連するすべてのバックアップ情報のプッシュがトリガーされます。

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Recovery Services コンテナーのアクティビティ ログを使用する

> [!CAUTION]
> 次の手順は、*Azure VM のバックアップ*にのみ適用されます。 Azure Backup エージェント、Azure 内での SQL バックアップ、Azure Files などのソリューションでは、これらの手順は使用できません。

アクティビティ ログを使用して、バックアップ成功などのイベントの通知を受け取ることもできます。 開始するには、次の手順に従います。

1. Azure Portal にサインインします。
1. 関連する Recovery Services コンテナーを開きます。 
1. コンテナーのプロパティで、 **[アクティビティ ログ]** セクションを開きます。

適切なログを指定してアラートを作成するには:

1. 次の図に示すようにフィルターを適用して、成功したバックアップのアクティビティ ログを受信しているかどうかを確認します。 レコードを表示するために、必要に応じて **[期間]** の値を変更します。

   ![Azure VM バックアップのアクティビティ ログを検索するためのフィルター処理](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. 操作名を選択すると、関連する詳細が表示されます。
1. **[新しいアラート ルール]** を選択して **[ルールの作成]** ページを開きます。 
1. 「[Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)」の手順に従って、アラートを作成します。

   ![新しいアラート ルール](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

ここで、リソースは Recovery Services コンテナー自体です。 アクティビティ ログを使用して通知を受け取るすべてのコンテナーに対して同じ手順を繰り返す必要があります。 このアラートはイベントに基づいているため、この条件にしきい値、期間、または頻度は設定されません。 関連するアクティビティ ログが生成されるとすぐに、アラートが発生します。

## <a name="using-log-analytics-to-monitor-at-scale"></a>Log Analytics を使用した Azure Backup の大規模な監視

Azure Monitor では、アクティビティ ログと Log Analytics ワークスペースから作成されたすべてのアラートを表示できます。 左側の **[アラート]** ウィンドウを開くだけです。

アクティビティ ログを使用して通知を受け取ることはできますが、大規模な監視にはアクティビティ ログではなく Log Analytics を使用することを強くお勧めします。 理由は次のとおりです。

- **シナリオの制限**: アクティビティ ログを使用した通知は、Azure VM バックアップにのみ適用されます。 通知は、Recovery Services コンテナーごとに設定する必要があります。
- **定義の一致**: スケジュール済みバックアップ アクティビティは、アクティビティ ログの最新定義と一致しません。 代わりに、[診断ログ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs)と一致します。 この一致によって、アクティビティ ログ チャネルを通過するデータが変更された場合に、予期しない影響が発生します。
- **アクティビティ ログ チャネルの問題**: Recovery Services コンテナーでは、Azure Backup から取り込まれたアクティビティ ログは、新しいモデルに従ったものになっています。 残念ながら、この変更は、Azure Government、Azure Germany、および Azure China 21Vianet のアクティビティ ログの生成に影響します。 これらのクラウド サービスのユーザーが Azure Monitor のアクティビティ ログからアラートを作成または構成した場合、アラートはトリガーされません。 また、すべての Azure パブリック リージョンにおいて、ユーザーが [Recovery Services のアクティビティ ログを Log Analytics ワークスペースに収集](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)した場合、それらのログは表示されません。

Azure Backup で保護されるすべてのワークロードでは、大規模な監視とアラートのためには Log Analytic ワークスペースを使用してください。

## <a name="next-steps"></a>次の手順

カスタム クエリを作成するには、[Log Analytics データ モデル](backup-azure-log-analytics-data-model.md)に関する記事を参照してください。
