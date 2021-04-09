---
title: Webhook を介して Azure Service Health の通知を送信する
description: サービス正常性イベントについて個人用に設定された通知を、既存の問題管理システムに送信します。
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: a70c4fcd01a1a95c598d980004ee60292a6cf24b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594608"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Webhook を使用して問題管理システム用に正常性通知を構成する

この記事では、既存の通知システムに Webhook を使用してデータを送信するように Azure Service Health アラートを構成する方法を示します。

Azure サービス インシデントの影響を受けた場合にテキスト メッセージまたはメール経由で通知を受け取るように Service Health アラートを構成することができます。

しかし、利用したい既存の外部通知システムが既に構成されている場合もあります。 この記事では、Webhook ペイロードの最も重要な部分について説明します。 また、関連するサービスの問題が発生したときに通知を受けられるようにカスタム アラートを作成する方法についても説明します。

構成済みの統合を使用する場合は、以下を参照してください。
* [ServiceNow を使用してアラートを構成する](service-health-alert-webhook-servicenow.md)
* [PagerDuty を使用してアラートを構成する](service-health-alert-webhook-pagerduty.md)
* [OpsGenie を使用してアラートを構成する](service-health-alert-webhook-opsgenie.md)

**紹介ビデオを視聴する:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Service Health の Webhook ペイロードを使用してカスタム通知を構成する
独自のカスタム Webhook 統合を設定するには、Service Health の通知を介して送信される JSON ペイロードを解析する必要があります。

Webhook ペイロードの[例](../azure-monitor/alerts/activity-log-alerts-webhook.md) `ServiceHealth` を参照してください。

それがサービス正常性アラートであることを確認するには、`context.eventSource == "ServiceHealth"` を探します。 以下のプロパティが最も関連性があります。
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>インシデントに関する Service Health ダッシュボードへのリンクを作成する
ご利用の Service Health ダッシュボードへの直接リンクをデスクトップまたはモバイル上に作成するには、特別な URL を生成します。 *trackingId* と、ご利用の *subscriptionId* の最初の 3 桁と最後の 3 桁を次の形式で使用します。

https <i></i>://app.azure.com/h/ *&lt;trackingId&gt;* / *&lt;subscriptionId の最初の 3 桁と最後の 3 桁&gt;*

たとえば、ご利用の *subscriptionId* が bba14129-e895-429b-8809-278e836ecdb3 であり、ご利用の *trackingId* が 0DET-URB の場合、Service Health の URL は次のようになります。

https<i></i>://app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>level を使用して問題の重大度を検出する
ペイロードの **level** プロパティは、*Informational*、*Warning*、*Error*、または *Critical* とすることができます (最低の重大度から順に最高の重大度まで)。

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>影響を受けるサービスを解析してインシデントの範囲を特定する
Service Health アラートによって通知される内容は、複数のリージョンおよびサービスにまたがる問題である場合もあります。 完全な詳細情報を取得するには、`impactedServices` の値を解析する必要があります。

その内部にあるコンテンツは、エスケープされた [JSON](https://json.org/) 文字列です。エスケープされていない場合は、規則的に解析することができる別の JSON オブジェクトが含まれます。 次に例を示します。

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

次のようになります。

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

この例では、次の問題を示します。
- オーストラリア東部とオーストラリア南東部での "アラートとメトリック"。
- オーストラリア南東部の "App Service"。

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 要求によって Webhook 統合をテストする

次の手順に従います。

1. 送信するサービス正常性のペイロードを作成します。 サービス正常性 Webhook ペイロードの例については、「[Azure アクティビティ ログ アラートのための Webhook](../azure-monitor/alerts/activity-log-alerts-webhook.md)」を参照してください。

1. 次のような HTTP POST 要求を作成します。

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   "2XX - Successful" という応答が表示されます。

1. [PagerDuty](https://www.pagerduty.com/) に移動して、統合が正常に設定されたことを確認します。

## <a name="next-steps"></a>次のステップ
- [アクティビティ ログ アラート webhook スキーマ](../azure-monitor/alerts/activity-log-alerts-webhook.md)を確認します。 
- [サービス正常性の通知](./service-notifications.md)について学習します。
- [アクション グループ](../azure-monitor/alerts/action-groups.md)について学習します。
