---
title: Azure Application Insights のデータ モデル | Microsoft Docs
description: JSON の連続エクスポートからエクスポートされ、フィルターとして使用されるプロパティについて説明します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: mbullwin
ms.openlocfilehash: 12025dfb93bbcfc86ae301f8fb63e7ac74697cf2
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119274"
---
# <a name="application-insights-export-data-model"></a>Application Insights エクスポート データ モデル
次のテーブルは、 [Application Insights SDK](../../azure-monitor/app/app-insights-overview.md) からポータルに送信されるテレメトリのプロパティを一覧表示したものです。
[連続エクスポート](export-telemetry.md)からのデータ出力で、テレメトリのプロパティが表示されます。
テレメトリは[メトリック エクスプローラー](../../azure-monitor/app/metrics-explorer.md)と[診断検索](../../azure-monitor/app/diagnostic-search.md)のプロパティ フィルターでも表示されます。

注意する点:

* これらの表の `[0]` は、インデックスを挿入する必要がある、パス内の点を示しています。ただし、常に 0 とは限りません。
* 期間は 1/10 マイクロ秒なので、10000000 == 1 秒です。
* 日付と時刻は UTC で、ISO 形式 ( `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>例
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Context
テレメトリのすべての種類には、コンテキストのセクションが付いています。 これらのフィールドのすべてが各データ ポイントで送信されるわけではありません。

| Path | type | メモ |
| --- | --- | --- |
| context.custom.dimensions [0] |object [ ] |カスタム プロパティ パラメーターによって設定される、キーと値の文字列ペア。 キーの最大長は 100 で、値の最大長は 1024 です。 100 を超える一意の値で、プロパティを検索することはできますが、セグメント化には使用できません。 ikey あたり最大 200 キー。 |
| context.custom.metrics [0] |object [ ] |カスタム測定パラメーターと TrackMetrics によって設定される、キーと値のペア。 キーの最大長は 100 で、値は通常は数値です。 |
| context.data.eventTime |文字列 |UTC |
| context.data.isSynthetic |ブール値 |要求がボットまたは Web テストから送られてきました。 |
| context.data.samplingRate |number |ポータルに送信される、SDK によって生成されたテレメトリの割合。 範囲 0.0 ～ 100.0。 |
| context.device |object@ |クライアント デバイス |
| context.device.browser |文字列 |IE、Chrome、 ... |
| context.device.browserVersion |文字列 |Chrome 48.0、 ... |
| context.device.deviceModel |文字列 | |
| context.device.deviceName |文字列 | |
| context.device.id |文字列 | |
| context.device.locale |文字列 |en-GB、de-DE、 ... |
| context.device.network |文字列 | |
| context.device.oemName |文字列 | |
| context.device.os |文字列 | |
| context.device.osVersion |文字列 |ホスト OS |
| context.device.roleInstance |文字列 |サーバー ホストの ID |
| context.device.roleName |文字列 | |
| context.device.screenResolution |文字列 | |
| context.device.type |文字列 |PC、ブラウザー、 ... |
| context.location |オブジェクト |clientip から派生します。 |
| context.location.city |文字列 |わかっている場合、clientip から派生されます。 |
| context.location.clientip |文字列 |最後の 8 文字は 0 に匿名化されます。 |
| context.location.continent |文字列 | |
| context.location.country |文字列 | |
| context.location.province |文字列 |都道府県 |
| context.operation.id |文字列 |同じ操作 ID を持つ項目は、ポータルで関連項目として表示されます。 通常は、要求 ID です。 |
| context.operation.name |文字列 |URL または要求の名前 |
| context.operation.parentId |文字列 |入れ子になった関連項目を許可します。 |
| context.session.id |文字列 |同じソースからの操作のグループの ID。 30 分間操作が行われないと、セッションの終了が通知されます。 |
| context.session.isFirst |ブール値 | |
| context.user.accountAcquisitionDate |文字列 | |
| context.user.accountId |文字列 | |
| context.user.anonAcquisitionDate |文字列 | |
| context.user.anonId |文字列 | |
| context.user.authAcquisitionDate |文字列 |[認証されたユーザー](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |文字列 | |
| context.user.isAuthenticated |ブール値 | |
| context.user.storeRegion |文字列 | |
| internal.data.documentVersion |文字列 | |
| internal.data.id |文字列 | Application Insights に項目が取り込まれるときに割り当てられる一意の ID |

## <a name="events"></a>events
[TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)によって生成されたカスタム イベント。

| Path | type | メモ |
| --- | --- | --- |
| event [0] count |integer |100/([サンプリング](../../azure-monitor/app/sampling.md) レート)。 たとえば、4 =&gt; 25% です。 |
| event [0] name |文字列 |イベント名。  最大長 250。 |
| event [0] url |文字列 | |
| event [0] urlData.base |文字列 | |
| event [0] urlData.host |文字列 | |

## <a name="exceptions"></a>例外
サーバーおよびブラウザーの [例外](../../azure-monitor/app/asp-net-exceptions.md) をレポートします。

| Path | type | メモ |
| --- | --- | --- |
| basicException [0] assembly |文字列 | |
| basicException [0] count |integer |100/([サンプリング](../../azure-monitor/app/sampling.md) レート)。 たとえば、4 =&gt; 25% です。 |
| basicException [0] exceptionGroup |文字列 | |
| basicException [0] exceptionType |文字列 | |
| basicException [0] failedUserCodeMethod |文字列 | |
| basicException [0] failedUserCodeAssembly |文字列 | |
| basicException [0] handledAt |文字列 | |
| basicException [0] hasFullStack |ブール値 | |
| basicException [0] id |文字列 | |
| basicException [0] method |文字列 | |
| basicException [0] message |文字列 |例外メッセージ。 最大長 10k。 |
| basicException [0] outerExceptionMessage |文字列 | |
| basicException [0] outerExceptionThrownAtAssembly |文字列 | |
| basicException [0] outerExceptionThrownAtMethod |文字列 | |
| basicException [0] outerExceptionType |文字列 | |
| basicException [0] outerId |文字列 | |
| basicException [0] parsedStack [0] assembly |文字列 | |
| basicException [0] parsedStack [0] fileName |文字列 | |
| basicException [0] parsedStack [0] level |integer | |
| basicException [0] parsedStack [0] line |integer | |
| basicException [0] parsedStack [0] method |文字列 | |
| basicException [0] stack |文字列 |最大長 10k。 |
| basicException [0] typeName |文字列 | |

## <a name="trace-messages"></a>トレース メッセージ
[TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) および[ログ アダプター](../../azure-monitor/app/asp-net-trace-logs.md)によって送信されます。

| Path | type | メモ |
| --- | --- | --- |
| message [0] loggerName |文字列 | |
| message [0] parameters |文字列 | |
| message [0] raw |文字列 |ログ メッセージ。最大長は 10k。 |
| message [0] severityLevel |文字列 | |

## <a name="remote-dependency"></a>リモート依存関係
TrackDependency によって送信されます。 サーバーでの [依存関係の呼び出し](../../azure-monitor/app/asp-net-dependencies.md) とブラウザーでの AJAX の呼び出しのパフォーマンスおよび使用状況をレポートするために使用されます。

| Path | type | メモ |
| --- | --- | --- |
| remoteDependency [0] async |ブール値 | |
| remoteDependency [0] baseName |文字列 | |
| remoteDependency [0] commandName |文字列 |例: "home/index" |
| remoteDependency [0] count |integer |100/([サンプリング](../../azure-monitor/app/sampling.md) レート)。 たとえば、4 =&gt; 25% です。 |
| remoteDependency [0] dependencyTypeName |文字列 |HTTP、SQL、... |
| remoteDependency [0] durationMetric.value |number |呼び出しから依存関係による応答の完了までの時間 |
| remoteDependency [0] id |文字列 | |
| remoteDependency [0] name |文字列 |URL。 最大長 250。 |
| remoteDependency [0] resultCode |文字列 |HTTP の依存関係から |
| remoteDependency [0] success |ブール値 | |
| remoteDependency [0] type |文字列 |Http、Sql、... |
| remoteDependency [0] url |文字列 |最大長 2000 |
| remoteDependency [0] urlData.base |文字列 |最大長 2000 |
| remoteDependency [0] urlData.hashTag |文字列 | |
| remoteDependency [0] urlData.host |文字列 |最大長 200 |

## <a name="requests"></a>Requests
[TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)によって送信されます。 標準モジュールはこれを使用して、サーバーで測定されたサーバー応答時間をレポートします。

| Path | type | メモ |
| --- | --- | --- |
| request [0] count |integer |100/([サンプリング](../../azure-monitor/app/sampling.md) レート)。 例: 4 =&gt; 25%。 |
| request [0] durationMetric.value |number |要求の到着から応答までの時間。 1e7 == 1 秒 |
| request [0] id |文字列 |操作 ID |
| request [0] name |文字列 |GET/POST + URL ベース。  最大長 250 |
| request [0] responseCode |integer |クライアントに送信された HTTP 応答 |
| request [0] success |ブール値 |既定 == (responseCode &lt; 400) |
| request [0] url |文字列 |ホストを含まない |
| request [0] urlData.base |文字列 | |
| request [0] urlData.hashTag |文字列 | |
| request [0] urlData.host |文字列 | |

## <a name="page-view-performance"></a>ページ ビュー パフォーマンス
ブラウザーによって送信されます。 ユーザーが要求を開始してから表示が完了するまでの時間を測定します (非同期 AJAX 呼び出しを除外)。

コンテキストの値は、クライアント OS やブラウザーのバージョンを示します。

| Path | type | メモ |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |integer |HTML の取得終了からページの表示までの時間。 |
| clientPerformance [0] name |文字列 | |
| clientPerformance [0] networkConnection.value |integer |ネットワーク接続の確立に要した時間。 |
| clientPerformance [0] receiveRequest.value |integer |要求の送信の終了から応答での HTML の取得までの時間。 |
| clientPerformance [0] sendRequest.value |integer |HTTP 要求の送信に要した時間。 |
| clientPerformance [0] total.value |integer |要求の送信の開始からページの表示までの時間。 |
| clientPerformance [0] url |文字列 |この要求の URL |
| clientPerformance [0] urlData.base |文字列 | |
| clientPerformance [0] urlData.hashTag |文字列 | |
| clientPerformance [0] urlData.host |文字列 | |
| clientPerformance [0] urlData.protocol |文字列 | |

## <a name="page-views"></a>ページ ビュー
trackPageView() または [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Path | type | メモ |
| --- | --- | --- |
| view [0] count |integer |100/([サンプリング](../../azure-monitor/app/sampling.md) レート)。 たとえば、4 =&gt; 25% です。 |
| view [0] durationMetric.value |integer |trackPageView() で、または startTrackPage() - stopTrackPage() によって、オプションで設定される値。 clientPerformance 値と同じではありません。 |
| view [0] name |文字列 |ページのタイトル。  最大長 250 |
| view [0] url |文字列 | |
| view [0] urlData.base |文字列 | |
| view [0] urlData.hashTag |文字列 | |
| view [0] urlData.host |文字列 | |

## <a name="availability"></a>可用性
[可用性 Web テスト](../../azure-monitor/app/monitor-web-app-availability.md)をレポートします。

| Path | type | メモ |
| --- | --- | --- |
| availability [0] availabilityMetric.name |文字列 |可用性 |
| availability [0] availabilityMetric.value |number |1.0 または 0.0 |
| availability [0] count |integer |100/([サンプリング](../../azure-monitor/app/sampling.md) レート)。 たとえば、4 =&gt; 25% です。 |
| availability [0] dataSizeMetric.name |文字列 | |
| availability [0] dataSizeMetric.value |integer | |
| availability [0] durationMetric.name |文字列 | |
| availability [0] durationMetric.value |number |テストの実行時間 1e7==1 秒 |
| availability [0] message |文字列 |エラーの診断 |
| availability [0] result |文字列 |合格/不合格 |
| availability [0] runLocation |文字列 |HTTP 要求の geo ソース |
| availability [0] testName |文字列 | |
| availability [0] testRunId |文字列 | |
| availability [0] testTimestamp |文字列 | |

## <a name="metrics"></a>メトリック
TrackMetric() によって生成されます。

メトリック値は context.custom.metrics[0] にあります。

例: 

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>メトリック値について
メトリック値は、メトリック レポートでもそれ以外でも、標準オブジェクト構造で報告されます。 例: 

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

現在 (今後変更される可能性があります)、標準 SDK モジュールから報告されるすべての値は、`count==1` であり、`name` と `value` フィールドだけが有益です。 それ以外の状態になるのは、独自の TrackMetric 呼び出しを記述して、他のパラメーターを設定する場合だけです。

他のフィールドの目的は、SDK でメトリックを集計できるようにして、ポータルへのトラフィックを減らすことです。 たとえば、各メトリック レポートを送信する前に、連続するいくつかの測定値の平均を求めることができます。 その後、最小、最大、標準偏差、集計値 (合計または平均) を計算し、レポートで表される測定値の数をカウントに設定します。

上の表では、あまり使用されないフィールドである count、min、max、stdDev、sampledValue は省略しました。

テレメトリの量を削減する必要がある場合は、事前集計メトリックの代わりに、 [サンプリング](../../azure-monitor/app/sampling.md) を使用することもできます。

### <a name="durations"></a>期間
特記がない限り、期間は 1/10 マイクロ秒で表されます。そのため、10000000.0 は 1 秒を意味します。

## <a name="see-also"></a>関連項目
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [連続エクスポート](export-telemetry.md)
* [コード サンプル](export-telemetry.md#code-samples)
