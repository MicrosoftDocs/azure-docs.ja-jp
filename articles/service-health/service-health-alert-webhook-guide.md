---
title: webhook を使用して既存の問題管理システム用に Azure サービス正常性通知を構成する
description: 既存の問題管理システムに送られたサービス正常性イベントについて、個人用に設定された通知を取得します。
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 3/27/2018
ms.openlocfilehash: 3e9a564310d750e63c9cf81d19f698e75712d09a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067191"
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>webhook を使用して既存の問題管理システム用に正常性通知を構成する

この記事では、既存の通知システムに webhook を使用してデータを送信するようにサービス正常性アラートを構成する方法を示します。

最近では、ユーザーが Azure サービス インシデントの影響を受けた場合にテキスト メッセージまたは電子メール経由で通知を受け取るようにサービス正常性アラートを構成できるようになっています。
しかし、利用したい既存の外部通知システムの構築が済んでいる場合もあります。
このドキュメントでは、webhook ペイロードの最も重要な部分と、ユーザーに影響を与えるサービス上の問題が発生した場合に通知されるカスタム アラートを作成する方法について説明します。

構成済みの統合を使用する場合は、次の方法をご覧ください。
* [ServiceNow を使用してアラートを構成する](service-health-alert-webhook-servicenow.md)
* [PagerDuty を使用してアラートを構成する](service-health-alert-webhook-pagerduty.md)
* [OpsGenie を使用してアラートを構成する](service-health-alert-webhook-opsgenie.md)

### <a name="watch-an-introductory-video"></a>紹介ビデオを見る

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>サービス正常性の webhook ペイロードを使用してカスタム通知を構成する
独自のカスタム webhook 統合をセットアップする場合は、サービス正常性の通知時に送信される JSON ペイロードを解析する必要があります。

`ServiceHealth` webhook ペイロードの具体例については、[こちらの例](../azure-monitor/platform/activity-log-alerts-webhook.md)をご覧ください。

サービス正常性アラートを検出するには、`context.eventSource == "ServiceHealth"` を探します。 そこにあるプロパティのうち、取り込みに最も関連するプロパティは次のとおりです。
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-the-service-health-dashboard-for-an-incident"></a>インシデントに関する Service Health ダッシュボードへの直接リンクを作成する
Service Health ダッシュボードへの直接リンクをデスクトップまたはモバイルに作成するには、特別な URL を生成します。 `trackingId` と、`subscriptionId` の最初 3 桁と最後の 3 桁を使用して、次のような形式にします。
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

たとえば、`subscriptionId` が `bba14129-e895-429b-8809-278e836ecdb3` で、`trackingId` が `0DET-URB` である場合、Service Health URL は次のようになります。

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>level を使用して問題の重大度を検出する
ペイロードの `level` プロパティは、重要度が最低のものから最高のものの順に、`Informational`、`Warning`、`Error`、`Critical` のいずれかになります。

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>影響を受けるサービスを解析してインシデントの全体像を把握する
サービス正常性アラートは、複数のリージョンとサービスにまたがる問題について通知することがあります。 完全な詳細情報を取得するには、`impactedServices` の値を解析する必要があります。
この値に含まれているのは[エスケープされた JSON](https://json.org/) 文字列で、エスケープを解除すると、規則的に解析することができる別の JSON オブジェクトになります。

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

次のようになります:

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

これを見ると、オーストラリア東部と南東部の両方に "Alerts & Metrics" の問題があり、オーストラリア南東部には "App Service" の問題があることがわかります。


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 要求によって webhook 統合をテストする
1. 送信するサービス正常性のペイロードを作成します。 サービス正常性 webhook ペイロードの例については、「[Azure アクティビティ ログ アラートのための webhook](../azure-monitor/platform/activity-log-alerts-webhook.md)」を参照してください。

2. 次のような HTTP POST 要求を作成します。

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. `2XX - Successful` 応答を受け取るはずです。

4. [PagerDuty](https://www.pagerduty.com/) に移動して、統合が正常に設定されたことを確認します。

## <a name="next-steps"></a>次の手順
- [アクティビティ ログ アラート webhook スキーマ](../azure-monitor/platform/activity-log-alerts-webhook.md)を確認します。 
- [サービス正常性の通知](../azure-monitor/platform/service-notifications.md)について学習します。
- [アクション グループ](../azure-monitor/platform/action-groups.md)について学習します。