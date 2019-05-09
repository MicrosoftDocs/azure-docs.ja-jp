---
title: Azure Monitor でのログ データ インジェスト時間 | Microsoft Docs
description: Azure Monitor でのログ データ収集の待機時間に影響するさまざまな要因について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: ba9a0ab775e062f21a058b537e289fe3ea2b40bb
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000048"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Azure Monitor でのログ データ インジェスト時間
Azure Monitor とは、毎月増加するテラバイト単位のデータを送信する何千もの顧客にサービスを提供する高スケールのデータ サービスです。 ログ データが収集されてから、そのデータが使用可能になるまでにかかる時間について、よく質問されることがあります。 この記事では、この待機時間に影響するさまざまな要因について説明します。

## <a name="typical-latency"></a>一般的な待機時間
待機時間は、データが監視対象のシステムで作成される時間と Azure Monitor で分析に使用できるようになる時間を指します。 ログ データを取り込むための一般的な待機時間は 2 分から 5 分です。 特定のデータに対する具体的な待機時間は、次に説明するさまざまな要因によって異なります。


## <a name="factors-affecting-latency"></a>待機時間に影響する要因
特定のデータ セットの合計インジェスト時間は、次の高度な領域に分割することができます。 

- エージェント時間: イベントを検出し、それを収集して、ログ レコードとして Azure Monitor のインジェスト ポイントに送信する時間。 ほとんどの場合、このプロセスは、エージェントによって処理されます。
- パイプライン時間: ログ レコードを処理するインジェスト パイプラインの時間。 これには、イベントのプロパティを解析し、場合によっては計算済みの情報を追加する時間が含まれます。
- インデックス作成時間: ログ レコードを Azure Monitor のビッグ データ ストアに取り込むために費やされた時間。

このプロセスによるさまざまな待機時間の詳細を以下に示します。

### <a name="agent-collection-latency"></a>エージェント コレクションの待機時間
エージェントと管理ソリューションは、さまざまな方法を使用して仮想マシンからデータを収集します。これが待機時間に影響する場合があります。 具体的な例をいくつか次に示します。

- Windows イベント、syslog イベント、およびパフォーマンス メトリックは、すぐに収集されます。 Linux パフォーマンス カウンターは、30 秒間隔でポーリングされます。
- IIS ログとカスタム ログのタイムスタンプが変更されると、これらのログが収集されます。 IIS ログの場合、これは [IIS で構成されているロール オーバー スケジュール](data-sources-iis-logs.md)の影響を受けます。 
- Active Directory Assessment では Active Directory インフラストラクチャの評価が毎週実行されるのに対し、Active Directory Replication ソリューションでは評価が 5 日おきに実行されます。 エージェントは評価が完了したときにのみ、これらのログを収集します。

### <a name="agent-upload-frequency"></a>エージェントのアップロードの頻度
Log Analytics エージェントを軽量にするため、エージェントではログがバッファーに格納されて、定期的に Azure Monitor にアップロードされます。 アップロードの頻度は、データの型に応じて 30 秒から 2 分間の範囲で異なります。 ほとんどのデータは、1 分未満でアップロードされます。 ネットワークの状態は、このデータが Azure Monitor のインジェスト ポイントに達するまでの待機時間に悪影響を及ぼす可能性があります。

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Azure のアクティビティ ログ、診断ログ、およびメトリック
Azure データが Log Analytics のインジェスト ポイントで使用可能になるまでには、処理のための追加の時間がかかります。

- 診断ログのデータの場合、Azure サービスによって異なりますが 2 分から 15 分かかります。 ご利用の環境におけるこの待機時間を調べる方法については、[下のクエリ](#checking-ingestion-time)を参照してください。
- Azure プラットフォームのメトリックが Log Analytics のインジェスト ポイントに送信されるまでには 3 分かかります。
- アクティビティ ログ データが Log Analytics のインジェスト ポイントに送信されるまでには 約 10 分から 15 分かかります。

インジェスト ポイントで使用可能になったデータがクエリに使用できるようになるまでには、さらに 2 分から 5 分かかります。

### <a name="management-solutions-collection"></a>管理ソリューションのコレクション
一部のソリューションでは、エージェントからデータを収集せず、さらに待機時間がかかるコレクション メソッドを使用する場合があります。 一部のソリューションでは、ほぼリアルタイムでの収集を試みることなく、一定の間隔でデータを収集します。 具体的な例を次に示します。

- Office 365 ソリューションでは、Office 365 Management Activity API (現在はほぼリアルタイムの待機時間を保証していません) を使用して、アクティビティ ログがポーリングされます。
- Windows Analytics ソリューション (Update Compliance など) のデータは、毎日の頻度でソリューションによって収集されます。

各ソリューションのコレクションの頻度を判断するには、そのドキュメントを参照してください。

### <a name="pipeline-process-time"></a>パイプライン処理時間
ログ レコードは Azure Monitor パイプラインに取り込まれると、テナントが分離され、データが失われないように、一時的なストレージに書き込まれます。 通常、このプロセスにより、さらに 5 - 15 秒が加算されます。 一部の管理ソリューションでは、データのストリーミング時に、データを集計し、分析情報を派生させるため、より重いアルゴリズムが実装されます。 たとえば、ネットワーク パフォーマンス監視は、3 分間隔で受信データを集計するため、事実上 3 分間の待機時間が加算されます。 待機時間を増やす別のプロセスは、カスタム ログを処理するプロセスです。 場合によっては、このプロセスによって、エージェントがファイルから収集するログの待機時間が数分増えることがあります。

### <a name="new-custom-data-types-provisioning"></a>新しいカスタム データ型のプロビジョニング
新しいカスタム データの型を[カスタム ログ](data-sources-custom-logs.md)または[データ コレクター API](data-collector-api.md) から作成すると、システムにより専用のストレージ コンテナーが作成されます。 これは、最初にこのデータ型が出現したときにのみ発生する 1 回限りのオーバーヘッドです。

### <a name="surge-protection"></a>急激な増加の保護
Azure Monitor の最優先事項は、顧客データが失われることがないようにすることです。そのため、システムにはデータの急激な増加に対する保護が組み込まれています。 これには、膨大な負荷でもシステムが機能し続けられるようにするバッファーが含まれます。 通常の負荷では、これらのコントロールにより追加される待機時間は 1 分未満ですが、極端な状態で障害が発生した場合、データの安全を確保する一方で、膨大な時間が追加される場合があります。

### <a name="indexing-time"></a>インデックス作成時間
すべてのビッグ データ プラットフォームには、分析および高度な検索機能の提供と、データへの即時アクセスの提供の間のバランスを取る機能が、組み込まれています。 Azure Monitor を使用すると、何十億ものレコードに対して強力なクエリを実行し、数秒以内で結果を取得できます。 これを可能にしているのは、インフラストラクチャがデータ インジェスト中にデータを動的に変換して、それを一意のコンパクトな構造に格納しているからです。 システムは、これらの構造を作成するのに十分なデータ量になるまで、データをバッファーします。 これは、ログ レコードが検索結果に表示される前に完了する必要があります。

このプロセスは現在、データ量が少ない場合は約 5 分かかりますが、データ レートが高くなるほど時間は短くなります。 これは直感に反しているように思えますが、このプロセスにより、大量の運用環境のワークロードの待機時間の最適化が可能になります。



## <a name="checking-ingestion-time"></a>インジェスト時間のチェック
インジェスト時間は、さまざまな状況とリソースの種類によって変わる場合があります。 ログ クエリを使用すると、ご利用の環境の具体的な動作を把握することができます。

### <a name="ingestion-latency-delays"></a>インジェストの待ち時間
特定のレコードの待ち時間は、[ingestion_time()](/azure/kusto/query/ingestiontimefunction) 関数の結果を _[TimeGenerated]_ フィールドと比較することによって測定することができます。 このデータを、さまざまな集計と組み合わせて使用すれば、取り込みの待ち時間の動作を突き止めることができます。 インジェスト時間のパーセンタイルを観察すれば、大量のデータの分析情報が得られます。 

たとえば、次のクエリを実行すると、その日インジェスト時間が最小であったコンピューターを表示できます。 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc  
```
 
一定の期間にわたる特定のコンピューターのインジェスト時間をドリルダウンしたい場合は、次のクエリを使用します。ここではさらに、データをグラフで視覚化しています。 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart  
```
 
コンピューターのインジェスト時間を、それが置かれている (対応する IP アドレスに基づく) 国ごとに表示するには、次のクエリを使用します。 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by RemoteIPCountry 
```
 
エージェントから送信されるデータの種類が異なれば、インジェストの待ち時間も異なる可能性があるので、前出のクエリを他の種類のデータにも使用してみましょう。 各種 Azure サービスのインジェスト時間を観察するには、次のクエリを使用します。 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>応答していないリソース 
ときには、リソースからのデータ送信が停止することもあります。 リソースからデータが送信されているかどうかを把握するには、標準の _TimeGenerated_ フィールドで識別できる最新のレコードに着目します。  

エージェントからは、1 分間に 1 回ハートビートが送信されます。そこで、_Heartbeat_ テーブルを使用して VM の状態をチェックします。 アクティブなコンピューターのうち、ハートビートを最近レポートしていないコンピューターを一覧表示するには、次のクエリを使用します。 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>次の手順
* Azure Monitor の[サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) をお読みください。

