---
title: "アクティビティ ログ アラートで使用される webhook スキーマの理解 | Microsoft Docs"
description: "アクティビティ ログ アラートがアクティブになったときに webhook URL に投稿される JSON のスキーマについて説明します。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 75c71bcd16573d4f4dd3377c623aa9b414aa3906
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Azure アクティビティ ログ アラートのための webhook
アクション グループの定義の一部として、アクティビティ ログ アラート通知を受信するように webhook エンドポイントを構成することができます。 webhook を使用すると、後処理やカスタム アクションのために、これらの通知を他のシステムにルーティングすることができます。 この記事では、webhook に対する HTTP POST のペイロードの概要について説明します。

アクティビティ ログ アラートについて詳しくは、「[アクティビティ ログ アラートの作成](monitoring-activity-log-alerts.md)」をご覧ください。

アクション グループについて詳しくは、[アクション グループを作成](monitoring-action-groups.md)する方法をご覧ください。

## <a name="authenticate-the-webhook"></a>webhook の認証
webhook は、認証のためにトークンベースの承認を使用することもできます。 webhook URI は `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`のようなトークン ID を使用して保存されます。

## <a name="payload-schema"></a>ペイロード スキーマ
POST 操作に含まれる JSON ペイロードは、ペイロードの data.context.activityLog.eventSource フィールドによって異なります。

###<a name="common"></a>一般
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
###<a name="administrative"></a>管理
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
###<a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                }
            }
        },
        "properties": {}
    }
}
```

サービス正常性通知のアクティビティ ログ アラートの特定のスキーマについて詳しくは、「[サービス正常性通知](monitoring-service-notifications.md)」をご覧ください。

その他のすべてのアクティビティ ログ アラートの特定のスキーマについて詳しくは、[Azure アクティビティ ログの概要](monitoring-overview-activity-logs.md)に関するページをご覧ください。

| 要素名 | Description |
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
| event |イベントに関するメタデータを含む要素。 |
| authorization |イベントのロールベースのアクセス制御プロパティ。 これらのプロパティには通常、action、role、scope が含まれます。 |
| カテゴリ |イベントのカテゴリ。 サポートされる値は Administrative、Alert、Security、ServiceHealth、Recommendation です。 |
| caller |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの電子メール アドレス。 一部のシステム呼び出しでは、null の場合があります。 |
| correlationId |通常は GUID (文字列形式)。 correlationId を含むイベントは、より大きな同じアクションに属し、通常は correlationId を共有します。 |
| eventDescription |イベントを説明する静的テキスト。 |
| eventDataId |イベントの一意識別子。 |
| eventSource |イベントを生成した Azure サービスまたはインフラストラクチャの名前。 |
| httpRequest |要求には通常、clientRequestId、clientIpAddress、および HTTP メソッド (たとえば PUT) が含まれます。 |
| level |Critical、Error、Warning、Informational、Verbose のいずれかの値。 |
| operationId |通常、単一の操作に対応する複数のイベントで共有される GUID。 |
| operationName |操作の名前。 |
| properties |イベントのプロパティ。 |
| status |文字列 をオンにします。 操作の状態。 一般的な値は Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus |通常、対応する REST 呼び出しの HTTP 状態コードが含まれます。 また、subStatus を説明する他の文字列を含めることもできます。 一般的な subStatus の値は、OK (HTTP 状態コード: 200)、Created (HTTP 状態コード: 201)、Accepted (HTTP 状態コード: 202)、No Content (HTTP 状態コード: 204)、Bad Request (HTTP 状態コード: 400)、Not Found (HTTP 状態コード: 404)、Conflict (HTTP 状態コード: 409)、Internal Server Error (HTTP 状態コード: 500)、Service Unavailable (HTTP 状態コード: 503)、Gateway Timeout (HTTP 状態コード: 504) です。 |

## <a name="next-steps"></a>次のステップ
* [アクティビティ ログについて詳しく学習します](monitoring-overview-activity-logs.md)。
* [Azure アラートで Azure Automation スクリプト (Runbook) を実行します](http://go.microsoft.com/fwlink/?LinkId=627081)。
* [ロジック アプリを使用して、Azure アラートから Twilio 経由で SMS を送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
* [ロジック アプリを使用して、Azure アラートから Slack メッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。
* [ロジック アプリを使用して、Azure アラートから Azure キューにメッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。 この例はメトリック アラートのためのものですが、変更を加えてアクティビティ ログ アラートで使用できます。

