---
title: Azure Alerts でのログ アラートのための webhook アクション | Microsoft Docs
description: この記事では、ログ分析ワークスペースまたは Application Insights を使用するログ アラート ルールでデータを HTTP webhook としてプッシュする方法と、さまざまなカスタマイズ例の詳細について説明します。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: cad1b0ab484d172000bd62146a88a27bfab1e9f2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448765"
---
# <a name="webhook-actions-for-log-alert-rules"></a>ログ アラート ルールの webhook アクション
[Azure でログ アラートを作成する](alerts-log.md)際に、1 つ以上のアクションを実行する[アクション グループの使用を構成する](action-groups.md)ことができます。  この記事では、使用できるさまざまな webhook アクションと、カスタム JSON ベース webhook の構成に関する詳細を示します。

> [!NOTE]
> [共通アラート スキーマ](https://aka.ms/commonAlertSchemaDocs)を使用することもできます。このスキーマの利点は、Azure Monitor のすべてのアラート サービスの垣根を越えて、拡張可能かつ一元化された単一のアラート ペイロードによって Webhook の統合を実現できることです。 [共通アラート スキーマの定義については、こちらを参照してください。](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook アクション

Webhook アクションは、1 つの HTTP POST 要求を使用して外部のプロセスを呼び出すことができます。  呼び出されるサービスは、Webhook をサポートし、受信したペイロードの使用方法を決定できる必要があります。    

webhook アクションには、次の表に示すプロパティが必要です。

| プロパティ | 説明 |
|:--- |:--- |
| Webhook URL |Webhook の URL。 |
| Custom JSON payload (カスタム JSON ペイロード) |アラート作成中にこのオプションを選択するときに、webhook と共に送信するカスタム ペイロード。 詳細については、[ログ アラートの管理](alerts-log.md)に関するページをご覧ください。 |

> [!NOTE]
> ログ アラートの *[webhook 用のカスタム JSON ペイロードを含む]* オプションの横にある [View Webhook]\(Webhook の表示\) をクリックすると、指定されたカスタマイズ用のサンプル Webhook ペイロードが表示されます。 この呼び出しには、ログ アラートに使用される実際のデータや JSON スキーマの表現は含まれません。 

Webhook には、URL と共に、外部のサービスに送信されるデータである JSON 形式のペイロードが含まれます。  既定では、ペイロードには次の表に示す値が格納されます。このペイロードは、独自のカスタム ペイロードに置き換えることができます。  その場合は、各パラメーターに対して表に示される変数を使用して、カスタム ペイロードにそれらの値を含めることができます。


| パラメーター | 変数 | 説明 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |アラート ルールの名前。 |
| Severity |#severity |起動されたログ アラートに設定されている重大度。 |
| AlertThresholdOperator |#thresholdoperator |アラート ルールのしきい値演算子。  "*Greater than*" または "*Less than*" を使用できます。 |
| AlertThresholdValue |#thresholdvalue |アラート ルールのしきい値。 |
| LinkToSearchResults |#linktosearchresults |アラートを作成したクエリからのレコードを返す Analytics ポータルへのリンク。 |
| ResultCount |#searchresultcount |検索結果に含まれるレコードの数。 |
| Search Interval End time |#searchintervalendtimeutc |UTC でのクエリの終了時刻。フォーマットは mm/dd/yyyy HH:mm:ss AM/PM です。 |
| Search Interval |#searchinterval |アラート ルールの時間枠。フォーマットは HH:mm:ss です。 |
| Search Interval StartTime |#searchintervalstarttimeutc |UTC でのクエリの開始時刻。フォーマットは mm/dd/yyyy HH:mm:ss AM/PM です。 
| SearchQuery |#searchquery |アラート ルールで使用されるログ検索クエリ。 |
| SearchResults |"IncludeSearchResults": true|クエリで JSON テーブルとして返されるレコード。上限は最初の 1,000 レコード。"IncludeSearchResults":true がカスタム JSON webhook 定義に最上位レベルのプロパティとして追加されている場合。 |
| アラートの種類| #alerttype | 構成されたログ アラート ルールの種類であり、[[メトリック測定]](alerts-unified-log.md#metric-measurement-alert-rules) または [[結果の数]](alerts-unified-log.md#number-of-results-alert-rules)。|
| WorkspaceID |#workspaceid |Log Analytics ワークスペースの ID |
| アプリケーション ID |#applicationid |Application Insight アプリの ID。 |
| サブスクリプション ID |#subscriptionid |使用された Azure サブスクリプションの ID。 

> [!NOTE]
> Analytics セクションで表示するために、LinkToSearchResults により、URL の SearchQuery、Search Interval StartTime、Search Interval EndTime などのパラメーターが Azure portal に渡されます。 Azure portal の URI サイズの上限は約 2,000 文字であり、パラメーター値がこの上限を超えると、アラートで指定されたリンクは "*開きません*"。 ユーザーは手動で詳細を入力して Analytics ポータルで結果を表示したり、[Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) または [Log Analytics REST API](/rest/api/loganalytics/) を使用して結果をプログラミングで取得したりできます。 

たとえば、 *text*という名前の 1 つのパラメーターを含む次のカスタム ペイロードを指定できます。  この Webhook で呼び出すサービスでは、このパラメーターが想定されます。

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
このセクションでは、ペイロードが標準の場合やカスタムの場合など、ログ アラートの webhook のサンプル ペイロードを紹介します。

### <a name="standard-webhook-for-log-alerts"></a>ログ アラートの標準 webhook 
以下のサンプルはどちらも、2 つの列と 2 つの行のみで構成されたダミー ペイロードを示しています。

#### <a name="log-alert-for-azure-log-analytics"></a>Azure Log Analytics のログ アラート
以下は、ログ分析ベースのアラートに使用される場合の、*カスタム JSON オプションが含まれていない*標準 webhook アクションのサンプル ペイロードです。

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
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
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> Log Analytics でログ アラートの [API 設定](alerts-log-api-switch.md)を切り替えていた場合、[重要度] フィールドの値が変化することがあります。


#### <a name="log-alert-for-azure-application-insights"></a>Azure Application Insights のログ アラート
以下は、Application Insights ベースのログ アラートに使用される場合の、*カスタム JSON オプションが含まれていない*標準 webhook のサンプル ペイロードです。
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
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
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>カスタム JSON ペイロードを使用したログ アラート
たとえば、アラート名と検索結果だけを含むカスタム ペイロードを作成するには、次のように入力します。 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

以下は、どのログ アラートでも使用できるカスタム webhook アクションのサンプル ペイロードです。
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
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


## <a name="next-steps"></a>次の手順
- [Azure アラートのログ アラート](alerts-unified-log.md)について学習します。
- [Azure のログ アラートの管理](alerts-log.md)について理解します
- [Azure でのアクション グループ](action-groups.md)の作成および管理
- [Application Insights](../../azure-monitor/app/analytics.md) についてさらに学習します。
- [ログ クエリ](../log-query/log-query-overview.md)についてさらに学習します 

