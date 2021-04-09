---
title: Azure アラートでのログ アラートに対する Webhook アクション
description: Webhook アクションを使用したログ アラート プッシュと使用可能なカスタマイズを構成する方法について説明します。
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 228193066c45421c4dddee1802aba1feed59e9c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042676"
---
# <a name="webhook-actions-for-log-alert-rules"></a>ログ アラート ルールの webhook アクション

[ログ アラート](alerts-log.md)は、[Webhook アクション グループの構成](./action-groups.md#webhook)をサポートします。 この記事では、使用できるプロパティと、カスタム JSON Webhook の構成方法について説明します。

> [!NOTE]
> 現在、JSON ベースの Webhook は API バージョン `2020-05-01-preview` ではサポートされていません。

> [!NOTE]
> Webhook 統合には、[共通アラート スキーマ](../alerts/alerts-common-schema.md)を使用することをお勧めします。 共通アラート スキーマには、Azure Monitor のすべてのアラート サービスで、1 つの拡張可能で統合されたアラート ペイロードを使用できる利点があります。 カスタム JSON ペイロードが定義されているログ アラート ルールの場合、共通スキーマを有効にすると、ペイロード スキーマが[ここ](../alerts/alerts-common-schema-definitions.md#log-alerts)に記載されたものに戻ります。 共通スキーマが有効なアラートには、アラートごとに 256 KB の上限サイズがあり、より大きいアラートには検索結果は含まれません。 検索結果が含まれていない場合は、`LinkToFilteredSearchResultsAPI` または `LinkToSearchResultsAPI` を使用して、Log Analytics API を使用してクエリ結果にアクセスする必要があります。

## <a name="webhook-payload-properties"></a>Webhook ペイロード プロパティ

Webhook アクションを使用して、1 つの HTTP POST 要求を呼び出せます。 呼び出されるサービスでは、Webhook がサポートされ、受信したペイロードの使用方法が認識される必要があります。

既定の Webhook アクション プロパティとそのカスタム JSON パラメーター名は次のとおりです。

| パラメーター | 変数 | 説明 |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |アラート ルールの名前。 |
| *Severity* |#severity |起動されたログ アラートに設定されている重大度。 |
| *AlertThresholdOperator* |#thresholdoperator |アラート ルールのしきい値演算子。 |
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
| *Alert Type*| #alerttype | 構成されたログ アラート ルールの種類であり、[メトリック測定または結果の数](./alerts-unified-log.md#measure)。|
| *WorkspaceID* |#workspaceid |Log Analytics ワークスペースの ID |
| *アプリケーション ID* |#applicationid |Application Insights アプリの ID。 |
| *サブスクリプション ID* |#subscriptionid |使用された Azure サブスクリプションの ID。 |

## <a name="custom-webhook-payload-definition"></a>カスタム Webhook ペイロード定義

上記のパラメーターを使用してカスタム JSON ペイロードを取得するには、 **[webhook 用のカスタム Json ペイロードを含む]** を使用できます。 追加のプロパティを生成することもできます。
たとえば、 *text* という名前の 1 つのパラメーターを含む次のカスタム ペイロードを指定できます。 この Webhook で呼び出すサービスでは、このパラメーターが想定されます。

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
カスタム Webhook の変数は、JSON エンクロージャ内で指定する必要があります。 たとえば、上記の Webhook の例で "#searchresultcount" を参照すると、アラートの結果に基づいて出力されます。

検索結果を含めるには、**IncludeSearchResults** をカスタム JSON の最上位レベルのプロパティとして追加します。 検索結果は JSON 構造体として含まれているため、カスタム定義フィールド内で結果を参照することはできません。 

> [!NOTE]
> **[webhook 用のカスタム Json ペイロードを含む]** オプションの横にある **[View Webhook]\(Webhook の表示\)** ボタンを使用すると、提供された内容のプレビューが表示されます。 これには実際のデータは含まれませんが、使用される JSON スキーマの見本です。 

## <a name="sample-payloads"></a>サンプル ペイロード
このセクションでは、ログ アラートの Webhook のサンプル ペイロードを示します。 サンプル ペイロードには、ペイロードが標準の場合とカスタムの場合の例が含まれます。

### <a name="log-alert-for-log-analytics"></a>Log Analytics のログ アラート
次のサンプル ペイロードは、Log Analytics に基づくアラートに使用される、標準 Webhook アクションに対するものです。

> [!NOTE]
> [重要度] フィールドの値は、[現在の scheduledQueryRules API](../alerts/alerts-log-api-switch.md) に[従来の Log Analytics Alert API](./api-alerts.md) から切り替えた場合に変更されます。

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

### <a name="log-alert-for-application-insights"></a>Application Insights のログ アラート
次のサンプル ペイロードは、Application Insights リソースに基づくログ アラートに使用されるときの、標準 Webhook に対するものです。
    
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

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>その他のリソース ログ (API バージョン `2020-05-01-preview` から) のログ アラート

> [!NOTE]
> 現時点では、API バージョン `2020-05-01-preview` とリソース中心のログ アラートに関して追加料金は発生しません。  プレビュー段階にある機能の価格は、後で発表され、課金が始まる前に通知されます。 通知期間後も新しい API バージョンとリソース中心ログ アラートを引き続き使用することを選択した場合は、該当する料金が適用されます。

次のサンプル ペイロードは、他のリソース ログ (ワークスペースと Application Insights を除く) に基づくアラートに使用されるときの、標準 Webhook に対するものです。

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>カスタム JSON ペイロードを使用したログ アラート
たとえば、アラート名と検索結果だけを含むカスタム ペイロードを作成するには、この構成を使用します。 

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
- [Azure アラートでのログ アラート](./alerts-unified-log.md)について学習します。
- [Azure でログ アラートを管理する](alerts-log.md)方法を理解します。
- [Azure でアクション グループ](./action-groups.md)を作成および管理します。
- [Application Insights](../logs/log-query-overview.md) についてさらに学習します。
- [ログ クエリ](../logs/log-query-overview.md)についてさらに学習します