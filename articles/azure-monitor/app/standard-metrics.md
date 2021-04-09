---
title: Azure Application Insights 標準メトリック | Microsoft Docs
description: この記事では Azure Application Insights のメトリックについて説明し、サポートされる集計方法およびディメンションを示します。
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 0a18088fa434efa76007607c067feec107bdae57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572351"
---
# <a name="application-insights-standard-metrics"></a>Application Insights 標準メトリック

標準メトリックは収集中に事前に集計され、クエリ時のパフォーマンスが優れています。 このことから、ダッシュボードやリアルタイム アラートで使用することをお勧めします。

## <a name="availability-metrics"></a>可用性のメトリック

可用性カテゴリのメトリックを使用すると、世界中の地点から観察された Web アプリケーションの正常性を確認できます。 このカテゴリにあるメトリックの使用を開始するには、[可用性テストを構成します](../app/monitor-web-app-availability.md)。

### <a name="availability-availabilityresultsavailabilitypercentage"></a>可用性 (availabilityResults/availabilityPercentage)
*可用性* メトリックは、問題が検出されなかった Web テストの実行の割合を示します。 可能な最小値は 0 で、これはすべての Web テストの実行が失敗したことを示します。 値 100 は、すべての Web テストの実行が検証条件に合格したことを意味します。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|---|---|---|
|パーセント|Average|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>可用性テスト継続時間 (availabilityResults/duration)

*可用性テスト継続時間* メトリックは、Web テストの実行にかかった時間を示します。 [複数ステップの Web テスト](../app/availability-multistep.md)の場合、メトリックには、すべてのステップの合計実行時間が反映されます。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|---|---|---|
|ミリ秒|平均、最小、最大|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>可用性テスト (availabilityResults/count)

*可用性テスト* のメトリックには、Azure Monitor によって実行される Web テストの数が反映されます。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|---|---|---|
|Count|Count|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>ブラウザー メトリック

ブラウザー メトリックは、実際のエンドユーザーのブラウザーから Application Insights JavaScript SDK によって収集されます。 これらは Web アプリでのユーザー エクスペリエンスに関する優れた分析情報を提供します。 通常、ブラウザー メトリックはサンプリングされません。つまり、サンプリングによって偏る可能性があるサーバー側のメトリックと比較して、使用状況の数値の精度が高くなります。

> [!NOTE]
> ブラウザー メトリックを収集するには、アプリケーションを [Application Insights JavaScript SDK](../app/javascript.md) でインストルメント化する必要があります。

### <a name="browser-page-load-time-browsertimingstotalduration"></a>ブラウザーのページ読み込み時間 (browserTimings/totalDuration)

|Unit of measure|サポートされる集計| サポートされるディメンション|
|---|---|---|
|ミリ秒|平均、最小、最大|なし|

### <a name="client-processing-time-browsertimingprocessingduration"></a>クライアントの処理時間 (browserTiming/processingDuration)

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
|ミリ秒|平均、最小、最大|なし|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>ページ読み込みのネットワーク接続時間 (browserTimings/networkDuration)

|Unit of measure|サポートされる集計| サポートされるディメンション|
|---|---|---|
|ミリ秒|平均、最小、最大|なし|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>受信側の応答時間 (browserTimings/receiveDuration)

|Unit of measure|サポートされる集計| サポートされるディメンション|
|---|---|---|
|ミリ秒|平均、最小、最大|なし|

### <a name="send-request-time-browsertimingssendduration"></a>送信要求時間 (browserTimings/sendDuration)

|Unit of measure|サポートされる集計| サポートされるディメンション|
|---|---|---|
|ミリ秒|平均、最小、最大|なし|

## <a name="failure-metrics"></a>エラー メトリック

**エラー** のメトリックには、処理要求、依存関係呼び出し、およびスローされた例外に関する問題が表示されます。

### <a name="browser-exceptions-exceptionsbrowser"></a>ブラウザーの例外 (exceptions/browser)

このメトリックは、ブラウザーで実行されているアプリケーション コードからスローされた例外の数を反映します。 メトリックには、```trackException()``` Application Insights API 呼び出しで追跡される例外のみが含まれます。

|Unit of measure|サポートされる集計 | サポートされるディメンション|
|---|---|---|---|
| Count | Count | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>依存関係呼び出しの失敗数 (dependencies/failed)

失敗した依存関係呼び出しの数。

|Unit of measure|サポートされる集計 | サポートされるディメンション |
|---|---|---|---|
|Count|Count| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>例外 (exceptions/count)

Application Insights に例外を記録するたびに、SDK の[trackexception () メソッド](../app/api-custom-events-metrics.md#trackexception)が呼び出されます。 例外メトリックは、ログに記録された例外の数を示します。

|Unit of measure|サポートされる集計 | サポートされるディメンション |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>失敗した要求 (requests/failed)

*失敗* としてマークされた追跡されたサーバー要求の数。 既定では、Application Insights SDK は HTTP 応答コード 5xx または 4xx を返した各サーバー要求を、失敗した要求として自動的にマークします。 このロジックをカスタマイズするには、[カスタムのテレメトリ初期化子](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)の要求テレメトリ項目の *success* プロパティを変更します。


|Unit of measure|サポートされる集計 | サポートされるディメンション |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>サーバーの例外 (exceptions/server)

このメトリックは、サーバーの例外の数を示します。

|Unit of measure|サポートされる集計 | サポートされるディメンション |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>パフォーマンス カウンター

**パフォーマンス カウンター** カテゴリのメトリックを使用して [、Application Insights](../app/performance-counters.md) によって収集されたシステム パフォーマンス カウンターにアクセスします。

### <a name="available-memory-performancecountersavailablememory"></a>使用可能なメモリ (performanceCounters/availableMemory)

|Unit of measure|サポートされる集計 | サポートされるディメンション |
|---|---|---|---|
|データに依存:メガバイト、ギガバイト|Average、Max、Min|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>例外レート (performanceCounters/exceptionRate)

|Unit of measure|サポートされる集計 | サポートされるディメンション |
|---|---|---|---|
| Count | Average、Max、Min | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 要求実行時間 (performanceCounters/requestExecutionTime)

|Unit of measure|サポートされる集計 | サポートされるディメンション |
|---|---|---|---|
| ミリ秒 | Average、Max、Min | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 要求率 (performanceCounters/requestsPerSecond)

|Unit of measure|サポートされる集計 | サポートされるディメンション |
|---|---|---|---|
| 1 秒あたりの要求数 | Average、Max、Min | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>アプリケーション キュー内の HTTP 要求 (performanceCounters/requestsInQueue)

|Unit of measure|サポートされる集計 | サポートされるディメンション |
|---|---|---|---|
| Count | Average、Max、Min | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>プロセス CPU (performanceCounters/processCpuPercentage)

このメトリックは、監視対象のアプリをホストしているプロセスによって消費されるプロセッサの容量の合計を示します。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
|パーセント|Average、Max、Min| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>プロセス IO 量 (performanceCounters/processIOBytesPerSecond)

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
|バイト/秒|平均、最小、最大|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>プロセス プライベート バイト (performanceCounters/processPrivateBytes)

監視対象プロセスによってデータに割り当てられた非共有メモリの量。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
|バイト|平均、最小、最大|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>プロセッサ時間 (performanceCounters/processorCpuPercentage)

監視対象のサーバー インスタンスで実行されている *すべての* プロセスによる CPU 使用量。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
|パーセント|平均、最小、最大|`Cloud role instance` |

>[!NOTE]
> プロセッサ時間メトリックは、Azure App Service でホストされているアプリケーションでは使用できません。 App Services でホストされている Web アプリケーションの CPU 使用率を追跡するには、[プロセス CPU](#process-cpu-performancecountersprocesscpupercentage) メトリックを使用します。

## <a name="server-metrics"></a>サーバー メトリック

### <a name="dependency-calls-dependenciescount"></a>依存関係呼び出し (dependencies/count)

このメトリックは、依存関係呼び出しの数に関連しています。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>依存関係の期間 (dependencies/duration)

このメトリックは、依存関係呼び出しの期間を示します。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
| 時刻 | 平均、最小、最大 | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>サーバー要求率 (requests/count)

このメトリックは、Web アプリケーションによって受信された受信サーバー要求の数を反映します。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
| Count | Average | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>サーバー要求 (requests/count)

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>サーバー応答時間 (requests/duration)

このメトリックは、サーバーが受信要求を処理するのにかかった時間を反映します。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
| 時刻 | 平均、最小、最大 | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>使用状況のメトリック

### <a name="page-view-load-time-pageviewsduration"></a>ページ ビューの読み込み時間 (pageViews/duration)

このメトリックは、PageView イベントの読み込みにかかった時間を示します。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
| 時刻 | 平均、最小、最大 | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>ページ ビュー (pageViews/count)

TrackPageView () Application Insights API でログに記録された PageView イベントの数。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
| Count | Count | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>トレース (traces/count)

TrackTrace () Application Insights API 呼び出しを使用してログに記録されたトレース ステートメントの数。

|Unit of measure|サポートされる集計|サポートされるディメンション|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>次のステップ

* [ログベースのメトリックと事前に集計されたメトリック](./pre-aggregated-metrics-log-metrics.md)に関する記事を確認する。
* [ログベースのメトリックのクエリと定義](../essentials/app-insights-metrics.md)。