---
title: webhook を使用してクラシック メトリック アラートが Azure 以外のシステムに通知するように設定する
description: 他の Azure 以外のシステムに Azure メトリック アラートを再ルーティングする方法について説明します。
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 264f3eb042a3c29523ed93df93dfa6d45c00ae87
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465668"
---
# <a name="have-a-classic-metric-alert-notify-a-non-azure-system-using-a-webhook"></a>webhook を使用してクラシック メトリック アラートが Azure 以外のシステムに通知するように設定する
webhook を使用して、後処理やカスタム アクションのために、Azure アラート通知を他のシステムにルーティングすることができます。 アラートで webhook を使用して、SMS メッセージを送信するサービス、バグのログ記録、チャット/メッセージング サービスを介したチームへの通知、またはその他のさまざまなアクションに対して、アラートをルーティングできます。 

この記事では、Azure メトリック アラートで webhook を設定する方法について説明します。 また、webhook に対する HTTP POST のペイロードの概要についても説明します。 Azure アクティビティ ログ アラート (イベントでのアラート) の設定とスキーマについては、「[Azure アクティビティ ログ アラートでの webhook の呼び出し](alerts-log-webhook.md)」を参照してください。

Azure アラートでは、HTTP POST を使用して、JSON 形式のアラート コンテンツを、アラートの作成時に指定した webhook URI に送信します。 スキーマは、この記事の後半で定義されます。 この URI は、有効な HTTP または HTTPS エンドポイントである必要があります。 Azure では、アラートがアクティブ化された場合に要求ごとに 1 つのエントリがポストされます。

## <a name="configure-webhooks-via-the-azure-portal"></a>Azure Portal を介して webhook を構成する
webhook URI を追加または更新するには、[Azure Portal](https://portal.azure.com/) の **[Create/Update Alerts]\(アラートの作成/更新\)** に移動します。

![[アラート ルールの追加] ウィンドウ](./media/alerts-webhooks/Alertwebhook.png)

また、[Azure PowerShell コマンドレット](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts)、[クロスプラットフォーム CLI](../../azure-monitor/platform/cli-samples.md#work-with-alerts)、または [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) を使用して、webhook URI にポストするアラートを構成することもできます。

## <a name="authenticate-the-webhook"></a>webhook の認証
webhook は、トークンベースの承認を使用して認証できます。 webhook URI は、トークン ID を使用して保存されます。 次に例を示します。`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

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
| conditionType |Y |"Metric"、"Event" |2 つの種類のアラート (メトリックとイベント) がサポートされています。 メトリック アラートは、メトリックの条件に基づいています。 イベント アラートは、アクティビティ ログのイベントに基づいています。 この値を使用して、アラートがメトリックとイベントのどちらに基づいているかを確認できます。 |
| condition |Y | |**conditionType** の値に基づいて確認する特定のフィールド。 |
| metricName |メトリック アラートの場合 | |ルールによる監視対象を定義するメトリックの名前。 |
| metricUnit |メトリック アラートの場合 |"Bytes"、"BytesPerSecond"、"Count"、"CountPerSecond"、"Percent"、"Seconds" |メトリックで使用できる単位。 [使用できる値](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx)を参照してください。 |
| metricValue |メトリック アラートの場合 | |アラートの原因となったメトリックの実際の値。 |
| threshold |メトリック アラートの場合 | |アラートがアクティブ化されるしきい値。 |
| windowSize |メトリック アラートの場合 | |しきい値に基づいてアラート アクティビティを監視するために使用される期間。 値は 5 分～ 1 日の範囲で指定する必要があります。 値は ISO 8601 の期間の形式である必要があります。 |
| timeAggregation |メトリック アラートの場合 |"Average"、"Last"、"Maximum"、"Minimum"、"None"、"Total" |収集されたデータの経時的な結合方法。 既定値は Average です。 [使用できる値](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx)を参照してください。 |
| operator |メトリック アラートの場合 | |設定したしきい値と現在のメトリック データを比較するために使用される演算子。 |
| subscriptionId |Y | |Azure サブスクリプション ID です。 |
| resourceGroupName |Y | |影響を受けるリソースのリソース グループの名前。 |
| resourceName |Y | |影響を受けるリソースのリソース名。 |
| resourceType |Y | |影響を受けるリソースの種類。 |
| resourceId |Y | |影響を受けるリソースのリソース ID。 |
| resourceRegion |Y | |影響を受けるリソースのリージョンまたは場所。 |
| portalLink |Y | |ポータルのリソースの概要ページへの直接リンク。 |
| properties |N |省略可能 |イベントに関する詳細を含むキー/値のペアのセット。 たとえば、「 `Dictionary<String, String>` 」のように入力します。 properties フィールドは省略可能です。 カスタム UI またはロジック アプリベースのワークフローでは、ユーザーは、ペイロードを使用して渡すことのできるキー/値のペアを入力できます。 webhook URI 自体を (クエリ パラメーターとして) 使用して、カスタム プロパティを webhook に戻すこともできます。 |

> [!NOTE]
> **properties** フィールドは、[Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx) を使用してのみ設定できます。
>
>

## <a name="next-steps"></a>次の手順
* [Azure アラートと PagerDuty との統合](https://go.microsoft.com/fwlink/?LinkId=627080)のビデオでは、Azure アラートと webhook について説明します。
* [Azure アラートで Azure Automation スクリプト (Runbook) を実行する](https://go.microsoft.com/fwlink/?LinkId=627081)方法について説明します。
* [ロジック アプリを使用して、Azure アラートから Twilio 経由で SMS メッセージを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)方法について説明します。
* [ロジック アプリを使用して、Azure アラートから Slack メッセージを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)方法について説明します。
* [ロジック アプリを使用して、Azure アラートから Azure キューにメッセージを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)方法について説明します。

