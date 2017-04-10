---
title: "Azure メトリック アラートでの webhook の構成 | Microsoft Docs"
description: "他の Azure 以外のシステムに Azure アラートを再ルーティングします。"
author: kamathashwin
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 8b3ae540-1d19-4f3d-a635-376042f8a5bb
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: c0d101266fecf04a84b5717c1b81cefed90cab40
ms.openlocfilehash: 440bd939f0c7d235d7be210c7fee9f2bc122718c
ms.lasthandoff: 01/24/2017


---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Azure メトリック アラートでの webhook の構成
webhook を使用すると、後処理やカスタム アクションのために、Azure アラート通知を他のシステムにルーティングすることができます。 アラートで webhook を使用することで、SMS の送信、バグのログ記録、チャット/メッセージング サービスを介したチームへの通知、またはその他のさまざまなアクションを実行するサービスに、アラートをルーティングできます。 この記事では、Azure メトリック アラートで webhook を設定する方法のほか、webhook に対する HTTP POST のペイロードの内容について説明します。 Azure アクティビティ ログ アラート (イベントでのアラート) の設定とスキーマについては、 [こちらのページをご覧ください](insights-auditlog-to-webhook-email.md)。

Azure は、JSON 形式 (以下で定義されているスキーマ) でアラートに含まれている HTTP POST を、アラートの作成時に指定した webhook URI に送信します。 この URI は有効な HTTP または HTTPS エンドポイントである必要があります。 Azure では、アラートがアクティブ化された場合に要求ごとに&1; つのエントリがポストされます。

## <a name="configuring-webhooks-via-the-portal"></a>ポータルを使用して Webhook を構成する
[ポータル](https://portal.azure.com/)のアラートの作成/更新画面では、webhook URI を追加または更新できます。

![アラート ルールの追加](./media/insights-webhooks-alerts/Alertwebhook.png)

また、webhook URI にポストするアラートの構成には、[Azure PowerShell コマンドレット](insights-powershell-samples.md#create-alert-rules)、[クロスプラットフォーム CLI](insights-cli-samples.md#work-with-alerts)、[Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) のいずれかを使用できます。

## <a name="authenticating-the-webhook"></a>webhook の認証
webhook は、次の方法のいずれかを使用して認証できます。

1. **トークンベースの認証** - webhook URI は次のようなトークン ID を使用して保存されます。 `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **パスワードベースの基本認証** - webhook URI は次のようなユーザー名とパスワードを使用して保存されます。 `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>ペイロード スキーマ
POST 操作には、すべてのメトリックベースのアラートについて以下の JSON ペイロードとスキーマが含まれます。

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| フィールド | 必須 | 値の固定セット | メモ |
|:--- |:--- |:--- |:--- |
| status |Y |"Activated"、"Resolved" |設定した条件に基づいたアラートの状態。 |
| context |Y | |アラート コンテキスト。 |
| timestamp |Y | |アラートがトリガーされた時刻。 |
| id |Y | |すべてのアラート ルールには一意の ID があります。 |
| name |Y | |アラートの名前。 |
| description |Y | |アラートの説明。 |
| conditionType |Y |"Metric"、"Event" |2 つの種類のアラートがサポートされています。 1 つはメトリックの条件に基づき、もう&1; つはアクティビティ ログ内のイベントに基づきます。 この値を使用して、アラートがメトリックとイベントのどちらに基づいているかを確認できます。 |
| condition |Y | |conditionType に基づいて確認する特定のフィールド。 |
| metricName |メトリック アラートの場合 | |ルールによる監視対象を定義するメトリックの名前。 |
| metricUnit |メトリック アラートの場合 |"Bytes"、"BytesPerSecond"、"Count"、"CountPerSecond"、"Percent"、"Seconds" |メトリックで使用できる単位。 [使用できる値はこちらに記載されています](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx)。 |
| metricValue |メトリック アラートの場合 | |アラートの原因となったメトリックの実際の値。 |
| threshold |メトリック アラートの場合 | |アラートがアクティブ化されるしきい値。 |
| windowSize |メトリック アラートの場合 | |しきい値に基づいてアラート アクティビティを監視するために使用される期間。 5 分から 1 日の間である必要があります。 ISO 8601 期間形式。 |
| timeAggregation |メトリック アラートの場合 |"Average"、"Last"、"Maximum"、"Minimum"、"None"、"Total" |収集されたデータの経時的な結合方法。 既定値は Average です。 [使用できる値はこちらに記載されています](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx)。 |
| operator |メトリック アラートの場合 | |設定したしきい値と現在のメトリック データを比較するために使用される演算子。 |
| subscriptionId |Y | |Azure サブスクリプション ID。 |
| resourceGroupName |Y | |影響を受けるリソースのリソース グループの名前。 |
| resourceName |Y | |影響を受けるリソースのリソース名。 |
| resourceType |Y | |影響を受けるリソースの種類。 |
| resourceId |Y | |影響を受けるリソースのリソース ID。 |
| resourceRegion |Y | |影響を受けるリソースのリージョンまたは場所。 |
| portalLink |Y | |ポータルのリソースの概要ページへのリンク。 |
| properties |N |省略可能 |イベントの詳細を含む `<Key, Value>` ペア (つまり、`Dictionary<String, String>`) のセット。 properties フィールドは省略可能です。 カスタム UI またはロジック アプリベースのワークフローでは、ユーザーは、ペイロードを使用して渡すことのできるキーと値を入力できます。 Webhook URI 自体を (クエリ パラメーターとして) 使用して、カスタム プロパティを Webhook に戻すこともできます。 |

> [!NOTE]
> properties フィールドを設定できるのは、[Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) を使用した場合のみです。
>
>

## <a name="next-steps"></a>次のステップ
* [Azure アラートと PagerDuty との統合](http://go.microsoft.com/fwlink/?LinkId=627080)
* [Azure アラートで Azure Automation スクリプト (Runbook) を実行します](http://go.microsoft.com/fwlink/?LinkId=627081)
* [ロジック アプリを使用して、Azure アラートから Twilio 経由で SMS を送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* [ロジック アプリを使用して、Azure アラートから Slack メッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
* [ロジック アプリを使用して、Azure アラートから Azure キューにメッセージを送信します](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

