---
title: Azure アラートでのログ アラートに対する Webhook アクション
description: この記事では、Log Analytics ワークスペースまたは Application Insights を使用してログ アラート ルールを作成する方法、アラートでデータが HTTP Webhook としてプッシュされる方法、および可能なさまざまなカスタマイズの詳細について説明します。
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 3311819f021533a28a41daf2c2f08193218fae96
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075269"
---
# <a name="webhook-actions-for-log-alert-rules"></a>ログ アラート ルールの webhook アクション
[Azure でログ アラートを作成する](alerts-log.md)ときは、[アクション グループを使用して構成](action-groups.md)し、1 つ以上のアクションを実行することができます。 この記事では、使用できるさまざまな Webhook アクションについて説明し、JSON ベースのカスタム Webhook の構成方法を示します。

> [!NOTE]
> また、Webhook の統合に[共通アラート スキーマ](https://aka.ms/commonAlertSchemaDocs)を使用することもできます。 共通アラート スキーマの利点は、Azure Monitor のすべてのアラート サービスの垣根を越えて、拡張可能かつ一元化された単一のアラート ペイロードを実現できることです。共通アラート スキーマでは、ログ アラートのカスタム JSON オプションが優先されないことに注意してください。 アラート ルール レベルで行ったカスタマイズに関係なく共通のアラート スキーマ ペイロードが選択されている場合は、これに従います。 [共通アラート スキーマの定義については、こちらを参照してください。](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook アクション

Webhook アクションでは、1 つの HTTP POST 要求を使用して外部のプロセスを呼び出すことができます。 呼び出されるサービスでは、Webhook がサポートされ、受信したペイロードの使用方法が決定される必要があります。

webhook アクションには、次の表に示すプロパティが必要です。

| プロパティ | 説明 |
|:--- |:--- |
| **Webhook URL** |Webhook の URL。 |
| **Custom JSON payload (カスタム JSON ペイロード)** |アラート作成中にこのオプションが選択されたときに、Webhook と共に送信するカスタム ペイロード。 詳しくは、[ログ アラートの管理](alerts-log.md)に関するページをご覧ください。|

> [!NOTE]
> ログ アラートの **[webhook 用のカスタム Json ペイロードを含む]** オプションの横にある **[View Webhook]\(Webhook の表示\)** ボタンをクリックすると、指定されたカスタマイズ用のサンプル Webhook ペイロードが表示されます。 実際のデータは含まれませんが、ログ アラートに使用される JSON スキーマの見本です。 

Webhook には、URL と共に、外部のサービスに送信されるデータである JSON 形式のペイロードが含まれます。 既定では、ペイロードには次の表に示す値が格納されます。 このペイロードは、独自のカスタム ペイロードに置き換えることができます。 その場合は、各パラメーターに対して表に示される変数を使用して、カスタム ペイロードにそれらの値を含めます。


| パラメーター | 変数 | 説明 |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |アラート ルールの名前。 |
| *Severity* |#severity |起動されたログ アラートに設定されている重大度。 |
| *AlertThresholdOperator* |#thresholdoperator |より大きい、またはより小さいを使用する、アラート ルールのしきい値演算子。 |
| *AlertThresholdValue* |#thresholdvalue |アラート ルールのしきい値。 |
| *LinkToSearchResults* |#linktosearchresults |アラートを作成したクエリからのレコードを返す Analytics ポータルへのリンク。 |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |アラートを作成したクエリからのレコードを返す Analytics API へのリンク。 |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |アラートを作成したディメンション値の組み合わせでフィルター処理されたクエリからのレコードを返す Analytics ポータルへのリンク。 |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |アラートを作成したディメンション値の組み合わせでフィルター処理されたクエリからのレコードを返す Analytics API へのリンク。 |
| *ResultCount* |#searchresultcount |検索結果に含まれるレコードの数。 |
| *Search Interval End time* |#searchintervalendtimeutc |UTC でのクエリの終了時刻。フォーマットは mm/dd/yyyy HH:mm:ss AM/PM です。 |
| *Search Interval* |#searchinterval |アラート ルールの時間枠。フォーマットは HH:mm:ss です。 |
| *Search Interval StartTime* |#searchintervalstarttimeutc |UTC でのクエリの開始時刻。フォーマットは mm/dd/yyyy HH:mm:ss AM/PM です。 
| *SearchQuery* |#searchquery |アラート ルールで使用されるログ検索クエリ。 |
| *SearchResults* |"IncludeSearchResults": true|最初の 1,000 レコードに制限された、クエリによって JSON テーブルとして返されるレコード。 "IncludeSearchResults": true が、最上位レベルのプロパティとしてカスタム JSON Webhook 定義に追加されます。 |
| *Dimensions* |"IncludeDimensions": true|JSON セクションとしてそのアラートをトリガーしたディメンション値の組み合わせ。 "IncludeDimensions": true が、最上位レベルのプロパティとしてカスタム JSON Webhook 定義に追加されます。 |
| *Alert Type*| #alerttype | 構成されたログ アラート ルールの種類であり、[メトリック測定](alerts-unified-log.md#metric-measurement-alert-rules)または[結果の数](alerts-unified-log.md#number-of-results-alert-rules)。|
| *WorkspaceID* |#workspaceid |Log Analytics ワークスペースの ID |
| *アプリケーション ID* |#applicationid |Application Insights アプリの ID。 |
| *サブスクリプション ID* |#subscriptionid |使用された Azure サブスクリプションの ID。 

> [!NOTE]
> 指定されたリンクは、URL で *SearchQuery*、*Search Interval StartTime*、*Search Interval End time* などのパラメーターが Azure portal または API に渡されます。

たとえば、 *text*という名前の 1 つのパラメーターを含む次のカスタム ペイロードを指定できます。 この Webhook で呼び出すサービスでは、このパラメーターが想定されます。

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
この例のペイロードは、Webhook に送信されると、次のような内容に解決されます。

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
カスタム webhook 内のすべての変数は、"#searchinterval" のように JSON エンクロージャ内で指定する必要があるため、結果の webhook にも、エンクロージャ内に "00:05:00" のような変数データが含まれることになります。

カスタム ペイロードに検索結果を含めるには、**IncudeSearchResults** が JSON ペイロードの最上位レベルのプロパティとして設定されていることを確認します。 

## <a name="sample-payloads"></a>サンプル ペイロード
このセクションでは、ログ アラートの Webhook のサンプル ペイロードを示します。 サンプル ペイロードには、ペイロードが標準の場合とカスタムの場合の例が含まれます。

### <a name="standard-webhook-for-log-alerts"></a>ログ アラートの標準 Webhook 
以下のサンプルではどちらも、2 つの列と 2 つの行のみで構成されるダミー ペイロードが示されています。

#### <a name="log-alert-for-log-analytics"></a>Log Analytics のログ アラート
次のサンプル ペイロードは、Log Analytics に基づくアラートに使用される、"*カスタム JSON オプションが含まれていない*" 標準 Webhook アクションに対するものです。

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
 ```

> [!NOTE]
> Log Analytics でログ アラートに対する [API 設定](alerts-log-api-switch.md)を切り替えていた場合、"Severity" フィールドの値が変化することがあります。


#### <a name="log-alert-for-application-insights"></a>Application Insights のログ アラート
次のサンプル ペイロードは、Application Insights に基づくアラートに使用されるときの、"*カスタム JSON オプションが含まれていない*" 標準 Webhook に対するものです。
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>カスタム JSON ペイロードを使用したログ アラート
たとえば、アラート名と検索結果だけを含むカスタム ペイロードを作成するには、以下を使用できます。 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

以下は、どのログ アラートでも使用できるカスタム Webhook アクションのサンプル ペイロードです。
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>次のステップ
- [Azure アラートでのログ アラート](alerts-unified-log.md)について学習します。
- [Azure でログ アラートを管理する](alerts-log.md)方法を理解します。
- [Azure でアクション グループ](action-groups.md)を作成および管理します。
- [Application Insights](../log-query/log-query-overview.md) についてさらに学習します。
- [ログ クエリ](../log-query/log-query-overview.md)についてさらに学習します 
