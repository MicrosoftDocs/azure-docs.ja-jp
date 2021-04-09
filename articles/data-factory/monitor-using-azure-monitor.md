---
title: Azure Monitor を使用して、データ ファクトリを監視する
description: Azure Data Factory からの情報を使用して診断ログを有効にし、Azure Monitor を使用して Data Factory パイプラインを監視する方法を説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 04aba71e49fedc8a4ca22aed576aaa978f733336
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783644"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Azure Monitor を使用してデータ ファクトリの監視とアラート送信を行う

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

クラウド アプリケーションは複雑であり、動的なパーツが多数あります。 モニターでは、アプリケーションを正常な状態で確実に稼働させ続けるためのデータを提供します。 モニターは、潜在的な問題を回避し、過去の問題をトラブルシューティングするのにも役立ちます。 監視データを使用して、アプリケーションに関する詳細な分析情報を取得できます。 この知識は、アプリケーションのパフォーマンスと保守容易性の向上に役立ちます。 また、手動操作を必要とするアクションの自動化にも役立ちます。

Azure Monitor では、ほとんどの Azure サービスに対して、基礎レベルのインフラストラクチャのメトリックとログを提供します。 Azure 診断ログはリソースによって出力され、そのリソースの操作に関する豊富な頻出データを提供します。 Azure Data Factory (ADF) では、Azure Monitor に診断ログを書き込むことができます。 この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

詳細については、「[Azure Monitor の概要](../azure-monitor/overview.md)」を参照してください。

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Azure Data Factory メトリックとパイプライン実行データの保持

Data Factory では、パイプライン実行データを 45 日間だけ格納します。 データをより長期間保持する場合は、Azure Monitor を使用してください。 Monitor を使用すると、診断ログを複数の異なるターゲットにルーティングして分析できます。

* **[ストレージ アカウント]** : 監査や手動での検査のために、診断ログをストレージ アカウントに保存します。 診断設定を使用して、リテンション期間 (日数) を指定できます。
* **イベント ハブ**: ログを Azure Event Hubs にストリーミングします。 ログは、パートナー サービス/カスタム分析ソリューション (Power BI など) への入力となります。
* **Log Analytics**:ログを Log Analytics で分析します。 Data Factory を Azure Monitor と統合することは、次のシナリオで役立ちます。
  * Data Factory から Monitor に発行された充実したメトリックのセットに対して、複雑なクエリを記述する必要がある場合。 Monitor を使用して、これらのクエリにカスタム アラートを作成できます。
  * データ ファクトリ全体を監視する必要があります。 複数のデータ ファクトリから 1 つの Monitor ワークスペースにデータをルーティングできます。

ログを出力するリソースのサブスクリプションにないストレージ アカウントまたはイベント ハブ名前空間を使用することもできます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な Azure ロールベースのアクセス制御 (Azure RBAC) アクセス権限を持っている必要があります。

## <a name="configure-diagnostic-settings-and-workspace"></a>診断設定とワークスペースを構成する

データ ファクトリに診断設定を作成または追加します。

1. ポータルで Monitor に移動します。 **[設定]**  >  **[診断設定]** の順に選択します。

1. 診断設定の対象となるデータ ファクトリを選択します。

1. 選択したデータ ファクトリの設定が存在しない場合は、設定を作成するように求められます。 **[診断を有効にする]** を選択します。

   ![設定が存在しない場合に診断設定を作成する](media/data-factory-monitor-oms/monitor-oms-image1.png)

   データ ファクトリに既存の設定が存在する場合は、データ ファクトリで構成済みの設定の一覧が表示されます。 **[診断設定の追加]** を選択します。

   ![設定が存在する場合に診断設定を追加する](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 設定に名前を付け、 **[Log Analytics への送信]** を選択して、 **[Log Analytics ワークスペース]** からワークスペースを選択します。

    * _Azure Diagnostics_ モードでは、診断ログは _AzureDiagnostics_ テーブルに送られます。

    * _Resource-Specific_ モードでは、Azure Data Factory からの診断ログは以下のテーブルに送られます。
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      お客様のワークロードに関連するさまざまなログを選択して、Log Analytics テーブルに送信できます。 たとえば、SQL Server Integration Services (SSIS) をまったく使用しない場合は、SSIS ログを選択する必要はありません。 SSIS Integration Runtime (IR) の開始、停止、またはメンテナンス操作をログに記録する場合は、SSIS IR ログを選択できます。 SQL Server Management Studio (SSMS)、SQL Server エージェント、またはその他の指定されたツールで T-SQL を使用して SSIS パッケージ実行を呼び出す場合は、SSIS パッケージ ログを選択できます。 SSIS パッケージの実行を ADF パイプラインの SSIS パッケージ実行アクティビティ経由で呼び出す場合は、すべてのログを選択できます。

    * _AllMetrics_ を選択すると、監視したりアラートを生成したりするために、ADF アクティビティ、パイプライン、およびトリガーの実行のメトリックに加え、SSIS IR 操作と SSIS パッケージ実行のメトリックを含むさまざまな ADF メトリックを使用できるようになります。

   ![設定に名前を付けてログ分析ワークスペースを選択する](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Azure ログ テーブルには 500 個を超える列を含めることができないため、"_リソース固有モード_" を選択することを **強くお勧めします**。 詳細については、[Azure Diagnostics のログ リファレンス](/azure-monitor/reference/tables/azurediagnostics#additionalfields-column)に関するページを参照してください。

1. **[保存]** を選択します。

しばらくすると、このデータ ファクトリに対する設定の一覧に新しい設定が表示されます。 新しいイベント データが生成されるとすぐに、診断ログがそのワークスペースにストリーミングされます。 イベントが生成されてから、Log Analytics に表示されるまでに最大 15 分かかかる場合があります。

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Marketplace から Azure Data Factory Analytics ソリューションをインストールする

このソリューションを使用すると、Data Factory の全体的な正常性の概要を確認できます。また、詳細を確認したり、予期しない動作パターンのトラブルシューティングを実行したりするためのオプションも備わっています。 すぐに利用できる豊富なビュー機能を活用することで、次の主要な処理に関する分析情報を得ることができます。

* データ ファクトリ パイプライン、アクティビティ、トリガーの実行に関する概要
* データ ファクトリ アクティビティの実行を種類別に詳しく表示する機能
* データ ファクトリのトップ パイプライン、アクティビティ エラーの概要

1. **Azure Marketplace** にアクセスして、 **[分析]** フィルターを選択し、**Azure Data Factory Analytics (プレビュー)** を検索します。

   !["Azure Marketplace" にアクセスして、「Analytics filter」と入力し、[Azure Data Factory Analytics (プレビュー)] を選択する](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. **Azure Data Factory Analytics (プレビュー)** の詳細

   ![[Azure Data Factory Analytics (プレビュー)] の詳細](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. **[作成]** を選択し、**Log Analytics ワークスペース** を作成または選択します。

   ![新しいソリューションの作成](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory メトリックを監視する

このソリューションをインストールすると、選択した Log Analytics ワークスペースの [ブック] セクションに既定のセットのビューが作成されます。 その結果、次のメトリックが有効になります。

* ADF の実行 - 1) Data Factory によるパイプラインの実行
* ADF の実行 - 2) Data Factory によるアクティビティの実行
* ADF の実行 - 3) Data Factory によるトリガーの実行
* ADF のエラー - 1) Data Factory によるパイプラインのエラーの上位 10 件
* ADF のエラー - 2) Data Factory によるアクティビティの実行エラーの上位 10 件
* ADF のエラー - 3) Data Factory によるトリガーのエラーの上位 10 件
* ADF の統計 - 1) 種類別のアクティビティの実行
* ADF の統計 - 2) 種類別のトリガーの実行
* ADF の統計 - 3) パイプラインの実行の最長期間

!["Workbooks (プレビュー)" と "AzureDataFactoryAnalytics" が強調表示されているウィンドウ](media/data-factory-monitor-oms/monitor-oms-image6.png)

前述のメトリックの視覚化、これらのメトリックの背後にあるクエリの確認、クエリの編集、アラートの作成、およびその他のアクションを実行できます。

![データ ファクトリによるパイプライン実行のグラフィック表示](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (プレビュー) では、診断ログが _リソース固有_ の宛先テーブルに送られます。 次のテーブルに対するクエリを作成できます: _ADFPipelineRun_、_ADFTriggerRun_、_ADFActivityRun_。

## <a name="data-factory-metrics"></a>Data Factory のメトリック

Monitor を使用すると、Azure ワークロードのパフォーマンスと正常性を可視化できます。 最も重要な種類の Monitor データは、パフォーマンス カウンターとも呼ばれるメトリックです。 メトリックは、ほとんどの Azure リソースによって出力されます。 Monitor には、監視およびトラブルシューティングのために、これらのメトリックを構成して使用する方法が複数用意されています。

Azure Data Factory バージョン 2 で出力されるメトリックの一部を次に示します。

| **メトリック**                           | **メトリックの表示名**                  | **単位** | **集計の種類** | **説明**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | キャンセルしたアクティビティの実行回数のメトリック           | Count    | 合計                | 1 分の枠内でキャンセルされたアクティビティの実行の合計数。 |
| ActivityFailedRuns                   | 失敗したアクティビティ実行回数のメトリック             | Count    | 合計                | 1 分の枠内で失敗したアクティビティの実行の合計数。 |
| ActivitySucceededRuns                | 成功したアクティビティ実行回数のメトリック          | Count    | 合計                | 1 分の枠内で成功したアクティビティの実行の合計数。 |
| PipelineCancelledRuns                 | キャンセルしたパイプライン実行回数のメトリック           | Count    | 合計                | 1 分の枠内でキャンセルされたパイプライン実行の合計数。 |
| PipelineFailedRuns                   | 失敗したパイプライン実行回数のメトリック             | Count    | 合計                | 1 分の枠内で失敗したパイプライン実行の合計数。 |
| PipelineSucceededRuns                | 成功したパイプライン実行回数のメトリック          | Count    | 合計                | 1 分の枠内で成功したパイプライン実行の合計数。 |
| TriggerCancelledRuns                  | キャンセルしたトリガー実行の回数メトリック            | Count    | 合計                | 1 分の枠内でキャンセルされたトリガー実行の合計数。 |
| TriggerFailedRuns                    | 失敗したトリガー実行の回数メトリック              | Count    | 合計                | 1 分の枠内で失敗したトリガー実行の合計数。 |
| TriggerSucceededRuns                 | 成功したトリガー実行の回数メトリック           | Count    | 合計                | 1 分の枠内で成功したトリガー実行の合計数。 |
| SSISIntegrationRuntimeStartCancelled  | キャンセルされた SSIS 統合ランタイム開始メトリック           | Count    | 合計                | 1 分の枠内でキャンセルされた SSIS 統合ランタイム開始の合計数。 |
| SSISIntegrationRuntimeStartFailed    | 失敗した SSIS 統合ランタイム開始メトリック             | Count    | 合計                | 1 分の枠内で失敗した SSIS 統合ランタイム開始の合計数。 |
| SSISIntegrationRuntimeStartSucceeded | 成功した SSIS 統合ランタイム開始メトリック          | Count    | 合計                | 1 分の枠内で成功した SSIS 統合ランタイム開始の合計数。 |
| SSISIntegrationRuntimeStopStuck      | 停止した SSIS 統合ランタイム停止メトリック               | Count    | 合計                | 1 分の枠内で停止した SSIS 統合ランタイム停止の合計数。 |
| SSISIntegrationRuntimeStopSucceeded  | 成功した SSIS 統合ランタイム停止メトリック           | Count    | 合計                | 1 分の枠内で成功した SSIS 統合ランタイム停止の合計数。 |
| SSISPackageExecutionCancelled         | キャンセルされた SSIS パッケージ実行回数のメトリック  | Count    | 合計                | 1 分の枠内でキャンセルされた SSIS パッケージ実行の合計数。 |
| SSISPackageExecutionFailed           | 失敗した SSIS パッケージ実行回数のメトリック    | Count    | 合計                | 1 分の枠内で失敗した SSIS パッケージ実行の合計数。 |
| SSISPackageExecutionSucceeded        | 成功した SSIS パッケージ実行回数のメトリック | Count    | 合計                | 1 分の枠内で成功した SSIS パッケージ実行の合計数。 |

メトリックにアクセスするには、「[Azure Monitor データ プラットフォーム](../azure-monitor/data-platform.md)」に記載された手順に従います。

> [!NOTE]
> 完了済みのトリガーされたアクティビティとパイプラインの実行からのイベントのみが出力されます。 進行中およびデバッグの実行は出力 **されません**。 その一方で、呼び出しメソッドに関係なく、**すべての** SSIS パッケージ実行からのイベントが出力されます。これには、呼び出し方法に関係なく、完了済みのものと進行中のものが含まれます。 たとえば、SSMS、SQL Server エージェント、またはその他の指定されたツールで T-SQL を使用して、ADF パイプラインでの Execute SSIS Package アクティビティのトリガーされたまたはデバッグの実行として、Azure 対応 SQL Server Data Tools (SSDT) のパッケージ実行を呼び出すことができます。

## <a name="data-factory-alerts"></a>Data Factory のアラート

Azure portal にサインインし、 **[モニター]**  >  **[アラート]** の順に選択してアラートを作成します。

![ポータル メニューのアラート](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>アラートを作成する

1. **[+ 新しいアラート ルール]** を選択して新しいアラートを作成します。

    ![新しいアラート ルール](media/monitor-using-azure-monitor/alerts_image4.png)

1. アラートの条件を定義します。

    > [!NOTE]
    > **[リソースの種類でフィルター]** ドロップダウン リストでは **[すべて]** を選択するようにしてください。

    ![[アラートの条件を定義します] > [ターゲットの選択] で [リソースの選択] ウィンドウが開きます ](media/monitor-using-azure-monitor/alerts_image5.png)

    ![[アラートの条件を定義します] > [条件の追加] で [シグナル ロジックの構成] ウィンドウを開きます](media/monitor-using-azure-monitor/alerts_image6.png)

    ![[シグナル ロジックの構成] ウィンドウ](media/monitor-using-azure-monitor/alerts_image7.png)

1. アラートの詳細を定義します。

    ![[アラートの詳細]](media/monitor-using-azure-monitor/alerts_image8.png)

1. アクション グループを定義します。

    ![ルールの作成 ([新しいアクショングループ] が強調表示されている)](media/monitor-using-azure-monitor/alerts_image9.png)

    ![新しいアクション グループを作成する](media/monitor-using-azure-monitor/alerts_image10.png)

    ![メール、SMS、プッシュ、音声の構成](media/monitor-using-azure-monitor/alerts_image11.png)

    ![アクション グループの定義](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Monitor REST API を使用して診断ログを設定する

### <a name="diagnostic-settings"></a>診断設定

診断設定を使用して、非コンピューティング リソースの診断ログを構成します。 リソース コントロールの設定には、次の機能があります。

* 診断ログを送信する場所を指定します。 たとえば、Azure ストレージ アカウント、Azure イベント ハブ、Monitor ログなどがあります。
* 送信するログ カテゴリを指定します。
* ログの各カテゴリをストレージ アカウントに保持する期間を指定します。
* リテンション期間が 0 日の場合、ログは永続的に保持されます。 または、1 日から 2,147,483,647 日の間の任意の日数を値として指定できます。
* 保持ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合、保持ポリシーは無効になります。 たとえば、この状態は、Event Hubs または Monitor ログのオプションのみが選択されている場合に生じる可能性があります。
* 保持ポリシーは日単位で適用されます。 日と日の間の境界は、協定世界時 (UTC) の午前 0 時になります。 1 日の終わりに、保持ポリシーの期間を超えた日のログが削除されます。 たとえば、保持ポリシーが 1 日の場合、今日が始まった時点で、昨日より前のログは削除されます。

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Monitor REST API を使用して診断ログを有効にする

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Monitor REST API の診断設定を作成または更新する

##### <a name="request"></a>Request

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>ヘッダー

* `{api-version}` を `2016-09-01` で置き換え
* `{resource-id}` を、診断設定を編集するリソースの ID に置き換えます。 詳細については、[リソース グループを使用した Azure リソースの管理](../azure-resource-manager/management/manage-resource-groups-portal.md)に関するページを参照してください。
* `Content-Type` ヘッダーを `application/json` に設定します。
* Azure Active Directory (Azure AD) から取得した Authorization ヘッダーを JSON Web トークンに設定します。 詳細については、[要求の認証](../active-directory/develop/authentication-vs-authorization.md)に関するページを参照してください。

##### <a name="body"></a>Body

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| プロパティ | Type | 説明 |
| --- | --- | --- |
| **storageAccountId** |String | 診断ログを送信するストレージ アカウントのリソース ID。 |
| **serviceBusRuleId** |String | 診断ログのストリーミングのために Event Hubs を作成するサービス バス名前空間のサービス バス ルール ID。 ルール ID の形式は、`{service bus resource ID}/authorizationrules/{key name}` です。|
| **workspaceId** | 複合型 | メトリックの時間グレインと、その保持ポリシーの配列。 このプロパティの値は空です。 |
|**メトリック**| 呼び出されたパイプラインに渡されるパイプライン実行のパラメーター値| パラメーター名を引数値にマップする JSON オブジェクト。 |
| **logs**| 複合型| リソースの種類に対応する診断ログ カテゴリの名前。 リソースの診断ログ カテゴリの一覧を取得するには、診断設定の取得操作を実行します。 |
| **category**| String| ログ カテゴリとその保持ポリシーの配列。 |
| **timeGrain** | String | ISO 8601 期間形式でキャプチャされるメトリックの粒度。 プロパティ値は、1 分を指定する `PT1M` でなければなりません。 |
| **有効**| Boolean | このリソースに対して、メトリックまたはログ カテゴリの収集を有効にするどうかを指定します。 |
| **retentionPolicy**| 複合型| メトリックまたはログのカテゴリの保持ポリシーを示します。 このプロパティは、ストレージ アカウントのみに使用されます。 |
|**days**| int| メトリックまたはログを保持する日数。 プロパティ値が 0 の場合、ログは永続的に保持されます。 このプロパティは、ストレージ アカウントのみに使用されます。 |

##### <a name="response"></a>Response

200 OK。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Monitor REST API の診断設定に関する情報を取得する

##### <a name="request"></a>Request

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>ヘッダー

* `{api-version}` を `2016-09-01` で置き換え
* `{resource-id}` を、診断設定を編集するリソースの ID に置き換えます。 詳細については、[リソース グループを使用した Azure リソースの管理](../azure-resource-manager/management/manage-resource-groups-portal.md)に関するページを参照してください。
* `Content-Type` ヘッダーを `application/json` に設定します。
* Azure AD から取得した Authorization ヘッダーを JSON Web トークンに設定します。 詳細については、[要求の認証](../active-directory/develop/authentication-vs-authorization.md)に関するページを参照してください。

##### <a name="response"></a>Response

200 OK。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
詳細については、「[診断設定](/rest/api/monitor/diagnosticsettings)」を参照してください。

## <a name="schema-of-logs-and-events"></a>ログとイベントのスキーマ

### <a name="monitor-schema"></a>スキーマの監視

#### <a name="activity-run-log-attributes"></a>アクティビティ実行ログの属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| プロパティ | Type | 説明 | 例 |
| --- | --- | --- | --- |
| **Level** |String | 診断ログのレベル。 アクティビティ実行ログの場合は、プロパティ値を 4 に設定します。 | `4` |
| **correlationId** |String | 特定の要求を追跡するための一意の ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | timespan でのイベントの時刻 (UTC 形式 `YYYY-MM-DDTHH:MM:SS.00000Z`)。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| アクティビティの実行の ID。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| パイプラインの実行の ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | データ ファクトリのリソースに関連付けられている ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 診断ログのカテゴリ。 プロパティ値は `ActivityRuns` に設定してください。 | `ActivityRuns` |
|**level**| String | 診断ログのレベル。 プロパティ値は `Informational` に設定してください。 | `Informational` |
|**operationName**| String | 状態付きのアクティビティの名前。 アクティビティが開始ハートビートである場合、プロパティ値は `MyActivity -` です。 アクティビティが終了ハートビートである場合、プロパティ値は `MyActivity - Succeeded` です。 | `MyActivity - Succeeded` |
|**pipelineName**| String | パイプラインの名前。 | `MyPipeline` |
|**activityName**| String | アクティビティの名前。 | `MyActivity` |
|**start**| String | timespan でのアクティビティの実行の開始時刻 (UTC 形式)。 | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | timespan でのアクティビティの実行の終了時刻 (UTC 形式)。 アクティビティが開始したがまだ終了していないことが診断ログに示されている場合、プロパティ値は `1601-01-01T00:00:00Z` です。 | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>パイプライン実行ログの属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| プロパティ | Type | 説明 | 例 |
| --- | --- | --- | --- |
| **Level** |String | 診断ログのレベル。 アクティビティ実行ログの場合は、プロパティ値を 4 に設定します。 | `4` |
| **correlationId** |String | 特定の要求を追跡するための一意の ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | timespan でのイベントの時刻 (UTC 形式 `YYYY-MM-DDTHH:MM:SS.00000Z`)。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| パイプラインの実行の ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | データ ファクトリのリソースに関連付けられている ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 診断ログのカテゴリ。 プロパティ値は `PipelineRuns` に設定してください。 | `PipelineRuns` |
|**level**| String | 診断ログのレベル。 プロパティ値は `Informational` に設定してください。 | `Informational` |
|**operationName**| String | パイプラインの名前とその状態。 パイプラインの実行が完了すると、プロパティ値は `Pipeline - Succeeded` になります。 | `MyPipeline - Succeeded`. |
|**pipelineName**| String | パイプラインの名前。 | `MyPipeline` |
|**start**| String | timespan でのアクティビティの実行の開始時刻 (UTC 形式)。 | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | timespan でのアクティビティの実行の終了時刻 (UTC 形式)。 アクティビティが開始したがまだ終了していないことが診断ログに示されている場合、プロパティ値は `1601-01-01T00:00:00Z` です。  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | パイプライン実行の最終的な状態。 プロパティ値は `Succeeded` または `Failed` のいずれかです。 | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>トリガー実行ログの属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| プロパティ | Type | 説明 | 例 |
| --- | --- | --- | --- |
| **Level** |String | 診断ログのレベル。 アクティビティ実行ログの場合は、プロパティ値を 4 に設定します。 | `4` |
| **correlationId** |String | 特定の要求を追跡するための一意の ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | timespan でのイベントの時刻 (UTC 形式 `YYYY-MM-DDTHH:MM:SS.00000Z`)。 | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| トリガー実行の ID。 | `08587023010602533858661257311` |
|**resourceId**| String | データ ファクトリのリソースに関連付けられている ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 診断ログのカテゴリ。 プロパティ値は `PipelineRuns` に設定してください。 | `PipelineRuns` |
|**level**| String | 診断ログのレベル。 プロパティ値は `Informational` に設定してください。 | `Informational` |
|**operationName**| String | トリガーの名前と、トリガーが正常に起動されたかどうかを示す最終的な状態。 ハートビートが成功した場合、プロパティ値は `MyTrigger - Succeeded` になります。 | `MyTrigger - Succeeded` |
|**triggerName**| String | トリガーの名前。 | `MyTrigger` |
|**triggerType**| String | トリガーの種類。 プロパティ値は `Manual Trigger` または `Schedule Trigger` のいずれかです。 | `ScheduleTrigger` |
|**triggerEvent**| String | トリガーのイベント。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | timespan でのトリガーの起動の開始時刻 (UTC 形式)。 | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | トリガーが正常に起動されたかどうかを示す最終的な状態。 プロパティ値は `Succeeded` または `Failed` のいずれかです。 | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>SSIS 統合ランタイム ログの属性

SSIS IR の開始/停止/メンテナンス操作のログの属性を次に示します。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| プロパティ                   | Type   | 説明                                                   | 例                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | SSIS IR 操作の名前                            | `Start/Stop/Maintenance` |
| **category**               | String | 診断ログのカテゴリ                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | 特定の操作を追跡するための一意の ID             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | ADF の名前                                          | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR の名前                                      | `MySSISIR` |
| **level**                  | String | 診断ログのレベル                                  | `Informational` |
| **resultType**             | String | SSIS IR 操作の結果                          | `Started/InProgress/Succeeded/Failed` |
| **message**                | String | SSIS IR 操作の出力メッセージ                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | String | ADF リソースの一意の ID                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>SSIS イベント メッセージ コンテキスト ログの属性

SSIS IR での SSIS パッケージ実行により生成されるイベント メッセージに関連する条件のログの属性を次に示します。 これらは、多くの SSIS パッケージのプロパティの実行時の値を示す [SSIS カタログ (SSISDB) イベント メッセージ コンテキスト テーブルまたはビュー](/sql/integration-services/system-views/catalog-event-message-context)と類似の情報を伝えます。 これらは、`Basic/Verbose` ログ レベルを選択するときに生成されるもので、デバッグやコンプライアンス チェックに役立ちます。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| プロパティ                   | Type   | 説明                                                          | 例                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | これは `YourSSISIRName-SSISPackageEventMessageContext` に設定されています       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | 診断ログのカテゴリ                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | 特定の操作を追跡するための一意の ID                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF の名前                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR の名前                                             | `MySSISIR` |
| **level**                  | String | 診断ログのレベル                                         | `Informational` |
| **operationId**            | String | SSISDB での特定の操作を追跡するための一意の ID          | `1` (1 は、SSISDB への格納/T-SQL による呼び出しが行われて **いない** パッケージに関連する操作を示します) |
| **contextDepth**           | String | イベント メッセージ コンテキストの深さ                              | `0` (0 はパッケージの実行が開始される前のコンテキストを示し、1 はエラーが発生したときのコンテキストを示します。これは、コンテキストがエラーから離れていくにつれて増加します) |
| **packagePath**            | String | イベント メッセージ コンテキストのソースとしてのパッケージ オブジェクトのパス      | `\Package` |
| **contextType**            | String | イベント メッセージ コンテキストのソースとしてのパッケージ オブジェクトの種類      | `60`([その他のコンテキストの種類](/sql/integration-services/system-views/catalog-event-message-context#remarks)を参照) |
| **contextSourceName**      | String | イベント メッセージ コンテキストのソースとしてのパッケージ オブジェクトの名前      | `MyPackage` |
| **contextSourceId**        | String | イベント メッセージ コンテキストのソースとしてのパッケージ オブジェクトの一意の ID | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | イベント メッセージ コンテキストのソースのパッケージ プロパティの名前   | `DelayValidation` |
| **propertyValue**          | String | イベント メッセージ コンテキストのソースのパッケージ プロパティの値  | `False` |
| **resourceId**             | String | ADF リソースの一意の ID                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>SSIS イベント メッセージ ログの属性

SSIS IR での SSIS パッケージ実行により生成されるイベント メッセージのログの属性を次に示します。 これらは、イベント メッセージの詳細なテキスト/メタデータを示す [SSISDB イベント メッセージ テーブルまたはビュー](/sql/integration-services/system-views/catalog-event-messages)と類似の情報を伝えます。 これらは、`None` 以外のすべてのログ レベルで生成されます。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| プロパティ                   | Type   | 説明                                                        | 例                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | これは `YourSSISIRName-SSISPackageEventMessages` に設定されています           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | 診断ログのカテゴリ                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | 特定の操作を追跡するための一意の ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF の名前                                               | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR の名前                                           | `MySSISIR` |
| **level**                  | String | 診断ログのレベル                                       | `Informational` |
| **operationId**            | String | SSISDB での特定の操作を追跡するための一意の ID        | `1` (1 は、SSISDB への格納/T-SQL による呼び出しが行われて **いない** パッケージに関連する操作を示します) |
| **messageTime**            | String | イベントメッセージが作成された時刻 (UTC 形式)          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | String | イベント メッセージの種類                                     | `70`([その他のメッセージ型](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)を参照) |
| **messageSourceType**      | String | イベント メッセージ ソースの種類                              | `20`([その他のメッセージ ソースの種類](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)を参照) |
| **message**                | String | イベント メッセージのテキスト                                     | `MyPackage:Validation has started.` |
| **packageName**            | String | 実行されたパッケージ ファイルの名前                             | `MyPackage.dtsx` |
| **eventName**              | String | 関連する実行時イベントの名前                                 | `OnPreValidate` |
| **messageSourceName**      | String | イベント メッセージ ソースとしてのパッケージ コンポーネントの名前         | `Data Flow Task` |
| **messageSourceId**        | String | イベント メッセージ ソースとしてのパッケージ コンポーネントの一意の ID    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | String | イベント メッセージ ソースとしてのデータ フロー コンポーネントの名前       | `SSIS.Pipeline` |
| **packagePath**            | String | イベント メッセージ ソースとしてのパッケージ オブジェクトのパス            | `\Package\Data Flow Task` |
| **executionPath**          | String | 親パッケージから、実行されたコンポーネントまでの完全なパス            | `\Transformation\Data Flow Task` (このパスではコンポーネントの繰り返しもキャプチャされます) |
| **threadId**               | String | イベント メッセージがログに記録されたときに実行されたスレッドの一意の ID | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>SSIS 実行可能ファイル統計ログの属性

SSIS IR での SSIS パッケージ実行により生成される実行可能ファイル統計ログの属性を次に示します。この場合の実行可能ファイルは、パッケージ制御フローのコンテナーまたはタスクです。 これらは、実行中の各実行可能ファイルとその繰り返しを 1 行で表示する [SSISDB 実行可能ファイル統計テーブルまたはビュー](/sql/integration-services/system-views/catalog-executable-statistics)と類似の情報を伝えます。 これらは、`None` 以外のすべてのログ レベルで生成され、タスク レベルのボトルネックや障害を特定するのに役立ちます。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| プロパティ                   | Type   | 説明                                                      | 例                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | これは `YourSSISIRName-SSISPackageExecutableStatistics` に設定されています  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | 診断ログのカテゴリ                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | 特定の操作を追跡するための一意の ID                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF の名前                                             | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR の名前                                         | `MySSISIR` |
| **level**                  | String | 診断ログのレベル                                     | `Informational` |
| **executionId**            | String | SSISDB での特定の実行を追跡するための一意の ID      | `1` (1 は、SSISDB への格納/T-SQL による呼び出しが行われて **いない** パッケージに関連する実行を示します) |
| **executionPath**          | String | 親パッケージから、実行されたコンポーネントまでの完全なパス          | `\Transformation\Data Flow Task` (このパスではコンポーネントの繰り返しもキャプチャされます) |
| **startTime**              | String | 実行可能ファイルが実行前フェーズに入ったときの時刻 (UTC 形式)  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 実行可能ファイルが実行後フェーズに入ったときの時刻 (UTC 形式) | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | String | 実行可能ファイルの実行時間 (ミリ秒)                   | `1,125` |
| **executionResult**        | String | 実行可能ファイルの実行結果                                 | `0` (0 は成功、1 は失敗、2 は完了、3 はキャンセルを意味します) |
| **executionValue**         | String | 実行可能ファイルを実行したときに返されるユーザー定義値            | `1` |
| **resourceId**             | String | ADF リソースの一意の ID                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS 実行コンポーネントのフェーズに関するログの属性

SSIS IR での SSIS パッケージ実行により生成されるデータ フロー コンポーネントの実行時の統計情報に関するログの属性を次に示します。 これらは、すべての実行フェーズでデータ フロー コンポーネントによって費やされた時間を示す [SSISDB 実行コンポーネントのフェーズ テーブルまたはビュー](/sql/integration-services/system-views/catalog-execution-component-phases)と類似の情報を伝えます。 これらは、`Performance/Verbose` ログ レベルを選択するときに生成されるもので、データ フロー実行の統計情報をキャプチャするのに役立ちます。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| プロパティ                   | Type   | 説明                                                         | 例                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | これは `YourSSISIRName-SSISPackageExecutionComponentPhases` に設定されています | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | 診断ログのカテゴリ                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | 特定の操作を追跡するための一意の ID                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF の名前                                                | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR の名前                                            | `MySSISIR` |
| **level**                  | String | 診断ログのレベル                                        | `Informational` |
| **executionId**            | String | SSISDB での特定の実行を追跡するための一意の ID         | `1` (1 は、SSISDB への格納/T-SQL による呼び出しが行われて **いない** パッケージに関連する実行を示します) |
| **packageName**            | String | 実行されたパッケージ ファイルの名前                              | `MyPackage.dtsx` |
| **taskName**               | String | 実行されたデータ フロー タスクの名前                                 | `Data Flow Task` |
| **subcomponentName**       | String | データ フロー コンポーネントの名前                                     | `Derived Column` |
| **phase**                  | String | 実行フェーズの名前                                         | `AcquireConnections` |
| **startTime**              | String | 実行フェーズの開始時刻 (UTC 形式)                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 実行フェーズの終了時刻 (UTC 形式)                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | String | データ フロー タスクの実行パス                            | `\Transformation\Data Flow Task` |
| **resourceId**             | String | ADF リソースの一意の ID                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS 実行データの統計情報に関するログの属性

データ フロー パイプラインの各区間を経由して上流から下流のコンポーネントに移動する、SSIS IR での SSIS パッケージ実行により生成されるデータ移動に関するログの属性を次に示します。 これらは、データ フロー タスクで移動されたデータの行数を示す [SSISDB 実行データの統計情報テーブルまたはビュー](/sql/integration-services/system-views/catalog-execution-data-statistics)と類似の情報を伝えます。 これらは、`Verbose` ログ レベルを選択するときに生成されるもので、データ フロー スループットを計算するのに役立ちます。

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| プロパティ                     | Type   | 説明                                                        | 例                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | イベントの時刻 (UTC 形式): `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | これは `YourSSISIRName-SSISPackageExecutionDataStatistics` に設定されています | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | 診断ログのカテゴリ                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | 特定の操作を追跡するための一意の ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | ADF の名前                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | SSIS IR の名前                                           | `MySSISIR` |
| **level**                    | String | 診断ログのレベル                                       | `Informational` |
| **executionId**              | String | SSISDB での特定の実行を追跡するための一意の ID        | `1` (1 は、SSISDB への格納/T-SQL による呼び出しが行われて **いない** パッケージに関連する実行を示します) |
| **packageName**              | String | 実行されたパッケージ ファイルの名前                             | `MyPackage.dtsx` |
| **taskName**                 | String | 実行されたデータ フロー タスクの名前                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | データ フロー パスを追跡するための一意の ID                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | String | データ フロー パスの名前                                         | `ADO NET Source Output` |
| **sourceComponentName**      | String | データを送信するデータ フロー コンポーネントの名前                    | `SQLDB Table3` |
| **destinationComponentName** | String | データを受信するデータ フロー コンポーネントの名前                 | `Derived Column` |
| **rowsSent**                 | String | ソース コンポーネントによって送信された行数                        | `500` |
| **createdTime**              | String | 行の値が取得された時刻 (UTC 形式)                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | String | データ フロー タスクの実行パス                           | `\Transformation\Data Flow Task` |
| **resourceId**               | String | ADF リソースの一意の ID                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics のスキーマ

Log Analytics は、Monitor からスキーマを継承します。ただし、次の例外があります。

* 各列名の最初の文字は、大文字になります。 たとえば、Monitor の列名 "correlationId" は、Log Analytics では "CorrelationId" です。
* "Level" 列はありません。
* 動的な "properties" 列は、次の動的 JSON BLOB の種類として維持されます。

    | Azure Monitor の列 | Log Analytics の列 | Type |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | 動的 |
    | $.properties.Annotations | 注釈 | 動的 |
    | $.properties.Input | 入力 | 動的 |
    | $.properties.Output | 出力 | 動的 |
    | $.properties.Error.errorCode | ErrorCode | INT |
    | $.properties.Error.message | ErrorMessage | string |
    | $.properties.Error | エラー | 動的 |
    | $.properties.Predecessors | Predecessors | 動的 |
    | $.properties.Parameters | パラメーター | 動的 |
    | $.properties.SystemParameters | SystemParameters | 動的 |
    | $.properties.Tags | Tags | 動的 |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Azure Monitor による SSIS 操作の監視

SSIS ワークロードをリフト アンド シフトするには、次をサポートする [ADF で SSIS IR をプロビジョニング](./tutorial-deploy-ssis-packages-azure.md)できます。

- Azure SQL Database サーバーまたは Managed Instance をホストとする SSIS カタログ (SSISDB) にデプロイされたパッケージを実行する (プロジェクト デプロイ モデル)
- Azure SQL Managed Instance をホストとするファイル システム、Azure Files、SQL Server データベース (MSDB) のいずれかにデプロイされたパッケージを実行する (パッケージ デプロイ モデル)

プロビジョニングが完了すると、[Azure PowerShell を使うか、ADF ポータルの **[監視]** ハブで、SSIS IR の動作状態をチェック](./monitor-integration-runtime.md#azure-ssis-integration-runtime)できるようになります。 プロジェクト デプロイ モデルでは、SSIS パッケージ実行ログは SSISDB 内部テーブルまたはビューに格納されるため、SSMS などの指定されたツールを使用して、クエリ、分析、および視覚的な表示を実行できます。 パッケージ デプロイ モデルでは、SSIS パッケージ実行ログをファイル システムまたは Azure Files に CSV ファイルとして保存できますが、クエリ、分析、および視覚的な表示を実行する前に、指定された他のツールを使用して解析と処理を行う必要があります。

[Azure Monitor](../azure-monitor/data-platform.md) の統合により、SSIS IR 操作と SSIS パッケージ実行で生成されるすべてのメトリックとログを、Azure portal でクエリ、分析、および視覚的に表示できるようになりました。 さらに、それらに関するアラートを生成することもできます。

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>SSIS 操作の診断設定とワークスペースを構成する

SSIS IR 操作と SSIS パッケージ実行で生成されるすべてのメトリックとログを Azure Monitor に送信するには、[ADF の診断設定とワークスペースの構成](#configure-diagnostic-settings-and-workspace)を実行する必要があります。

### <a name="ssis-operational-metrics"></a>SSIS 操作のメトリック

SSIS 操作の[メトリック](../azure-monitor/essentials/data-platform-metrics.md)は、SSIS IR の開始および停止操作の状態と特定の時点での SSIS パッケージ実行の状態を示すパフォーマンス カウンターまたは数値です。 それらは、[Azure Monitor の ADF メトリック](#data-factory-metrics)の一部です。

Azure Monitor で ADF の診断設定とワークスペースを構成するときに、 _[AllMetrics]_ チェック ボックスをオンにすると、[Azure メトリックス エクスプローラーを使用した対話型分析](../azure-monitor/essentials/metrics-getting-started.md)、[Azure ダッシュボードでの表示](../azure-monitor/app/tutorial-app-dashboards.md)、および [準リアルタイムのアラート](../azure-monitor/alerts/alerts-metric.md)のために SSIS 操作のメトリックを使用できるようになります。

![設定に名前を付けてログ分析ワークスペースを選択する](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>SSIS 操作のアラート

ADF ポータルから SSIS 操作のメトリックに関するアラートを生成するには、[ADF の **[監視]** ハブの **[アラートとメトリック]** ページを選択し、示される詳細な手順に従います](./monitor-visually.md#alerts)。

![ADF ポータルから SSIS 操作のアラートを生成](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Azure portal から SSIS 操作のメトリックに関するアラートを生成するには、[Azure の **[監視]** ハブの **[アラート]** ページを選択し、示される詳細な手順に従います](#data-factory-alerts)。

![Azure portal から SSIS 操作のアラートを生成](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>SSIS 操作のログ

SSIS 操作の[ログ](../azure-monitor/logs/data-platform-logs.md)は、SSIS IR 操作と SSIS パッケージ実行によって生成されるイベントであり、特定された任意の問題に関する十分なコンテキストを提供するため、根本原因の分析に役立ちます。 

Azure Monitor で ADF の診断設定やワークスペースを構成するとき、関連する SSIS 操作ログを選択して、Azure Data Explorer に基づく Log Analytics にそれらを送信できます。 そこで、[豊富なクエリ言語を使用した分析](../azure-monitor/logs/log-query-overview.md)、[Azure ダッシュボードでの表示](../azure-monitor/app/tutorial-app-dashboards.md)、および[準リアルタイムのアラート](../azure-monitor/alerts/alerts-log.md)のためにそれらを使用できます。

![設定に名前を付けてログ分析ワークスペースを選択する](media/data-factory-monitor-oms/monitor-oms-image2.png)

Azure Monitor と Log Analytics の SSIS パッケージ実行ログのスキーマと内容は、SSISDB 内部テーブルまたはビューのスキーマに似ています。

| Azure Monitor のログのカテゴリ          | Log Analytics のテーブル                     | SSISDB 内部テーブルまたはビュー              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

SSIS 操作のログの属性とプロパティの詳細については、[ADF のための Azure Monitor と Log Analytics のスキーマ](#schema-of-logs-and-events)に関するページを参照してください。

選択された SSIS パッケージ実行ログは、呼び出しメソッドに関係なく、常に Log Analytics に送信されます。 たとえば、SSMS、SQL Server エージェント、またはその他の指定されたツールで T-SQL を使用して、ADF パイプラインでの Execute SSIS Package アクティビティのトリガーされたまたはデバッグの実行として、Azure 対応 SSDT のパッケージ実行を呼び出すことができます。

Logs Analytics で SSIS IR 操作ログのクエリを実行するとき、それぞれが `Start/Stop/Maintenance` と `Started/InProgress/Succeeded/Failed` に設定された **OperationName** プロパティと **ResultType** プロパティを使用できます。 

![Log Analytics での SSIS IR パッケージ操作ログのクエリの実行](media/data-factory-monitor-oms/log-analytics-query.png)

Logs Analytics で SSIS パッケージ実行ログのクエリを実行する場合、**OperationId**/**ExecutionId**/**CorrelationId** プロパティを使用してそれらを結合できます。 SSISDB への格納/T-SQL を使用した呼び出しが行われて **いない** パッケージに関連するすべての操作/実行の場合、**OperationId**/**ExecutionId** には常に `1` が設定されます。

![SSIS パッケージ実行ログに対する Log Analytics でのクエリの実行](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>次のステップ
[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)
