---
title: アクティビティ ログ アラートで使用される webhook スキーマについて理解する
description: アクティビティ ログ アラートがアクティブになったときに webhook URL に投稿される JSON のスキーマについて説明します。
ms.topic: conceptual
ms.date: 03/31/2017
ms.openlocfilehash: 31b9f4b41d741475a031efd4392c7df2fd2260c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034338"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Azure アクティビティ ログ アラートのための webhook
アクション グループの定義の一部として、アクティビティ ログ アラート通知を受信するように webhook エンドポイントを構成することができます。 webhook を使用すると、後処理やカスタム アクションのために、これらの通知を他のシステムにルーティングすることができます。 この記事では、webhook に対する HTTP POST のペイロードの概要について説明します。

アクティビティ ログ アラートについて詳しくは、「[アクティビティ ログ アラートの作成](./activity-log-alerts.md)」をご覧ください。

アクション グループについて詳しくは、[アクション グループを作成](./action-groups.md)する方法をご覧ください。

> [!NOTE]
> [共通アラート スキーマ](./alerts-common-schema.md)を使用することもできます。このスキーマの利点は、Azure Monitor のすべてのアラート サービスの垣根を越えて、拡張可能かつ一元化された単一のアラート ペイロードによって Webhook の統合を実現できることです。 [共通アラート スキーマの定義については、こちらを参照してください。](./alerts-common-schema-definitions.md)


## <a name="authenticate-the-webhook"></a>webhook の認証
webhook は、認証のためにトークンベースの承認を使用することもできます。 webhook URI は `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`のようなトークン ID を使用して保存されます。

## <a name="payload-schema"></a>ペイロード スキーマ
POST 操作に含まれる JSON ペイロードは、ペイロードの data.context.activityLog.eventSource フィールドによって異なります。

> [!NOTE]
> 現時点では、アクティビティ ログ イベントの一部である説明は、発生した **"アラートの説明"** プロパティにコピーされます。
>
> アクティビティ ログのペイロードを他のアラートの種類に合わせるために、2021 年 4 月 1 日以降は、発生したアラートの **"説明"** プロパティにアラート ルールの説明が含まれるようになります。
>
> この変更に向けた準備として、アクティビティ ログの発生したアラートに、 **"Activity Log Event Description"\(アクティビティ ログ イベントの説明\)** という新しいプロパティが作成されました。 この新しいプロパティには、既に使用可能な **"説明"** プロパティが入力されます。 つまり、新しい **"Activity Log Event Description"\(アクティビティ ログ イベントの説明\)** フィールドには、アクティビティ ログ イベントの一部である説明が含まれます。
>
> 発生したアラートの **"説明"** プロパティを使用している可能性のあるアラート ルール、アクション ルール、Webhook、ロジック アプリ、またはその他の構成を確認し、 **"Activity Log Event Description"\(アクティビティ ログ イベントの説明\)** プロパティに置き換えてください。
>
> 現在、(アクション ルール、Webhook、ロジック アプリ、またはその他の構成の) 状態が、アクティビティ ログ アラートの **"説明"** プロパティに基づいている場合は、代わりに **"Activity Log Event Description"\(アクティビティ ログ イベントの説明\)** プロパティに基づくように変更することが必要な場合があります。
>
> 新しい **"説明"** プロパティに入力するために、アラート ルールの定義に説明を追加できます。
> ![発生したアクティビティ ログ アラート](media/activity-log-alerts-webhook/activity-log-alert-fired.png)

### <a name="common"></a>共通

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>管理

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Security

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>推奨

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

サービス正常性通知のアクティビティ ログ アラートの特定のスキーマについて詳しくは、「[サービス正常性通知](../../service-health/service-notifications.md)」をご覧ください。 また、[既存の問題管理ソリューションでサービス正常性の webhook 通知を構成する](../../service-health/service-health-alert-webhook-guide.md)方法についてご確認ください。

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| 要素名 | 説明 |
| --- | --- |
| status |メトリック アラートで使用されます。 アクティビティ ログ アラートでは常に "activated" に設定されます。 |
| context |イベントのコンテキスト。 |
| resourceProviderName |影響を受けるリソースのリソース プロバイダー。 |
| conditionType |常に "Event" です。 |
| name |アラート ルールの名前。 |
| id |アラートのリソース ID。 |
| description |アラートの作成時に設定したアラートの説明。 |
| subscriptionId |Azure サブスクリプション ID。 |
| timestamp |要求を処理した Azure サービスによってイベントが生成された時刻。 |
| resourceId |影響を受けるリソースのリソース ID。 |
| resourceGroupName |影響を受けるリソースのリソース グループの名前。 |
| properties |イベントの詳細を含む `<Key, Value>` ペア (つまり、`Dictionary<String, String>`) のセット。 |
| イベント |イベントに関するメタデータを含む要素。 |
| authorization |イベントの Azure ロールベースのアクセス制御プロパティ。 これらのプロパティには通常、action、role、scope が含まれます。 |
| category |イベントのカテゴリ。 サポートされる値は Administrative、Alert、Security、ServiceHealth、Recommendation です。 |
| caller |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの電子メール アドレス。 一部のシステム呼び出しでは、null の場合があります。 |
| correlationId |通常は GUID (文字列形式)。 correlationId を含むイベントは、より大きな同じアクションに属し、通常は correlationId を共有します。 |
| eventDescription |イベントを説明する静的テキスト。 |
| eventDataId |イベントの一意識別子。 |
| eventSource |イベントを生成した Azure サービスまたはインフラストラクチャの名前。 |
| httpRequest |要求には通常、clientRequestId、clientIpAddress、および HTTP メソッド (たとえば PUT) が含まれます。 |
| level |Critical、Error、Warning、または Informational のいずれかの値。 |
| operationId |通常、単一の操作に対応する複数のイベントで共有される GUID。 |
| operationName |操作の名前。 |
| properties |イベントのプロパティ。 |
| status |文字列 をオンにします。 操作の状態。 一般的な値は Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus |通常、対応する REST 呼び出しの HTTP 状態コードが含まれます。 また、subStatus を説明する他の文字列を含めることもできます。 一般的な subStatus の値は、OK (HTTP 状態コード: 200)、Created (HTTP 状態コード: 201)、Accepted (HTTP 状態コード: 202)、No Content (HTTP 状態コード: 204)、Bad Request (HTTP 状態コード: 400)、Not Found (HTTP 状態コード: 404)、Conflict (HTTP 状態コード: 409)、Internal Server Error (HTTP 状態コード: 500)、Service Unavailable (HTTP 状態コード: 503)、Gateway Timeout (HTTP 状態コード: 504) です。 |

その他のすべてのアクティビティ ログ アラートの特定のスキーマについて詳しくは、[Azure アクティビティ ログの概要](../essentials/platform-logs-overview.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
* [アクティビティ ログについて詳しく学習します](../essentials/platform-logs-overview.md)。
* [Azure アラートで Azure Automation スクリプト (Runbook) を実行します](https://go.microsoft.com/fwlink/?LinkId=627081)。
* [ロジック アプリを使用して、Azure アラートから Twilio 経由で SMS を送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
* [ロジック アプリを使用して、Azure アラートから Slack メッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
* [ロジック アプリを使用して、Azure アラートから Azure キューにメッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。