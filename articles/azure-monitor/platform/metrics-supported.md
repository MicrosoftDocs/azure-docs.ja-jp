---
title: Azure Monitor でサポートされているメトリック (リソースの種類別)
description: Azure Monitor で使用できるメトリックの一覧 (リソースの種類別) です。
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 70f6e26d423781ba53865304a3fe8440fb120a7a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705168"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor のサポートされるメトリック

Azure Monitor では、複数の方法を使用してメトリックを操作できます。たとえば、ポータルでメトリックをグラフ化したり、REST API でアクセスしたり、PowerShell や CLI を使ってクエリを実行したりできます。 ここで示しているのは、Azure Monitor のメトリック パイプラインで現在利用できるメトリックの一覧です。 他のメトリックについては、ポータルや従来の API で使用できる場合があります。 以下の一覧には、統合された Azure Monitor メトリック パイプラインで使うことができるメトリックのみが含まれます。 これらのメトリックを照会してアクセスするには、[2018-01-01 バージョンの API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) を使ってください。

> [!NOTE]
> 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。
>
> *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|平均|QPU。 範囲は S1 で 0 ～ 100、S2 で 0 ～ 200、S4 で 0 ～ 400|ServerResourceType|
|memory_metric|メモリ|バイト|平均|メモリ。 範囲は S1 で 0 ～ 25 GB、S2 で 0 ～ 50 GB、S4 で 0 ～ 100 GB|ServerResourceType|
|TotalConnectionRequests|合計接続要求数|Count|平均|合計接続要求数。 これらは到着した接続要求です。|ServerResourceType|
|SuccessfullConnectionsPerSec|成功した接続数 (秒単位)|CountPerSecond|平均|接続が正常に完了した割合。|ServerResourceType|
|TotalConnectionFailures|合計接続失敗数|Count|平均|失敗した接続試行数の合計。|ServerResourceType|
|CurrentUserSessions|現在のユーザー セッション|Count|平均|確立された現在のユーザー セッションの数。|ServerResourceType|
|QueryPoolBusyThreads|クエリ プール ビジー スレッド|Count|平均|クエリ スレッド プールのビジー スレッドの数。|ServerResourceType|
|CommandPoolJobQueueLength|コマンド プールのジョブ キューの長さ|Count|平均|コマンド スレッド プールのキュー内のジョブの数。|ServerResourceType|
|ProcessingPoolJobQueueLength|処理プール ジョブ キューの長さ|Count|平均|処理スレッド プールのキュー内の非 I/O ジョブの数。|ServerResourceType|
|CurrentConnections|接続:現在の接続数|Count|平均|現在確立されているクライアント接続の数。|ServerResourceType|
|CleanerCurrentPrice|メモリ:クリーナーの現在の価格|Count|平均|現在のメモリ価格 ($/バイト/時刻)。標準は 1,000 です。|ServerResourceType|
|CleanerMemoryShrinkable|メモリ:クリーナーの圧縮可能なメモリ|Bytes|平均|バックグラウンド クリーナーによる削除の対象となるメモリの量 (バイト単位)。|ServerResourceType|
|CleanerMemoryNonshrinkable|メモリ:クリーナーの圧縮不可能なメモリ|Bytes|平均|バックグラウンド クリーナーによる削除の対象とならないメモリの量 (バイト単位)。|ServerResourceType|
|MemoryUsage|メモリ:メモリ使用量|Bytes|平均|クリーナー メモリの価格の計算で使用されるサーバー プロセスのメモリ使用量。 Process\PrivateBytes カウンターの値に、メモリがマップされたデータのサイズを加えた値と等しくなります。xVelocity インメモリ分析エンジン (VertiPaq) によって xVelocity エンジンのメモリの制限を超えてマップされた (割り当てられた) メモリは無視されます。|ServerResourceType|
|MemoryLimitHard|メモリ:メモリの制限 - ハード|Bytes|平均|構成ファイルに指定されているハードのメモリの制限。|ServerResourceType|
|MemoryLimitHigh|メモリ:メモリの制限 - 高|Bytes|平均|構成ファイルに指定されているメモリの上限。|ServerResourceType|
|MemoryLimitLow|メモリ:メモリの制限 - 低|Bytes|平均|構成ファイルに指定されているメモリの下限。|ServerResourceType|
|MemoryLimitVertiPaq|メモリ:メモリの制限 - VertiPaq|Bytes|平均|構成ファイルに指定されているインメモリの制限。|ServerResourceType|
|Quota|メモリ:Quota|Bytes|平均|現在のメモリ クォータ (バイト単位)。 メモリ クォータはメモリ付与またはメモリ予約とも呼ばれます。|ServerResourceType|
|QuotaBlocked|メモリ:ブロックされているクォータ|Count|平均|他のメモリ クォータが解放されるまでブロックされている現在のクォータ要求の数。|ServerResourceType|
|VertiPaqNonpaged|メモリ:VertiPaq 非ページ|Bytes|平均|メモリ内エンジン用にワーキング セットでロックされているメモリの量 (バイト単位)。|ServerResourceType|
|VertiPaqPaged|メモリ:VertiPaq ページ|Bytes|平均|メモリ内エンジン用に使用されているページ メモリの量 (バイト単位)。|ServerResourceType|
|RowsReadPerSec|処理:1 秒あたりの読み取り行数|CountPerSecond|平均|すべてのリレーショナル データベースから読み取った行の比率。|ServerResourceType|
|RowsConvertedPerSec|処理:1 秒あたりの変換行数|CountPerSecond|平均|処理中に変換された行の比率。|ServerResourceType|
|RowsWrittenPerSec|処理:1 秒あたりの書き込み行数|CountPerSecond|平均|処理中に書き込まれた行の比率。|ServerResourceType|
|CommandPoolBusyThreads|スレッド:コマンド プールのビジー状態のスレッド|Count|平均|コマンド スレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|CommandPoolIdleThreads|スレッド:コマンド プールのアイドル状態のスレッド|Count|平均|コマンド スレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|LongParsingBusyThreads|スレッド:長時間解析を行っているビジー状態のスレッド|Count|平均|長時間解析を行っているスレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|LongParsingIdleThreads|スレッド:長時間解析を行っているアイドル状態のスレッド|Count|平均|長時間解析を行っているスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|LongParsingJobQueueLength|スレッド:長時間解析を行っているジョブのキューの長さ|Count|平均|長時間解析を行っているスレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|スレッド:処理中のプールのビジー状態の I/O ジョブのスレッド|Count|平均|処理中のスレッド プール内で I/O ジョブを実行しているスレッドの数。|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|スレッド:処理中のプールのビジー状態の I/O 以外のスレッド|Count|平均|処理中のスレッド プール内で I/O 以外のジョブを実行しているスレッドの数。|ServerResourceType|
|ProcessingPoolIOJobQueueLength|スレッド:処理中のプール I/O ジョブ キューの長さ|Count|平均|処理中のスレッド プールのキューに登録されている I/O ジョブの数。|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|スレッド:処理中のプールのアイドル状態の I/O ジョブのスレッド|Count|平均|処理中のスレッド プール内にある I/O ジョブのアイドル状態のスレッドの数。|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|スレッド:処理中のプールのアイドル状態の I/O 以外のスレッド|Count|平均|I/O 以外のジョブ専用の、処理中のスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|QueryPoolIdleThreads|スレッド:クエリ プールのアイドル状態のスレッド|Count|平均|処理中のスレッド プール内にある I/O ジョブのアイドル状態のスレッドの数。|ServerResourceType|
|QueryPoolJobQueueLength|スレッド:クエリ プール ジョブ キューの長さ|Count|平均|クエリ スレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|ShortParsingBusyThreads|スレッド:短時間解析を行っているビジー状態のスレッド|Count|平均|短時間解析を行っているスレッド プール内にあるビジー状態のスレッドの数。|ServerResourceType|
|ShortParsingIdleThreads|スレッド:短時間解析を行っているアイドル状態のスレッド|Count|平均|短時間解析を行っているスレッド プール内にあるアイドル状態のスレッドの数。|ServerResourceType|
|ShortParsingJobQueueLength|スレッド:短時間解析を行っているジョブ キューの長さ|Count|平均|短時間解析を行っているスレッド プールのキューに登録されているジョブの数。|ServerResourceType|
|memory_thrashing_metric|メモリ スラッシング|Percent|平均|平均的なメモリ スラッシング。|ServerResourceType|
|mashup_engine_qpu_metric|M エンジン QPU|Count|平均|マッシュアップ エンジン プロセスによる QPU 使用率|ServerResourceType|
|mashup_engine_memory_metric|M エンジン メモリ|Bytes|平均|マッシュアップ エンジン プロセスによるメモリ使用率|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TotalRequests|ゲートウェイ要求の合計|Count|合計|ゲートウェイ要求の数|Location、Hostname|
|SuccessfulRequests|成功したゲートウェイ要求|Count|合計|成功したゲートウェイ要求の数|Location、Hostname|
|UnauthorizedRequests|未承認ゲートウェイ要求|Count|合計|未承認ゲートウェイ要求の数|Location、Hostname|
|FailedRequests|失敗したゲートウェイ要求|Count|合計|ゲートウェイ要求における失敗の数|Location、Hostname|
|OtherRequests|その他のゲートウェイ要求|Count|合計|その他のゲートウェイ要求の数|Location、Hostname|
|Duration|ゲートウェイ要求の全体の期間|ミリ秒|平均|ゲートウェイ要求の全体の期間 (ミリ秒単位)|Location、Hostname|
|容量|容量|Percent|平均|ApiManagement サービスの使用状況メトリック|Location|
|EventHubTotalEvents|EventHub イベントの合計数|Count|合計|EventHub に送信されるイベントの数|Location|
|EventHubSuccessfulEvents|成功した EventHub イベント数|Count|合計|成功した EventHub イベントの数|Location|
|EventHubTotalFailedEvents|失敗した EventHub イベント数|Count|合計|失敗した EventHub イベントの数|Location|
|EventHubRejectedEvents|拒否された EventHub イベント数|Count|合計|拒否された EventHub イベントの数 (誤った構成または未承認)|Location|
|EventHubThrottledEvents|調整された EventHub イベント数|Count|合計|調整された EventHub イベントの数|Location|
|EventHubTimedoutEvents|タイムアウトした EventHub イベント数|Count|合計|タイムアウトした EventHub イベントの数|Location|
|EventHubDroppedEvents|破棄された EventHub イベント数|Count|合計|キューのサイズ制限に達したためスキップされたイベントの数|Location|
|EventHubTotalBytesSent|EventHub イベントのサイズ|Bytes|合計|EventHub イベントの合計サイズ (バイト単位)|Location|
|Requests|Requests|Count|合計|ゲートウェイ要求数|Location、BackendResponseCode、LastErrorReason、GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TotalJob|合計ジョブ数|Count|合計|ジョブの合計数|Runbook、Status|
|TotalUpdateDeploymentRuns|更新プログラムのデプロイの合計実行回数|Count|合計|ソフトウェア更新プログラムのデプロイの実行回数の合計|SoftwareUpdateConfigurationName、Status|
|TotalUpdateDeploymentMachineRuns|更新プログラムのデプロイ マシンの合計実行回数|Count|合計|ソフトウェア更新プログラムのデプロイの実行における、ソフトウェア更新プログラムのデプロイ マシンの実行回数の合計|SoftwareUpdateConfigurationName、Status、TargetComputer、SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count (専用コアの数)|Count|合計|Batch アカウントの専用コアの合計数|ディメンションなし|
|TotalNodeCount|Dedicated Node Count (専用ノードの数)|Count|合計|Batch アカウントの専用ノードの合計数|ディメンションなし|
|LowPriorityCoreCount|LowPriority Core Count (優先順位の低いコアの数)|Count|合計|Batch アカウントの優先順位の低いコアの合計数|ディメンションなし|
|TotalLowPriorityNodeCount|Low-Priority Node Count (優先順位の低いノードの数)|Count|合計|Batch アカウントの優先順位の低いノードの合計数|ディメンションなし|
|CreatingNodeCount|作成ノード数|Count|合計|作成されるノードの数|ディメンションなし|
|StartingNodeCount|開始ノード数|Count|合計|開始するノードの数|ディメンションなし|
|WaitingForStartTaskNodeCount|開始タスクの待機ノード数|Count|合計|開始タスクの完了を待機しているノードの数|ディメンションなし|
|StartTaskFailedNodeCount|開始タスクの失敗ノード数|Count|合計|開始タスクが失敗したノードの数|ディメンションなし|
|IdleNodeCount|アイドル状態のノード数|Count|合計|アイドル状態のノードの数|ディメンションなし|
|OfflineNodeCount|オフライン ノード数|Count|合計|オフライン ノードの数|ディメンションなし|
|RebootingNodeCount|再起動中のノード数|Count|合計|再起動中のノードの数|ディメンションなし|
|ReimagingNodeCount|再イメージ化中のノード数|Count|合計|再イメージ化中のノードの数|ディメンションなし|
|RunningNodeCount|実行中のノード数|Count|合計|実行中のノードの数|ディメンションなし|
|LeavingPoolNodeCount|プールから移動中のノード数|Count|合計|プールから移動中のノードの数|ディメンションなし|
|UnusableNodeCount|使用できないノード数|Count|合計|使用できないノードの数|ディメンションなし|
|PreemptedNodeCount|Preempted Node Count (割り込まれたノードの数)|Count|合計|割り込まれたノードの数|ディメンションなし|
|TaskStartEvent|タスク開始イベント|Count|合計|開始したタスクの合計数|ディメンションなし|
|TaskCompleteEvent|タスク完了イベント|Count|合計|完了したタスクの合計数|ディメンションなし|
|TaskFailEvent|タスク失敗イベント|Count|合計|障害状態で完了したタスクの合計数|ディメンションなし|
|PoolCreateEvent|プール作成イベント|Count|合計|作成されたプールの合計数|ディメンションなし|
|PoolResizeStartEvent|プールのサイズ変更の開始イベント|Count|合計|開始したプールのサイズ変更の合計数|ディメンションなし|
|PoolResizeCompleteEvent|プールのサイズ変更の完了イベント|Count|合計|完了したプールのサイズ変更の合計数|ディメンションなし|
|PoolDeleteStartEvent|プール削除の開始イベント|Count|合計|開始したプール削除の合計数|ディメンションなし|
|PoolDeleteCompleteEvent|プール削除の完了イベント|Count|合計|完了したプール削除の合計数|ディメンションなし|
|JobDeleteCompleteEvent|ジョブ削除の完了イベント|Count|合計|正常に削除されたジョブの合計数。|ディメンションなし|
|JobDeleteStartEvent|ジョブ削除の開始イベント|Count|合計|削除を要求されたジョブの合計数。|ディメンションなし|
|JobDisableCompleteEvent|ジョブ無効化の完了イベント|Count|合計|正常に無効化されたジョブの合計数。|ディメンションなし|
|JobDisableStartEvent|ジョブ無効化の開始イベント|Count|合計|無効化を要求されたジョブの合計数。|ディメンションなし|
|JobStartEvent|ジョブの開始イベント|Count|合計|正常に開始されたジョブの合計数。|ディメンションなし|
|JobTerminateCompleteEvent|ジョブ終了の完了イベント|Count|合計|正常に終了されたジョブの合計数。|ディメンションなし|
|JobTerminateStartEvent|ジョブ終了の開始イベント|Count|合計|終了を要求されたジョブの合計数。|ディメンションなし|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|connectedclients|接続されているクライアント数|Count|最大値||ShardId|
|totalcommandsprocessed|合計処理数|Count|合計||ShardId|
|cachehits|キャッシュ ヒット数|Count|合計||ShardId|
|cachemisses|キャッシュ ミス数|Count|合計||ShardId|
|getcommands|取得数|Count|合計||ShardId|
|setcommands|設定数|Count|合計||ShardId|
|operationsPerSecond|1 秒あたりの操作回数|Count|最大値||ShardId|
|evictedkeys|削除されたキー数|Count|合計||ShardId|
|totalkeys|合計キー数|Count|最大値||ShardId|
|expiredkeys|期限切れキー数|Count|合計||ShardId|
|usedmemory|メモリ使用量|Bytes|最大値||ShardId|
|usedmemorypercentage|使用されているメモリの割合|Percent|最大値||ShardId|
|usedmemoryRss|RSS メモリ使用量|Bytes|最大値||ShardId|
|serverLoad|サーバーの負荷|Percent|最大値||ShardId|
|cacheWrite|キャッシュの書き込み|BytesPerSecond|最大値||ShardId|
|cacheRead|キャッシュの読み取り|BytesPerSecond|最大値||ShardId|
|percentProcessorTime|CPU|Percent|最大値||ShardId|
|cacheLatency|キャッシュの待機時間 (マイクロ秒単位) (プレビュー)|Count|平均||ShardId、SampleType|
|errors|Errors|Count|最大値||ShardId、ErrorType|
|connectedclients0|接続されているクライアント数 (シャード 0)|Count|最大値||ディメンションなし|
|totalcommandsprocessed0|合計処理数 (シャード 0)|Count|合計||ディメンションなし|
|cachehits0|キャッシュ ヒット数 (シャード 0)|Count|合計||ディメンションなし|
|cachemisses0|キャッシュ ミス数 (シャード 0)|Count|合計||ディメンションなし|
|getcommands0|取得数 (シャード 0)|Count|合計||ディメンションなし|
|setcommands0|設定数 (シャード 0)|Count|合計||ディメンションなし|
|operationsPerSecond0|1 秒あたりの操作回数 (シャード 0)|Count|最大値||ディメンションなし|
|evictedkeys0|削除されたキー数 (シャード 0)|Count|合計||ディメンションなし|
|totalkeys0|合計キー数 (シャード 0)|Count|最大値||ディメンションなし|
|expiredkeys0|期限切れキー数 (シャード 0)|Count|合計||ディメンションなし|
|usedmemory0|メモリ使用量 (シャード 0)|Bytes|最大値||ディメンションなし|
|usedmemoryRss0|RSS メモリ使用量 (シャード 0)|Bytes|最大値||ディメンションなし|
|serverLoad0|サーバーの負荷 (シャード 0)|Percent|最大値||ディメンションなし|
|cacheWrite0|キャッシュの書き込み (シャード 0)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead0|キャッシュの読み取り (シャード 0)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime0|CPU (シャード 0)|Percent|最大値||ディメンションなし|
|connectedclients1|接続されているクライアント数 (シャード 1)|Count|最大値||ディメンションなし|
|totalcommandsprocessed1|合計処理数 (シャード 1)|Count|合計||ディメンションなし|
|cachehits1|キャッシュ ヒット数 (シャード 1)|Count|合計||ディメンションなし|
|cachemisses1|キャッシュ ミス数 (シャード 1)|Count|合計||ディメンションなし|
|getcommands1|取得数 (シャード 1)|Count|合計||ディメンションなし|
|setcommands1|設定数 (シャード 1)|Count|合計||ディメンションなし|
|operationsPerSecond1|1 秒あたりの操作回数 (シャード 1)|Count|最大値||ディメンションなし|
|evictedkeys1|削除されたキー数 (シャード 1)|Count|合計||ディメンションなし|
|totalkeys1|合計キー数 (シャード 1)|Count|最大値||ディメンションなし|
|expiredkeys1|期限切れキー数 (シャード 1)|Count|合計||ディメンションなし|
|usedmemory1|メモリ使用量 (シャード 1)|Bytes|最大値||ディメンションなし|
|usedmemoryRss1|RSS メモリ使用量 (シャード 1)|Bytes|最大値||ディメンションなし|
|serverLoad1|サーバーの負荷 (シャード 1)|Percent|最大値||ディメンションなし|
|cacheWrite1|キャッシュの書き込み (シャード 1)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead1|キャッシュの読み取り (シャード 1)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime1|CPU (シャード 1)|Percent|最大値||ディメンションなし|
|connectedclients2|接続されているクライアント数 (シャード 2)|Count|最大値||ディメンションなし|
|totalcommandsprocessed2|合計処理数 (シャード 2)|Count|合計||ディメンションなし|
|cachehits2|キャッシュ ヒット数 (シャード 2)|Count|合計||ディメンションなし|
|cachemisses2|キャッシュ ミス数 (シャード 2)|Count|合計||ディメンションなし|
|getcommands2|取得数 (シャード 2)|Count|合計||ディメンションなし|
|setcommands2|設定数 (シャード 2)|Count|合計||ディメンションなし|
|operationsPerSecond2|1 秒あたりの操作回数 (シャード 2)|Count|最大値||ディメンションなし|
|evictedkeys2|削除されたキー数 (シャード 2)|Count|合計||ディメンションなし|
|totalkeys2|合計キー数 (シャード 2)|Count|最大値||ディメンションなし|
|expiredkeys2|期限切れキー数 (シャード 2)|Count|合計||ディメンションなし|
|usedmemory2|メモリ使用量 (シャード 2)|Bytes|最大値||ディメンションなし|
|usedmemoryRss2|RSS メモリ使用量 (シャード 2)|Bytes|最大値||ディメンションなし|
|serverLoad2|サーバーの負荷 (シャード 2)|Percent|最大値||ディメンションなし|
|cacheWrite2|キャッシュの書き込み (シャード 2)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead2|キャッシュの読み取り (シャード 2)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime2|CPU (シャード 2)|Percent|最大値||ディメンションなし|
|connectedclients3|接続されているクライアント数 (シャード 3)|Count|最大値||ディメンションなし|
|totalcommandsprocessed3|合計処理数 (シャード 3)|Count|合計||ディメンションなし|
|cachehits3|キャッシュ ヒット数 (シャード 3)|Count|合計||ディメンションなし|
|cachemisses3|キャッシュ ミス数 (シャード 3)|Count|合計||ディメンションなし|
|getcommands3|取得数 (シャード 3)|Count|合計||ディメンションなし|
|setcommands3|設定数 (シャード 3)|Count|合計||ディメンションなし|
|operationsPerSecond3|1 秒あたりの操作回数 (シャード 3)|Count|最大値||ディメンションなし|
|evictedkeys3|削除されたキー数 (シャード 3)|Count|合計||ディメンションなし|
|totalkeys3|合計キー数 (シャード 3)|Count|最大値||ディメンションなし|
|expiredkeys3|期限切れキー数 (シャード 3)|Count|合計||ディメンションなし|
|usedmemory3|メモリ使用量 (シャード 3)|Bytes|最大値||ディメンションなし|
|usedmemoryRss3|RSS メモリ使用量 (シャード 3)|Bytes|最大値||ディメンションなし|
|serverLoad3|サーバーの負荷 (シャード 3)|Percent|最大値||ディメンションなし|
|cacheWrite3|キャッシュの書き込み (シャード 3)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead3|キャッシュの読み取り (シャード 3)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime3|CPU (シャード 3)|Percent|最大値||ディメンションなし|
|connectedclients4|接続されているクライアント数 (シャード 4)|Count|最大値||ディメンションなし|
|totalcommandsprocessed4|合計処理数 (シャード 4)|Count|合計||ディメンションなし|
|cachehits4|キャッシュ ヒット数 (シャード 4)|Count|合計||ディメンションなし|
|cachemisses4|キャッシュ ミス数 (シャード 4)|Count|合計||ディメンションなし|
|getcommands4|取得数 (シャード 4)|Count|合計||ディメンションなし|
|setcommands4|設定数 (シャード 4)|Count|合計||ディメンションなし|
|operationsPerSecond4|1 秒あたりの操作回数 (シャード 4)|Count|最大値||ディメンションなし|
|evictedkeys4|削除されたキー数 (シャード 4)|Count|合計||ディメンションなし|
|totalkeys4|合計キー数 (シャード 4)|Count|最大値||ディメンションなし|
|expiredkeys4|期限切れキー数 (シャード 4)|Count|合計||ディメンションなし|
|usedmemory4|メモリ使用量 (シャード 4)|Bytes|最大値||ディメンションなし|
|usedmemoryRss4|RSS メモリ使用量 (シャード 4)|Bytes|最大値||ディメンションなし|
|serverLoad4|サーバーの負荷 (シャード 4)|Percent|最大値||ディメンションなし|
|cacheWrite4|キャッシュの書き込み (シャード 4)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead4|キャッシュの読み取り (シャード 4)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime4|CPU (シャード 4)|Percent|最大値||ディメンションなし|
|connectedclients5|接続されているクライアント数 (シャード 5)|Count|最大値||ディメンションなし|
|totalcommandsprocessed5|合計処理数 (シャード 5)|Count|合計||ディメンションなし|
|cachehits5|キャッシュ ヒット数 (シャード 5)|Count|合計||ディメンションなし|
|cachemisses5|キャッシュ ミス数 (シャード 5)|Count|合計||ディメンションなし|
|getcommands5|取得数 (シャード 5)|Count|合計||ディメンションなし|
|setcommands5|設定数 (シャード 5)|Count|合計||ディメンションなし|
|operationsPerSecond5|1 秒あたりの操作回数 (シャード 5)|Count|最大値||ディメンションなし|
|evictedkeys5|削除されたキー数 (シャード 5)|Count|合計||ディメンションなし|
|totalkeys5|合計キー数 (シャード 5)|Count|最大値||ディメンションなし|
|expiredkeys5|期限切れキー数 (シャード 5)|Count|合計||ディメンションなし|
|usedmemory5|メモリ使用量 (シャード 5)|Bytes|最大値||ディメンションなし|
|usedmemoryRss5|RSS メモリ使用量 (シャード 5)|Bytes|最大値||ディメンションなし|
|serverLoad5|サーバーの負荷 (シャード 5)|Percent|最大値||ディメンションなし|
|cacheWrite5|キャッシュの書き込み (シャード 5)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead5|キャッシュの読み取り (シャード 5)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime5|CPU (シャード 5)|Percent|最大値||ディメンションなし|
|connectedclients6|接続されているクライアント数 (シャード 6)|Count|最大値||ディメンションなし|
|totalcommandsprocessed6|合計処理数 (シャード 6)|Count|合計||ディメンションなし|
|cachehits6|キャッシュ ヒット数 (シャード 6)|Count|合計||ディメンションなし|
|cachemisses6|キャッシュ ミス数 (シャード 6)|Count|合計||ディメンションなし|
|getcommands6|取得数 (シャード 6)|Count|合計||ディメンションなし|
|setcommands6|設定数 (シャード 6)|Count|合計||ディメンションなし|
|operationsPerSecond6|1 秒あたりの操作回数 (シャード 6)|Count|最大値||ディメンションなし|
|evictedkeys6|削除されたキー数 (シャード 6)|Count|合計||ディメンションなし|
|totalkeys6|合計キー数 (シャード 6)|Count|最大値||ディメンションなし|
|expiredkeys6|期限切れキー数 (シャード 6)|Count|合計||ディメンションなし|
|usedmemory6|メモリ使用量 (シャード 6)|Bytes|最大値||ディメンションなし|
|usedmemoryRss6|RSS メモリ使用量 (シャード 6)|Bytes|最大値||ディメンションなし|
|serverLoad6|サーバーの負荷 (シャード 6)|Percent|最大値||ディメンションなし|
|cacheWrite6|キャッシュの書き込み (シャード 6)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead6|キャッシュの読み取り (シャード 6)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime6|CPU (シャード 6)|Percent|最大値||ディメンションなし|
|connectedclients7|接続されているクライアント数 (シャード 7)|Count|最大値||ディメンションなし|
|totalcommandsprocessed7|合計処理数 (シャード 7)|Count|合計||ディメンションなし|
|cachehits7|キャッシュ ヒット数 (シャード 7)|Count|合計||ディメンションなし|
|cachemisses7|キャッシュ ミス数 (シャード 7)|Count|合計||ディメンションなし|
|getcommands7|取得数 (シャード 7)|Count|合計||ディメンションなし|
|setcommands7|設定数 (シャード 7)|Count|合計||ディメンションなし|
|operationsPerSecond7|1 秒あたりの操作回数 (シャード 7)|Count|最大値||ディメンションなし|
|evictedkeys7|削除されたキー数 (シャード 7)|Count|合計||ディメンションなし|
|totalkeys7|合計キー数 (シャード 7)|Count|最大値||ディメンションなし|
|expiredkeys7|期限切れキー数 (シャード 7)|Count|合計||ディメンションなし|
|usedmemory7|メモリ使用量 (シャード 7)|Bytes|最大値||ディメンションなし|
|usedmemoryRss7|RSS メモリ使用量 (シャード 7)|Bytes|最大値||ディメンションなし|
|serverLoad7|サーバーの負荷 (シャード 7)|Percent|最大値||ディメンションなし|
|cacheWrite7|キャッシュの書き込み (シャード 7)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead7|キャッシュの読み取り (シャード 7)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime7|CPU (シャード 7)|Percent|最大値||ディメンションなし|
|connectedclients8|接続されているクライアント数 (シャード 8)|Count|最大値||ディメンションなし|
|totalcommandsprocessed8|合計処理数 (シャード 8)|Count|合計||ディメンションなし|
|cachehits8|キャッシュ ヒット数 (シャード 8)|Count|合計||ディメンションなし|
|cachemisses8|キャッシュ ミス数 (シャード 8)|Count|合計||ディメンションなし|
|getcommands8|取得数 (シャード 8)|Count|合計||ディメンションなし|
|setcommands8|設定数 (シャード 8)|Count|合計||ディメンションなし|
|operationsPerSecond8|1 秒あたりの操作回数 (シャード 8)|Count|最大値||ディメンションなし|
|evictedkeys8|削除されたキー数 (シャード 8)|Count|合計||ディメンションなし|
|totalkeys8|合計キー数 (シャード 8)|Count|最大値||ディメンションなし|
|expiredkeys8|期限切れキー数 (シャード 8)|Count|合計||ディメンションなし|
|usedmemory8|メモリ使用量 (シャード 8)|Bytes|最大値||ディメンションなし|
|usedmemoryRss8|RSS メモリ使用量 (シャード 8)|Bytes|最大値||ディメンションなし|
|serverLoad8|サーバーの負荷 (シャード 8)|Percent|最大値||ディメンションなし|
|cacheWrite8|キャッシュの書き込み (シャード 8)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead8|キャッシュの読み取り (シャード 8)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime8|CPU (シャード 8)|Percent|最大値||ディメンションなし|
|connectedclients9|接続されているクライアント数 (シャード 9)|Count|最大値||ディメンションなし|
|totalcommandsprocessed9|合計処理数 (シャード 9)|Count|合計||ディメンションなし|
|cachehits9|キャッシュ ヒット数 (シャード 9)|Count|合計||ディメンションなし|
|cachemisses9|キャッシュ ミス数 (シャード 9)|Count|合計||ディメンションなし|
|getcommands9|取得数 (シャード 9)|Count|合計||ディメンションなし|
|setcommands9|設定数 (シャード 9)|カウント|合計||ディメンションなし|
|operationsPerSecond9|1 秒あたりの操作回数 (シャード 9)|Count|最大値||ディメンションなし|
|evictedkeys9|削除されたキー数 (シャード 9)|Count|合計||ディメンションなし|
|totalkeys9|合計キー数 (シャード 9)|Count|最大値||ディメンションなし|
|expiredkeys9|期限切れキー数 (シャード 9)|Count|合計||ディメンションなし|
|usedmemory9|メモリ使用量 (シャード 9)|Bytes|最大値||ディメンションなし|
|usedmemoryRss9|RSS メモリ使用量 (シャード 9)|Bytes|最大値||ディメンションなし|
|serverLoad9|サーバーの負荷 (シャード 9)|Percent|最大値||ディメンションなし|
|cacheWrite9|キャッシュの書き込み (シャード 9)|BytesPerSecond|最大値||ディメンションなし|
|cacheRead9|キャッシュの読み取り (シャード 9)|BytesPerSecond|最大値||ディメンションなし|
|percentProcessorTime9|CPU (シャード 9)|Percent|最大値||ディメンションなし|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合。|ディメンションなし|
|Network In|ネットワーク受信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)。|ディメンションなし|
|ネットワーク送信|ネットワーク送信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)。|ディメンションなし|
|Disk Read Bytes/Sec|ディスクの読み取り|BytesPerSecond|平均|監視期間中にディスクから読み取られた平均バイト数。|ディメンションなし|
|Disk Write Bytes/Sec|ディスクの書き込み|BytesPerSecond|平均|監視期間中にディスクに書き込まれた平均バイト数。|ディメンションなし|
|Disk Read Operations/Sec|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS。|ディメンションなし|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS。|ディメンションなし|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合。|RoleInstanceId|
|Network In|ネットワーク受信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)。|RoleInstanceId|
|ネットワーク送信|ネットワーク送信|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)。|RoleInstanceId|
|Disk Read Bytes/Sec|ディスクの読み取り|BytesPerSecond|平均|監視期間中にディスクから読み取られた平均バイト数。|RoleInstanceId|
|Disk Write Bytes/Sec|ディスクの書き込み|BytesPerSecond|平均|監視期間中にディスクに書き込まれた平均バイト数。|RoleInstanceId|
|Disk Read Operations/Sec|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS。|RoleInstanceId|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS。|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TotalCalls|合計呼び出し数|Count|合計|呼び出しの合計数。|ApiName、OperationName、Region|
|SuccessfulCalls|成功した呼び出し|Count|合計|成功した呼び出しの数。|ApiName、OperationName、Region|
|TotalErrors|合計エラー数|Count|合計|エラー応答 (HTTP 応答コード 4xx または 5xx) があった呼び出しの合計回数。|ApiName、OperationName、Region|
|BlockedCalls|ブロックされた呼び出し|Count|合計|レートまたはクォータの制限を超えた呼び出しの回数。|ApiName、OperationName、Region|
|ServerErrors|サーバー エラー|Count|合計|サービスの内部エラー (HTTP 応答コード 5xx) があった呼び出しの回数。|ApiName、OperationName、Region|
|ClientErrors|クライアント エラー|Count|合計|クライアント側のエラー (HTTP 応答コード 4xx) があった呼び出しの回数。|ApiName、OperationName、Region|
|DataIn|受信データ|Bytes|合計|受信データのサイズ (バイト)。|ApiName、OperationName、Region|
|DataOut|送信データ|Bytes|合計|送信データのサイズ (バイト)。|ApiName、OperationName、Region|
|Latency|Latency|MilliSeconds|平均|待機時間 (ミリ秒)。|ApiName、OperationName、Region|
|CharactersTranslated|Characters Translated (変換文字数)|Count|合計|受信テキスト要求の合計文字数。|ApiName、OperationName、Region|
|CharactersTrained|トレーニングされた文字数|Count|合計|トレーニングされた文字の合計数。|ApiName、OperationName、Region|
|SpeechSessionDuration|Speech Session Duration (音声セッション継続時間)|Seconds|合計|音声セッションの合計継続時間 (秒)。|ApiName、OperationName、Region|
|TotalTransactions|合計トランザクション|Count|合計|トランザクションの合計数。|ディメンションなし|
|TotalTokenCalls|トークン呼び出しの合計|Count|合計|トークン呼び出しの合計回数。|ApiName、OperationName、Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|ディメンションなし|
|Network In|課金対象の受信ネットワーク|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信した課金対象のバイト数 (受信トラフィック)|ディメンションなし|
|ネットワーク送信|課金対象の送信ネットワーク|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信した課金対象のバイト数 (送信トラフィック)|ディメンションなし|
|Disk Read Bytes|ディスク読み取りバイト数|Bytes|合計|監視期間中にディスクから読み取られたバイト数|ディメンションなし|
|Disk Write Bytes|ディスク書き込みバイト数|Bytes|合計|監視期間中にディスクに書き込まれたバイト数|ディメンションなし|
|Disk Read Operations/Sec|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS|ディメンションなし|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS|ディメンションなし|
|CPU Credits Remaining|未使用の CPU クレジット|Count|平均|バーストに使用できるクレジットの合計|ディメンションなし|
|CPU Credits Consumed|使用済みの CPU クレジット|Count|平均|仮想マシンによって消費されたクレジットの合計数|ディメンションなし|
|Per Disk Read Bytes/sec|データ ディスク読み取りバイト数/秒 (非推奨)|CountPerSecond|平均|監視期間中に 1 つのディスクから読み取られたバイト数/秒|SlotId|
|Per Disk Write Bytes/sec|データ ディスク書き込みバイト数/秒 (非推奨)|CountPerSecond|平均|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|SlotId|
|Per Disk Read Operations/Sec|データ ディスク読み取り操作数/秒 (非推奨)|CountPerSecond|平均|監視期間中の 1 つのディスクからの読み取り IOPS|SlotId|
|Per Disk Write Operations/Sec|データ ディスク書き込み操作数/秒 (非推奨)|CountPerSecond|平均|監視期間中の 1 つのディスクからの書き込み IOPS|SlotId|
|Per Disk QD|データ ディスク QD (非推奨)|Count|平均|データ ディスクのキューの深さ (またはキューの長さ)|SlotId|
|OS Per Disk Read Bytes/sec|OS ディスク読み取りバイト数/秒 (非推奨)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|ディメンションなし|
|OS Per Disk Write Bytes/sec|OS ディスク書き込みバイト数/秒 (非推奨)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|ディメンションなし|
|OS Per Disk Read Operations/Sec|OS ディスク読み取り操作数/秒 (非推奨)|CountPerSecond|平均|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|ディメンションなし|
|OS Per Disk Write Operations/Sec|OS ディスク書き込み操作数/秒 (非推奨)|CountPerSecond|平均|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|ディメンションなし|
|OS Per Disk QD|OS ディスク QD (非推奨)|Count|平均|OS ディスクのキューの深さ (またはキューの長さ)|ディメンションなし|
|データ ディスク読み取りバイト数/秒|データ ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|平均|監視期間中に 1 つのディスクから読み取られたバイト数/秒|LUN|
|データ ディスク書き込みバイト数/秒|データ ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|平均|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|LUN|
|データ ディスク読み取り操作数/秒|データ ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|平均|監視期間中の 1 つのディスクからの読み取り IOPS|LUN|
|データ ディスク書き込み操作数/秒|データ ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|平均|監視期間中の 1 つのディスクからの書き込み IOPS|LUN|
|データ ディスクのキューの深さ|データ ディスクのキューの深さ (プレビュー)|Count|平均|データ ディスクのキューの深さ (またはキューの長さ)|LUN|
|OS ディスク読み取りバイト数/秒|OS ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|ディメンションなし|
|OS ディスク書き込みバイト数/秒|OS ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|ディメンションなし|
|OS ディスク読み取り操作数/秒|OS ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|ディメンションなし|
|OS ディスク書き込み操作数/秒|OS ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|ディメンションなし|
|OS ディスクのキューの深さ|OS ディスクのキューの深さ (プレビュー)|Count|平均|OS ディスクのキューの深さ (またはキューの長さ)|ディメンションなし|
|受信フロー数|受信フロー数 (プレビュー)|Count|平均|受信フロー数は、受信方向の現在のフロー数です (VM に送信されるトラフィック)|ディメンションなし|
|送信フロー数|送信フロー数 (プレビュー)|Count|平均|送信フロー数は、送信方向の現在のフロー数です (VM から送信されるトラフィック)|ディメンションなし|
|受信フローの最大作成速度|受信フローの最大作成速度 (プレビュー)|CountPerSecond|平均|受信フローの最大作成速度 (VM に送信されるトラフィック)|ディメンションなし|
|送信フローの最大作成速度|送信フローの最大作成速度 (プレビュー)|CountPerSecond|平均|送信フローの最大作成速度 (VM から送信されるトラフィック)|ディメンションなし|
|Premium データ ディスク キャッシュ読み取りヒット|Premium データ ディスク キャッシュ読み取りヒット (プレビュー)|Percent|平均|Premium データ ディスク キャッシュ読み取りヒット|LUN|
|Premium データ ディスク キャッシュ読み取りミス|Premium データ ディスク キャッシュ読み取りミス (プレビュー)|Percent|平均|Premium データ ディスク キャッシュ読み取りミス|LUN|
|Premium OS ディスク キャッシュ読み取りヒット|Premium OS ディスク キャッシュ読み取りヒット (プレビュー)|Percent|平均|Premium OS ディスク キャッシュ読み取りヒット|ディメンションなし|
|Premium OS ディスク キャッシュ読み取りミス|Premium OS ディスク キャッシュ読み取りミス (プレビュー)|Percent|平均|Premium OS ディスク キャッシュ読み取りミス|ディメンションなし|
|受信ネットワーク合計|受信ネットワーク合計|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|ディメンションなし|
|送信ネットワーク合計|送信ネットワーク合計|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|ディメンションなし|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|CPU の割合|Percent|平均|仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合|VMName|
|Network In|課金対象の受信ネットワーク|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信した課金対象のバイト数 (受信トラフィック)|VMName|
|ネットワーク送信|課金対象の送信ネットワーク|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信した課金対象のバイト数 (送信トラフィック)|VMName|
|Disk Read Bytes|ディスク読み取りバイト数|Bytes|合計|監視期間中にディスクから読み取られたバイト数|VMName|
|Disk Write Bytes|ディスク書き込みバイト数|Bytes|合計|監視期間中にディスクに書き込まれたバイト数|VMName|
|Disk Read Operations/Sec|ディスク読み取り操作数/秒|CountPerSecond|平均|ディスク読み取り IOPS|VMName|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|ディスク書き込み IOPS|VMName|
|未使用の CPU クレジット|未使用の CPU クレジット|Count|平均|バーストに使用できるクレジットの合計|ディメンションなし|
|CPU Credits Consumed|使用済みの CPU クレジット|Count|平均|仮想マシンによって消費されたクレジットの合計数|ディメンションなし|
|Per Disk Read Bytes/sec|データ ディスク読み取りバイト数/秒 (非推奨)|CountPerSecond|平均|監視期間中に 1 つのディスクから読み取られたバイト数/秒|SlotId|
|Per Disk Write Bytes/sec|データ ディスク書き込みバイト数/秒 (非推奨)|CountPerSecond|平均|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|SlotId|
|Per Disk Read Operations/Sec|データ ディスク読み取り操作数/秒 (非推奨)|CountPerSecond|平均|監視期間中の 1 つのディスクからの読み取り IOPS|SlotId|
|Per Disk Write Operations/Sec|データ ディスク書き込み操作数/秒 (非推奨)|CountPerSecond|平均|監視期間中の 1 つのディスクからの書き込み IOPS|SlotId|
|Per Disk QD|データ ディスク QD (非推奨)|Count|平均|データ ディスクのキューの深さ (またはキューの長さ)|SlotId|
|OS Per Disk Read Bytes/sec|OS ディスク読み取りバイト数/秒 (非推奨)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|ディメンションなし|
|OS Per Disk Write Bytes/sec|OS ディスク書き込みバイト数/秒 (非推奨)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|ディメンションなし|
|OS Per Disk Read Operations/Sec|OS ディスク読み取り操作数/秒 (非推奨)|CountPerSecond|平均|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|ディメンションなし|
|OS Per Disk Write Operations/Sec|OS ディスク書き込み操作数/秒 (非推奨)|CountPerSecond|平均|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|ディメンションなし|
|OS Per Disk QD|OS ディスク QD (非推奨)|Count|平均|OS ディスクのキューの深さ (またはキューの長さ)|ディメンションなし|
|データ ディスク読み取りバイト数/秒|データ ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|平均|監視期間中に 1 つのディスクから読み取られたバイト数/秒|LUN、VMName|
|データ ディスク書き込みバイト数/秒|データ ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|平均|監視期間中に 1 つのディスクに書き込まれたバイト数/秒|LUN、VMName|
|データ ディスク読み取り操作数/秒|データ ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|平均|監視期間中の 1 つのディスクからの読み取り IOPS|LUN、VMName|
|データ ディスク書き込み操作数/秒|データ ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|平均|監視期間中の 1 つのディスクからの書き込み IOPS|LUN、VMName|
|データ ディスクのキューの深さ|データ ディスクのキューの深さ (プレビュー)|Count|平均|データ ディスクのキューの深さ (またはキューの長さ)|LUN、VMName|
|OS ディスク読み取りバイト数/秒|OS ディスク読み取りバイト数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクから読み取られたバイト数/秒|VMName|
|OS ディスク書き込みバイト数/秒|OS ディスク書き込みバイト数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中に 1 つのディスクに書き込まれたバイト数/秒|VMName|
|OS ディスク読み取り操作数/秒|OS ディスク読み取り操作数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中の 1 つのディスクからの読み取り IOPS|VMName|
|OS ディスク書き込み操作数/秒|OS ディスク書き込み操作数/秒 (プレビュー)|CountPerSecond|平均|OS ディスクの監視期間中の 1 つのディスクからの書き込み IOPS|VMName|
|OS ディスクのキューの深さ|OS ディスクのキューの深さ (プレビュー)|Count|平均|OS ディスクのキューの深さ (またはキューの長さ)|VMName|
|受信フロー数|受信フロー数 (プレビュー)|Count|平均|受信フロー数は、受信方向の現在のフロー数です (VM に送信されるトラフィック)|VMName|
|送信フロー数|送信フロー数 (プレビュー)|Count|平均|送信フロー数は、送信方向の現在のフロー数です (VM から送信されるトラフィック)|VMName|
|受信フローの最大作成速度|受信フローの最大作成速度 (プレビュー)|CountPerSecond|平均|受信フローの最大作成速度 (VM に送信されるトラフィック)|VMName|
|送信フローの最大作成速度|送信フローの最大作成速度 (プレビュー)|CountPerSecond|平均|送信フローの最大作成速度 (VM から送信されるトラフィック)|VMName|
|Premium データ ディスク キャッシュ読み取りヒット|Premium データ ディスク キャッシュ読み取りヒット (プレビュー)|Percent|平均|Premium データ ディスク キャッシュ読み取りヒット|LUN、VMName|
|Premium データ ディスク キャッシュ読み取りミス|Premium データ ディスク キャッシュ読み取りミス (プレビュー)|Percent|平均|Premium データ ディスク キャッシュ読み取りミス|LUN、VMName|
|Premium OS ディスク キャッシュ読み取りヒット|Premium OS ディスク キャッシュ読み取りヒット (プレビュー)|Percent|平均|Premium OS ディスク キャッシュ読み取りヒット|VMName|
|Premium OS ディスク キャッシュ読み取りミス|Premium OS ディスク キャッシュ読み取りミス (プレビュー)|Percent|平均|Premium OS ディスク キャッシュ読み取りミス|VMName|
|受信ネットワーク合計|受信ネットワーク合計|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが受信したバイト数 (受信トラフィック)|VMName|
|送信ネットワーク合計|送信ネットワーク合計|Bytes|合計|すべてのネットワーク インターフェイスで仮想マシンが送信したバイト数 (送信トラフィック)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CpuUsage|CPU 使用率|Count|平均|すべてのコアの CPU 使用率 (ミリコア単位)。|containerName|
|MemoryUsage|メモリ使用量|Bytes|平均|合計メモリ使用量 (バイト単位)。|containerName|
|NetworkBytesReceivedPerSecond|1 秒あたりの受信ネットワーク バイト数|Bytes|平均|1 秒あたりの受信ネットワーク バイト数。|ディメンションなし|
|NetworkBytesTransmittedPerSecond|1 秒あたりの送信ネットワーク バイト数|Bytes|平均|1 秒あたりの送信ネットワーク バイト数。|ディメンションなし|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TotalPullCount|プルの合計数|Count|平均|イメージのプルの合計数|ディメンションなし|
|SuccessfulPullCount|成功したプルの数|Count|平均|成功したイメージ プルの数|ディメンションなし|
|TotalPushCount|合計のプッシュ数|Count|平均|イメージのプッシュの合計数|ディメンションなし|
|SuccessfulPushCount|成功したプッシュの数|Count|平均|成功したイメージ プッシュの数|ディメンションなし|
|RunDuration|実行継続時間|ミリ秒|合計|実行継続時間 (ミリ秒単位)|ディメンションなし|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|マネージド クラスターで使用可能な CPU コアの合計数|Count|合計|マネージド クラスターで使用可能な CPU コアの合計数|ディメンションなし|
|kube_node_status_allocatable_memory_bytes|マネージド クラスターで使用可能なメモリの合計量|Bytes|合計|マネージド クラスターで使用可能なメモリの合計量|ディメンションなし|
|kube_pod_status_ready|準備完了状態のポッドの数|Count|合計|準備完了状態のポッドの数|namespace、pod|
|kube_node_status_condition|さまざまなノードの状態の条件|Count|合計|さまざまなノードの状態の条件|condition、status、status2、node|
|kube_pod_status_phase|フェーズごとのポッドの数|Count|合計|フェーズごとのポッドの数|phase、namespace、pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls (Customer Insights API 呼び出し)|Count|合計||ディメンションなし|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines (マッピングのインポート操作に成功した行数)|Count|合計||ディメンションなし|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines (マッピングのインポート操作に失敗した行数)|Count|合計||ディメンションなし|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines (マッピングのインポート操作の合計行数)|Count|合計||ディメンションなし|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds (マッピングのインポート操作の実行時間 (秒))|Seconds|合計||ディメンションなし|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded (成功した送信プロファイルのエクスポート)|Count|合計||ディメンションなし|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed (失敗した送信プロファイルのエクスポート)|Count|合計||ディメンションなし|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration (送信プロファイルのエクスポート時間)|Seconds|合計||ディメンションなし|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded (成功した送信 KPI のエクスポート)|Count|合計||ディメンションなし|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed (失敗した送信 KPI のエクスポート)|Count|合計||ディメンションなし|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration (送信 KPI のエクスポート時間)|Seconds|合計||ディメンションなし|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started (送信 KPI のエクスポートの開始)|Seconds|合計||ディメンションなし|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count (送信 KPI レコードの数)|Seconds|合計||ディメンションなし|
|DCIOutboundProfileExportCount|Outbound Profile Export Count (送信プロファイルのエクスポートの数)|Seconds|合計||ディメンションなし|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed (失敗した送信初期プロファイルのエクスポート)|Seconds|合計||ディメンションなし|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded (成功した送信初期プロファイルのエクスポート)|Seconds|合計||ディメンションなし|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed (失敗した送信初期 KPI のエクスポート)|Seconds|合計||ディメンションなし|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded (成功した送信初期 KPI のエクスポート)|Seconds|合計||ディメンションなし|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds (送信初期プロファイルのエクスポート時間 (秒))|Seconds|合計||ディメンションなし|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds (失敗した標準 KPI の Adla ジョブ (秒))|Seconds|合計||ディメンションなし|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds (標準 KPI の Adla ジョブのタイムアウト (秒))|Seconds|合計||ディメンションなし|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds (完了した標準 KPI の Adla ジョブ (秒))|Seconds|合計||ディメンションなし|
|ImportASAValuesFailed|Import ASA Values Failed Count (ASA 値のインポートの失敗数)|Count|合計||ディメンションなし|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count (ASA 値のインポートの成功数)|Count|合計||ディメンションなし|
|DCIProfilesCount|Profile Instance Count (プロファイル インスタンス数)|Count|Last (最後へ)||ディメンションなし|
|DCIInteractionsPerMonthCount|1 か月間のインタラクション数|Count|Last (最後へ)||ディメンションなし|
|DCIKpisCount|KPI Count (KPI の数)|Count|Last (最後へ)||ディメンションなし|
|DCISegmentsCount|Segment Count (セグメントの数)|Count|Last (最後へ)||ディメンションなし|
|DCIPredictiveMatchPoliciesCount|Predictive Match Count (予測マッチの数)|Count|Last (最後へ)||ディメンションなし|
|DCIPredictionsCount|Prediction Count (予測の数)|Count|Last (最後へ)||ディメンションなし|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|NICReadThroughput|読み取りスループット (ネットワーク)|BytesPerSecond|平均|ゲートウェイのすべてのボリュームに対するレポート期間におけるデバイスでのネットワーク インターフェイスの読み取りスループット。|InstanceName|
|NICWriteThroughput|書き込みスループット (ネットワーク)|BytesPerSecond|平均|ゲートウェイのすべてのボリュームに対するレポート期間におけるデバイスでのネットワーク インターフェイスの書き込みスループット。|InstanceName|
|CloudReadThroughputPerShare|クラウドのダウンロード スループット (共有)|BytesPerSecond|平均|レポート期間中の共有から Azure へのダウンロード スループット。|共有|
|CloudUploadThroughputPerShare|クラウドへのアップロード時のスループット (共有)|BytesPerSecond|平均|レポート期間中の共有から Azure へのアップロード スループット。|共有|
|BytesUploadedToCloudPerShare|クラウドのアップロードされたバイト数 (共有)|Bytes|平均|レポート期間中に共有から Azure にアップロードされるバイトの合計数。|共有|
|TotalCapacity|合計容量|Bytes|平均|合計容量|ディメンションなし|
|AvailableCapacity|使用可能な容量|Bytes|平均|レポート期間中の使用可能な容量 (バイト単位)。|ディメンションなし|
|CloudUploadThroughput|クラウド アップロード スループット|BytesPerSecond|平均|レポート期間中の Azure へのクラウド アップロード スループット。|ディメンションなし|
|CloudReadThroughput|クラウド ダウンロード スループット|BytesPerSecond|平均|レポート期間中の Azure へのクラウド ダウンロード スループット。|ディメンションなし|
|BytesUploadedToCloud|クラウドのアップロードされたバイト数 (デバイス)|Bytes|平均|レポート期間中にデバイスから Azure にアップロードされるバイトの合計数。|ディメンションなし|
|HyperVVirtualProcessorUtilization|Edge コンピューティング - CPU 使用率|Percent|平均|CPU 使用率 (パーセント)|InstanceName|
|HyperVMemoryUtilization|Edge コンピューティング - メモリ使用量|Percent|平均|使用中の RAM の容量|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|FailedRuns|失敗した実行|Count|合計||pipelineName、activityName|
|SuccessfulRuns|成功した実行|Count|合計||pipelineName、activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PipelineFailedRuns|失敗したパイプライン実行回数のメトリック|Count|合計||FailureType、Name|
|PipelineSucceededRuns|成功したパイプライン実行回数のメトリック|Count|合計||FailureType、Name|
|ActivityFailedRuns|失敗したアクティビティ実行回数のメトリック|Count|合計||ActivityType、PipelineName、FailureType、Name|
|ActivitySucceededRuns|成功したアクティビティ実行回数のメトリック|Count|合計||ActivityType、PipelineName、FailureType、Name|
|TriggerFailedRuns|失敗したトリガー実行の回数メトリック|Count|合計||Name、FailureType|
|TriggerSucceededRuns|成功したトリガー実行の回数メトリック|Count|合計||Name、FailureType|
|IntegrationRuntimeCpuPercentage|統合実行時の CPU 使用率|Percent|平均||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeAvailableMemory|統合実行時に使用可能なメモリ|Bytes|平均||IntegrationRuntimeName、NodeName|
|MaxAllowedResourceCount|許可されているエンティティの最大数|Count|最大値||ディメンションなし|
|MaxAllowedFactorySizeInGbUnits|許可されている最大の工場出荷時サイズ (GB 単位)|Count|最大値||ディメンションなし|
|ResourceCount|エンティティの合計数|Count|最大値||ディメンションなし|
|FactorySizeInGbUnits|合計の工場出荷時サイズ (GB 単位)|Count|最大値||ディメンションなし|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs (成功したジョブ)|Count|合計|成功したジョブの数。|ディメンションなし|
|JobEndedFailure|Failed Jobs (失敗したジョブ)|Count|合計|失敗したジョブの数。|ディメンションなし|
|JobEndedCancelled|取り消されたジョブ|Count|合計|取り消されたジョブの数。|ディメンションなし|
|JobAUEndedSuccess|Successful AU Time (成功した AU 時間)|Seconds|合計|成功したジョブの AU 時間の合計。|ディメンションなし|
|JobAUEndedFailure|Failed AU Time (失敗した AU 時間)|Seconds|合計|失敗したジョブの AU 時間の合計。|ディメンションなし|
|JobAUEndedCancelled|取り消された AU 時間|Seconds|合計|取り消されたジョブの AU 時間の合計。|ディメンションなし|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TotalStorage|保存量の合計|Bytes|最大値|アカウントに保存されたデータの総量。|ディメンションなし|
|DataWritten|データ書き込み量|Bytes|合計|アカウントに書き込まれたデータの総量。|ディメンションなし|
|DataRead|データ読み取り量|Bytes|合計|アカウントから読み取られたデータの総量。|ディメンションなし|
|WriteRequests|書き込み要求|Count|合計|アカウントへのデータ書き込み要求の数。|ディメンションなし|
|ReadRequests|読み取り要求|Count|合計|アカウントへのデータ読み取り要求の数。|ディメンションなし|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|平均|CPU 使用率|ディメンションなし|
|memory_percent|メモリの割合|Percent|平均|メモリの割合|ディメンションなし|
|io_consumption_percent|IO の割合|Percent|平均|IO の割合|ディメンションなし|
|storage_percent|ストレージの割合|Percent|平均|ストレージの割合|ディメンションなし|
|storage_used|使用済みストレージ|Bytes|平均|使用済みストレージ|ディメンションなし|
|storage_limit|ストレージの制限|Bytes|平均|ストレージの制限|ディメンションなし|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|平均|サーバー ログ ストレージの割合|ディメンションなし|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|Bytes|平均|サーバー ログ ストレージの使用量|ディメンションなし|
|serverlog_storage_limit|サーバー ログ ストレージの上限|Bytes|平均|サーバー ログ ストレージの上限|ディメンションなし|
|active_connections|アクティブな接続|Count|平均|アクティブな接続|ディメンションなし|
|connections_failed|失敗した接続|Count|合計|失敗した接続|ディメンションなし|
|seconds_behind_master|レプリケーションのラグ (秒単位)|Count|平均|レプリケーションのラグ (秒単位)|ディメンションなし|
|backup_storage_used|使用されたバックアップ ストレージ|Bytes|平均|使用されたバックアップ ストレージ|ディメンションなし|
|network_bytes_egress|ネットワーク送信|Bytes|合計|アクティブな接続全体のネットワーク送信|ディメンションなし|
|network_bytes_ingress|Network In|Bytes|合計|アクティブな接続全体のネットワーク受信|ディメンションなし|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|平均|CPU 使用率|ディメンションなし|
|memory_percent|メモリの割合|Percent|平均|メモリの割合|ディメンションなし|
|io_consumption_percent|IO の割合|Percent|平均|IO の割合|ディメンションなし|
|storage_percent|ストレージの割合|Percent|平均|ストレージの割合|ディメンションなし|
|storage_used|使用済みストレージ|Bytes|平均|使用済みストレージ|ディメンションなし|
|storage_limit|ストレージの制限|Bytes|平均|ストレージの制限|ディメンションなし|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|平均|サーバー ログ ストレージの割合|ディメンションなし|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|Bytes|平均|サーバー ログ ストレージの使用量|ディメンションなし|
|serverlog_storage_limit|サーバー ログ ストレージの上限|Bytes|平均|サーバー ログ ストレージの上限|ディメンションなし|
|active_connections|アクティブな接続|Count|平均|アクティブな接続|ディメンションなし|
|connections_failed|失敗した接続|Count|合計|失敗した接続|ディメンションなし|
|seconds_behind_master|レプリケーションのラグ (秒単位)|Count|平均|レプリケーションのラグ (秒単位)|ディメンションなし|
|backup_storage_used|使用されたバックアップ ストレージ|Bytes|平均|使用されたバックアップ ストレージ|ディメンションなし|
|network_bytes_egress|ネットワーク送信|Bytes|合計|アクティブな接続全体のネットワーク送信|ディメンションなし|
|network_bytes_ingress|Network In|Bytes|合計|アクティブな接続全体のネットワーク受信|ディメンションなし|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|平均|CPU 使用率|ディメンションなし|
|memory_percent|メモリの割合|Percent|平均|メモリの割合|ディメンションなし|
|io_consumption_percent|IO の割合|Percent|平均|IO の割合|ディメンションなし|
|storage_percent|ストレージの割合|Percent|平均|ストレージの割合|ディメンションなし|
|storage_used|使用済みストレージ|Bytes|平均|使用済みストレージ|ディメンションなし|
|storage_limit|ストレージの制限|Bytes|平均|ストレージの制限|ディメンションなし|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|平均|サーバー ログ ストレージの割合|ディメンションなし|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|Bytes|平均|サーバー ログ ストレージの使用量|ディメンションなし|
|serverlog_storage_limit|サーバー ログ ストレージの上限|Bytes|平均|サーバー ログ ストレージの上限|ディメンションなし|
|active_connections|アクティブな接続|Count|平均|アクティブな接続|ディメンションなし|
|connections_failed|失敗した接続|Count|合計|失敗した接続|ディメンションなし|
|backup_storage_used|使用されたバックアップ ストレージ|Bytes|平均|使用されたバックアップ ストレージ|ディメンションなし|
|network_bytes_egress|ネットワーク送信|Bytes|合計|アクティブな接続全体のネットワーク送信|ディメンションなし|
|network_bytes_ingress|Network In|Bytes|合計|アクティブな接続全体のネットワーク受信|ディメンションなし|
|pg_replica_log_delay_in_seconds|Replica Lag (レプリカ ラグ)|Seconds|最大値|レプリカのラグ (秒単位)|ディメンションなし|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (レプリカ間の最大ラグ)|Bytes|最大値|最も遅れているレプリカのラグ (バイト単位)|ディメンションなし|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|平均|CPU 使用率|ディメンションなし|
|memory_percent|メモリの割合|Percent|平均|メモリの割合|ディメンションなし|
|iops|IOPS|Count|平均|1 秒あたりの IO 操作回数|ディメンションなし|
|storage_percent|ストレージの割合|Percent|平均|ストレージの割合|ディメンションなし|
|storage_used|使用済みストレージ|Bytes|平均|使用済みストレージ|ディメンションなし|
|active_connections|アクティブな接続|Count|平均|アクティブな接続|ディメンションなし|
|network_bytes_egress|ネットワーク送信|Bytes|合計|アクティブな接続全体のネットワーク送信|ディメンションなし|
|network_bytes_ingress|Network In|Bytes|合計|アクティブな接続全体のネットワーク受信|ディメンションなし|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|テレメトリ メッセージ送信試行|Count|合計|IoT Hub への送信が試行された Device to Cloud テレメトリ メッセージの数|ディメンションなし|
|d2c.telemetry.ingress.success|送信済みテレメトリ メッセージ|Count|合計|IoT Hub に正常に送信された Device to Cloud テレメトリ メッセージの数|ディメンションなし|
|c2d.commands.egress.complete.success|完了したコマンド|Count|合計|デバイスで正常に完了した Cloud to Device コマンドの数|ディメンションなし|
|c2d.commands.egress.abandon.success|中止したコマンド|Count|合計|デバイスで中止された Cloud to Device コマンドの数|ディメンションなし|
|c2d.commands.egress.reject.success|拒否されたコマンド|Count|合計|デバイスで拒否された Cloud to Device コマンドの数|ディメンションなし|
|devices.totalDevices|デバイスの総数 (非推奨)|Count|合計|IoT Hub に登録されたデバイスの数|ディメンションなし|
|devices.connectedDevices.allProtocol|接続されているデバイス (非推奨) |Count|合計|IoT Hub に接続されているデバイスの数|ディメンションなし|
|d2c.telemetry.egress.success|ルーティング: 配信されたテレメトリ メッセージ|Count|合計|IoT Hub ルーティングを使用して、メッセージがすべてのエンドポイントに正常に配信された回数。 メッセージが複数のエンドポイントにルーティングされている場合、この値は正常に配信されるたびに 1 つずつ増えます。 メッセージが同じエンドポイントに複数回配信されている場合、この値は正常に配信されるたびに 1 つずつ増えます。|ディメンションなし|
|d2c.telemetry.egress.dropped|ルーティング: 破棄されたテレメトリ メッセージ |Count|合計|デッド エンドポイントであるため、メッセージが IoT Hub ルーティングで破棄された回数。 この値では、フォールバック ルートに配信されるメッセージはカウントされません。破棄されたメッセージはそこには配信されないためです。|ディメンションなし|
|d2c.telemetry.egress.orphaned|ルーティング: 孤立したテレメトリ メッセージ |Count|合計|どのルーティング規則 (フォールバック規則を含む) にも一致しなかっため、メッセージが IoT Hub ルーティングによって孤立した回数。 |ディメンションなし|
|d2c.telemetry.egress.invalid|ルーティング: 互換性のないテレメトリ メッセージ|Count|合計|エンドポイントと互換性がないため、IoT Hub ルーティングでメッセージを配信できなかった回数。 この値に再試行回数は含まれません。|ディメンションなし|
|d2c.telemetry.egress.fallback|ルーティング: フォールバックのために配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、フォールバック ルートに関連付けられているエンドポイントにメッセージが配信された回数。|ディメンションなし|
|d2c.endpoints.egress.eventHubs|ルーティング: Event Hub に配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Event Hub エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.latency.eventHubs|ルーティング: Event Hub のメッセージの待機時間|ミリ秒|平均|IoT Hub の受信メッセージと Event Hub エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.egress.serviceBusQueues|ルーティング: Service Bus キューに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus キュー エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.latency.serviceBusQueues|ルーティング: Service Bus キューのメッセージの待機時間|ミリ秒|平均|IoT Hub の受信メッセージと Service Bus キュー エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.egress.serviceBusTopics|ルーティング: Service Bus トピックに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus トピック エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.latency.serviceBusTopics|ルーティング: Service Bus トピックのメッセージの待機時間|ミリ秒|平均|IoT Hub の受信メッセージと Service Bus トピック エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.egress.builtIn.events|ルーティング: メッセージ/イベントに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが組み込みのエンドポイント (メッセージ/イベント) に正常に配信された回数。 このメトリックは、IoT ハブに対してルーティングが有効になっている (https://aka.ms/iotrouting) ) 場合にのみ動作を開始します。|ディメンションなし|
|d2c.endpoints.latency.builtIn.events|ルーティング: メッセージ/イベントのメッセージの待機時間|ミリ秒|平均|IoT Hub の受信メッセージと組み込みエンドポイント (メッセージ/イベント) の受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。 このメトリックは、IoT ハブに対してルーティングが有効になっている (https://aka.ms/iotrouting) ) 場合にのみ動作を開始します。|ディメンションなし|
|d2c.endpoints.egress.storage|ルーティング: ストレージに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージがストレージ エンドポイントに正常に配信された回数。|ディメンションなし|
|d2c.endpoints.latency.storage|ルーティング: ストレージのメッセージの待機時間|ミリ秒|平均|IoT Hub の受信メッセージとストレージ エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|ディメンションなし|
|d2c.endpoints.egress.storage.bytes|ルーティング: ストレージに配信されたデータ|Bytes|合計|IoT Hub ルーティングでストレージ エンドポイントに配信されたデータの量 (バイト)。|ディメンションなし|
|d2c.endpoints.egress.storage.blobs|ルーティング: ストレージに配信された BLOB|Count|合計|IoT Hub ルーティングで BLOB がストレージ エンドポイントに配信された回数。|ディメンションなし|
|EventGridDeliveries|Event Grid の配信数 (プレビュー)|Count|合計|Event Grid に発行された IoT Hub イベントの数。 成功および失敗した要求の数には、Result ディメンションを使用します。 EventType ディメンションはイベントの種類 (https://aka.ms/ioteventgrid) ) を示します。|Result、EventType|
|EventGridLatency|oT Hub イベントが生成されてから、そのイベントが Event Grid に発行されるまでの平均待機時間 (ミリ秒)。 この数は、すべてのイベントの種類の間の平均値です。 特定の種類のイベントの待機時間を確認するには、EventType ディメンションを使用します。|EventType|
|d2c.twin.read.success|成功したデバイスからのツイン読み取り|Count|合計|デバイスが開始して成功したツイン読み取りの数。|ディメンションなし|
|d2c.twin.read.failure|失敗したデバイスからのツイン読み取り|Count|合計|デバイスが開始したツイン読み取りの失敗数。|ディメンションなし|
|d2c.twin.read.size|デバイスからのツイン読み取りの応答サイズ|Bytes|平均|デバイスが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|ディメンションなし|
|d2c.twin.update.success|成功したデバイスからのツイン更新|Count|合計|デバイスが開始して成功したツイン更新の数。|ディメンションなし|
|d2c.twin.update.failure|失敗したデバイスからのツイン更新|Count|合計|デバイスが開始して失敗したツイン更新の数。|ディメンションなし|
|d2c.twin.update.size|デバイスからのツイン更新のサイズ|Bytes|平均|デバイスが開始して成功したツイン更新の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.methods.success|成功したダイレクト メソッドの呼び出し|Count|合計|成功したダイレクト メソッドの呼び出しの数。|ディメンションなし|
|c2d.methods.failure|失敗したダイレクト メソッドの呼び出し|Count|合計|失敗したダイレクト メソッドの呼び出しの数。|ディメンションなし|
|c2d.methods.requestSize|ダイレクト メソッドの呼び出しの要求サイズ|Bytes|平均|成功したダイレクト メソッド要求の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.methods.responseSize|ダイレクト メソッドの呼び出しの応答サイズ|Bytes|平均|成功したダイレクト メソッド応答の平均、最小、および最大サイズ。|ディメンションなし|
|c2d.twin.read.success|成功したバック エンドからのツイン読み取り|Count|合計|バックエンドが開始して成功したツイン読み取りの数。|ディメンションなし|
|c2d.twin.read.failure|失敗したバックエンドからのツイン読み取り|Count|合計|バックエンドが開始して失敗したツイン読み取りの数。|ディメンションなし|
|c2d.twin.read.size|バック エンドからのツイン読み取りの応答サイズ|Bytes|平均|バックエンドが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|ディメンションなし|
|c2d.twin.update.success|成功したバックエンドからのツイン更新|Count|合計|バックエンドが開始して成功したツイン更新の数。|ディメンションなし|
|c2d.twin.update.failure|失敗したバック エンドからのツイン更新|Count|合計|バックエンドが開始して失敗したツイン更新の数。|ディメンションなし|
|c2d.twin.update.size|バックエンドからのツイン更新のサイズ|Bytes|平均|バックエンドが開始して成功したツイン更新の平均、最小、および最大サイズ。|ディメンションなし|
|twinQueries.success|成功したツイン クエリ|Count|合計|成功したツイン クエリの数。|ディメンションなし|
|twinQueries.failure|失敗したツイン クエリ|Count|合計|失敗したツイン クエリの数。|ディメンションなし|
|twinQueries.resultSize|ツイン クエリの結果のサイズ|Bytes|平均|成功したツイン クエリの結果の平均、最小、および最大サイズ。|ディメンションなし|
|jobs.createTwinUpdateJob.success|成功したツイン更新ジョブの作成|Count|合計|正常に作成されたツイン更新ジョブの数。|ディメンションなし|
|jobs.createTwinUpdateJob.failure|失敗したツイン更新ジョブの作成|Count|合計|作成に失敗したツイン更新ジョブの数。|ディメンションなし|
|jobs.createDirectMethodJob.success|成功したメソッド呼び出しジョブの作成|Count|合計|正常に作成されたダイレクト メソッド呼び出しジョブの数。|ディメンションなし|
|jobs.createDirectMethodJob.failure|失敗したメソッド呼び出しジョブの作成|Count|合計|作成に失敗したダイレクト メソッド呼び出しジョブの数。|ディメンションなし|
|jobs.listJobs.success|成功したジョブ一覧の呼び出し|Count|合計|正常に実行されたジョブ一覧の呼び出しの数。|ディメンションなし|
|jobs.listJobs.failure|失敗したジョブ一覧の呼び出し|Count|合計|失敗したジョブ一覧の呼び出しの数。|ディメンションなし|
|jobs.cancelJob.success|成功したジョブの取り消し|Count|合計|正常に実行されたジョブ取り消しの呼び出し。|ディメンションなし|
|jobs.cancelJob.failure|失敗したジョブの取り消し|Count|合計|失敗したジョブ取り消しの呼び出し。|ディメンションなし|
|jobs.queryJobs.success|成功したジョブ クエリ|Count|合計|正常に実行されたクエリ ジョブの呼び出しの数。|ディメンションなし|
|jobs.queryJobs.failure|失敗したジョブ クエリ|Count|合計|失敗したジョブ クエリの呼び出しの数。|ディメンションなし|
|jobs.completed|完了したジョブ|Count|合計|完了したジョブの数。|ディメンションなし|
|jobs.failed|失敗したジョブ|Count|合計|失敗したジョブの数。|ディメンションなし|
|d2c.telemetry.ingress.sendThrottle|調整エラーの数|Count|合計|デバイスのスループット調整による調整エラーの数|ディメンションなし|
|dailyMessageQuotaUsed|使用されているメッセージの合計数|Count|平均|現在使用されているメッセージの合計数。 これは累積値であり、毎日 00 時 00 分 (UTC) になるとゼロにリセットされます。|ディメンションなし|
|deviceDataUsage|デバイス データの合計使用量|Bytes|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|ディメンションなし|
|totalDeviceCount|デバイスの合計数 (プレビュー)|Count|平均|IoT Hub に登録されたデバイスの数|ディメンションなし|
|connectedDeviceCount|接続されているデバイス (プレビュー)|Count|平均|IoT Hub に接続されているデバイスの数|ディメンションなし|
|configuration|構成メトリック|Count|合計|構成操作のメトリック|ディメンションなし|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|RegistrationAttempts|Registration attempts (登録試行回数)|Count|合計|試行されたデバイス登録の回数|ProvisioningServiceName、IotHubName、Status|
|DeviceAssignments|Devices assigned (割り当て済みデバイス数)|Count|合計|IoT Hub に割り当てられているデバイスの数|ProvisioningServiceName、IotHubName|
|AttestationAttempts|Attestation attempts (構成証明試行回数)|Count|合計|試行されたデバイス構成証明の回数|ProvisioningServiceName、Status、Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|AvailableStorage|使用可能なストレージ|Bytes|合計|5 分単位の細分性で報告された使用可能なストレージの合計|CollectionName、DatabaseName、Region|
|CassandraConnectionClosures|Cassandra 接続の終了|Count|合計|1 分単位の細分性で報告された、閉じられた Cassandra 接続の数|Region、ClosureReason|
|CassandraRequestCharges|Cassandra 要求の料金|Count|合計|行われた Cassandra 要求で使用された RU|DatabaseName、CollectionName、Region、OperationType、ResourceType|
|CassandraRequests|Cassandra 要求|Count|Count|行われた Cassandra 要求の数|DatabaseName、CollectionName、Region、OperationType、ResourceType、ErrorCode|
|DataUsage|データ利用状況|Bytes|合計|5 分単位の細分性で報告されたデータ使用量の合計|CollectionName、DatabaseName、Region|
|DocumentCount|ドキュメント数|Count|合計|5 分単位の細分性で報告されたドキュメントの合計数|CollectionName、DatabaseName、Region|
|DocumentQuota|ドキュメントのクォータ|Bytes|合計|5 分単位の細分性で報告されたストレージ クォータの合計|CollectionName、DatabaseName、Region|
|IndexUsage|インデックスの使用量|Bytes|合計|5 分単位の細分性で報告されたインデックス使用量の合計|CollectionName、DatabaseName、Region|
|MetadataRequests|メタデータの要求数|Count|Count|メタデータの要求数。 Cosmos DB はメタデータ コレクションをアカウントごとに保持します。これにより、コレクションやデータベースなどとそれらの構成を無料で列挙できます。|DatabaseName、CollectionName、Region、StatusCode、 |
|MongoRequestCharge|Mongo 要求の料金|Count|合計|使用された Mongo 要求の単位数|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequests|Mongo Requests (Mongo 要求数)|Count|Count|実行された Mongo 要求の数|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|ProvisionedThroughput|プロビジョニングされたスループット|Count|最大値|プロビジョニングされたスループット|DatabaseName、CollectionName|
|ReplicationLatency|P99 のレプリケーション待機時間|MilliSeconds|平均|geo 対応アカウントのソースおよびターゲット リージョン全体の P99 のレプリケーション待機時間|SourceRegion、TargetRegion|
|ServiceAvailability|サービスの可用性|Percent|平均|アカウントは 1 時間、1 日、または 1 か月単位の細分性で可用性を要求します|ディメンションなし|
|TotalRequestUnits|合計要求単位数|Count|合計|消費された要求の単位数|DatabaseName、CollectionName、Region、StatusCode、OperationType|
|TotalRequests|要求の合計数|Count|Count|行われた要求の数|DatabaseName、CollectionName、Region、StatusCode、OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|ディメンションなし|
|PublishFailCount|発行失敗イベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|UnmatchedEventCount|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|ディメンションなし|
|PublishSuccessLatencyInMs|成功した発行の待機時間|Count|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|MatchedEventCount|一致するイベント|Count|合計|このイベント サブスクリプションに一致するイベントの合計|ディメンションなし|
|DeliveryAttemptFailCount|配信できなかったイベント|Count|合計|このイベント サブスクリプションに配信できなかったイベントの合計|Error、ErrorType|
|DeliverySuccessCount|配信されたイベント|Count|合計|このイベント サブスクリプションに配信されたイベントの合計|ディメンションなし|
|DestinationProcessingDurationInMs|宛先処理継続時間|ミリ秒|平均|宛先処理継続時間 (ミリ秒単位)|ディメンションなし|
|DroppedEventCount|削除されたイベント|Count|合計|このイベント サブスクリプションに一致する削除されたイベントの合計|DropReason|
|DeadLetteredCount|配信不能イベント|Count|合計|このイベント サブスクリプションに一致する配信不能イベントの合計|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|発行されたイベント|Count|合計|このトピックに発行されたイベントの合計数|ディメンションなし|
|PublishFailCount|失敗したイベント|Count|合計|このトピックに発行できなかったイベントの合計|ErrorType、Error|
|UnmatchedEventCount|不一致のイベント|Count|合計|このトピックのどのイベント サブスクリプションにも一致しないイベントの合計|ディメンションなし|
|PublishSuccessLatencyInMs|成功した発行の待機時間|Count|合計|成功した発行の待機時間 (ミリ秒単位)|ディメンションなし|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|成功した要求|Count|合計|Microsoft.EventHub の成功した要求数。|EntityName、 |
|ServerErrors|サーバー エラー。|Count|合計|Microsoft.EventHub のサーバー エラー数。|EntityName、 |
|UserErrors|ユーザー エラー数。|Count|合計|Microsoft.EventHub のユーザー エラー数。|EntityName、 |
|QuotaExceededErrors|クォータ超過エラー数。|Count|合計|Microsoft.EventHub のクォータ超過エラー数。|EntityName、 |
|ThrottledRequests|スロットルされた要求数。|Count|合計|Microsoft.EventHub のスロットルされた要求数。|EntityName、 |
|IncomingRequests|受信要求|Count|合計|Microsoft.EventHub の受信要求数。|EntityName|
|IncomingMessages|受信メッセージ|Count|合計|Microsoft.EventHub の受信メッセージ数。|EntityName|
|OutgoingMessages|送信メッセージ|Count|合計|Microsoft.EventHub の送信メッセージ数。|EntityName|
|IncomingBytes|着信バイト数。|Bytes|合計|Microsoft.EventHub の受信バイト数。|EntityName|
|OutgoingBytes|発信バイト数。|Bytes|合計|Microsoft.EventHub の発信バイト数。|EntityName|
|ActiveConnections|ActiveConnections|Count|平均|Microsoft.EventHub のアクティブな接続の合計数。|ディメンションなし|
|ConnectionsOpened|開かれている接続数。|Count|平均|Microsoft.EventHub の開かれている接続数。|EntityName|
|ConnectionsClosed|切断された接続数。|Count|平均|Microsoft.EventHub の切断された接続数。|EntityName|
|CaptureBacklog|バックログのキャプチャ。|Count|合計|Microsoft.EventHub のバックログをキャプチャします。|EntityName|
|CapturedMessages|キャプチャされたメッセージ数。|Count|合計|Microsoft.EventHub のキャプチャされたメッセージ数。|EntityName|
|CapturedBytes|キャプチャされたバイト数。|Bytes|合計|Microsoft.EventHub のキャプチャされたバイト数。|EntityName|
|Size|Size|Bytes|平均|EventHub のサイズ (バイト単位)。|EntityName|
|INREQS|受信要求数 (非推奨)|Count|合計|名前空間に対して受信した送信要求の合計数 (非推奨)|ディメンションなし|
|SUCCREQ|成功した要求数 (非推奨)|Count|合計|名前空間の成功した要求の合計数 (非推奨)|ディメンションなし|
|FAILREQ|失敗した要求数 (非推奨)|Count|合計|名前空間の失敗した要求の合計数 (非推奨)|ディメンションなし|
|SVRBSY|サーバー ビジー エラー数 (非推奨)|Count|合計|名前空間のサーバー ビジー エラーの合計数 (非推奨)|ディメンションなし|
|INTERR|内部サーバー エラー数 (非推奨)|Count|合計|名前空間の内部サーバー エラーの合計数 (非推奨)|ディメンションなし|
|MISCERR|その他のエラー数 (非推奨)|Count|合計|名前空間の失敗した要求の合計数 (非推奨)|ディメンションなし|
|INMSGS|受信メッセージ数 (非推奨)|Count|合計|名前空間の受信メッセージの総数。 このメトリックは非推奨です。 代わりに、"受信メッセージ" メトリックを使ってください (非推奨)|ディメンションなし|
|EHINMSGS|受信メッセージ (非推奨)|Count|合計|名前空間の受信メッセージの合計数 (非推奨)|ディメンションなし|
|OUTMSGS|送信メッセージ数 (非推奨)|Count|合計|名前空間の送信メッセージの総数。 このメトリックは非推奨です。 代わりに、"送信メッセージ" メトリックを使ってください (非推奨)|ディメンションなし|
|EHOUTMSGS|送信メッセージ (非推奨)|Count|合計|名前空間の送信メッセージの合計数 (非推奨)|ディメンションなし|
|EHINMBS|受信バイト数 (非推奨)|Bytes|合計|名前空間の Event Hub 受信メッセージのスループット。 このメトリックは非推奨です。 代わりに、"受信バイト数" メトリックを使ってください (非推奨)|ディメンションなし|
|EHINBYTES|受信バイト数 (非推奨)|Bytes|合計|名前空間の Event Hub 受信メッセージのスループット (非推奨)|ディメンションなし|
|EHOUTMBS|送信バイト数 (非推奨)|Bytes|合計|名前空間の Event Hub 送信メッセージのスループット。 このメトリックは非推奨です。 代わりに、"発信バイト数" メトリックを使ってください (非推奨)|ディメンションなし|
|EHOUTBYTES|送信バイト数 (非推奨)|Bytes|合計|名前空間の Event Hub 送信メッセージのスループット (非推奨)|ディメンションなし|
|EHABL|アーカイブ バックログ メッセージ数 (非推奨)|Count|合計|名前空間のバックログ内の Event Hub アーカイブ メッセージ数 (非推奨)|ディメンションなし|
|EHAMSGS|アーカイブ メッセージ数 (非推奨)|Count|合計|名前空間内の Event Hub アーカイブ メッセージ数 (非推奨)|ディメンションなし|
|EHAMBS|アーカイブ メッセージ スループット (非推奨)|Bytes|合計|名前空間内の Event Hub アーカイブ メッセージ スループット (非推奨)|ディメンションなし|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|成功した要求 (プレビュー)|Count|合計|Microsoft.EventHub の成功した要求数。 (プレビュー)|ディメンションなし|
|ServerErrors|サーバー エラー。 (プレビュー)|Count|合計|Microsoft.EventHub のサーバー エラー数。 (プレビュー)|ディメンションなし|
|UserErrors|ユーザー エラー数。 (プレビュー)|Count|合計|Microsoft.EventHub のユーザー エラー数。 (プレビュー)|ディメンションなし|
|QuotaExceededErrors|クォータ超過エラー数。 (プレビュー)|Count|合計|Microsoft.EventHub のクォータ超過エラー数。 (プレビュー)|ディメンションなし|
|ThrottledRequests|スロットルされた要求数。 (プレビュー)|Count|合計|Microsoft.EventHub のスロットルされた要求数。 (プレビュー)|ディメンションなし|
|IncomingRequests|受信要求 (プレビュー)|Count|合計|Microsoft.EventHub の受信要求数。 (プレビュー)|ディメンションなし|
|IncomingMessages|受信メッセージ (プレビュー)|Count|合計|Microsoft.EventHub の受信メッセージ数。 (プレビュー)|ディメンションなし|
|OutgoingMessages|送信メッセージ (プレビュー)|Count|合計|Microsoft.EventHub の送信メッセージ数。 (プレビュー)|ディメンションなし|
|IncomingBytes|着信バイト数。 (プレビュー)|Bytes|合計|Microsoft.EventHub の受信バイト数。 (プレビュー)|ディメンションなし|
|OutgoingBytes|発信バイト数。 (プレビュー)|Bytes|合計|Microsoft.EventHub の発信バイト数。 (プレビュー)|ディメンションなし|
|ActiveConnections|ActiveConnections (プレビュー)|Count|平均|Microsoft.EventHub のアクティブな接続の合計数。 (プレビュー)|ディメンションなし|
|ConnectionsOpened|開かれている接続数。 (プレビュー)|Count|平均|Microsoft.EventHub の開かれている接続数。 (プレビュー)|ディメンションなし|
|ConnectionsClosed|切断された接続数。 (プレビュー)|Count|平均|Microsoft.EventHub の切断された接続数。 (プレビュー)|ディメンションなし|
|CaptureBacklog|バックログのキャプチャ。 (プレビュー)|Count|合計|Microsoft.EventHub のバックログをキャプチャします。 (プレビュー)|ディメンションなし|
|CapturedMessages|キャプチャされたメッセージ数。 (プレビュー)|Count|合計|Microsoft.EventHub のキャプチャされたメッセージ数。 (プレビュー)|ディメンションなし|
|CapturedBytes|キャプチャされたバイト数。 (プレビュー)|Bytes|合計|Microsoft.EventHub のキャプチャされたバイト数。 (プレビュー)|ディメンションなし|
|CPU|CPU (プレビュー)|Percent|最大値|イベント ハブ クラスターの CPU 使用率 (%)|Role|
|AvailableMemory|使用可能なメモリ (プレビュー)|Count|最大値|イベント ハブ クラスターの使用可能なメモリ (バイト単位)|Role|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|GatewayRequests|ゲートウェイ要求数|Count|合計|ゲートウェイ要求の数|ClusterDnsName、HttpStatus|
|CategorizedGatewayRequests|カテゴリ別のゲートウェイ要求数|Count|合計|カテゴリ別のゲートウェイ要求の数 (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName、HttpStatus|
|NumActiveWorkers|アクティブなワーカーの数|Count|最大値|アクティブなワーカーの数|ClusterDnsName、MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ObservedMetricValue|実際のメトリック値|Count|平均|実行時に自動スケールによって計算された値|MetricTriggerSource|
|MetricThreshold|メトリックのしきい値|Count|平均|自動スケールの実行時に構成されていた自動スケールのしきい値。|MetricTriggerRule|
|ObservedCapacity|実際の容量|Count|平均|実行時に自動スケールに報告された容量。|ディメンションなし|
|ScaleActionsInitiated|スケール アクション開始|Count|合計|スケール操作の指示。|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(パブリック プレビュー)

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|可用性|Percent|平均|正常に完了した可用性テストの割合|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|可用性テスト|Count|Count|可用性テストの数|availabilityResult/name、availabilityResult/location、availabilityResult/success|
|availabilityResults/duration|可用性テスト継続時間|MilliSeconds|平均|可用性テスト継続時間|availabilityResult/name、availabilityResult/location、availabilityResult/success|
|browserTimings/networkDuration|ページ読み込みのネットワーク接続時間|MilliSeconds|平均|ユーザー要求からネットワーク接続までの時間。 DNS の参照とトランスポートの接続が含まれます。|ディメンションなし|
|browserTimings/processingDuration|クライアントの処理時間|MilliSeconds|平均|ドキュメントの最終バイトを受信してから、DOM が読み込まれるまでの時間。 非同期要求がまだ処理されている可能性があります。|ディメンションなし|
|browserTimings/receiveDuration|受信応答時間|MilliSeconds|平均|最初のバイトから最後のバイトまで、または切断までの時間。|ディメンションなし|
|browserTimings/sendDuration|要求送信時間|MilliSeconds|平均|ネットワーク接続から、最初のバイトを受信するまでの時間。|ディメンションなし|
|browserTimings/totalDuration|ブラウザーのページ読み込み時間|MilliSeconds|平均|ユーザーが要求を出してから DOM、スタイル シート、スクリプト、およびイメージが読み込まれるまでの時間。|ディメンションなし|
|dependencies/count|依存関係呼び出し|Count|Count|外部リソースに対するアプリケーションからの呼び出しの数。|dependency/type、dependency/performanceBucket、dependency/success、operation/synthetic、cloud/roleInstance、cloud/roleName|
|dependencies/duration|依存関係の期間|MilliSeconds|平均|外部リソースに対するアプリケーションからの呼び出しの期間。|dependency/type、dependency/performanceBucket、dependency/success、operation/synthetic、cloud/roleInstance、cloud/roleName|
|dependencies/failed|依存関係呼び出しの失敗回数|Count|Count|外部リソースに対するアプリケーションからの失敗した依存関係呼び出しの数。|dependency/type、dependency/performanceBucket、operation/synthetic、cloud/roleInstance、cloud/roleName|
|pageViews/count|ページ ビュー|Count|Count|ページ ビューの数。|operation/synthetic|
|pageViews/duration|ページ ビューの読み込み時間|MilliSeconds|平均|ページ ビューの読み込み時間|operation/synthetic|
|performanceCounters/requestExecutionTime|HTTP 要求の実行時間|MilliSeconds|平均|最新の要求の実行時間。|cloud/roleInstance|
|performanceCounters/requestsInQueue|アプリケーション キュー内の HTTP 要求|Count|平均|アプリケーション要求キューの長さ。|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP 要求率|CountPerSecond|平均|ASP.NET からの 1 秒あたりのアプリケーションへのすべての要求のレート。|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|例外レート|CountPerSecond|平均|.NET 例外、および .NET 例外に変換されたアンマネージ例外を含む、Windows に報告された処理済み例外と未処理の例外の数。|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|プロセス IO 量|BytesPerSecond|平均|ファイル、ネットワーク、およびデバイスに対する読み書きで 1 秒あたりに処理される合計バイト数。|cloud/roleInstance|
|performanceCounters/processCpuPercentage|プロセス CPU|Percent|平均|すべてのプロセス スレッドで命令を実行するためにプロセッサを使用した経過時間の割合。 これは 0 から 100 の間で変化することがあります。 このメトリックは、w3wp プロセスのみのパフォーマンスを示します。|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|プロセッサ時間|Percent|平均|プロセッサによって非アイドル スレッドで費やされる時間の割合。|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|使用可能なメモリ|Bytes|平均|プロセスへの割り当てまたはシステムの使用にすぐに利用可能な物理メモリ。|cloud/roleInstance|
|performanceCounters/processPrivateBytes|プロセスのプライベート バイト|Bytes|平均|監視対象のアプリケーション プロセスに対して専用に割り当てられるメモリ。|cloud/roleInstance|
|requests/duration|サーバーの応答時間|MilliSeconds|平均|HTTP 要求を受信してから、応答の送信を終了するまでの時間。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|requests/count|サーバー要求|Count|Count|完了した HTTP 要求の数。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|requests/failed|失敗した要求|Count|Count|失敗としてマークされた HTTP 要求の数。 ほとんどの場合、応答コードが 400 以上で、かつ 401 に等しくない要求です。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、cloud/roleName|
|要求/率|サーバー要求率|CountPerSecond|平均|1 秒あたりのサーバー要求数による率|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|exceptions/count|例外|Count|Count|キャッチされていないすべての例外の合計数。|cloud/roleName、cloud/roleInstance、client/type|
|exceptions/browser|ブラウザーの例外|Count|Count|ブラウザーでスローされた、キャッチされない例外の数。|ディメンションなし|
|exceptions/server|サーバーの例外|Count|Count|サーバー アプリケーションでスローされ、キャッチされていない例外の数。|cloud/roleName、cloud/roleInstance|
|traces/count|トレース|Count|Count|トレース ドキュメント数|trace/severityLevel、operation/synthetic、cloud/roleName、cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ServiceApiHit|サービス API ヒット数の合計|Count|Count|サービス API ヒット数の合計|ActivityType、ActivityName|
|ServiceApiLatency|サービス API の全体的な待機時間|ミリ秒|平均|サービス API 要求の全体的な待機時間|ActivityType、ActivityName、StatusCode|
|ServiceApiResult|サービス API 結果数の合計|Count|Count|サービス API 結果数の合計|ActivityType、ActivityName、StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CacheUtilization|キャッシュ使用率|Percent|平均|クラスター スコープ内の使用率レベル|なし|
|QueryDuration|クエリ実行時間|ミリ秒|平均|クエリの実行時間 (秒単位)|クエリの状態|
|IngestionUtilization|インジェストの使用率|Percent|平均|クラスターでのインジェスト スロットの使用率|なし|
|KeepAlive|キープ アライブ|Count|平均|サニティ チェックでは、クラスターがクエリに応答していることが示されます|なし|
|IngestionVolumeInMB|インジェストの量 (MB 単位)|Count|合計|クラスターに取り込まれたデータの全体的な量 (MB 単位)|Database|
|IngestionLatencyInSeconds|インジェストの待機時間 (秒単位)|Seconds|平均|ソース (メッセージが EventHub 内にある場合など) からクラスターへのインジェスト時間 (秒単位)|なし|
|EventProcessedForEventHubs|(Event Hubs の) 処理されたイベント|Count|合計|Event Hub からのインジェスト時に、クラスターによって処理されたイベントの数|なし|
|IngestionResult|インジェストの結果|Count|Count|インジェスト操作の回数|Status|
|CPU|CPU|Percent|平均|CPU 使用率レベル|なし|
| ContinuousExportNumOfRecordsExported | 連続エクスポートでエクスポートされたレコードの数 | Count | 合計 | エクスポート操作中に作成されたすべてのストレージ アーティファクトについてエクスポートされたレコードの数  | なし |
| ExportUtilization | エクスポート使用率 | Percent | 最大値 | エクスポート使用率 | なし |
| ContinuousExportPendingCount | 保留中の連続エクスポートの数 | Count | 最大値 | 実行の準備ができている保留中の連続エクスポート ジョブの数 | なし |
| ContinuousExportMaxLatenessMinutes | 連続エクスポートの最大遅延分数 | Count | 最大値 | 保留中で実行準備ができているすべての連続エクスポートの最大時間 (分) | なし |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|使用法|使用法|Count|Count|API 呼び出しの数|ApiCategory、ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|開始した実行|Count|合計|開始したワークフロー実行の数。|ディメンションなし|
|RunsCompleted|完了した実行|Count|合計|完了したワークフロー実行の数。|ディメンションなし|
|RunsSucceeded|成功した実行|Count|合計|成功したワークフロー実行の数。|ディメンションなし|
|RunsFailed|失敗した実行|Count|合計|失敗したワークフロー実行の数。|ディメンションなし|
|RunsCancelled|取り消された実行|Count|合計|取り消されたワークフロー実行の数。|ディメンションなし|
|RunLatency|実行の待機時間|Seconds|平均|完了したワークフロー実行の待機時間。|ディメンションなし|
|RunSuccessLatency|成功した実行の待機時間|Seconds|平均|成功したワークフロー実行の待機時間。|ディメンションなし|
|RunThrottledEvents|実行スロットル イベント|Count|合計|ワークフロー アクションまたはトリガー スロットル イベントの数。|ディメンションなし|
|RunFailurePercentage|実行エラーの割合|Percent|合計|失敗したワークフロー実行の割合。|ディメンションなし|
|ActionsStarted|開始したアクション |Count|合計|開始したワークフロー アクションの数。|ディメンションなし|
|ActionsCompleted|完了したアクション |Count|合計|完了したワークフロー アクションの数。|ディメンションなし|
|ActionsSucceeded|成功したアクション |Count|合計|成功したワークフロー アクションの数。|ディメンションなし|
|ActionsFailed|失敗したアクション|Count|合計|失敗したワークフロー アクションの数。|ディメンションなし|
|ActionsSkipped|スキップされたアクション |Count|合計|スキップされたワークフロー アクションの数。|ディメンションなし|
|ActionLatency|アクションの待機時間 |Seconds|平均|完了したワークフロー アクションの待機時間。|ディメンションなし|
|ActionSuccessLatency|成功したアクションの待機時間 |Seconds|平均|成功したワークフロー アクションの待機時間。|ディメンションなし|
|ActionThrottledEvents|アクション スロットル イベント|Count|合計|ワークフロー アクション スロットル イベントの数。|ディメンションなし|
|TriggersStarted|開始したトリガー |Count|合計|開始したワークフロー トリガーの数。|ディメンションなし|
|TriggersCompleted|完了したトリガー |Count|合計|完了したワークフロー トリガーの数。|ディメンションなし|
|TriggersSucceeded|成功したトリガー |Count|合計|成功したワークフロー トリガーの数。|ディメンションなし|
|TriggersFailed|失敗したトリガー |Count|合計|失敗したワークフロー トリガーの数。|ディメンションなし|
|TriggersSkipped|スキップされたトリガー|Count|合計|スキップされたワークフロー トリガーの数。|ディメンションなし|
|TriggersFired|起動されたトリガー |Count|合計|起動されたワークフロー トリガーの数。|ディメンションなし|
|TriggerLatency|トリガーの待機時間 |Seconds|平均|完了したワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerFireLatency|起動されたトリガーの待機時間 |Seconds|平均|起動されたワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerSuccessLatency|成功したトリガーの待機時間 |Seconds|平均|成功したワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerThrottledEvents|トリガー スロットル イベント|Count|合計|ワークフロー トリガー スロットル イベントの数。|ディメンションなし|
|BillableActionExecutions|課金対象のアクションの実行|Count|合計|課金されるワークフロー アクションの実行数。|ディメンションなし|
|BillableTriggerExecutions|課金対象のトリガーの実行|Count|合計|課金されるワークフロー トリガーの実行数。|ディメンションなし|
|TotalBillableExecutions|課金対象の実行の合計数|Count|合計|課金されるワークフローの実行数。|ディメンションなし|
|BillingUsageNativeOperation|ネイティブ操作実行の利用状況に応じた課金|Count|合計|請求対象となるネイティブ操作の実行回数。|ディメンションなし|
|BillingUsageStandardConnector|Standard コネクタ実行の利用状況に応じた課金|Count|合計|請求対象となる Standard コネクタの実行回数。|ディメンションなし|
|BillingUsageStorageConsumption|ストレージ使用実行の利用状況に応じた課金|Count|合計|請求対象となるストレージ使用の実行回数。|ディメンションなし|
|BillingUsageNativeOperation|ネイティブ操作実行の利用状況に応じた課金|Count|合計|請求対象となるネイティブ操作の実行回数。|ディメンションなし|
|BillingUsageStandardConnector|Standard コネクタ実行の利用状況に応じた課金|Count|合計|請求対象となる Standard コネクタの実行回数。|ディメンションなし|
|BillingUsageStorageConsumption|ストレージ使用実行の利用状況に応じた課金|Count|合計|請求対象となるストレージ使用の実行回数。|ディメンションなし|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|開始した実行|Count|合計|開始したワークフロー実行の数。|ディメンションなし|
|RunsCompleted|完了した実行|Count|合計|完了したワークフロー実行の数。|ディメンションなし|
|RunsSucceeded|成功した実行|Count|合計|成功したワークフロー実行の数。|ディメンションなし|
|RunsFailed|失敗した実行|Count|合計|失敗したワークフロー実行の数。|ディメンションなし|
|RunsCancelled|取り消された実行|Count|合計|取り消されたワークフロー実行の数。|ディメンションなし|
|RunLatency|実行の待機時間|Seconds|平均|完了したワークフロー実行の待機時間。|ディメンションなし|
|RunSuccessLatency|成功した実行の待機時間|Seconds|平均|成功したワークフロー実行の待機時間。|ディメンションなし|
|RunThrottledEvents|実行スロットル イベント|Count|合計|ワークフロー アクションまたはトリガー スロットル イベントの数。|ディメンションなし|
|RunStartThrottledEvents|実行開始スロットル イベント数|Count|合計|ワークフロー実行開始スロットル イベントの数。|ディメンションなし|
|RunFailurePercentage|実行エラーの割合|Percent|合計|失敗したワークフロー実行の割合。|ディメンションなし|
|ActionsStarted|開始したアクション |Count|合計|開始したワークフロー アクションの数。|ディメンションなし|
|ActionsCompleted|完了したアクション |Count|合計|完了したワークフロー アクションの数。|ディメンションなし|
|ActionsSucceeded|成功したアクション |Count|合計|成功したワークフロー アクションの数。|ディメンションなし|
|ActionsFailed|失敗したアクション |Count|合計|失敗したワークフロー アクションの数。|ディメンションなし|
|ActionsSkipped|スキップされたアクション |Count|合計|スキップされたワークフロー アクションの数。|ディメンションなし|
|ActionLatency|アクションの待機時間 |Seconds|平均|完了したワークフロー アクションの待機時間。|ディメンションなし|
|ActionSuccessLatency|成功したアクションの待機時間 |Seconds|平均|成功したワークフロー アクションの待機時間。|ディメンションなし|
|ActionThrottledEvents|アクション スロットル イベント|Count|合計|ワークフロー アクション スロットル イベントの数。|ディメンションなし|
|TriggersStarted|開始したトリガー |Count|合計|開始したワークフロー トリガーの数。|ディメンションなし|
|TriggersCompleted|完了したトリガー |Count|合計|完了したワークフロー トリガーの数。|ディメンションなし|
|TriggersSucceeded|成功したトリガー |Count|合計|成功したワークフロー トリガーの数。|ディメンションなし|
|TriggersFailed|失敗したトリガー |Count|合計|失敗したワークフロー トリガーの数。|ディメンションなし|
|TriggersSkipped|スキップされたトリガー|Count|合計|スキップされたワークフロー トリガーの数。|ディメンションなし|
|TriggersFired|起動されたトリガー |Count|合計|起動されたワークフロー トリガーの数。|ディメンションなし|
|TriggerLatency|トリガーの待機時間 |Seconds|平均|完了したワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerFireLatency|起動されたトリガーの待機時間 |Seconds|平均|起動されたワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerSuccessLatency|成功したトリガーの待機時間 |Seconds|平均|成功したワークフロー トリガーの待機時間。|ディメンションなし|
|TriggerThrottledEvents|トリガー スロットル イベント|Count|合計|ワークフロー トリガー スロットル イベントの数。|ディメンションなし|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|統合サービス環境のワークフロー プロセッサ使用率|Percent|平均|統合サービス環境のワークフロー プロセッサ使用率。|ディメンションなし|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|統合サービス環境のワークフロー メモリ使用率|Percent|平均|統合サービス環境のワークフロー メモリ使用率。|ディメンションなし|
|IntegrationServiceEnvironmentConnectorProcessorUsage|統合サービス環境のコネクタ プロセッサ使用率|Percent|平均|統合サービス環境のコネクタ プロセッサ使用率。|ディメンションなし|
|IntegrationServiceEnvironmentConnectorMemoryUsage|統合サービス環境のコネクタ メモリ使用率|Percent|平均|統合サービス環境のコネクタ メモリ使用率。|ディメンションなし|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|完了した実行数|完了した実行数|Count|合計|このワークスペースに対して正常に完了した実行の数|シナリオ|
|開始した実行数|開始した実行数|Count|合計|このワークスペースに対して開始された実行数|シナリオ|
|失敗した実行|失敗した実行|Count|合計|このワークスペースに対して失敗した実行数|シナリオ|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|使用法|使用法|Count|Count|API 呼び出しの数|ApiCategory、ApiName、ResultType、ResponseCode|
|可用性|可用性|Percent|平均|API の可用性|ApiCategory、ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|AverageOtherLatency|他の平均待機時間|ms/op|平均|操作ごとのミリ秒単位の他の (つまり、読み取りや書き込みではない) 待機時間の平均|ディメンションなし|
|AverageReadLatency|読み取りの平均待機時間|ms/op|平均|操作ごとの読み取りの平均待機時間 (ミリ秒単位)|ディメンションなし|
|AverageTotalLatency|待機時間の合計平均|ms/op|平均|操作ごとの待機時間の合計平均 (ミリ秒単位)|ディメンションなし|
|AverageWriteLatency|書き込みの平均待機時間|ms/op|平均|操作ごとの書き込みの平均待機時間 (ミリ秒単位)|ディメンションなし|
|FilesystemOtherOps|ファイルシステムの他の操作|ops|平均|ファイルシステムの他の操作 (つまり、読み取りや書き込みではない) の数|ディメンションなし|
|FilesystemReadOps|ファイルシステムの読み取り操作|ops|平均|ファイルシステムの読み取り操作の数|ディメンションなし|
|FilesystemTotalOps|ファイルシステムの操作の合計|ops|平均|すべてのファイルシステム操作の合計|ディメンションなし|
|FilesystemWriteOps|ファイルシステムの書き込み操作|ops|平均|ファイルシステムの書き込み操作の数|ディメンションなし|
|IoBytesPerOtherOps|他の操作あたりの IO バイト|bytes/op|平均|他の操作 (つまり、読み取りや書き込みではない) あたりの入出力バイトの数|ディメンションなし|
|IoBytesPerReadOps|読み取り操作あたりの IO バイト|bytes/op|平均|読み取り操作あたりの入出力バイトの数|ディメンションなし|
|IoBytesPerTotalOps|操作全体の操作あたりの IO バイト|bytes/op|平均|すべての入出力操作バイトの合計|ディメンションなし|
|IoBytesPerWriteOps|書き込み操作あたりの IO バイト|bytes/op|平均|書き込み操作あたりの入出力バイトの数|ディメンションなし|
|OtherIops|その他の IOPS|operations/second|平均|1 秒あたりのその他の入出力操作|ディメンションなし|
|OtherThroughput|その他のスループット|MBps|平均|1 秒あたりのメガバイト単位のその他のスループット (つまり、読み取りや書き込みではない)|ディメンションなし|
|ReadIops|読み取り IOPS|operations/second|平均|1 秒あたりの読み取り入出力操作|ディメンションなし|
|ReadThroughput|読み取りスループット|MBps|平均|1 秒あたりの読み取りスループット (メガバイト単位)|ディメンションなし|
|TotalIops|合計 IOPS|operations/second|平均|1 秒あたりのすべての入出力操作の合計|ディメンションなし|
|TotalThroughput|スループットの合計|MBps|平均|1 秒あたりのすべてのスループットの合計 (メガバイト単位)|ディメンションなし|
|VolumeAllocatedSize|ボリュームの割り当てサイズ|バイト|平均|ボリュームの割り当てサイズ (実際に使用されているバイトではない)|ディメンションなし|
|VolumeLogicalSize|ボリュームの論理サイズ|バイト|平均|ボリュームの論理サイズ (使用されているバイト)|ディメンションなし|
|VolumeSnapshotSize|ボリューム スナップショットのサイズ|バイト|平均|ボリューム内のすべてのスナップショットのサイズ|ディメンションなし|
|WriteIops|書き込み IOPS|operations/second|平均|1 秒あたりの書き込み入出力操作|ディメンションなし|
|WriteThroughput|書き込みスループット|MBps|平均|1 秒あたりの書き込みスループット (メガバイト単位)|ディメンションなし|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|ボリューム プールの割り当てサイズ|バイト|平均|プールの割り当てサイズ (実際に使用されているバイトではない)|ディメンションなし|
|VolumePoolAllocatedUsed|ボリューム プールの使用済みの割り当て|バイト|平均|プールの使用済みの割り当てサイズ|ディメンションなし|
|VolumePoolTotalLogicalSize|ボリューム プールの論理サイズの合計|バイト|平均|プールに属するすべてのボリュームの論理サイズの合計|ディメンションなし|
|VolumePoolTotalSnapshotSize|ボリューム プールのスナップショット サイズの合計|バイト|平均|プール内のすべてのスナップショットの合計|ディメンションなし|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BytesSentRate|送信バイト数|Count|合計|ネットワーク インターフェイスが送信したバイト数|ディメンションなし|
|BytesReceivedRate|受信バイト数|Count|合計|ネットワーク インターフェイスが受信したバイト数|ディメンションなし|
|PacketsSentRate|送信されたパケット数|Count|合計|ネットワーク インターフェイスが送信したパケット数|ディメンションなし|
|PacketsReceivedRate|受信したパケット数|Count|合計|ネットワーク インターフェイスが受信したパケット数|ディメンションなし|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|VipAvailability|データ パスの可用性|Count|平均|期間あたりの Load Balancer データ パスの可用性平均|FrontendIPAddress、FrontendPort|
|DipAvailability|正常性プローブの状態|Count|平均|期間あたりの Load Balancer 正常性プローブの状態平均|ProtocolType、BackendPort、FrontendIPAddress、FrontendPort、BackendIPAddress|
|ByteCount|Byte Count (バイト数)|Count|合計|期間内に送信された合計バイト数|FrontendIPAddress、FrontendPort、Direction|
|PacketCount|Packet Count (パケット数)|Count|合計|期間内に送信された合計パケット数|FrontendIPAddress、FrontendPort、Direction|
|SYNCount|SYN Count (SYN 数)|Count|合計|期間内に送信された合計 SYN パケット数|FrontendIPAddress、FrontendPort、Direction|
|SnatConnectionCount|SNAT Connection Count (SNAT 接続数)|Count|合計|期間内に作成された新しい SNAT 接続の合計数|FrontendIPAddress、BackendIPAddress、ConnectionState|
|AllocatedSnatPorts|割り当てられた SNAT ポート (プレビュー)|Count|合計|期間内に割り当てられた SNAT ポートの合計数|FrontendIPAddress、BackendIPAddress、ProtocolType|
|UsedSnatPorts|使用された SNAT ポート (プレビュー)|Count|合計|期間内に使用された SNAT ポートの合計数|FrontendIPAddress、BackendIPAddress、ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|QueryVolume|クエリ数|Count|合計|DNS ゾーンで処理されたクエリの数|ディメンションなし|
|RecordSetCount|レコード セット数|Count|最大値|DNS ゾーンのレコード セットの数|ディメンションなし|
|RecordSetCapacityUtilization|レコード セットの容量使用率|Percent|最大値|DNS ゾーンで使用されるレコード セットの容量の割合|ディメンションなし|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS 受信パケット数|CountPerSecond|最大値|DDoS 受信パケット数|ディメンションなし|
|PacketsDroppedDDoS|DDoS 受信パケット破棄数|CountPerSecond|最大値|DDoS 受信パケット破棄数|ディメンションなし|
|PacketsForwardedDDoS|DDoS 受信パケット転送数|CountPerSecond|最大値|DDoS 受信パケット転送数|ディメンションなし|
|TCPPacketsInDDoS|DDoS 受信 TCP パケット数|CountPerSecond|最大値|DDoS 受信 TCP パケット数|ディメンションなし|
|TCPPacketsDroppedDDoS|DDoS 受信 TCP パケット破棄数|CountPerSecond|最大値|DDoS 受信 TCP パケット破棄数|ディメンションなし|
|TCPPacketsForwardedDDoS|DDoS 受信 TCP パケット転送数|CountPerSecond|最大値|DDoS 受信 TCP パケット転送数|ディメンションなし|
|UDPPacketsInDDoS|DDoS 受信 UDP パケット数|CountPerSecond|最大値|DDoS 受信 UDP パケット数|ディメンションなし|
|UDPPacketsDroppedDDoS|DDoS 受信 UDP パケット破棄数|CountPerSecond|最大値|DDoS 受信 UDP パケット破棄数|ディメンションなし|
|UDPPacketsForwardedDDoS|Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数)|CountPerSecond|最大値|Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数)|ディメンションなし|
|BytesInDDoS|DDoS 受信バイト数|BytesPerSecond|最大値|DDoS 受信バイト数|ディメンションなし|
|BytesDroppedDDoS|DDoS 受信バイト破棄数|BytesPerSecond|最大値|DDoS 受信バイト破棄数|ディメンションなし|
|BytesForwardedDDoS|DDoS 受信バイト転送数|BytesPerSecond|最大値|DDoS 受信バイト転送数|ディメンションなし|
|TCPBytesInDDoS|DDoS 受信 TCP バイト数|BytesPerSecond|最大値|DDoS 受信 TCP バイト数|ディメンションなし|
|TCPBytesDroppedDDoS|DDoS 受信 TCP バイト破棄数|BytesPerSecond|最大値|DDoS 受信 TCP バイト破棄数|ディメンションなし|
|TCPBytesForwardedDDoS|DDoS 受信 TCP バイト転送数|BytesPerSecond|最大値|DDoS 受信 TCP バイト転送数|ディメンションなし|
|UDPBytesInDDoS|DDoS 受信 UDP バイト数|BytesPerSecond|最大値|DDoS 受信 UDP バイト数|ディメンションなし|
|UDPBytesDroppedDDoS|DDoS 受信 UDP バイト破棄数|BytesPerSecond|最大値|DDoS 受信 UDP バイト破棄数|ディメンションなし|
|UDPBytesForwardedDDoS|DDoS 受信 UDP バイト転送数|BytesPerSecond|最大値|DDoS 受信 UDP バイト転送数|ディメンションなし|
|IfUnderDDoSAttack|DDoS 攻撃中かどうか|Count|最大値|DDoS 攻撃中かどうか|ディメンションなし|
|DDoSTriggerTCPPackets|DDoS 軽減をトリガーする受信 TCP パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 TCP パケット数|ディメンションなし|
|DDoSTriggerUDPPackets|DDoS 軽減をトリガーする受信 UDP パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 UDP パケット数|ディメンションなし|
|DDoSTriggerSYNPackets|DDoS 軽減をトリガーする受信 SYN パケット数|CountPerSecond|最大値|DDoS 軽減をトリガーする受信 SYN パケット数|ディメンションなし|
|VipAvailability|データ パスの可用性|Count|平均|期間あたりの IP アドレス可用性平均|Port|
|ByteCount|Byte Count (バイト数)|Count|合計|期間内に送信された合計バイト数|Port、Direction|
|PacketCount|Packet Count (パケット数)|Count|合計|期間内に送信された合計パケット数|Port、Direction|
|SynCount|SYN Count (SYN 数)|Count|合計|期間内に送信された合計 SYN パケット数|Port、Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ApplicationRuleHit|アプリケーション規則のヒット数|Count|合計|アプリケーション規則がヒットした回数|Status、Reason、Protocol|
|NetworkRuleHit|ネットワーク規則のヒット数|Count|合計|ネットワーク規則がヒットした回数|Status、Reason、Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|スループット|スループット|BytesPerSecond|合計|Application Gateway が処理した 1 秒あたりのバイト数|ディメンションなし|
|UnhealthyHostCount|異常なホストの数|Count|平均|異常なバックエンド ホストの数|BackendSettingsPool|
|HealthyHostCount|正常なホストの数|Count|平均|正常なバックエンド ホストの数|BackendSettingsPool|
|TotalRequests|要求の合計数|Count|合計|Application Gateway が処理した成功した要求の数|BackendSettingsPool|
|FailedRequests|失敗した要求|Count|合計|Application Gateway が処理した失敗した要求の数|BackendSettingsPool|
|ResponseStatus|応答の状態|Count|合計|Application Gateway によって返された HTTP 応答の状態|HttpStatusGroup|
|CurrentConnections|現在の接続数|Count|合計|Application Gateway で確立された現在の接続の数|ディメンションなし|
|CapacityUnits|現在の容量ユニット|Count|平均|使用された容量ユニット|ディメンションなし|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|AverageBandwidth|ゲートウェイの S2S 帯域幅|BytesPerSecond|平均|1 秒あたりのゲートウェイのサイト対サイト帯域幅の平均 (バイト単位)|ディメンションなし|
|P2SBandwidth|ゲートウェイの P2S 帯域幅|BytesPerSecond|平均|1 秒あたりのゲートウェイのポイント対サイト帯域幅の平均 (バイト単位)|ディメンションなし|
|P2SConnectionCount|P2S 接続数|Count|最大値|ゲートウェイのポイント対サイト接続の数|Protocol|
|TunnelAverageBandwidth|Tunnel Bandwidth (トンネル帯域幅)|BytesPerSecond|平均|1 秒あたりの平均トンネル帯域幅バイト数|ConnectionName、RemoteIP|
|TunnelEgressBytes|Tunnel Egress Bytes (トンネル エグレス バイト数)|Bytes|合計|トンネルの送信バイト数|ConnectionName、RemoteIP|
|TunnelIngressBytes|Tunnel Ingress Bytes (トンネル イングレス バイト数)|Bytes|合計|トンネルの受信バイト数|ConnectionName、RemoteIP|
|TunnelEgressPackets|Tunnel Egress Packets (トンネル エグレス パケット数)|Count|合計|トンネルの送信パケット数|ConnectionName、RemoteIP|
|TunnelIngressPackets|Tunnel Ingress Packets (トンネル イングレス パケット数)|Count|合計|トンネルの受信パケット数|ConnectionName、RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel Egress TS Mismatch Packet Drop (トンネル エグレス TS 不一致パケット破棄数)|Count|合計|トンネルのトラフィック セレクター不一致からの発信パケット破棄数|ConnectionName、RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel Ingress TS Mismatch Packet Drop (トンネル イングレス TS 不一致パケット破棄数)|Count|合計|トンネルのトラフィック セレクター不一致からの受信パケット破棄数|ConnectionName、RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond (受信ビット数/秒)|BitsInPerSecond (受信ビット数/秒)|CountPerSecond|平均|1 秒あたりの Azure へのイングレス ビット数|PeeringType|
|BitsOutPerSecond (送信ビット数/秒)|BitsOutPerSecond (送信ビット数/秒)|CountPerSecond|平均|1 秒あたりの Azure からのエグレス ビット数|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond (受信ビット数/秒)|BitsInPerSecond (受信ビット数/秒)|CountPerSecond|平均|1 秒あたりの Azure へのイングレス ビット数|ディメンションなし|
|BitsOutPerSecond|BitsOutPerSecond (送信ビット数/秒)|CountPerSecond|平均|1 秒あたりの Azure からのエグレス ビット数|ディメンションなし|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond (受信ビット数/秒)|BitsInPerSecond (受信ビット数/秒)|CountPerSecond|平均|1 秒あたりの Azure へのイングレス ビット数|ディメンションなし|
|BitsOutPerSecond|BitsOutPerSecond (送信ビット数/秒)|CountPerSecond|平均|1 秒あたりの Azure からのエグレス ビット数|ディメンションなし|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|QpsByEndpoint|Queries by Endpoint Returned (エンドポイント別の返されたクエリ数)|Count|合計|指定された期間内に Traffic Manager エンドポイントが返された回数|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Endpoint Status by Endpoint (エンドポイント別のエンドポイント状態)|Count|最大値|エンドポイントのプローブの状態が "有効" の場合は 1、それ以外の場合は 0。|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ProbesFailedPercent|失敗したプローブの割合|Percent|平均|失敗した接続監視プローブの割合|ディメンションなし|
|AverageRoundtripMs|Avg.ラウンド トリップ時間 (ミリ秒)|MilliSeconds|平均|ソースと接続先の間で送信された接続監視プローブのネットワークの平均ラウンド トリップ時間 (ミリ秒)|ディメンションなし|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|RequestCount|要求数|Count|合計|HTTP/S プロキシによって処理されるクライアント要求の数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|RequestSize|要求サイズ|Bytes|合計|HTTP/S プロキシにクライアントからの要求として送信されたバイトの数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|ResponseSize|応答サイズ|Bytes|合計|クライアントに HTTP/S プロキシからの応答として送信されたバイトの数|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|BackendRequestCount|バックエンド要求数|Count|合計|バックエンドに HTTP/S プロキシから送信された要求の数|HttpStatus、HttpStatusGroup、Backend|
|BackendRequestLatency|バックエンド要求の待機時間|MilliSeconds|平均|HTTP/S プロキシによってバックエンドに要求が送信されてから、HTTP/S プロキシでバックエンドから最後の応答バイトが受信されるまでの算出時間|バックエンド|
|TotalLatency|合計待機時間|MilliSeconds|平均|HTTP/S プロキシによってクライアント要求が受信されてから、HTTP/S プロキシからの最後の応答バイトがクライアントで確認されるまでの算出時間|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|BackendHealthPercentage|バックエンドの正常性の割合|Percent|平均|HTTP/S プロキシからバックエンドへの成功した正常性プローブの割合|Backend、BackendPool|
|WebApplicationFirewallRequestCount|Web アプリケーション ファイアウォール要求の数|Count|合計|Web アプリケーション ファイアウォールによって処理されるクライアント要求の数|PolicyName、RuleName、Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|registration.all|登録操作|Count|合計|成功した登録操作の数 (作成、更新、クエリ、および削除)。 |ディメンションなし|
|registration.create|登録作成操作|Count|合計|成功した登録作成の数。|ディメンションなし|
|registration.update|登録更新操作|Count|合計|成功した登録更新の数。|ディメンションなし|
|registration.get|登録読み取り操作|Count|合計|成功した登録クエリの数。|ディメンションなし|
|registration.delete|登録削除操作|Count|合計|成功した登録削除の数。|ディメンションなし|
|incoming|受信メッセージ|Count|合計|成功した送信 API 呼び出しの数。 |ディメンションなし|
|incoming.scheduled|予定されていたプッシュ通知の送信|Count|合計|予定されていたプッシュ通知の取り消し|ディメンションなし|
|incoming.scheduled.cancel|予定されていたプッシュ通知の取り消し|Count|合計|予定されていたプッシュ通知の取り消し|ディメンションなし|
|scheduled.pending|予定されていた通知の保留|Count|合計|予定されていた通知の保留|ディメンションなし|
|installation.all|インストール管理操作|Count|合計|インストール管理操作|ディメンションなし|
|installation.get|インストール操作の取得|Count|合計|インストール操作の取得|ディメンションなし|
|installation.upsert|インストール操作の作成または更新|Count|合計|インストール操作を作成または更新します|ディメンションなし|
|installation.patch|インストール操作のパッチ|Count|合計|インストール操作をパッチします|ディメンションなし|
|installation.delete|インストール操作の削除|Count|合計|インストール操作を削除します|ディメンションなし|
|outgoing.allpns.success|正常な通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.allpns.invalidpayload|ペイロード エラー|Count|合計|PNS が不良ペイロード エラーを返しために失敗したプッシュの数。|ディメンションなし|
|outgoing.allpns.pnserror|外部通知システム エラー|Count|合計|PNS との通信に問題があったために失敗したプッシュの数 (認証の問題は除きます)。|ディメンションなし|
|outgoing.allpns.channelerror|チャネル エラー|Count|合計|チャネルが適切に調整されていないアプリまたは有効期限が切れたアプリに関連付けられていたために失敗したプッシュの数。|ディメンションなし|
|outgoing.allpns.badorexpiredchannel|適切または期限切れのチャネル エラー|Count|合計|登録内のチャネル/トークン/登録 ID が期限切れまたは無効なために失敗したプッシュの数。|ディメンションなし|
|outgoing.wns.success|WNS 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.wns.invalidcredentials|WNS 承認エラー (無効な資格情報)|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数 (Windows Live は資格情報を認識しません)。|ディメンションなし|
|outgoing.wns.badchannel|WNS 不良チャネル エラー|Count|合計|登録の ChannelURI が認識されなかったために失敗したプッシュの数 (WNS 状態: 404 - 見つかりません)。|ディメンションなし|
|outgoing.wns.expiredchannel|WNS 期限切れチャネル エラー|Count|合計|ChannelURI の有効期限が切れているために失敗したプッシュの数 (WNS 状態: 410 - 削除)。|ディメンションなし|
|outgoing.wns.throttled|WNS 調整済み通知|Count|合計|WNS によってこのアプリが調整されているために失敗したプッシュの数 (WNS 状態: 406 - 受信不可)。|ディメンションなし|
|outgoing.wns.tokenproviderunreachable|WNS 承認エラー (到達不可)|Count|合計|Windows Live に到達できません。|ディメンションなし|
|outgoing.wns.invalidtoken|WNS 承認エラー (無効なトークン)|Count|合計|WNS に提供されたトークンは無効です (WNS 状態: 401 - 許可されていません)。|ディメンションなし|
|outgoing.wns.wrongtoken|WNS 承認エラー (間違ったトークン)|Count|合計|WNS に提供されたトークンは有効ですが、別のアプリケーション用です (WNS 状態: 403 - 禁止されています)。 これは、登録の ChannelURI が別のアプリケーションに関連付けられている場合に発生します。 クライアント アプリに関連付けられているアプリの資格情報が通知ハブ内にあることを確認します。|ディメンションなし|
|outgoing.wns.invalidnotificationformat|WNS 無効通知形式|Count|合計|通知の形式が無効です (WNS 状態: 400)。 WNS ではすべての無効なペイロードを拒否するわけではないことに注意してください。|ディメンションなし|
|outgoing.wns.invalidnotificationsize|WNS 無効通知サイズ エラー|Count|合計|通知ペイロードが大きすぎます (WNS 状態: 413)。|ディメンションなし|
|outgoing.wns.channelthrottled|WNS 調整済みチャネル|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (WNS 応答ヘッダー: X-WNS-NotificationStatus:channelThrottled)。|ディメンションなし|
|outgoing.wns.channeldisconnected|WNS チャネル切断|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (WNS 応答ヘッダー: X-WNS-DeviceConnectionStatus: disconnected)。|ディメンションなし|
|outgoing.wns.dropped|WNS ドロップ通知|Count|合計|登録の ChannelURI が調整されているため、通知は破棄されました (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected)。|ディメンションなし|
|outgoing.wns.pnserror|WNS エラー|Count|合計|WNS との通信エラーにより、通知が配信されません。|ディメンションなし|
|outgoing.wns.authenticationerror|WNS 認証エラー|Count|合計|資格情報無効または間違ったトークンによる Windows Live との通信エラーのため、通知が配信されません。|ディメンションなし|
|outgoing.apns.success|APNS 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.apns.invalidcredentials|APNS 認証エラー|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|ディメンションなし|
|outgoing.apns.badchannel|APNS 不良チャネル エラー|Count|合計|トークンが無効なために失敗したプッシュの数 (APNS 状態コード: 8)。|ディメンションなし|
|outgoing.apns.expiredchannel|APNS 期限切れチャネル エラー|Count|合計|APNS フィードバック チャネルによって無効化されたトークンの数。|ディメンションなし|
|outgoing.apns.invalidnotificationsize|APNS 無効通知サイズ エラー|Count|合計|ペイロードが長すぎたために失敗したプッシュの数 (APNS 状態コード: 7)。|ディメンションなし|
|outgoing.apns.pnserror|APNS エラー|Count|合計|APNS との通信エラーにより失敗したプッシュの数。|ディメンションなし|
|outgoing.gcm.success|GCM 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.gcm.invalidcredentials|GCM 承認エラー (無効な資格情報)|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|ディメンションなし|
|outgoing.gcm.badchannel|GCM 不良チャネル エラー|Count|合計|登録内の registrationId が認識されなかったために失敗したプッシュの数 (GCM 結果: 無効な登録)。|ディメンションなし|
|outgoing.gcm.expiredchannel|GCM 期限切れチャネル エラー|Count|合計|登録内の registrationId が有効期限切れだったために失敗したプッシュの数 (GCM 結果: NotRegistered)。|ディメンションなし|
|outgoing.gcm.throttled|GCM 調整済み通知|Count|合計|GCM によってこのアプリが調整されたために失敗したプッシュの数 (GCM 状態コード: 501-599、または結果: 利用不可)。|ディメンションなし|
|outgoing.gcm.invalidnotificationformat|GCM 無効通知形式|Count|合計|ペイロードの形式が正しくなかったために失敗したプッシュの数 (GCM 結果: InvalidDataKey または InvalidTtl)。|ディメンションなし|
|outgoing.gcm.invalidnotificationsize|GCM 無効通知サイズ エラー|Count|合計|ペイロードが長すぎたために失敗したプッシュの数 (GCM 結果: MessageTooBig)。|ディメンションなし|
|outgoing.gcm.wrongchannel|GCM 誤チャネル エラー|Count|合計|登録内の registrationId が現在のアプリに関連付けられていないために失敗したプッシュの数 (GCM 結果: InvalidPackageName)。|ディメンションなし|
|outgoing.gcm.pnserror|GCM エラー|Count|合計|GCM との通信エラーにより失敗したプッシュの数。|ディメンションなし|
|outgoing.gcm.authenticationerror|GCM 認証エラー|Count|合計|PNS が提供された資格情報を受け入れなかったために失敗したプッシュの数。資格情報がブロックされているか、アプリで SenderId が正しく構成されていません (GCM 結果: MismatchedSenderId)。|ディメンションなし|
|outgoing.mpns.success|MPNS 正常通知|Count|合計|成功した通知の数。|ディメンションなし|
|outgoing.mpns.invalidcredentials|MPNS 無効な資格情報|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|ディメンションなし|
|outgoing.mpns.badchannel|MPNS 不良チャネル エラー|Count|合計|登録の ChannelURI が認識されなかったために失敗したプッシュの数 (MPNS 状態: 404 - 見つかりません)。|ディメンションなし|
|outgoing.mpns.throttled|MPNS 調整済み通知|Count|合計|MPNS によってこのアプリが調整されているために失敗したプッシュの数 (WNS MPNS: 406 - 受信不可)。|ディメンションなし|
|outgoing.mpns.invalidnotificationformat|MPNS 無効通知形式|Count|合計|通知のペイロードが長すぎたために失敗したプッシュの数。|ディメンションなし|
|outgoing.mpns.channeldisconnected|MPNS チャネル切断|Count|合計|登録の ChannelURI が切断されたために失敗したプッシュの数 (MPNS 状態: 412 - 見つかりません)。|ディメンションなし|
|outgoing.mpns.dropped|MPNS ドロップ通知|Count|合計|MPNS によって破棄されたプッシュの数 (MPNS 応答ヘッダー: X-NotificationStatus: QueueFull または Suppressed)。|ディメンションなし|
|outgoing.mpns.pnserror|MPNS エラー|Count|合計|MPNS との通信エラーにより失敗したプッシュの数。|ディメンションなし|
|outgoing.mpns.authenticationerror|MPNS 認証エラー|Count|合計|PNS が提供された資格情報を受け取らなかったか、資格情報がブロックされたために失敗したプッシュの数|ディメンションなし|
|notificationhub.pushes|All Outgoing Notifications (すべての送信通知)|Count|合計|Notification Hub のすべての送信通知|ディメンションなし|
|incoming.all.requests|すべての受信要求|Count|合計|Notification Hub の受信要求の合計数|ディメンションなし|
|incoming.all.failedrequests|失敗したすべての受信要求|Count|合計|Notification Hub の失敗した受信要求の合計数|ディメンションなし|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Count|平均|Average_% Free Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Space|% Free Space|Count|平均|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Inodes|% Used Inodes|Count|平均|Average_% Used Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Space|% Used Space|Count|平均|Average_% Used Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Read Bytes/sec|Disk Read Bytes/sec|Count|平均|Average_Disk Read Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Count|平均|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Count|平均|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Write Bytes/sec|Disk Write Bytes/sec|Count|平均|Average_Disk Write Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Count|平均|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Megabytes|Free Megabytes|Count|平均|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Count|平均|Average_Logical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Available Memory|% Available Memory|Count|平均|Average_% Available Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Count|平均|Average_% Available Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Memory|% Used Memory|Count|平均|Average_% Used Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Count|平均|Average_% Used Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Count|平均|Average_Available MBytes Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Count|平均|Average_Available MBytes Swap|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Count|平均|Average_Page Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Count|平均|Average_Page Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pages/sec|Pages/sec|Count|平均|Average_Pages/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Count|平均|Average_Used MBytes Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Count|平均|Average_Used Memory MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Count|平均|Average_Total Bytes Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Count|平均|Average_Total Bytes Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes|Total Bytes|Count|平均|Average_Total Bytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Count|平均|Average_Total Packets Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Received|Total Packets Received|Count|平均|Average_Total Packets Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Count|平均|Average_Total Rx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Count|平均|Average_Total Tx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Collisions|Total Collisions|Count|平均|Average_Total Collisions|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg。 Disk sec/Read|Avg.Disk sec/Read|Count|平均|Average_Avg。 Disk sec/Read|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg。 Disk sec/Transfer|Avg.Disk sec/Transfer|Count|平均|Average_Avg。 Disk sec/Transfer|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg。 Disk sec/Write|Avg.Disk sec/Write|Count|平均|Average_Avg。 Disk sec/Write|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Count|平均|Average_Physical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Count|平均|Average_Pct Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct User Time|Pct User Time|Count|平均|Average_Pct User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Count|平均|Average_Used Memory kBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Count|平均|Average_Virtual Shared Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% DPC Time|% DPC Time|Count|平均|Average_% DPC Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Idle Time|% Idle Time|Count|平均|Average_% Idle Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Count|平均|Average_% Interrupt Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Count|平均|Average_% IO Wait Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Nice Time|% Nice Time|Count|平均|Average_% Nice Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Privileged Time|% Privileged Time|Count|平均|Average_% Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Processor Time|% Processor Time|Count|平均|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% User Time|% User Time|Count|平均|Average_% User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Count|平均|Average_Free Physical Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Count|平均|Average_Free Space in Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Count|平均|Average_Free Virtual Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processes|処理|Count|平均|Average_Processes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Count|平均|Average_Size Stored In Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Uptime|Uptime|Count|平均|Average_Uptime|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Users|ユーザー|Count|平均|Average_Users|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg。 Disk sec/Read|Avg.Disk sec/Read|Count|平均|Average_Avg。 Disk sec/Read|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg。 Disk sec/Write|Avg.Disk sec/Write|Count|平均|Average_Avg。 Disk sec/Write|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Current Disk Queue Length|現在のディスク キューの長さ|Count|平均|Average_Current Disk Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Count|平均|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Count|平均|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Count|平均|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Megabytes|Free Megabytes|Count|平均|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Space|% Free Space|Count|平均|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes|Available MBytes|Count|平均|Average_Available MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|Count|平均|Average_% Committed Bytes In Use|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Received/sec|Bytes Received/sec|Count|平均|Average_Bytes Received/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|Count|平均|Average_Bytes Sent/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Total/sec|Bytes Total/sec|Count|平均|Average_Bytes Total/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Processor Time|% Processor Time|Count|平均|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processor Queue Length|プロセッサ キューの長さ|Count|平均|Average_Processor Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Heartbeat|Heartbeat|Count|合計|Heartbeat|Computer、OSType、Version、SourceComputerId|
|アップデート|アップデート|Count|平均|アップデート|Computer、Product、Classification、UpdateState、Optional、Approved|
|Event|Event|Count|平均|Event|Source、EventLog、Computer、EventCategory、EventLevel、EventLevelName、EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|QueryDuration|クエリ実行時間|ミリ秒|平均|最後の間隔における DAX クエリの実行時間|ディメンションなし|
|QueryPoolJobQueueLength|スレッド:クエリ プール ジョブ キューの長さ|Count|平均|クエリ スレッド プールのキューに登録されているジョブの数。|ディメンションなし|
|qpu_high_utilization_metric|QPU High Utilization (QPU の高使用率)|Count|合計|過去 1 分間の QPU の高使用率、QPU 使用率が高い場合は 1、それ以外の場合は 0|ディメンションなし|
|memory_metric|メモリ|バイト|平均|メモリ。 範囲は A1 で 0 から 3 GB、A2 で 0 から 5 GB、A3 で 0 から 10 GB、A4 で 0 から 25 GB、A5 で 0 から 50 GB、A6 で 0 から 100 GB|ディメンションなし|
|memory_thrashing_metric|メモリ スラッシング|Percent|平均|平均的なメモリ スラッシング。|ディメンションなし|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|合計|Microsoft.Relay の正常な ListenerConnections。|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|合計|Microsoft.Relay の ListenerConnections での ClientError。|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|合計|Microsoft.Relay の ListenerConnections での ServerError。|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|合計|Microsoft.Relay の正常な SenderConnections。|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|合計|Microsoft.Relay の SenderConnections での ClientError。|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|合計|Microsoft.Relay の SenderConnections での ServerError。|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|合計|Microsoft.Relay の ListenerConnections の合計。|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|合計|Microsoft.Relay の SenderConnections 要求の合計。|EntityName|
|ActiveConnections|ActiveConnections|Count|合計|Microsoft.Relay の ActiveConnections の合計。|EntityName|
|ActiveListeners|ActiveListeners|Count|合計|Microsoft.Relay の ActiveListeners の合計。|EntityName|
|BytesTransferred|BytesTransferred|Count|合計|Microsoft.Relay の BytesTransferred の合計。|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|合計|Microsoft.Relay の ListenerDisconnects の合計。|EntityName|
|SenderDisconnects|SenderDisconnects|Count|合計|Microsoft.Relay の SenderDisconnects の合計。|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SearchLatency|検索の待機時間|Seconds|平均|検索サービスの平均検索待機時間|ディメンションなし|
|SearchQueriesPerSecond|1 秒あたりの検索クエリ数|CountPerSecond|平均|検索サービスの 1 秒あたりの検索クエリ数|ディメンションなし|
|ThrottledSearchQueriesPercentage|スロットルされた検索クエリの割合|Percent|平均|検索サービスのスロットルされた検索クエリの割合|ディメンションなし|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|成功した要求 (プレビュー)|Count|合計|名前空間の成功した要求の総数 (プレビュー)|EntityName|
|ServerErrors|サーバー エラー。 (プレビュー)|Count|合計|Microsoft.ServiceBus のサーバー エラー数。 (プレビュー)|EntityName|
|UserErrors|ユーザー エラー数。 (プレビュー)|Count|合計|Microsoft.ServiceBus のユーザー エラー数。 (プレビュー)|EntityName|
|ThrottledRequests|スロットルされた要求数。 (プレビュー)|Count|合計|Microsoft.ServiceBus のスロットルされた要求数。 (プレビュー)|EntityName|
|IncomingRequests|受信要求 (プレビュー)|Count|合計|Microsoft.ServiceBus の受信要求数。 (プレビュー)|EntityName|
|IncomingMessages|受信メッセージ (プレビュー)|Count|合計|Microsoft.ServiceBus の受信メッセージ数。 (プレビュー)|EntityName|
|OutgoingMessages|送信メッセージ (プレビュー)|Count|合計|Microsoft.ServiceBus の送信メッセージ数。 (プレビュー)|EntityName|
|ActiveConnections|ActiveConnections (プレビュー)|Count|合計|Microsoft.ServiceBus のアクティブな接続の合計数。 (プレビュー)|ディメンションなし|
|Size|サイズ (プレビュー)|Bytes|平均|キュー/トピックのサイズ (バイト単位)。 (プレビュー)|EntityName|
|メッセージ|キュー/トピック内のメッセージの数。 (プレビュー)|Count|平均|キュー/トピック内のメッセージの数。 (プレビュー)|EntityName|
|ActiveMessages|キュー/トピック内のアクティブなメッセージの数。 (プレビュー)|Count|平均|キュー/トピック内のアクティブなメッセージの数。 (プレビュー)|EntityName|
|DeadletteredMessages|キュー/トピック内の配信不能メッセージの数。 (プレビュー)|Count|平均|キュー/トピック内の配信不能メッセージの数。 (プレビュー)|EntityName|
|ScheduledMessages|キュー/トピック内のスケジュール済みメッセージの数。 (プレビュー)|Count|平均|キュー/トピック内のスケジュール済みメッセージの数。 (プレビュー)|EntityName|
|CPUXNS|名前空間あたりの CPU 使用率|Percent|最大値|Service Bus Premium 名前空間の CPU 使用率メトリック|ディメンションなし|
|WSXNS|名前空間あたりのメモリ サイズの使用量|Percent|最大値|Service Bus Premium 名前空間のメモリ使用量メトリック|ディメンションなし|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|平均|このコンテナーに割り当てられた CPU (ミリコア単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|平均|このコンテナーに割り当てられたメモリ (MB 単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualCpu|ActualCpu|Count|平均|実際の CPU 使用率 (ミリコア単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|平均|実際のメモリ使用量 (MB 単位)|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|平均|AllocatedCpu の割合としてのこのコンテナーの CPU の 使用率|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|平均|AllocatedCpu の割合としてのこのコンテナーの CPU の 使用率|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|平均|Service Fabric Mesh アプリケーションの状態|ApplicationName、Status|
|ServiceStatus|ServiceStatus|Count|平均|Service Fabric Mesh アプリケーションのサービスの正常性状態|ApplicationName、Status、ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|平均|Service Fabric Mesh アプリケーションのサービス レプリカの正常性状態|ApplicationName、Status、ServiceName、ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|平均|Service Fabric Mesh アプリケーションのコンテナーの状態|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName、Status|
|RestartCount|RestartCount|Count|平均|Service Fabric Mesh アプリケーションのコンテナーの再起動数|ApplicationName、Status、ServiceName、ServiceReplicaName、CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ConnectionCount|Connection Count (接続数)|Count|最大値|ユーザー接続の量。|エンドポイント|
|MessageCount|メッセージ数|Count|合計|メッセージの総量。|ディメンションなし|
|InboundTraffic|受信トラフィック|Bytes|合計|サービスの受信トラフィック|ディメンションなし|
|OutboundTraffic|送信トラフィック|Bytes|合計|サービスの送信トラフィック|ディメンションなし|
|UserErrors|User Errors (ユーザー エラー)|Percent|最大値|ユーザー エラーの割合|ディメンションなし|
|SystemErrors|System Errors (システム エラー)|Percent|最大値|システム エラーの割合|ディメンションなし|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU の割合|Percent|平均|CPU の割合|ディメンションなし|
|physical_data_read_percent|データ IO の割合|Percent|平均|データ IO の割合|ディメンションなし|
|log_write_percent|ログ IO の割合|Percent|平均|ログ IO の割合|ディメンションなし|
|dtu_consumption_percent|DTU の割合|Percent|平均|DTU の割合|ディメンションなし|
|storage|使用済みのデータ領域|Bytes|最大値|合計データベース サイズ|ディメンションなし|
|connection_successful|成功した接続|Count|合計|成功した接続|ディメンションなし|
|connection_failed|失敗した接続|Count|合計|失敗した接続|ディメンションなし|
|blocked_by_firewall|ファイアウォールによってブロックされる|Count|合計|ファイアウォールによってブロックされる|ディメンションなし|
|deadlock|デッドロック|Count|合計|デッドロック|ディメンションなし|
|storage_percent|使用済みのデータ領域の割合|Percent|最大値|データベース サイズの割合|ディメンションなし|
|xtp_storage_percent|インメモリ OLTP ストレージの割合|Percent|平均|インメモリ OLTP ストレージの割合|ディメンションなし|
|workers_percent|ワーカーの割合|Percent|平均|ワーカーの割合|ディメンションなし|
|sessions_percent|セッションの割合|Percent|平均|セッションの割合|ディメンションなし|
|dtu_limit|DTU の上限|Count|平均|DTU の上限|ディメンションなし|
|dtu_used|使用された DTU|Count|平均|使用された DTU|ディメンションなし|
|cpu_limit|CPU 制限|Count|平均|CPU 制限|ディメンションなし|
|cpu_used|使用された CPU|Count|平均|使用された CPU|ディメンションなし|
|dwu_limit|DWU 上限|Count|最大値|DWU 上限|ディメンションなし|
|dwu_consumption_percent|DWU の割合|Percent|最大値|DWU の割合|ディメンションなし|
|dwu_used|使用済み DWU|Count|最大値|使用済み DWU|ディメンションなし|
|dw_cpu_percent|DW ノード レベルの CPU の割合|Percent|平均|DW ノード レベルの CPU の割合|DwLogicalNodeId|
|dw_physical_data_read_percent|DW ノード レベルのデータ IO の割合|Percent|平均|DW ノード レベルのデータ IO の割合|DwLogicalNodeId|
    |cache_hit_percent|キャッシュ ヒットの割合|Percent|最大値|キャッシュ ヒットの割合|ディメンションなし|
|cache_used_percent|使用されたキャッシュの割合|Percent|最大値|使用されたキャッシュの割合|ディメンションなし|
|local_tempdb_usage_percent|ローカル tempdb の割合|Percent|平均|ローカル tempdb の割合|ディメンションなし|
|app_cpu_billed|課金されるアプリ CPU|Count|合計|課金されるアプリ CPU|ディメンションなし|
|app_cpu_percent|アプリ CPU の割合|Percent|平均|アプリ CPU の割合|ディメンションなし|
|app_memory_percent|アプリのメモリ使用率|Percent|平均|アプリのメモリ使用率|ディメンションなし|
|allocated_data_storage|割り当て済みのデータ領域|Bytes|平均|割り当て済みのデータ領域|ディメンションなし|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|CPU の割合|Percent|平均|CPU の割合|ディメンションなし|
|physical_data_read_percent|データ IO の割合|Percent|平均|データ IO の割合|ディメンションなし|
|log_write_percent|ログ IO の割合|Percent|平均|ログ IO の割合|ディメンションなし|
|dtu_consumption_percent|DTU の割合|Percent|平均|DTU の割合|ディメンションなし|
|storage_percent|使用済みのデータ領域の割合||Percent|平均|ストレージの割合|ディメンションなし|
|workers_percent|ワーカーの割合|Percent|平均|ワーカーの割合|ディメンションなし|
|sessions_percent|セッションの割合|Percent|平均|セッションの割合|ディメンションなし|
|eDTU_limit|eDTU 制限|Count|平均|eDTU 制限|ディメンションなし|
|storage_limit|データの最大サイズ|Bytes|平均|ストレージの制限|ディメンションなし|
|eDTU_used|使用済み eDTU|Count|平均|使用済み eDTU|ディメンションなし|
|storage_used|使用済みのデータ領域|Bytes|平均|使用済みストレージ|ディメンションなし|
|xtp_storage_percent|インメモリ OLTP ストレージの割合|Percent|平均|インメモリ OLTP ストレージの割合|ディメンションなし|
|cpu_limit|CPU 制限|Count|平均|CPU 制限|ディメンションなし|
|cpu_used|使用された CPU|Count|平均|使用された CPU|ディメンションなし|
|allocated_data_storage|割り当て済みのデータ領域|Bytes|平均|割り当て済みのデータ領域|ディメンションなし|
|allocated_data_storage_percent|割り当て済みのデータ領域の割合|Percent|最大値|割り当て済みのデータ領域の割合|ディメンションなし|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|virtual_core_count|仮想コア数|Count|平均|仮想コア数|ディメンションなし|
|avg_cpu_percent|平均 CPU の割合|Percent|平均|平均 CPU の割合|ディメンションなし|
|reserved_storage_mb|予約済みストレージ スペース|Count|平均|予約済みストレージ スペース|ディメンションなし|
|storage_space_used_mb|使用済みストレージ スペース|Count|平均|使用済みストレージ スペース|ディメンションなし|
|io_requests|IO 要求数|Count|平均|IO 要求数|ディメンションなし|
|io_bytes_read|読み取り IO バイト|Bytes|平均|読み取り IO バイト|ディメンションなし|
|io_bytes_written|書き込み IO バイト|Bytes|平均|書き込み IO バイト|ディメンションなし|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Used capacity (使用済み容量)|Bytes|平均|アカウントの使用済み容量|ディメンションなし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|Bytes|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|Bytes|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|平均|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|平均|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|平均|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Blob Capacity (BLOB 容量)|Bytes|平均|ストレージ アカウントの Blob service によって使われているストレージの量 (バイト単位)。|BlobType、Tier|
|BlobCount|Blob Count (BLOB 数)|Count|合計|ストレージ アカウントの Blob service 内の BLOB の数。|BlobType|       |BlobCount|Blob Count (BLOB 数)|Count|平均|ストレージ アカウントの Blob service 内の BLOB の数。|BlobType、Tier|
|ContainerCount|Blob Container Count (BLOB コンテナー数)|Count|平均|ストレージ アカウントの Blob service 内のコンテナーの数。|ディメンションなし|
|IndexCapacity|インデックスの容量|Bytes|平均|ADLS Gen2 (階層構造) インデックスで使用されるストレージの容量 (バイト単位)。|ディメンションなし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|Bytes|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|Bytes|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|平均|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|平均|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|平均|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|File Capacity (ファイル容量)|Bytes|平均|ストレージ アカウントの File サービスによって使われているストレージの量 (バイト単位)。|ディメンションなし|
|FileCount|File Count (ファイル数)|Count|平均|ストレージ アカウントの File サービス内のファイルの数。|ディメンションなし|
|FileShareCount|File Share Count (ファイル共有数)|Count|平均|ストレージ アカウントの File サービス内のファイル共有の数。|ディメンションなし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|Bytes|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|Bytes|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|平均|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|平均|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|平均|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Queue Capacity (キュー容量)|Bytes|平均|ストレージ アカウントの Queue サービスによって使われているストレージの量 (バイト単位)。|ディメンションなし|
|QueueCount|Queue Count (キュー数)|Count|平均|ストレージ アカウントの Queue サービス内のキューの数。|ディメンションなし|
|QueueMessageCount|Queue Message Count (キュー メッセージ数)|Count|平均|ストレージ アカウントの Queue サービス内のキュー メッセージの概数。|ディメンションなし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|Bytes|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|Bytes|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|平均|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|平均|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|平均|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Table Capacity (テーブル容量)|Bytes|平均|ストレージ アカウントの Table service によって使われているストレージの量 (バイト単位)。|ディメンションなし|
|TableCount|Table Count (テーブル数)|Count|平均|ストレージ アカウントの Table service 内のテーブルの数。|ディメンションなし|
|TableEntityCount|Table Entity Count (テーブル エンティティ数)|Count|平均|ストレージ アカウントの Table service 内のテーブル エンティティの数。|ディメンションなし|
|トランザクション|トランザクション|Count|合計|ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。|ResponseType、GeoType、ApiName、Authentication|
|イングレス|イングレス|Bytes|合計|イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。|GeoType、ApiName、Authentication|
|エグレス|エグレス|Bytes|合計|エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。|GeoType、ApiName、Authentication|
|SuccessServerLatency|Success Server Latency (成功サーバー待機時間)|ミリ秒|平均|成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|Success E2E Latency (成功 E2E 待機時間)|ミリ秒|平均|ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。|GeoType、ApiName、Authentication|
|可用性|可用性|Percent|平均|ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ServerSyncSessionResult|同期セッションの結果|Count|平均|サーバー エンドポイントがクラウド エンドポイントとの同期セッションを正常に完了するたびに 1 の値をログ記録するメトリック|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|同期されたファイル数|Count|合計|同期したファイルの数|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|ファイルが同期していない|Count|合計|同期に失敗したファイルの数|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncBatchTransferredFileBytes|同期したバイト数|Bytes|合計|同期セッションに転送されたファイルの合計サイズ|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncServerHeartbeat|サーバーのオンライン状態|Count|最大値|クラウド エンドポイントとのハートビートを正常に記録するたびに 1 の値をログ記録するメトリック|ServerName|
|StorageSyncRecallIOTotalSizeBytes|クラウドを使った階層化の呼び戻し|Bytes|合計|サーバーに呼び戻されたデータの合計サイズ|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|ResourceUtilization|SU % 使用率|Percent|最大値|SU % 使用率|LogicalName、PartitionId|
|InputEvents|入力イベント|Count|合計|入力イベント|LogicalName、PartitionId|
|InputEventBytes|入力イベントのバイト数|Bytes|合計|入力イベントのバイト数|LogicalName、PartitionId|
|LateInputEvents|遅延入力イベント|Count|合計|遅延入力イベント|LogicalName、PartitionId|
|OutputEvents|出力イベント|Count|合計|出力イベント|LogicalName、PartitionId|
|ConversionErrors|データ変換エラー|Count|合計|データ変換エラー|LogicalName、PartitionId|
|Errors|実行時エラー|Count|合計|実行時エラー|LogicalName、PartitionId|
|DroppedOrAdjustedEvents|順不同のイベント|Count|合計|順不同のイベント|LogicalName、PartitionId|
|AMLCalloutRequests|関数の要求|Count|合計|関数の要求|LogicalName、PartitionId|
|AMLCalloutFailedRequests|失敗した関数の要求|Count|合計|失敗した関数の要求|LogicalName、PartitionId|
|AMLCalloutInputEvents|関数のイベント|Count|合計|関数のイベント|LogicalName、PartitionId|
|DeserializationError|逆シリアル化の入力エラー|Count|合計|逆シリアル化の入力エラー|LogicalName、PartitionId|
|EarlyInputEvents|初期入力イベント|Count|合計|初期入力イベント|LogicalName、PartitionId|
|OutputWatermarkDelaySeconds|透かしの遅延|Seconds|最大値|透かしの遅延|LogicalName、PartitionId|
|InputEventsSourcesBacklogged|バックログされた入力イベント|Count|最大値|バックログされた入力イベント|LogicalName、PartitionId|
|InputEventsSourcesPerSecond|受信した入力ソース|Count|合計|受信した入力ソース|LogicalName、PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (受信メッセージの受信)|Count|合計|すべてのイベント ハブまたは IoT ハブ イベント ソースから読み取られたメッセージの数|ディメンションなし|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (無効な受信メッセージの受信)|Count|合計|すべてのイベント ハブまたは IoT ハブ イベント ソースから読み取られた無効なメッセージの数|ディメンションなし|
|IngressReceivedBytes|Ingress Received Bytes (受信バイトの受信)|Bytes|合計|すべてのイベント ソースから読み取られたバイト数|ディメンションなし|
|IngressStoredBytes|Ingress Stored Bytes (保存済みバイトの受信)|Bytes|合計|正常に処理され、クエリで利用できるイベントの合計サイズ|ディメンションなし|
|IngressStoredEvents|Ingress Stored Events (保存済みイベントの受信)|Count|合計|正常に処理され、クエリで利用できるフラット化されたイベントの数|ディメンションなし|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (受信メッセージの受信のタイム ラグ)|Seconds|最大値|メッセージがイベント ソースでエンキューされた時刻とそれがイングレスで処理された時刻の間の差|ディメンションなし|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (受信メッセージの受信のカウント ラグ)|Count|平均|イベント ソース パーティションで最後にエンキューされたメッセージのシーケンス番号とイングレスで処理されているメッセージのシーケンス番号の間の差|ディメンションなし|
|WarmStorageMaxProperties|ウォーム ストレージの最大プロパティ数|Count|最大値|S1/S2 SKU の環境で許可されているプロパティの最大数と PAYG SKU のウォーム ストアで許可されたプロパティの最大数|ディメンションなし|
|WarmStorageUsedProperties|ウォーム ストレージ使用済みプロパティ数 |Count|最大値|S1/S2 SKU の環境で使用されているプロパティ数と PAYG SKU のウォーム ストアで使用されているプロパティ数|ディメンションなし|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (受信メッセージの受信)|Count|合計|イベント ソースから読み取られたメッセージの数|ディメンションなし|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (無効な受信メッセージの受信)|Count|合計|イベント ソースから読み取られた無効なメッセージの数|ディメンションなし|
|IngressReceivedBytes|Ingress Received Bytes (受信バイトの受信)|Bytes|合計|イベント ソースから読み取られたバイト数|ディメンションなし|
|IngressStoredBytes|Ingress Stored Bytes (保存済みバイトの受信)|Bytes|合計|正常に処理され、クエリで利用できるイベントの合計サイズ|ディメンションなし|
|IngressStoredEvents|Ingress Stored Events (保存済みイベントの受信)|Count|合計|正常に処理され、クエリで利用できるフラット化されたイベントの数|ディメンションなし|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (受信メッセージの受信のタイム ラグ)|Seconds|最大値|メッセージがイベント ソースでエンキューされた時刻とそれがイングレスで処理された時刻の間の差|ディメンションなし|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (受信メッセージの受信のカウント ラグ)|Count|平均|イベント ソース パーティションで最後にエンキューされたメッセージのシーケンス番号とイングレスで処理されているメッセージのシーケンス番号の間の差|ディメンションなし|
|WarmStorageMaxProperties|ウォーム ストレージの最大プロパティ数|Count|最大値|S1/S2 SKU の環境で許可されているプロパティの最大数と PAYG SKU のウォーム ストアで許可されたプロパティの最大数|ディメンションなし|
|WarmStorageUsedProperties|ウォーム ストレージ使用済みプロパティ数 |Count|最大値|S1/S2 SKU の環境で使用されているプロパティ数と PAYG SKU のウォーム ストアで使用されているプロパティ数|ディメンションなし|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|平均|サンプル期間にわたる読み取り操作による平均ディスク スループット。|ディメンションなし|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|平均|サンプル期間にわたる書き込み操作による平均ディスク スループット。|ディメンションなし|
|Disk Read Bytes|ディスク読み取りバイト数|Bytes|合計|サンプル期間にわたる読み取り操作による合計ディスク スループット。|ディメンションなし|
|Disk Write Bytes|ディスク書き込みバイト数|Bytes|合計|サンプル期間にわたる書き込み操作による合計ディスク スループット。|ディメンションなし|
|DiskReadOperations|ディスク読み取り操作数|Count|合計|以前のサンプル期間内の IO 読み取り操作数。 これらの操作はサイズが変動する場合があることに注意してください。|ディメンションなし|
|DiskWriteOperations|Disk Write Operations|Count|合計|以前のサンプル期間内の IO 書き込み操作数。 これらの操作はサイズが変動する場合があることに注意してください。|ディメンションなし|
|Disk Read Operations/Sec|ディスク読み取り操作数/秒|CountPerSecond|平均|以前のサンプル期間内の IO 読み取り操作の平均数。 これらの操作はサイズが変動する場合があることに注意してください。|ディメンションなし|
|ディスク書き込み操作数/秒|ディスク書き込み操作数/秒|CountPerSecond|平均|以前のサンプル期間内の IO 書き込み操作の平均数。 これらの操作はサイズが変動する場合があることに注意してください。|ディメンションなし|
|DiskReadLatency|ディスク読み取り待機時間|ミリ秒|平均|合計読み取り待機時間。 デバイスとカーネルの読み取り待機時間の合計。|ディメンションなし|
|DiskWriteLatency|ディスク書き込み待機時間|ミリ秒|平均|合計書き込み待機時間。 デバイスとカーネルの書き込み待機時間の合計。|ディメンションなし|
|NetworkInBytesPerSecond|受信ネットワークのバイト数/秒|BytesPerSecond|平均|受信トラフィックの平均ネットワーク スループット。|ディメンションなし|
|NetworkOutBytesPerSecond|送信ネットワークのバイト数/秒|BytesPerSecond|平均|送信トラフィックの平均ネットワーク スループット。|ディメンションなし|
|Network In|ネットワーク受信|Bytes|合計|受信トラフィックの合計ネットワーク スループット。|ディメンションなし|
|ネットワーク送信|ネットワーク送信|Bytes|合計|送信トラフィックの合計ネットワーク スループット。|ディメンションなし|
|MemoryUsed|メモリ使用量|Bytes|平均|VM によって使用されているマシンのメモリの容量。|ディメンションなし|
|MemoryGranted|許可されているメモリ量|Bytes|平均|ホストによって VM に許可されたメモリの容量。 VMkernel がメモリを必要とした場合、一度アクセスされ、許可されたメモリがスワップ アウトされるか、バルーニングされるまで、メモリはホストに与えられません。|ディメンションなし|
|MemoryActive|アクティブなメモリ|Bytes|平均|過去の小さな時間枠で VM によって使用されたメモリの容量。 これは、VM が現在必要としているメモリの容量の「真」の数値です。 ゲストのパフォーマンスに影響を与えずに、追加の未使用メモリをスワップ アウトするか、バルーニングできます。|ディメンションなし|
|Percentage CPU|CPU の割合|Percent|平均|ノードの CPU 使用率。 この値は、100% でシステム上のすべてのプロセッサ コアを表します。 たとえば、4 コア システムの 50% を使用する双方向 VM は、2 つのコアを完全に使用します。|ディメンションなし|
|PercentageCpuReady|CPU Ready の割合|ミリ秒|合計|準備時間は、過去の更新間隔で CPU が利用可能になるまで待機するのに費やされた時間です。|ディメンションなし|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CpuPercentage|CPU の割合|Percent|平均|CPU の割合|インスタンス|
|MemoryPercentage|メモリの割合|Percent|平均|メモリの割合|インスタンス|
|DiskQueueLength|ディスク キューの長さ|Count|平均|ディスク キューの長さ|インスタンス|
|HttpQueueLength|HTTP キューの長さ|Count|平均|HTTP キューの長さ|インスタンス|
|BytesReceived|受信データ|Bytes|合計|受信データ|インスタンス|
|BytesSent|送信データ|Bytes|合計|送信データ|インスタンス|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (関数を除く)

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|Seconds|合計|CPU 時間|インスタンス|
|Requests|Requests|Count|合計|Requests|インスタンス|
|BytesReceived|受信データ|Bytes|合計|受信データ|インスタンス|
|BytesSent|送信データ|Bytes|合計|送信データ|インスタンス|
|Http101|Http 101|Count|合計|Http 101|インスタンス|
|Http2xx|HTTP 2xx|Count|合計|HTTP 2xx|インスタンス|
|Http3xx|HTTP 3xx|Count|合計|HTTP 3xx|インスタンス|
|Http401|HTTP 401|Count|合計|HTTP 401|インスタンス|
|Http403|HTTP 403|Count|合計|HTTP 403|インスタンス|
|Http404|HTTP 404|Count|合計|HTTP 404|インスタンス|
|Http406|HTTP 406|Count|合計|HTTP 406|インスタンス|
|Http4xx|HTTP 4xx|Count|合計|HTTP 4xx|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|MemoryWorkingSet|メモリ ワーキング セット|Bytes|平均|メモリ ワーキング セット|インスタンス|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|Bytes|平均|平均メモリ ワーキング セット|インスタンス|
|AverageResponseTime|平均応答時間|Seconds|平均|平均応答時間|インスタンス|
|AppConnections|Connections|Count|平均|Connections|インスタンス|
|Handles|ハンドルの数|Count|平均|ハンドルの数|インスタンス|
|Threads|Thread Count|Count|平均|Thread Count|インスタンス|
|PrivateBytes|Private Bytes|Bytes|平均|Private Bytes|インスタンス|
|IoReadBytesPerSecond|IO 読み取りバイト/秒|BytesPerSecond|合計|IO 読み取りバイト/秒|インスタンス|
|IoWriteBytesPerSecond|IO 書き込みバイト/秒|BytesPerSecond|合計|IO 書き込みバイト/秒|インスタンス|
|IoOtherBytesPerSecond|IO その他のバイト/秒|BytesPerSecond|合計|IO その他のバイト/秒|インスタンス|
|IoReadOperationsPerSecond|IO 読み取り操作/秒|BytesPerSecond|合計|IO 読み取り操作/秒|インスタンス|
|IoWriteOperationsPerSecond|IO 書き込み操作/秒|BytesPerSecond|合計|IO 書き込み操作/秒|インスタンス|
|IoOtherOperationsPerSecond|IO その他の操作/秒|BytesPerSecond|合計|IO その他の操作/秒|インスタンス|
|RequestsInApplicationQueue|アプリケーション キュー内の要求数|Count|平均|アプリケーション キュー内の要求数|インスタンス|
|CurrentAssemblies|現在のアセンブリ|Count|平均|現在のアセンブリ|インスタンス|
|TotalAppDomains|アプリ ドメイン合計数|Count|平均|アプリ ドメイン合計数|インスタンス|
|TotalAppDomainsUnloaded|アンロード済みのアプリ ドメイン合計数|Count|平均|アンロード済みのアプリ ドメイン合計数|インスタンス|
|Gen0Collections|Gen 0 ガベージ コレクション|Count|合計|Gen 0 ガベージ コレクション|インスタンス|
|Gen1Collections|Gen 1 ガベージ コレクション|Count|合計|Gen 1 ガベージ コレクション|インスタンス|
|Gen2Collections|Gen 2 ガベージ コレクション|Count|合計|Gen 2 ガベージ コレクション|インスタンス|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (関数)

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|BytesReceived|受信データ|Bytes|合計|受信データ|インスタンス|
|BytesSent|送信データ|Bytes|合計|送信データ|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|MemoryWorkingSet|メモリ ワーキング セット|Bytes|平均|メモリ ワーキング セット|インスタンス|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|Bytes|平均|平均メモリ ワーキング セット|インスタンス|
|FunctionExecutionUnits|関数の実行単位|MB/ミリ秒|合計|[関数の実行単位](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|インスタンス|
|FunctionExecutionCount|関数の実行回数|Count|合計|関数の実行回数|インスタンス|
|PrivateBytes|Private Bytes|Bytes|平均|Private Bytes|インスタンス|
|IoReadBytesPerSecond|IO 読み取りバイト/秒|BytesPerSecond|合計|IO 読み取りバイト/秒|インスタンス|
|IoWriteBytesPerSecond|IO 書き込みバイト/秒|BytesPerSecond|合計|IO 書き込みバイト/秒|インスタンス|
|IoOtherBytesPerSecond|IO その他のバイト/秒|BytesPerSecond|合計|IO その他のバイト/秒|インスタンス|
|IoReadOperationsPerSecond|IO 読み取り操作/秒|BytesPerSecond|合計|IO 読み取り操作/秒|インスタンス|
|IoWriteOperationsPerSecond|IO 書き込み操作/秒|BytesPerSecond|合計|IO 書き込み操作/秒|インスタンス|
|IoOtherOperationsPerSecond|IO その他の操作/秒|BytesPerSecond|合計|IO その他の操作/秒|インスタンス|
|RequestsInApplicationQueue|アプリケーション キュー内の要求数|Count|平均|アプリケーション キュー内の要求数|インスタンス|
|CurrentAssemblies|現在のアセンブリ|Count|平均|現在のアセンブリ|インスタンス|
|TotalAppDomains|アプリ ドメイン合計数|Count|平均|アプリ ドメイン合計数|インスタンス|
|TotalAppDomainsUnloaded|アンロード済みのアプリ ドメイン合計数|Count|平均|アンロード済みのアプリ ドメイン合計数|インスタンス|
|Gen0Collections|Gen 0 ガベージ コレクション|Count|合計|Gen 0 ガベージ コレクション|インスタンス|
|Gen1Collections|Gen 1 ガベージ コレクション|Count|合計|Gen 1 ガベージ コレクション|インスタンス|
|Gen2Collections|Gen 2 ガベージ コレクション|Count|合計|Gen 2 ガベージ コレクション|インスタンス|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|Seconds|合計|CPU 時間|インスタンス|
|Requests|Requests|Count|合計|Requests|インスタンス|
|BytesReceived|受信データ|Bytes|合計|受信データ|インスタンス|
|BytesSent|送信データ|Bytes|合計|送信データ|インスタンス|
|Http101|Http 101|Count|合計|Http 101|インスタンス|
|Http2xx|HTTP 2xx|Count|合計|HTTP 2xx|インスタンス|
|Http3xx|HTTP 3xx|Count|合計|HTTP 3xx|インスタンス|
|Http401|HTTP 401|Count|合計|HTTP 401|インスタンス|
|Http403|HTTP 403|Count|合計|HTTP 403|インスタンス|
|Http404|HTTP 404|Count|合計|HTTP 404|インスタンス|
|Http406|HTTP 406|Count|合計|HTTP 406|インスタンス|
|Http4xx|HTTP 4xx|Count|合計|HTTP 4xx|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|MemoryWorkingSet|メモリ ワーキング セット|Bytes|平均|メモリ ワーキング セット|インスタンス|
|AverageMemoryWorkingSet|平均メモリ ワーキング セット|Bytes|平均|平均メモリ ワーキング セット|インスタンス|
|AverageResponseTime|平均応答時間|Seconds|平均|平均応答時間|インスタンス|
|FunctionExecutionUnits|関数の実行単位|Count|合計|関数の実行単位|インスタンス|
|FunctionExecutionCount|関数の実行回数|Count|合計|関数の実行回数|インスタンス|
|AppConnections|Connections|Count|平均|Connections|インスタンス|
|Handles|ハンドルの数|Count|平均|ハンドルの数|インスタンス|
|Threads|Thread Count|Count|平均|Thread Count|インスタンス|
|PrivateBytes|Private Bytes|Bytes|平均|Private Bytes|インスタンス|
|IoReadBytesPerSecond|IO 読み取りバイト/秒|BytesPerSecond|合計|IO 読み取りバイト/秒|インスタンス|
|IoWriteBytesPerSecond|IO 書き込みバイト/秒|BytesPerSecond|合計|IO 書き込みバイト/秒|インスタンス|
|IoOtherBytesPerSecond|IO その他のバイト/秒|BytesPerSecond|合計|IO その他のバイト/秒|インスタンス|
|IoReadOperationsPerSecond|IO 読み取り操作/秒|BytesPerSecond|合計|IO 読み取り操作/秒|インスタンス|
|IoWriteOperationsPerSecond|IO 書き込み操作/秒|BytesPerSecond|合計|IO 書き込み操作/秒|インスタンス|
|IoOtherOperationsPerSecond|IO その他の操作/秒|BytesPerSecond|合計|IO その他の操作/秒|インスタンス|
|RequestsInApplicationQueue|アプリケーション キュー内の要求数|Count|平均|アプリケーション キュー内の要求数|インスタンス|
|CurrentAssemblies|現在のアセンブリ|Count|平均|現在のアセンブリ|インスタンス|
|TotalAppDomains|アプリ ドメイン合計数|Count|平均|アプリ ドメイン合計数|インスタンス|
|TotalAppDomainsUnloaded|アンロード済みのアプリ ドメイン合計数|Count|平均|アンロード済みのアプリ ドメイン合計数|インスタンス|
|Gen0Collections|Gen 0 ガベージ コレクション|Count|合計|Gen 0 ガベージ コレクション|インスタンス|
|Gen1Collections|Gen 1 ガベージ コレクション|Count|合計|Gen 1 ガベージ コレクション|インスタンス|
|Gen2Collections|Gen 2 ガベージ コレクション|Count|合計|Gen 2 ガベージ コレクション|インスタンス|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|Requests|Requests|Count|合計|Requests|インスタンス|
|BytesReceived|受信データ|Bytes|合計|受信データ|インスタンス|
|BytesSent|送信データ|Bytes|合計|送信データ|インスタンス|
|Http101|Http 101|Count|合計|Http 101|インスタンス|
|Http2xx|HTTP 2xx|Count|合計|HTTP 2xx|インスタンス|
|Http3xx|HTTP 3xx|Count|合計|HTTP 3xx|インスタンス|
|Http401|HTTP 401|Count|合計|HTTP 401|インスタンス|
|Http403|HTTP 403|Count|合計|HTTP 403|インスタンス|
|Http404|HTTP 404|Count|合計|HTTP 404|インスタンス|
|Http406|HTTP 406|Count|合計|HTTP 406|インスタンス|
|Http4xx|HTTP 4xx|Count|合計|HTTP 4xx|インスタンス|
|Http5xx|HTTP サーバー エラー|Count|合計|HTTP サーバー エラー|インスタンス|
|AverageResponseTime|平均応答時間|Seconds|平均|平均応答時間|インスタンス|
|CpuPercentage|CPU の割合|Percent|平均|CPU の割合|インスタンス|
|MemoryPercentage|メモリの割合|Percent|平均|メモリの割合|インスタンス|
|DiskQueueLength|ディスク キューの長さ|Count|平均|ディスク キューの長さ|インスタンス|
|HttpQueueLength|HTTP キューの長さ|Count|平均|HTTP キューの長さ|インスタンス|
|ActiveRequests|アクティブな要求数|Count|合計|アクティブな要求数|インスタンス|
|TotalFrontEnds|フロント エンドの合計|Count|平均|フロント エンドの合計|ディメンションなし|
|SmallAppServicePlanInstances|S App Service プランの worker の数|Count|平均|S App Service プランの worker の数|ディメンションなし|
|MediumAppServicePlanInstances|M App Service プランの worker の数|Count|平均|M App Service プランの worker の数|ディメンションなし|
|LargeAppServicePlanInstances|L App Service プランの worker の数|Count|平均|L App Service プランの worker の数|ディメンションなし|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|メトリック|メトリックの表示名|単位|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|WorkersTotal|worker の合計数|Count|平均|worker の合計数|ディメンションなし|
|WorkersAvailable|利用可能な worker の数|Count|平均|利用可能な worker の数|ディメンションなし|
|WorkersUsed|使用済みの worker の数|Count|平均|使用済みの worker の数|ディメンションなし|
|CpuPercentage|CPU の割合|Percent|平均|CPU の割合|インスタンス|
|MemoryPercentage|メモリの割合|Percent|平均|メモリの割合|インスタンス|

## <a name="next-steps"></a>次の手順
* [Azure Monitor のメトリックを確認します](data-platform.md)
* [メトリックでアラートを作成します](alerts-overview.md)
* [メトリックをストレージ、Event Hub、または Log Analytics にエクスポートします](diagnostic-logs-overview.md)
