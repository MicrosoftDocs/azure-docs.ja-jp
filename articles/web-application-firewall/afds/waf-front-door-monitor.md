---
title: Azure Web アプリケーション ファイアウォールの監視とログ記録
description: Front Door の監視とログ記録での Web アプリケーション ファイアウォール (WAF) について説明します
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84808949"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web アプリケーション ファイアウォールの監視とログ記録

Azure Web アプリケーション ファイアウォール (WAF) の監視とログ記録は、Azure Monitor および Azure Monitor ログのログ記録およびそれらとの統合を通じて提供されます。

## <a name="azure-monitor"></a>Azure Monitor

Front Door での WAF のログは、[Azure Monitor](../../azure-monitor/overview.md) と統合されます。 Azure Monitor により、WAF のアラートやログなどの診断情報を追跡できます。 Front Door リソース内での WAF の監視は、ポータルの **[診断]** タブから、または Azure Monitor サービスから直接、構成できます。

Azure portal から、Front Door リソースの種類に移動します。 左側の **[監視]** > **[メトリック]** タブから **WebApplicationFirewallRequestCount** を追加し、WAF 規則に一致する要求の数を追跡できます。 アクションの種類と規則の名前に基づいて、カスタム フィルターを作成できます。

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics ":::

## <a name="logs-and-diagnostics"></a>ログと診断

Front Door の WAF では、検出された脅威ごとに詳細なレポートが提供されます。 ログ記録は Azure Diagnostics ログに統合されており、json 形式でアラートが記録されます。 これらのログは、[Azure Monitor ログ](../../azure-monitor/insights/azure-networking-analytics.md)と統合できます。

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) では、すべての要求が記録されます。 FrontdoorWebApplicationFirewallLog では、次のスキーマを持つ WAF 規則と一致するすべての要求が記録されます。

| プロパティ  | 説明 |
| ------------- | ------------- |
|アクション|要求に対して実行されるアクション|
| ClientIp | 要求を行ったクライアントの IP アドレス。 要求に X-Forwarded-For ヘッダーがあった場合、ヘッダーフィールドからクライアント IP が選択されます。 |
| ClientPort | 要求を行ったクライアントの IP アドレス。 |
| 詳細|一致する要求に関する追加の詳細 |
|| matchVariableName: ヘッダー名など、一致した要求の http パラメーター名|
|| matchVariableValue: 一致をトリガーした値|
| Host | 一致した要求の host ヘッダー |
| ポリシー | 要求が一致した WAF ポリシーの名前。 |
| PolicyMode | WAF ポリシーの操作モード。 取り得る値は "Prevention" と "Detection" です |
| RequestUri | 一致した要求の完全な URI。 |
| RuleName | 要求が一致した WAF 規則の名前。 |
| SocketIp | WAF によって検出されたソース IP アドレス。 この IP アドレスは、いずれの要求ヘッダーとも関係なく、TCP セッションに基づいています。|
| TrackingReference | Front Door によって提供された要求を識別する一意の参照文字列。X-Azure-Ref ヘッダーとしてクライアントにも送信されます。 アクセス ログで特定の要求の詳細を検索するために必要です。 |

次のクエリ例では、ブロックされた要求についての WAF ログが返されます。

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

WAF ログに記録された要求の例を次に示します。

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

次のクエリ例では、AccessLogs のエントリが返されます。

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

アクセス ログに記録された要求の例を次に示します。

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>次のステップ

- [Front Door](../../frontdoor/front-door-overview.md) の詳細を確認する。
