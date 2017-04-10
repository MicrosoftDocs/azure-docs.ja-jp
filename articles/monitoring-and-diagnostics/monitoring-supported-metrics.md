---
title: "Azure Monitor のメトリック - サポートされているメトリック (リソースの種類別) | Microsoft Docs"
description: "Azure Monitor で使用できるメトリックの一覧 (リソースの種類別) です。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/17/2017
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 354bf45625c209c22118804d3835ca71e3128580
ms.openlocfilehash: deda64fb779e176bb00c3256fa3028e7e3567eb4
ms.lasthandoff: 02/18/2017


---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor のサポートされるメトリック
Azure Monitor では、複数の方法を使用してメトリックを操作できます。たとえば、ポータルでメトリックをグラフ化したり、REST API でアクセスしたり、PowerShell や CLI を使ってクエリを実行したりできます。 ここで示しているのは、Azure Monitor のメトリック パイプラインで現在利用できるメトリックの一覧です。

> [!NOTE]
> 他のメトリックについては、ポータルや従来の API で使用できる場合があります。 この一覧には、統合された Azure Monitor メトリック パイプラインのパブリック プレビューで使用できる、パブリック プレビュー メトリックのみが含まれます。
> 
> 

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|メトリック|メトリックの表示名|単位|集計の種類|説明|
|---|---|---|---|---|
|qpu_metric|QPU|カウント|平均|QPU。 範囲は S1 で 0 ～ 100、S2 で 0 ～ 200、S4 で 0 ～ 400|
|memory_metric|メモリ|バイト|平均|メモリ。 範囲は S1 で 0 ～ 25 GB、S2 で 0 ～ 50 GB、S4 で 0 ～ 100 GB|
|TotalConnectionRequests|合計接続要求数|カウント|平均|合計接続要求数。 これらは到着した接続要求です。|
|SuccessfullConnectionsPerSec|成功した接続数 (秒単位)|CountPerSecond|平均|接続が正常に完了した割合。|
|TotalConnectionFailures|合計接続失敗数|カウント|平均|失敗した接続試行数の合計。|
|CurrentUserSessions|現在のユーザー セッション|カウント|平均|確立された現在のユーザー セッションの数。|
|QueryPoolBusyThreads|クエリ プール ビジー スレッド|カウント|平均|クエリ スレッド プールのビジー スレッドの数。|
|CommandPoolJobQueueLength|コマンド プールのジョブ キューの長さ|カウント|平均|コマンド スレッド プールのキュー内のジョブの数。|
|ProcessingPoolJobQueueLength|処理プール ジョブ キューの長さ|カウント|平均|処理スレッド プールのキュー内の非 I/O ジョブの数。|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|メトリック|メトリックの表示名|単位|集計の種類|説明|
|---|---|---|---|---|
|TotalRequests|ゲートウェイ要求の合計|カウント|合計|ゲートウェイ要求の数|
|SuccessfulRequests|成功したゲートウェイ要求|カウント|合計|成功したゲートウェイ要求の数|
|UnauthorizedRequests|未承認ゲートウェイ要求|カウント|合計|未承認ゲートウェイ要求の数|
|FailedRequests|失敗したゲートウェイ要求|カウント|合計|ゲートウェイ要求における失敗の数|
|OtherRequests|その他のゲートウェイ要求|カウント|合計|その他のゲートウェイ要求の数|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|
|---|---|---|---|---|
|CoreCount|コア数|カウント|合計|Batch アカウントの合計コア数|
|TotalNodeCount|ノード数|カウント|合計|Batch アカウントの合計ノード数|
|CreatingNodeCount|作成ノード数|カウント|合計|作成されるノードの数|
|StartingNodeCount|開始ノード数|カウント|合計|開始するノードの数|
|WaitingForStartTaskNodeCount|開始タスクの待機ノード数|カウント|合計|開始タスクの完了を待機しているノードの数|
|StartTaskFailedNodeCount|開始タスクの失敗ノード数|カウント|合計|開始タスクが失敗したノードの数|
|IdleNodeCount|アイドル状態のノード数|カウント|合計|アイドル状態のノードの数|
|OfflineNodeCount|オフライン ノード数|カウント|合計|オフライン ノードの数|
|RebootingNodeCount|再起動中のノード数|カウント|合計|再起動中のノードの数|
|ReimagingNodeCount|再イメージ化中のノード数|カウント|合計|再イメージ化中のノードの数|
|RunningNodeCount|実行中のノード数|カウント|合計|実行中のノードの数|
|LeavingPoolNodeCount|プールから移動中のノード数|カウント|合計|プールから移動中のノードの数|
|UnusableNodeCount|使用できないノード数|カウント|合計|使用できないノードの数|
|TaskStartEvent|タスク開始イベント|カウント|合計|開始したタスクの合計数|
|TaskCompleteEvent|タスク完了イベント|カウント|合計|完了したタスクの合計数|
|TaskFailEvent|タスク失敗イベント|カウント|合計|障害状態で完了したタスクの合計数|
|PoolCreateEvent|プール作成イベント|カウント|合計|作成されたプールの合計数|
|PoolResizeStartEvent|プールのサイズ変更の開始イベント|カウント|合計|開始したプールのサイズ変更の合計数|
|PoolResizeCompleteEvent|プールのサイズ変更の完了イベント|カウント|合計|完了したプールのサイズ変更の合計数|
|PoolDeleteStartEvent|プール削除の開始イベント|カウント|合計|開始したプール削除の合計数|
|PoolDeleteCompleteEvent|プール削除の完了イベント|カウント|合計|完了したプール削除の合計数|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|connectedclients|接続されているクライアント数|カウント|最大値||
|totalcommandsprocessed|合計処理数|カウント|合計||
|cachehits|キャッシュ ヒット数|カウント|合計||
|cachemisses|キャッシュ ミス数|カウント|合計||
|getcommands|取得数|カウント|合計||
|setcommands|設定数|カウント|合計||
|evictedkeys|削除されたキー数|カウント|合計||
|totalkeys|合計キー数|カウント|最大値||
|expiredkeys|期限切れキー数|カウント|合計||
|usedmemory|メモリ使用量|Bytes|最大値||
|usedmemoryRss|RSS メモリ使用量|Bytes|最大値||
|serverLoad|サーバーの負荷|Percent|最大値||
|cacheWrite|キャッシュの書き込み|BytesPerSecond|最大値||
|cacheRead|キャッシュの読み取り|BytesPerSecond|最大値||
|percentProcessorTime|CPU|Percent|最大値||
|connectedclients0|接続されているクライアント数 (シャード 0)|カウント|最大値||
|totalcommandsprocessed0|合計処理数 (シャード 0)|カウント|合計||
|cachehits0|キャッシュ ヒット数 (シャード 0)|カウント|合計||
|cachemisses0|キャッシュ ミス数 (シャード 0)|カウント|合計||
|getcommands0|取得数 (シャード 0)|カウント|合計||
|setcommands0|設定数 (シャード 0)|カウント|合計||
|evictedkeys0|削除されたキー数 (シャード 0)|カウント|合計||
|totalkeys0|合計キー数 (シャード 0)|カウント|最大値||
|expiredkeys0|期限切れキー数 (シャード 0)|カウント|合計||
|usedmemory0|メモリ使用量 (シャード 0)|Bytes|最大値||
|usedmemoryRss0|RSS メモリ使用量 (シャード 0)|Bytes|最大値||
|serverLoad0|サーバーの負荷 (シャード 0)|Percent|最大値||
|cacheWrite0|キャッシュの書き込み (シャード 0)|BytesPerSecond|最大値||
|cacheRead0|キャッシュの読み取り (シャード 0)|BytesPerSecond|最大値||
|percentProcessorTime0|CPU (シャード 0)|Percent|最大値||
|connectedclients1|接続されているクライアント数 (シャード 1)|カウント|最大値||
|totalcommandsprocessed1|合計処理数 (シャード 1)|カウント|合計||
|cachehits1|キャッシュ ヒット数 (シャード 1)|カウント|合計||
|cachemisses1|キャッシュ ミス数 (シャード 1)|カウント|合計||
|getcommands1|取得数 (シャード 1)|カウント|合計||
|setcommands1|設定数 (シャード 1)|カウント|合計||
|evictedkeys1|削除されたキー数 (シャード 1)|カウント|合計||
|totalkeys1|合計キー数 (シャード 1)|カウント|最大値||
|expiredkeys1|期限切れキー数 (シャード 1)|カウント|合計||
|usedmemory1|メモリ使用量 (シャード 1)|Bytes|最大値||
|usedmemoryRss1|RSS メモリ使用量 (シャード 1)|Bytes|最大値||
|serverLoad1|サーバーの負荷 (シャード 1)|Percent|最大値||
|cacheWrite1|キャッシュの書き込み (シャード 1)|BytesPerSecond|最大値||
|cacheRead1|キャッシュの読み取り (シャード 1)|BytesPerSecond|最大値||
|percentProcessorTime1|CPU (シャード 1)|Percent|最大値||
|connectedclients2|接続されているクライアント数 (シャード 2)|カウント|最大値||
|totalcommandsprocessed2|合計処理数 (シャード 2)|カウント|合計||
|cachehits2|キャッシュ ヒット数 (シャード 2)|カウント|合計||
|cachemisses2|キャッシュ ミス数 (シャード 2)|カウント|合計||
|getcommands2|取得数 (シャード 2)|カウント|合計||
|setcommands2|設定数 (シャード 2)|カウント|合計||
|evictedkeys2|削除されたキー数 (シャード 2)|カウント|合計||
|totalkeys2|合計キー数 (シャード 2)|カウント|最大値||
|expiredkeys2|期限切れキー数 (シャード 2)|カウント|合計||
|usedmemory2|メモリ使用量 (シャード 2)|Bytes|最大値||
|usedmemoryRss2|RSS メモリ使用量 (シャード 2)|Bytes|最大値||
|serverLoad2|サーバーの負荷 (シャード 2)|Percent|最大値||
|cacheWrite2|キャッシュの書き込み (シャード 2)|BytesPerSecond|最大値||
|cacheRead2|キャッシュの読み取り (シャード 2)|BytesPerSecond|最大値||
|percentProcessorTime2|CPU (シャード 2)|Percent|最大値||
|connectedclients3|接続されているクライアント数 (シャード 3)|カウント|最大値||
|totalcommandsprocessed3|合計処理数 (シャード 3)|カウント|合計||
|cachehits3|キャッシュ ヒット数 (シャード 3)|カウント|合計||
|cachemisses3|キャッシュ ミス数 (シャード 3)|カウント|合計||
|getcommands3|取得数 (シャード 3)|カウント|合計||
|setcommands3|設定数 (シャード 3)|カウント|合計||
|evictedkeys3|削除されたキー数 (シャード 3)|カウント|合計||
|totalkeys3|合計キー数 (シャード 3)|カウント|最大値||
|expiredkeys3|期限切れキー数 (シャード 3)|カウント|合計||
|usedmemory3|メモリ使用量 (シャード 3)|Bytes|最大値||
|usedmemoryRss3|RSS メモリ使用量 (シャード 3)|Bytes|最大値||
|serverLoad3|サーバーの負荷 (シャード 3)|Percent|最大値||
|cacheWrite3|キャッシュの書き込み (シャード 3)|BytesPerSecond|最大値||
|cacheRead3|キャッシュの読み取り (シャード 3)|BytesPerSecond|最大値||
|percentProcessorTime3|CPU (シャード 3)|Percent|最大値||
|connectedclients4|接続されているクライアント数 (シャード 4)|カウント|最大値||
|totalcommandsprocessed4|合計処理数 (シャード 4)|カウント|合計||
|cachehits4|キャッシュ ヒット数 (シャード 4)|カウント|合計||
|cachemisses4|キャッシュ ミス数 (シャード 4)|カウント|合計||
|getcommands4|取得数 (シャード 4)|カウント|合計||
|setcommands4|設定数 (シャード 4)|カウント|合計||
|evictedkeys4|削除されたキー数 (シャード 4)|カウント|合計||
|totalkeys4|合計キー数 (シャード 4)|カウント|最大値||
|expiredkeys4|期限切れキー数 (シャード 4)|カウント|合計||
|usedmemory4|メモリ使用量 (シャード 4)|Bytes|最大値||
|usedmemoryRss4|RSS メモリ使用量 (シャード 4)|Bytes|最大値||
|serverLoad4|サーバーの負荷 (シャード 4)|Percent|最大値||
|cacheWrite4|キャッシュの書き込み (シャード 4)|BytesPerSecond|最大値||
|cacheRead4|キャッシュの読み取り (シャード 4)|BytesPerSecond|最大値||
|percentProcessorTime4|CPU (シャード 4)|Percent|最大値||
|connectedclients5|接続されているクライアント数 (シャード 5)|カウント|最大値||
|totalcommandsprocessed5|合計処理数 (シャード 5)|カウント|合計||
|cachehits5|キャッシュ ヒット数 (シャード 5)|カウント|合計||
|cachemisses5|キャッシュ ミス数 (シャード 5)|カウント|合計||
|getcommands5|取得数 (シャード 5)|カウント|合計||
|setcommands5|設定数 (シャード 5)|カウント|合計||
|evictedkeys5|削除されたキー数 (シャード 5)|カウント|合計||
|totalkeys5|合計キー数 (シャード 5)|カウント|最大値||
|expiredkeys5|期限切れキー数 (シャード 5)|カウント|合計||
|usedmemory5|メモリ使用量 (シャード 5)|Bytes|最大値||
|usedmemoryRss5|RSS メモリ使用量 (シャード 5)|Bytes|最大値||
|serverLoad5|サーバーの負荷 (シャード 5)|Percent|最大値||
|cacheWrite5|キャッシュの書き込み (シャード 5)|BytesPerSecond|最大値||
|cacheRead5|キャッシュの読み取り (シャード 5)|BytesPerSecond|最大値||
|percentProcessorTime5|CPU (シャード 5)|Percent|最大値||
|connectedclients6|接続されているクライアント数 (シャード 6)|カウント|最大値||
|totalcommandsprocessed6|合計処理数 (シャード 6)|カウント|合計||
|cachehits6|キャッシュ ヒット数 (シャード 6)|カウント|合計||
|cachemisses6|キャッシュ ミス数 (シャード 6)|カウント|合計||
|getcommands6|取得数 (シャード 6)|カウント|合計||
|setcommands6|設定数 (シャード 6)|カウント|合計||
|evictedkeys6|削除されたキー数 (シャード 6)|カウント|合計||
|totalkeys6|合計キー数 (シャード 6)|カウント|最大値||
|expiredkeys6|期限切れキー数 (シャード 6)|カウント|合計||
|usedmemory6|メモリ使用量 (シャード 6)|Bytes|最大値||
|usedmemoryRss6|RSS メモリ使用量 (シャード 6)|Bytes|最大値||
|serverLoad6|サーバーの負荷 (シャード 6)|Percent|最大値||
|cacheWrite6|キャッシュの書き込み (シャード 6)|BytesPerSecond|最大値||
|cacheRead6|キャッシュの読み取り (シャード 6)|BytesPerSecond|最大値||
|percentProcessorTime6|CPU (シャード 6)|Percent|最大値||
|connectedclients7|接続されているクライアント数 (シャード 7)|カウント|最大値||
|totalcommandsprocessed7|合計処理数 (シャード 7)|カウント|合計||
|cachehits7|キャッシュ ヒット数 (シャード 7)|カウント|合計||
|cachemisses7|キャッシュ ミス数 (シャード 7)|カウント|合計||
|getcommands7|取得数 (シャード 7)|カウント|合計||
|setcommands7|設定数 (シャード 7)|カウント|合計||
|evictedkeys7|削除されたキー数 (シャード 7)|カウント|合計||
|totalkeys7|合計キー数 (シャード 7)|カウント|最大値||
|expiredkeys7|期限切れキー数 (シャード 7)|カウント|合計||
|usedmemory7|メモリ使用量 (シャード 7)|Bytes|最大値||
|usedmemoryRss7|RSS メモリ使用量 (シャード 7)|Bytes|最大値||
|serverLoad7|サーバーの負荷 (シャード 7)|Percent|最大値||
|cacheWrite7|キャッシュの書き込み (シャード 7)|BytesPerSecond|最大値||
|cacheRead7|キャッシュの読み取り (シャード 7)|BytesPerSecond|最大値||
|percentProcessorTime7|CPU (シャード 7)|Percent|最大値||
|connectedclients8|接続されているクライアント数 (シャード 8)|カウント|最大値||
|totalcommandsprocessed8|合計処理数 (シャード 8)|カウント|合計||
|cachehits8|キャッシュ ヒット数 (シャード 8)|カウント|合計||
|cachemisses8|キャッシュ ミス数 (シャード 8)|カウント|合計||
|getcommands8|取得数 (シャード 8)|カウント|合計||
|setcommands8|設定数 (シャード 8)|カウント|合計||
|evictedkeys8|削除されたキー数 (シャード 8)|カウント|合計||
|totalkeys8|合計キー数 (シャード 8)|カウント|最大値||
|expiredkeys8|期限切れキー数 (シャード 8)|カウント|合計||
|usedmemory8|メモリ使用量 (シャード 8)|Bytes|最大値||
|usedmemoryRss8|RSS メモリ使用量 (シャード 8)|Bytes|最大値||
|serverLoad8|サーバーの負荷 (シャード 8)|Percent|最大値||
|cacheWrite8|キャッシュの書き込み (シャード 8)|BytesPerSecond|最大値||
|cacheRead8|キャッシュの読み取り (シャード 8)|BytesPerSecond|最大値||
|percentProcessorTime8|CPU (シャード 8)|Percent|最大値||
|connectedclients9|接続されているクライアント数 (シャード 9)|カウント|最大値||
|totalcommandsprocessed9|合計処理数 (シャード 9)|カウント|合計||
|cachehits9|キャッシュ ヒット数 (シャード 9)|カウント|合計||
|cachemisses9|キャッシュ ミス数 (シャード 9)|カウント|合計||
|getcommands9|取得数 (シャード 9)|カウント|合計||
|setcommands9|設定数 (シャード 9)|カウント|合計||
|evictedkeys9|削除されたキー数 (シャード 9)|カウント|合計||
|totalkeys9|合計キー数 (シャード 9)|カウント|最大値||
|expiredkeys9|期限切れキー数 (シャード 9)|カウント|合計||
|usedmemory9|メモリ使用量 (シャード 9)|Bytes|最大値||
|usedmemoryRss9|RSS メモリ使用量 (シャード 9)|Bytes|最大値||
|serverLoad9|サーバーの負荷 (シャード 9)|Percent|最大値||
|cacheWrite9|キャッシュの書き込み (シャード 9)|BytesPerSecond|最大値||
|cacheRead9|キャッシュの読み取り (シャード 9)|BytesPerSecond|最大値||
|percentProcessorTime9|CPU (シャード 9)|Percent|最大値||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|TotalCalls|合計呼び出し数|カウント|合計|呼び出しの合計数。|
|SuccessfulCalls|成功した呼び出し|カウント|合計|成功した呼び出しの数。|
|TotalErrors|合計エラー数|カウント|合計|エラー応答 (HTTP 応答コード 4xx または 5xx) があった呼び出しの合計回数。|
|BlockedCalls|ブロックされた呼び出し|カウント|合計|レートまたはクォータの制限を超えた呼び出しの回数。|
|ServerErrors|サーバー エラー|カウント|合計|サービスの内部エラー (HTTP 応答コード 5xx) があった呼び出しの回数。|
|ClientErrors|クライアント エラー|カウント|合計|クライアント側のエラー (HTTP 応答コード 4xx) があった呼び出しの回数。|
|DataIn|受信データ|Bytes|合計|受信データのサイズ (バイト)。|
|DataOut|送信データ|Bytes|合計|送信データのサイズ (バイト)。|
|待機時間|待機時間|MilliSeconds|平均|待機時間 (ミリ秒)。|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|CPU の割合|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|
|ネットワーク受信|ネットワーク受信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|
|ネットワーク送信|ネットワーク送信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|
|ディスク読み取りバイト数|ディスク読み取りバイト数|Bytes|合計|監視期間中にディスクから読み取られた合計バイト数|
|ディスク書き込みバイト数|ディスク書き込みバイト数|Bytes|合計|監視期間中にディスクに書き込まれた合計バイト数|
|ディスク読み取り操作数/秒|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|CPU の割合|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|
|ネットワーク受信|ネットワーク受信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|
|ネットワーク送信|ネットワーク送信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|
|ディスク読み取りバイト数|ディスク読み取りバイト数|Bytes|合計|監視期間中にディスクから読み取られた合計バイト数|
|ディスク書き込みバイト数|ディスク書き込みバイト数|Bytes|合計|監視期間中にディスクに書き込まれた合計バイト数|
|ディスク読み取り操作数/秒|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|CPU の割合|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|
|ネットワーク受信|ネットワーク受信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|
|ネットワーク送信|ネットワーク送信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|
|ディスク読み取りバイト数|ディスク読み取りバイト数|Bytes|合計|監視期間中にディスクから読み取られた合計バイト数|
|ディスク書き込みバイト数|ディスク書き込みバイト数|Bytes|合計|監視期間中にディスクに書き込まれた合計バイト数|
|ディスク読み取り操作数/秒|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|メトリック|メトリックの表示名|単位|集計の種類|説明|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|テレメトリ メッセージ送信試行|カウント|合計|IoT Hub への送信が試行された Device to Cloud テレメトリ メッセージの数|
|d2c.telemetry.ingress.success|送信済みテレメトリ メッセージ|カウント|合計|IoT Hub に正常に送信された Device to Cloud テレメトリ メッセージの数|
|c2d.commands.egress.complete.success|完了したコマンド|カウント|合計|デバイスで正常に完了した Cloud to Device コマンドの数|
|c2d.commands.egress.abandon.success|中止したコマンド|カウント|合計|デバイスで中止された Cloud to Device コマンドの数|
|c2d.commands.egress.reject.success|拒否されたコマンド|カウント|合計|デバイスで拒否された Cloud to Device コマンドの数|
|devices.totalDevices|合計デバイス|カウント|合計|IoT Hub に登録されたデバイスの数|
|devices.connectedDevices.allProtocol|接続されているデバイス|カウント|合計|IoT Hub に接続されているデバイスの数|
|d2c.telemetry.egress.success|配信されたテレメトリ メッセージ|カウント|合計|メッセージがエンドポイントに正常に書き込まれた回数 (合計)|
|d2c.telemetry.egress.dropped|破棄されたメッセージ|カウント|合計|ルートが一致せず、フォールバック ルートが無効になったために破棄されたメッセージの数|
|d2c.telemetry.egress.orphaned|孤立したメッセージ|カウント|合計|フォールバック ルートを含め、すべてのルートと一致しないメッセージの数|
|d2c.telemetry.egress.invalid|無効なメッセージ|カウント|合計|エンドポイントとの互換性がないために配信されなかったメッセージの数|
|d2c.telemetry.egress.fallback|フォールバック条件に一致するメッセージ|カウント|合計|フォールバック エンドポイントに書き込まれたメッセージの数|
|d2c.endpoints.egress.eventHubs|Event Hub エンドポイントに配信されたメッセージ|カウント|合計|メッセージが Event Hub エンドポイントに正常に書き込まれた回数|
|d2c.endpoints.latency.eventHubs|イベント ハブ エンドポイントのメッセージの待機時間|ミリ秒|平均|IoT ハブの受信メッセージとイベント ハブ エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)|
|d2c.endpoints.egress.serviceBusQueues|Service Bus キュー エンドポイントに配信されたメッセージ|カウント|合計|メッセージが Service Bus キュー エンドポイントに正常に書き込まれた回数|
|d2c.endpoints.latency.serviceBusQueues|Service Bus キュー エンドポイントのメッセージの待機時間|ミリ秒|平均|IoT ハブの受信メッセージと Service Bus キュー エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)|
|d2c.endpoints.egress.serviceBusTopics|Service Bus トピック エンドポイントに配信されたメッセージ|カウント|合計|メッセージが Service Bus トピック エンドポイントに正常に書き込まれた回数|
|d2c.endpoints.latency.serviceBusTopics|Service Bus トピック エンドポイントのメッセージの待機時間|ミリ秒|平均|IoT ハブの受信メッセージと Service Bus トピック エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)|
|d2c.endpoints.egress.builtIn.events|組み込みエンドポイント (イベント/メッセージ) に配信されたメッセージ|カウント|合計|メッセージが組み込みエンドポイント (イベント/メッセージ) に正常に書き込まれた回数|
|d2c.endpoints.latency.builtIn.events|組み込みエンドポイント (イベント/メッセージ) のメッセージ待機時間|ミリ秒|平均|IoT ハブの受信メッセージと組み込みエンドポイント (メッセージ/イベント) の受信メッセージの間の平均待機時間 (ミリ秒) |
|d2c.twin.read.success|成功したデバイスからのツイン読み取り|カウント|合計|デバイスが開始して成功したツイン読み取りの数。|
|d2c.twin.read.failure|失敗したデバイスからのツイン読み取り|カウント|合計|デバイスが開始したツイン読み取りの失敗数。|
|d2c.twin.read.size|デバイスからのツイン読み取りの応答サイズ|バイト|平均|デバイスが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|
|d2c.twin.update.success|成功したデバイスからのツイン更新|カウント|合計|デバイスが開始して成功したツイン更新の数。|
|d2c.twin.update.failure|失敗したデバイスからのツイン更新|カウント|合計|デバイスが開始して失敗したツイン更新の数。|
|d2c.twin.update.size|デバイスからのツイン更新のサイズ|バイト|平均|デバイスが開始して成功したツイン更新の平均、最小、および最大サイズ。|
|c2d.methods.success|成功したダイレクト メソッドの呼び出し|カウント|合計|成功したダイレクト メソッドの呼び出しの数。|
|c2d.methods.failure|失敗したダイレクト メソッドの呼び出し|カウント|合計|失敗したダイレクト メソッドの呼び出しの数。|
|c2d.methods.requestSize|ダイレクト メソッドの呼び出しの要求サイズ|バイト|平均|成功したダイレクト メソッド要求の平均、最小、および最大サイズ。|
|c2d.methods.responseSize|ダイレクト メソッドの呼び出しの応答サイズ|バイト|平均|成功したダイレクト メソッド応答の平均、最小、および最大サイズ。|
|c2d.twin.read.success|成功したバック エンドからのツイン読み取り|カウント|合計|バックエンドが開始して成功したツイン読み取りの数。|
|c2d.twin.read.failure|失敗したバックエンドからのツイン読み取り|カウント|合計|バックエンドが開始して失敗したツイン読み取りの数。|
|c2d.twin.read.size|バック エンドからのツイン読み取りの応答サイズ|バイト|平均|バックエンドが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|
|c2d.twin.update.success|成功したバックエンドからのツイン更新|カウント|合計|バックエンドが開始して成功したツイン更新の数。|
|c2d.twin.update.failure|失敗したバック エンドからのツイン更新|カウント|合計|バックエンドが開始して失敗したツイン更新の数。|
|c2d.twin.update.size|バックエンドからのツイン更新のサイズ|バイト|平均|バックエンドが開始して成功したツイン更新の平均、最小、および最大サイズ。|
|twinQueries.success|成功したツイン クエリ|カウント|合計|成功したツイン クエリの数。|
|twinQueries.failure|失敗したツイン クエリ|カウント|合計|失敗したツイン クエリの数。|
|twinQueries.resultSize|ツイン クエリの結果のサイズ|バイト|平均|成功したツイン クエリの結果の平均、最小、および最大サイズ。|
|jobs.createTwinUpdateJob.success|成功したツイン更新ジョブの作成|カウント|合計|正常に作成されたツイン更新ジョブの数。|
|jobs.createTwinUpdateJob.failure|失敗したツイン更新ジョブの作成|カウント|合計|作成に失敗したツイン更新ジョブの数。|
|jobs.createDirectMethodJob.success|成功したメソッド呼び出しジョブの作成|カウント|合計|正常に作成されたダイレクト メソッド呼び出しジョブの数。|
|jobs.createDirectMethodJob.failure|失敗したメソッド呼び出しジョブの作成|カウント|合計|作成に失敗したダイレクト メソッド呼び出しジョブの数。|
|jobs.listJobs.success|成功したジョブ一覧の呼び出し|カウント|合計|正常に実行されたジョブ一覧の呼び出しの数。|
|jobs.listJobs.failure|失敗したジョブ一覧の呼び出し|カウント|合計|失敗したジョブ一覧の呼び出しの数。|
|jobs.cancelJob.success|成功したジョブの取り消し|カウント|合計|正常に実行されたジョブ取り消しの呼び出し。|
|jobs.cancelJob.failure|失敗したジョブの取り消し|カウント|合計|失敗したジョブ取り消しの呼び出し。|
|jobs.queryJobs.success|成功したジョブ クエリ|カウント|合計|正常に実行されたクエリ ジョブの呼び出しの数。|
|jobs.queryJobs.failure|失敗したジョブ クエリ|カウント|合計|失敗したジョブ クエリの呼び出しの数。|
|jobs.completed|完了したジョブ|カウント|合計|完了したジョブの数。|
|jobs.failed|失敗したジョブ|カウント|合計|失敗したジョブの数。|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|INREQS|受信要求|カウント|合計|名前空間の受信要求の総数|
|SUCCREQ|成功した要求|カウント|合計|名前空間の成功した要求の総数|
|FAILREQ|失敗した要求|カウント|合計|名前空間の失敗した要求の総数|
|SVRBSY|サーバー ビジー エラー|カウント|合計|名前空間のサーバー ビジー エラーの総数|
|INTERR|内部サーバー エラー|カウント|合計|名前空間の内部サーバー エラーの総数|
|MISCERR|その他のエラー|カウント|合計|名前空間の失敗した要求の総数|
|INMSGS|受信メッセージ|カウント|合計|名前空間の受信メッセージの総数|
|OUTMSGS|送信メッセージ|カウント|合計|名前空間の送信メッセージの総数|
|EHINMBS|着信バイト数|BytesPerSecond|合計|名前空間のEvent Hub 受信メッセージのスループット|
|EHOUTMBS|発信バイト数|BytesPerSecond|合計|名前空間の送信メッセージの総数|
|EHABL|アーカイブ バックログ メッセージ|カウント|合計|名前空間のバックログ内の Event Hub アーカイブ メッセージ|
|EHAMSGS|アーカイブ メッセージ|カウント|合計|名前空間内の Event Hub アーカイブ メッセージ|
|EHAMBS|アーカイブ メッセージ スループット|BytesPerSecond|合計|名前空間内の HubEvent アーカイブ メッセージのスループット|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|RunsStarted|開始した実行|カウント|合計|開始したワークフロー実行の数。|
|RunsCompleted|完了した実行|カウント|合計|完了したワークフロー実行の数。|
|RunsSucceeded|成功した実行|カウント|合計|成功したワークフロー実行の数。|
|RunsFailed|失敗した実行|カウント|合計|失敗したワークフロー実行の数。|
|RunsCancelled|キャンセルされた実行|カウント|合計|キャンセルされたワークフロー実行の数。|
|RunLatency|実行の待機時間|Seconds|平均|完了したワークフロー実行の待機時間。|
|RunSuccessLatency|成功した実行の待機時間|Seconds|平均|成功したワークフロー実行の待機時間。|
|RunThrottledEvents|実行スロットル イベント|カウント|合計|ワークフロー アクションまたはトリガー スロットル イベントの数。|
|RunFailurePercentage|実行エラーの割合|Percent|合計|失敗したワークフロー実行の割合。|
|ActionsStarted|開始したアクション |カウント|合計|開始したワークフロー アクションの数。|
|ActionsCompleted|完了したアクション |カウント|合計|完了したワークフロー アクションの数。|
|ActionsSucceeded|成功したアクション |カウント|合計|成功したワークフロー アクションの数。|
|ActionsFailed|失敗したアクション|カウント|合計|失敗したワークフロー アクションの数。|
|ActionsSkipped|スキップされたアクション |カウント|合計|スキップされたワークフロー アクションの数。|
|ActionLatency|アクションの待機時間 |Seconds|平均|完了したワークフロー アクションの待機時間。|
|ActionSuccessLatency|成功したアクションの待機時間 |Seconds|平均|成功したワークフロー アクションの待機時間。|
|ActionThrottledEvents|アクション スロットル イベント|カウント|合計|ワークフロー アクション スロットル イベントの数。|
|TriggersStarted|開始したトリガー |カウント|合計|開始したワークフロー トリガーの数。|
|TriggersCompleted|完了したトリガー |カウント|合計|完了したワークフロー トリガーの数。|
|TriggersSucceeded|成功したトリガー |カウント|合計|成功したワークフロー トリガーの数。|
|TriggersFailed|失敗したトリガー |カウント|合計|失敗したワークフロー トリガーの数。|
|TriggersSkipped|スキップされたトリガー|カウント|合計|スキップされたワークフロー トリガーの数。|
|TriggersFired|起動されたトリガー |カウント|合計|起動されたワークフロー トリガーの数。|
|TriggerLatency|トリガーの待機時間 |Seconds|平均|完了したワークフロー トリガーの待機時間。|
|TriggerFireLatency|起動されたトリガーの待機時間 |Seconds|平均|起動されたワークフロー トリガーの待機時間。|
|TriggerSuccessLatency|成功したトリガーの待機時間 |Seconds|平均|成功したワークフロー トリガーの待機時間。|
|TriggerThrottledEvents|トリガー スロットル イベント|カウント|合計|ワークフロー トリガー スロットル イベントの数。|
|BillableActionExecutions|課金対象のアクションの実行|カウント|合計|課金されるワークフロー アクションの実行数。|
|BillableTriggerExecutions|課金対象のトリガーの実行|カウント|合計|課金されるワークフロー トリガーの実行数。|
|TotalBillableExecutions|課金対象の実行の合計数|カウント|合計|課金されるワークフローの実行数。|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|スループット|スループット|BytesPerSecond|平均||

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|メトリック|メトリックの表示名|単位|集計の種類|説明|
|---|---|---|---|---|
|registration.all|登録操作|カウント|合計|成功した登録操作の数 (作成、更新、クエリ、および削除)。 |
|registration.create|登録作成操作|カウント|合計|成功した登録作成の数。|
|registration.update|登録更新操作|カウント|合計|成功した登録更新の数。|
|registration.get|登録読み取り操作|カウント|合計|成功した登録クエリの数。|
|registration.delete|登録削除操作|カウント|合計|成功した登録削除の数。|
|incoming|受信メッセージ|カウント|合計|成功した送信 API 呼び出しの数。 |
|incoming.scheduled|予定されていたプッシュ通知の送信|カウント|合計|予定されていたプッシュ通知が取り消されました|
|incoming.scheduled.cancel|予定されていたプッシュ通知の取り消し|カウント|合計|予定されていたプッシュ通知の取り消し|
|scheduled.pending|予定されていた通知の保留|カウント|合計|予定されていた通知の保留|
|installation.all|インストール管理操作|カウント|合計|インストール管理操作|
|installation.get|インストール操作の取得|カウント|合計|インストール操作の取得|
|installation.upsert|インストール操作の作成または更新|カウント|合計|インストール操作を作成または更新します|
|installation.patch|インストール操作のパッチ|カウント|合計|インストール操作をパッチします|
|installation.delete|インストール操作の削除|カウント|合計|インストール操作を削除します|
|outgoing.allpns.success|正常な通知|カウント|合計|成功した通知の数。|
|outgoing.allpns.invalidpayload|ペイロード エラー|カウント|合計|PNS が不良ペイロード エラーを返しために失敗したプッシュの数。|
|outgoing.allpns.pnserror|外部通知システム エラー|カウント|合計|PNS との通信に問題があったために失敗したプッシュの数 (認証の問題は除きます)。|
|outgoing.allpns.channelerror|チャネル エラー|カウント|合計|チャネルが適切に調整されていないアプリまたは有効期限が切れたアプリに関連付けられていたために失敗したプッシュの数。|
|outgoing.allpns.badorexpiredchannel|適切または期限切れのチャネル エラー|カウント|合計|登録内のチャネル/トークン/登録 ID が期限切れまたは無効なために失敗したプッシュの数。|
|outgoing.wns.success|WNS 正常通知|カウント|合計|成功した通知の数。|
|outgoing.wns.invalidcredentials|WNS 承認エラー (無効な資格情報)|カウント|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数  (Windows Live は資格情報を認識しません)。|
|outgoing.wns.badchannel|WNS 不良チャネル エラー|カウント|合計|登録の ChannelURI が認識されなかったために失敗したプッシュの数 (WNS ステータス: 404 - 見つかりません)。|
|outgoing.wns.expiredchannel|WNS 期限切れチャネル エラー|カウント|合計|ChannelURI の有効期限が切れているために失敗したプッシュの数 (WNS ステータス: 410 - 削除)。|
|outgoing.wns.throttled|WNS 調整済み通知|カウント|合計|WNS がこのアプリを調整しているために失敗したプッシュの数 (WNS ステータス: 406 - 受信不可)。|
|outgoing.wns.tokenproviderunreachable|WNS 承認エラー (到達不可)|カウント|合計|Windows Live に到達できません。|
|outgoing.wns.invalidtoken|WNS 承認エラー (無効なトークン)|カウント|合計|WNS に提供されたトークンは無効です (WNS ステータス: 401 - 許可されていません)。|
|outgoing.wns.wrongtoken|WNS 承認エラー (間違ったトークン)|カウント|合計|WNS に提供されたトークンは有効ですが、別のアプリケーション用です (WNS ステータス: 403 - 禁止されています)。 これは、登録の ChannelURI が別のアプリケーションに関連付けられている場合に発生します。 クライアント アプリに関連付けられているアプリの資格情報が通知ハブ内にあることを確認します。|
|outgoing.wns.invalidnotificationformat|WNS 無効通知形式|カウント|合計|通知形式が無効です (WNS ステータス: 400)。 WNS ではすべての無効なペイロードを拒否するわけではないことに注意してください。|
|outgoing.wns.invalidnotificationsize|WNS 無効通知サイズ エラー|カウント|合計|通知ペイロードが大きすぎます (WNS ステータス: 413)。|
|outgoing.wns.channelthrottled|WNS 調整済みチャネル|カウント|合計|登録の ChannelURI が調整されているため、通知は破棄されました (WNS 応答ヘッダー: X-WNS-NotificationStatus:channelThrottled)。|
|outgoing.wns.channeldisconnected|WNS チャネル切断|カウント|合計|登録の ChannelURI が調整されているため、通知は破棄されました (WNS 応答ヘッダー: X-WNS-DeviceConnectionStatus: disconnected)。|
|outgoing.wns.dropped|WNS ドロップ通知|カウント|合計|登録の ChannelURI が調整されているため、通知は破棄されました (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected)。|
|outgoing.wns.pnserror|WNS エラー|カウント|合計|WNS との通信エラーにより、通知が配信されません。|
|outgoing.wns.authenticationerror|WNS 認証エラー|カウント|合計|資格情報無効または間違ったトークンによる Windows Live との通信エラーのため、通知が配信されません。|
|outgoing.apns.success|APNS 正常通知|カウント|合計|成功した通知の数。|
|outgoing.apns.invalidcredentials|APNS 認証エラー|カウント|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 |
|outgoing.apns.badchannel|APNS 不良チャネル エラー|カウント|合計|トークンが無効なために失敗したプッシュの数 (APNS 状態コード: 8)。|
|outgoing.apns.expiredchannel|APNS 期限切れチャネル エラー|カウント|合計|APNS フィードバック チャネルによって無効化されたトークンの数。|
|outgoing.apns.invalidnotificationsize|APNS 無効通知サイズ エラー|カウント|合計|ペイロードが長すぎたために失敗したプッシュの数 (APNS 状態コード: 7)。|
|outgoing.apns.pnserror|APNS エラー|カウント|合計|APNS との通信エラーにより失敗したプッシュの数。|
|outgoing.gcm.success|GCM 正常通知|カウント|合計|成功した通知の数。|
|outgoing.gcm.invalidcredentials|GCM 承認エラー (無効な資格情報)|カウント|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 |
|outgoing.gcm.badchannel|GCM 不良チャネル エラー|カウント|合計|登録内の registrationId が認識されなかったために失敗したプッシュの数 (GCM 結果: 無効な登録)。|
|outgoing.gcm.expiredchannel|GCM 期限切れチャネル エラー|カウント|合計|登録内の registrationId が有効期限切れだったために失敗したプッシュの数 (GCM 結果: NotRegistered)。|
|outgoing.gcm.throttled|GCM 調整済み通知|カウント|合計|GCM がこのアプリを調整したために失敗したプッシュの数 (GCM 状態コード: 501-599、または結果: 利用不可)。|
|outgoing.gcm.invalidnotificationformat|GCM 無効通知形式|カウント|合計|ペイロードの形式が正しくなかったために失敗したプッシュの数 (GCM 結果: InvalidDataKey または InvalidTtl)。|
|outgoing.gcm.invalidnotificationsize|GCM 無効通知サイズ エラー|カウント|合計|ペイロードが長すぎたために失敗したプッシュの数 (GCM 結果: MessageTooBig)。|
|outgoing.gcm.wrongchannel|GCM 誤チャネル エラー|カウント|合計|登録内の registrationId が現在のアプリに関連付けられていないために失敗したプッシュの数 (GCM 結果: InvalidPackageName)。|
|outgoing.gcm.pnserror|GCM エラー|カウント|合計|GCM との通信エラーにより失敗したプッシュの数。|
|outgoing.gcm.authenticationerror|GCM 認証エラー|カウント|合計|PNS が提供された資格情報を受け入れなかったかったために失敗したプッシュの数。資格情報がブロックされているか、アプリでSenderId が正しく構成されていません (GCM 結果: MismatchedSenderId)。|
|outgoing.mpns.success|MPNS 正常通知|カウント|合計|成功した通知の数。|
|outgoing.mpns.invalidcredentials|MPNS 無効な資格情報|カウント|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 |
|outgoing.mpns.badchannel|MPNS 不良チャネル エラー|カウント|合計|登録の ChannelURI が認識されなかったために失敗したプッシュの数 (MPNS ステータス: 404 - 見つかりません)。|
|outgoing.mpns.throttled|MPNS 調整済み通知|カウント|合計|MPNS がこのアプリを調整しているために失敗したプッシュの数 (WNS MPNS: 406 - 受信不可)。|
|outgoing.mpns.invalidnotificationformat|MPNS 無効通知形式|カウント|合計|通知のペイロードが長すぎたために失敗したプッシュの数。|
|outgoing.mpns.channeldisconnected|MPNS チャネル切断|カウント|合計|登録の ChannelURI が切断されたために失敗したプッシュの数 (MPNS ステータス: 412 - 見つかりません)。|
|outgoing.mpns.dropped|MPNS ドロップ通知|カウント|合計|MPNS によって破棄されたプッシュの数 (MPNS 応答ヘッダー: X-NotificationStatus: QueueFull または Suppressed)。|
|outgoing.mpns.pnserror|MPNS エラー|カウント|合計|MPNS との通信エラーにより失敗したプッシュの数。|
|outgoing.mpns.authenticationerror|MPNS 認証エラー|カウント|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 |

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|
|---|---|---|---|---|
|SearchLatency|検索の待機時間|Seconds|平均|検索サービスの平均検索待機時間|
|SearchQueriesPerSecond|1 秒あたりの検索クエリ数|CountPerSecond|平均|検索サービスの&1; 秒あたりの検索クエリ数|
|ThrottledSearchQueriesPercentage|スロットルされた検索クエリの割合|Percent|平均|検索サービスのスロットルされた検索クエリの割合|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|CPUXNS|名前空間あたりの CPU 使用率|Percent|最大値|Service Bus Premium 名前空間の CPU 使用率メトリック|
|WSXNS|名前空間あたりのメモリ サイズの使用量|Percent|最大値|Service Bus Premium 名前空間のメモリ使用量メトリック|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|cpu_percent|CPU の割合|Percent|平均|CPU の割合|
|physical_data_read_percent|データ IO の割合|Percent|平均|データ IO の割合|
|log_write_percent|ログ IO の割合|Percent|平均|ログ IO の割合|
|dtu_consumption_percent|DTU の割合|Percent|平均|DTU の割合|
|storage|合計データベース サイズ|Bytes|最大値|合計データベース サイズ|
|connection_successful|成功した接続|カウント|合計|成功した接続|
|connection_failed|失敗した接続|カウント|合計|失敗した接続|
|blocked_by_firewall|ファイアウォールによってブロックされる|カウント|合計|ファイアウォールによってブロックされる|
|deadlock|デッドロック|カウント|合計|デッドロック|
|storage_percent|データベース サイズの割合|Percent|最大値|データベース サイズの割合|
|xtp_storage_percent|インメモリ OLTP ストレージの割合|Percent|平均|インメモリ OLTP ストレージの割合|
|workers_percent|ワーカーの割合|Percent|平均|ワーカーの割合|
|sessions_percent|セッションの割合|Percent|平均|セッションの割合|
|dtu_limit|DTU の上限|カウント|平均|DTU の上限|
|dtu_used|使用された DTU|カウント|平均|使用された DTU|
|service_level_objective|データベースのサービス レベル目標|カウント|合計|データベースのサービス レベル目標|
|dwu_limit|DWU 上限|カウント|最大値|DWU 上限|
|dwu_consumption_percent|DWU の割合|Percent|平均|DWU の割合|
|dwu_used|使用済み DWU|カウント|平均|使用済み DWU|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|cpu_percent|CPU の割合|Percent|平均|CPU の割合|
|physical_data_read_percent|データ IO の割合|Percent|平均|データ IO の割合|
|log_write_percent|ログ IO の割合|Percent|平均|ログ IO の割合|
|dtu_consumption_percent|DTU の割合|Percent|平均|DTU の割合|
|storage_percent|ストレージの割合|Percent|平均|ストレージの割合|
|workers_percent|ワーカーの割合|Percent|平均|ワーカーの割合|
|sessions_percent|セッションの割合|Percent|平均|セッションの割合|
|eDTU_limit|eDTU 制限|カウント|平均|eDTU 制限|
|storage_limit|ストレージの制限|バイト|平均|ストレージの制限|
|eDTU_used|使用済み eDTU|カウント|平均|使用済み eDTU|
|storage_used|使用済みストレージ|バイト|平均|使用済みストレージ|
|xtp_storage_percent|インメモリ OLTP ストレージの割合|Percent|平均|インメモリ OLTP ストレージの割合|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|ResourceUtilization|SU % 使用率|Percent|最大値|SU % 使用率|
|InputEvents|入力イベント|カウント|合計|入力イベント|
|InputEventBytes|入力イベントのバイト数|Bytes|合計|入力イベントのバイト数|
|LateInputEvents|遅延入力イベント|カウント|合計|遅延入力イベント|
|OutputEvents|出力イベント|カウント|合計|出力イベント|
|ConversionErrors|データ変換エラー|カウント|合計|データ変換エラー|
|エラー|実行時エラー|カウント|合計|実行時エラー|
|DroppedOrAdjustedEvents|順不同のイベント|カウント|合計|順不同のイベント|
|AMLCalloutRequests|関数の要求|カウント|合計|関数の要求|
|AMLCalloutFailedRequests|失敗した関数の要求|カウント|合計|失敗した関数の要求|
|AMLCalloutInputEvents|関数のイベント|カウント|合計|関数のイベント|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|CpuPercentage|CPU の割合|Percent|平均|CPU の割合|
|MemoryPercentage|メモリの割合|Percent|平均|メモリの割合|
|DiskQueueLength|ディスク キューの長さ|カウント|合計|ディスク キューの長さ|
|HttpQueueLength|HTTP キューの長さ|カウント|合計|HTTP キューの長さ|
|BytesReceived|受信データ|Bytes|合計|受信データ|
|BytesSent|送信データ|Bytes|合計|送信データ|

## <a name="microsoftwebsites-including-functions"></a>Microsoft.Web/sites (Functions を含みます)

|メトリック|メトリックの表示名|単位|集計の種類|説明|
|---|---|---|---|---|
|CpuTime|CPU 時間|Seconds|合計|CPU 時間|
|要求数|要求数|カウント|合計|要求数|
|BytesReceived|受信データ|Bytes|合計|受信データ|
|BytesSent|送信データ|Bytes|合計|送信データ|
|Http101|Http 101|カウント|合計|Http 101|
|Http2xx|HTTP 2xx|カウント|合計|HTTP 2xx|
|Http3xx|HTTP 3xx|カウント|合計|HTTP 3xx|
|Http401|HTTP 401|カウント|合計|HTTP 401|
|Http403|HTTP 403|カウント|合計|HTTP 403|
|Http404|HTTP 404|カウント|合計|HTTP 404|
|Http406|HTTP 406|カウント|合計|HTTP 406|
|Http4xx|HTTP 4xx|カウント|合計|HTTP 4xx|
|Http5xx|HTTP サーバー エラー|カウント|合計|HTTP サーバー エラー|
|MemoryWorkingSet|メモリ ワーキング セット|バイト|平均|メモリ ワーキング セット|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|Bytes|平均|平均メモリ ワーキング セット|
|AverageResponseTime|平均応答時間|Seconds|平均|平均応答時間|
|FunctionExecutionUnits|関数の実行単位|カウント|平均|関数の実行単位|
|FunctionExecutionCount|関数の実行回数|カウント|平均|関数の実行回数|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|メトリック|メトリックの表示名|単位|集計の種類|Description|
|---|---|---|---|---|
|CpuTime|CPU 時間|Seconds|合計|CPU 時間|
|要求数|要求数|カウント|合計|要求数|
|BytesReceived|受信データ|Bytes|合計|受信データ|
|BytesSent|送信データ|Bytes|合計|送信データ|
|Http101|Http 101|カウント|合計|Http 101|
|Http2xx|HTTP 2xx|カウント|合計|HTTP 2xx|
|Http3xx|HTTP 3xx|カウント|合計|HTTP 3xx|
|Http401|HTTP 401|カウント|合計|HTTP 401|
|Http403|HTTP 403|カウント|合計|HTTP 403|
|Http404|HTTP 404|カウント|合計|HTTP 404|
|Http406|HTTP 406|カウント|合計|HTTP 406|
|Http4xx|HTTP 4xx|カウント|合計|HTTP 4xx|
|Http5xx|HTTP サーバー エラー|カウント|合計|HTTP サーバー エラー|
|MemoryWorkingSet|メモリ ワーキング セット|バイト|平均|メモリ ワーキング セット|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|Bytes|平均|平均メモリ ワーキング セット|
|AverageResponseTime|平均応答時間|Seconds|平均|平均応答時間|
|FunctionExecutionUnits|関数の実行単位|カウント|平均|関数の実行単位|
|FunctionExecutionCount|関数の実行回数|カウント|平均|関数の実行回数|

## <a name="next-steps"></a>次のステップ
* [Azure Monitor のメトリックを確認します](monitoring-overview.md#monitoring-sources)
* [メトリックでアラートを作成します](insights-receive-alert-notifications.md)
* [メトリックをストレージ、Event Hub、または Log Analytics にエクスポートします](monitoring-overview-of-diagnostic-logs.md)


