---
title: ServiceNow で webhook を使用して Azure サービス正常性アラートを構成する
description: ServiceNow インスタンスに送られたサービス正常性イベントについて、個人用に設定された通知を取得します。
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 06/10/2019
ms.openlocfilehash: e32a32e4961043e0cd967247c8c13420ca8a1969
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067120"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>ServiceNow で webhook を使用して Azure サービス正常性アラートを構成する

この記事では、webhook を使用して Azure サービス正常性アラートを ServiceNow と統合する方法について説明します。 ServiceNow インスタンスと webhook の統合を設定すると、Azure サービスの問題で影響を受ける場合に、既存の通知インフラストラクチャを通じてアラートを受け取ることになります。 Azure サービス正常性アラートが発生するたびに、ServiceNow のスクリプト化された REST API を介して webhook が呼び出されます。

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>ServiceNow でスクリプト化された REST API を作成する

1.  [ServiceNow](https://www.servicenow.com/) アカウントをサインアップ済みであることを確認した後、サインインします。

1.  ServiceNow の **[System Web Services]\(システム Web サービス\)** セクションに移動し、 **[Scripted REST APIs]\(スクリプト化された REST API\)** を選択します。

    ![ServiceNow の [Scripted Web Service]\(スクリプト化された Web サービス\) セクション](./media/webhook-alerts/servicenow-sws-section.png)

1.  **[New]\(新規\)** を選択して、スクリプト化された REST サービスを新しく作成します。
 
    ![ServiceNow の "新しいスクリプト化された REST API" のボタン](./media/webhook-alerts/servicenow-new-button.png)

1.  **[Name]\(名前\)** に REST API の名前を追加し、 **[API ID]** を `azureservicehealth` に設定します。

1.  **[Submit]\(送信\)** をクリックします。

    ![ServiceNow の "REST API 設定"](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  作成した REST API を選択し、 **[Resources]\(リソース\)** タブで **[New]\(新規\)** を選択します。

    ![ServiceNow の "リソース タブ"](./media/webhook-alerts/servicenow-resources-tab.png)

1.  新しいリソースの **[Name]\(名前\)** を `event` に設定し、 **[HTTP method]\(HTTP メソッド\)** を `POST` に変更します。

1.  **[Script]\(スクリプト\)** セクションで、次の JavaScript コードを追加します。

    >[!NOTE]
    >以下のスクリプトで、`<secret>`、`<group>`、`<email>` の値を更新する必要があります。
    >* `<secret>` は、GUID のようなランダムな文字列にします。
    >* `<group>` は、インシデントを割り当てる ServiceNow グループです。
    >* `<email>` は、インシデントを割り当てる特定のユーザーです (省略可能)。
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  セキュリティのタブで **[Requires authentication]\(認証が必要\)** チェック ボックスをオフにして、 **[Submit]\(送信\)** を選択します。 設定した `<secret>` が代わりにこの API を保護します。

    ![ServiceNow の [Requires authentication]\(認証が必要\) チェック ボックス](./media/webhook-alerts/servicenow-resource-settings.png)

1.  [Scripted REST APIs]\(スクリプト化された REST API\) セクションに戻って、新しい REST API の **[Base API Path]\(ベース API パス\)** を探します。

     ![ServiceNow の [Base API Path]\(ベース API パス\)](./media/webhook-alerts/servicenow-base-api-path.png)

1.  完全統合 URL は次のようになります。
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>ServiceNow を使用して Azure Portal でアラートを作成する
### <a name="for-a-new-action-group"></a>新しいアクション グループの場合:
1. [この記事](../azure-monitor/platform/alerts-activity-log-service-notifications.md)の手順 1. ～ 8. に従って、新しいアクション グループでアラートを作成します。

1. **[アクション]** の一覧で以下を定義します。

    a. **[アクションの種類]:** *webhook*

    b. **詳細:** 先ほど保存した ServiceNow の**統合 URL**。

    c. **[名前]:** Webhook の名前、別名、または識別子。

1. 完了したら **[保存]** を選択して、アラートを作成します。

### <a name="for-an-existing-action-group"></a>既存のアクション グループの場合:
1. [Azure Portal](https://portal.azure.com/) で、 **[モニター]** を選択します。

1. **[設定]** セクションで **[アクション グループ]** を選択します。

1. 編集するアクション グループを見つけて選択します。

1. **[アクション]** の一覧に以下を追加します。

    a. **[アクションの種類]:** *webhook*

    b. **詳細:** 先ほど保存した ServiceNow の**統合 URL**。

    c. **[名前]:** Webhook の名前、別名、または識別子。

1. 完了したら **[保存]** を選択して、アクション グループを更新します。

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 要求によって webhook 統合をテストする
1. 送信するサービス正常性のペイロードを作成します。 サービス正常性 webhook ペイロードの例については、「[Azure アクティビティ ログ アラートのための webhook](../azure-monitor/platform/activity-log-alerts-webhook.md)」を参照してください。

1. 次のような HTTP POST 要求を作成します。

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. "Incident created (インシデントが作成されました)" というメッセージと共に `200 OK` 応答を受信します。

1. [ServiceNow](https://www.servicenow.com/) に移動して、統合が正常に設定されたことを確認します。

## <a name="next-steps"></a>次の手順
- [既存の問題管理システム用の webhook 通知を構成する](service-health-alert-webhook-guide.md)方法について学習します。
- [アクティビティ ログ アラート webhook スキーマ](../azure-monitor/platform/activity-log-alerts-webhook.md)を確認します。 
- [サービス正常性の通知](../azure-monitor/platform/service-notifications.md)について学習します。
- [アクション グループ](../azure-monitor/platform/action-groups.md)について学習します。