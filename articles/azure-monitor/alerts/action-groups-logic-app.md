---
title: Azure Monitor アラートによって複雑なアクションをトリガーする方法
description: Azure Monitor アラートを処理するためのロジック アプリのアクションを作成する方法を説明します。
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: a1371e00a6d4c5db609466e25c9d94aad5e73398
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045719"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Azure Monitor アラートによって複雑なアクションをトリガーする方法

この記事では、アラート発生時に Microsoft Teams で会話を作成するために、ロジック アプリをセットアップおよびトリガーする方法を示します。

## <a name="overview"></a>概要

Azure Monitor アラートは、トリガー時に[アクション グループ](./action-groups.md)を呼び出します。 アクション グループを使用すると、アラートについて他ユーザーに通知したり、アラートを修復したりするための 1 つまたは複数のアクションをトリガーできます。

一般的なプロセスは次のとおりです:

-   それぞれのアラートの種類に対応したロジック アプリを作成する。

-   それぞれのアラートの種類のサンプル ペイロードをロジック アプリにインポートする。

-   ロジック アプリの動作を定義する。

-   ロジック アプリの HTTP エンドポイントを Azure アクション グループにコピーする。

ロジック アプリで別のアクションを実行させる場合も、プロセスは同様です。

## <a name="create-an-activity-log-alert-administrative"></a>アクティビティ ログ アラートを作成する:管理

1. [ロジック アプリの作成](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md)

2.  次のトリガーを選択します:**HTTP 要求の受信時**。

1. **[HTTP 要求の受信時]** のダイアログで、 **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

    ![[HTTP 要求の受信時] ダイアログ ボックスのスクリーンショット。[サンプルのペイロードを使用してスキーマを生成する] オプションが選択されています。 ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

3.  次のサンプル ペイロードをコピーしてダイアログ ボックスに貼り付けます。

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. **ロジック アプリ デザイナー** により、ロジック アプリに送信する要求の **Content-Type** ヘッダーを **application/json** に設定する必要があることを示すポップアップ ウィンドウが表示されます。 ポップアップ ウィンドウを閉じます。 Azure Monitor アラートによってヘッダーが設定されます。

    ![Content-Type ヘッダーの設定](media/action-groups-logic-app/content-type-header.png "Content-Type ヘッダーの設定")

10. **[+** **新しいステップ]** 、 **[アクションの追加]** の順に選択します。

    ![[アクションの追加]](media/action-groups-logic-app/add-action.png "アクションを追加する")

11. Microsoft Teams コネクタを検索して選択します。 **[Microsoft Teams – メッセージの投稿]** アクションを選択します。

    ![Microsoft Teams アクション](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft Teams アクション")

12. Microsoft Teams アクションを構成します。 **Logic Apps デザイナー** により、職場または学校のアカウントを認証するよう求められます。 メッセージ送信先の **[Team Id] (チーム ID)** と **[Channel Id] (チャネル ID)** を選択します。

13. 静的テキストと、動的コンテンツ内の \<fields\> への参照の組み合わせを使用してメッセージを構成します。 次のテキストをコピーして **[メッセージ]** フィールドに貼り付けます。

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    次に、\<fields\> を検索して、同じ名前の動的コンテンツ タグに置き換えます。

    > [!NOTE]
    > **status** という名前の 2 つの動的フィールドがあります。 その両方のフィールドをメッセージに追加します。 **activityLog** プロパティ バッグにあるフィールドを使用し、もう一方のフィールドを削除します。 **status** フィールドにカーソルを重ねると、次のスクリーンショットに示すように、完全修飾のフィールド参照が表示されます

    ![Microsoft Teams アクション:メッセージを投稿する](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams アクション:メッセージの投稿")

14. **Logic Apps デザイナー** の上部にある **[保存]** を選択して、ロジック アプリを保存します。

15. 既存のアクション グループを開き、ロジック アプリを参照するためのアクションを追加します。 既存のアクション グループがない場合は、「[Azure portal でのアクション グループの作成および管理](./action-groups.md)」を参照してアクション グループを作成します。 忘れずに変更を保存してください。

    ![アクション グループの更新](media/action-groups-logic-app/update-action-group.png "アクション グループの更新")

アラートが次にアクション グループを起動するときに、ロジック アプリが呼び出されます。

## <a name="create-a-service-health-alert"></a>サービス正常性アラートの作成

Azure Service Health エントリは、アクティビティ ログの一部です。 アラートの作成プロセスは[アクティビティ ログ アラートの作成](#create-an-activity-log-alert-administrative)プロセスと似ていますが、いくつかの点が異なります。

- 手順 1 から 3 は同じです。
- 手順 4 で、HTTP 要求トリガーに次のサンプル ペイロードを使用します。

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{\"ImpactedRegions\"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  手順 5 と 6 は同じです。
-  手順 7 から 11 では、次の手順に従います。

   1. **[+** **新しいステップ]** 、 **[条件の追加]** の順に選択します。 次の条件を設定して、入力データが下の値と一致する場合にのみロジック アプリが実行されるようにします。  テキスト ボックスにバージョンの値を入力するときに、数値型ではなく文字列として評価されるように引用符で囲みます ("0.1.1")。  ページに戻ったとき、基になるコードが引き続き文字列型の場合、引用符は表示されません。   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Service Health ペイロードの条件"](media/action-groups-logic-app/service-health-payload-condition.png "Service Health ペイロードの条件")

   1. **[true の場合]** 条件で、「[アクティビティ ログ アラートの作成](#create-an-activity-log-alert-administrative)」の手順 11 から 13 に従って、Microsoft Teams アクションを追加します。

   1. HTML と動的コンテンツの組み合わせを使用してメッセージを定義します。 次のコンテンツをコピーして **[メッセージ]** フィールドに貼り付けます。 `[incidentType]`、`[trackingID]`、`[title]`、`[communication]` の各フィールドを同じ名前の動的コンテンツ タグに置き換えます。

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Service Health の true 条件後のアクション"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Service Health の true 条件後のアクション")

   1. **[false の場合]** 条件には有用なメッセージを指定します

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Service Health の false 条件後のアクション"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Service Health の false 条件後のアクション")

- 手順 15 は同じです。 指示に従ってロジック アプリを保存し、アクション グループを更新します

## <a name="create-a-metric-alert"></a>メトリック アラートの作成

メトリック アラートの作成プロセスは[アクティビティ ログ アラートの作成](#create-an-activity-log-alert-administrative)プロセスと似ていますが、いくつかの点が異なります。

- 手順 1 から 3 は同じです。
- 手順 4 で、HTTP 要求トリガーに次のサンプル ペイロードを使用します。

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- 手順 5 と 6 は同じです。
- 手順 7 から 11 では、次の手順に従います。

  1. **[+** **新しいステップ]** 、 **[条件の追加]** の順に選択します。 次の条件を設定して、入力データが下の値と一致する場合にのみロジック アプリが実行されるようにします。 テキスト ボックスにバージョンの値を入力するときに、数値型ではなく文字列として評価されるように引用符で囲みます ("2.0")。  ページに戻ったとき、基になるコードが引き続き文字列型の場合、引用符は表示されません。 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["メトリック アラート ペイロードの条件"](media/action-groups-logic-app/metric-alert-payload-condition.png "メトリック アラート ペイロードの条件")

  1. **[true の場合]** 条件で、**For each** ループと Microsoft Teams アクションを追加します。 HTML と動的コンテンツの組み合わせを使用してメッセージを定義します。

      !["メトリック アラートの true 条件後のアクション"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "メトリック アラートの true 条件後のアクション")

  1. **[false の場合]** 条件で、メトリック アラートがロジック アプリの期待に一致しないことを知らせる Microsoft Teams アクションを定義します。 JSON ペイロードを含めます。 `json()` 式で `triggerBody` 動的コンテンツを参照する方法に注目してください。

      !["メトリック アラートの false 条件後のアクション"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "メトリック アラートの false 条件後のアクション")

- 手順 15 は同じです。 指示に従ってロジック アプリを保存し、アクション グループを更新します

## <a name="calling-other-applications-besides-microsoft-teams"></a>Microsoft Teams 以外のアプリケーションを呼び出す
Logic Apps には、幅広いアプリケーションやデータベースのアクションをトリガーできるさまざまなコネクタがあります。 Slack、SQL Server、Oracle、Salesforce はその一例です。 コネクタの詳細については、[Logic Apps コネクタ](../../connectors/apis-list.md)に関するページを参照してください。  

## <a name="next-steps"></a>次のステップ
* [Azure アクティビティ ログ アラートの概要](./alerts-overview.md)を把握し、アラートを受信する方法について学習します。  
* [Azure Service Health 通知の投稿に関するアラートを構成](../../service-health/alerts-activity-log-service-notifications-portal.md)する方法について学習します。
* [アクション グループ](./action-groups.md)について学習します。