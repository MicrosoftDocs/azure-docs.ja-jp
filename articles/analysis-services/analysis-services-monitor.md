---
title: Azure Analysis Services でのサーバー メトリックの監視 | Microsoft Docs
description: Azure Portal で、Analysis Services のサーバー メトリックを監視する方法を説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7c7aaf911930f83775f66c47377bc68edb059519
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958982"
---
# <a name="monitor-server-metrics"></a>サーバー メトリックの監視

Analysis Services は、ポータルの無料ツールの Azure メトリックス エクスプローラーで、サーバーのパフォーマンスと正常性を監視するために役立つメトリックを提供します。 たとえば、メモリと CPU 使用率、クライアント接続数、およびクエリのリソース消費を監視します。 Analysis Services では、他のほとんどの Azure サービスと同じ監視フレームワークを使用します。 詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/platform/metrics-getting-started.md)」を参照してください。

詳細な診断の実行、パフォーマンスの追跡、リソース グループまたはサブスクリプションで複数のサービス リソース全体の傾向を特定するには、[Azure Monitor](../azure-monitor/overview.md) を使用します。 Azure Monitor (サービス) には、課金対象のサービスがあります。


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Analysis Services サーバーのメトリックを監視するには

1. Azure Portal で **[メトリック]** を選択します。

    ![Azure Portal での監視](./media/analysis-services-monitor/aas-monitor-portal.png)

2. **[利用可能なメトリック]** で、お使いのグラフに含めるメトリックを選択します。 

    ![監視グラフ](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>サーバー メトリック

この表を使用すると、お使いの監視シナリオに最適なメトリックを確認できます。 同じグラフには、同じ単位のメトリックのみを表示することができます。

|メトリック|メトリックの表示名|単位|集計の種類|説明|
|---|---|---|---|---|
|CommandPoolJobQueueLength|コマンド プールのジョブ キューの長さ|Count|平均|コマンド スレッド プールのキュー内のジョブの数。|
|CurrentConnections|接続:現在の接続数|Count|平均|現在確立されているクライアント接続の数。|
|CurrentUserSessions|現在のユーザー セッション|Count|平均|確立された現在のユーザー セッションの数。|
|mashup_engine_memory_metric|M エンジン メモリ|Bytes|平均|マッシュアップ エンジン プロセスによるメモリ使用率|
|mashup_engine_qpu_metric|M エンジン QPU|Count|平均|マッシュアップ エンジン プロセスによる QPU 使用率|
|memory_metric|メモリ|バイト|平均|メモリ。 範囲は S1 で 0 ～ 25 GB、S2 で 0 ～ 50 GB、S4 で 0 ～ 100 GB|
|memory_thrashing_metric|メモリ スラッシング|Percent|平均|平均的なメモリ スラッシング。|
|CleanerCurrentPrice|メモリ:クリーナーの現在の価格|Count|平均|現在のメモリ価格 ($/バイト/時刻)。標準は 1,000 です。|
|CleanerMemoryNonshrinkable|メモリ:クリーナーの圧縮不可能なメモリ|Bytes|平均|バックグラウンド クリーナーによる削除の対象とならないメモリの量 (バイト単位)。|
|CleanerMemoryShrinkable|メモリ:クリーナーの圧縮可能なメモリ|Bytes|平均|バックグラウンド クリーナーによる削除の対象となるメモリの量 (バイト単位)。|
|MemoryLimitHard|メモリ:メモリの制限 - ハード|Bytes|平均|構成ファイルに指定されているハードのメモリの制限。|
|MemoryLimitHigh|メモリ:メモリの制限 - 高|Bytes|平均|構成ファイルに指定されているメモリの上限。|
|MemoryLimitLow|メモリ:メモリの制限 - 低|Bytes|平均|構成ファイルに指定されているメモリの下限。|
|MemoryLimitVertiPaq|メモリ:メモリの制限 - VertiPaq|Bytes|平均|構成ファイルに指定されているインメモリの制限。|
|MemoryUsage|メモリ:メモリ使用量|Bytes|平均|クリーナー メモリの価格の計算で使用されるサーバー プロセスのメモリ使用量。 Process\PrivateBytes カウンターの値に、メモリがマップされたデータのサイズを加えた値と等しくなります。インメモリ分析エンジン (VertiPaq) によって、エンジンのメモリの制限を超えてマップされた (割り当てられた) メモリは無視されます。|
|private_bytes_metric|Private Bytes |Bytes|平均|他のプロセスと共有されているメモリを含まない、Analysis Services エンジン プロセスと Mashup コンテナー プロセスによって割り当てられたメモリの総量。|
|virtual_bytes_metric|Virtual Bytes |Bytes|平均|Analysis Services エンジン プロセスと Mashup コンテナー プロセスで使用されている仮想アドレス空間の現在のサイズ。|
|mashup_engine_private_bytes_metric|M エンジン プライベート バイト |Bytes|平均|他のプロセスと共有されているメモリを含まない、メモリの Mashup コンテナー プロセスによって割り当てられたメモリの総量。|
|mashup_engine_virtual_bytes_metric|M エンジン仮想バイト |Bytes|平均|仮想アドレス空間の Mashup コンテナー プロセスが使用している現在のサイズ。|
|Quota|メモリ:Quota|Bytes|平均|現在のメモリ クォータ (バイト単位)。 メモリ クォータはメモリ付与またはメモリ予約とも呼ばれます。|
|QuotaBlocked|メモリ:ブロックされているクォータ|Count|平均|他のメモリ クォータが解放されるまでブロックされている現在のクォータ要求の数。|
|VertiPaqNonpaged|メモリ:VertiPaq 非ページ|Bytes|平均|メモリ内エンジン用にワーキング セットでロックされているメモリの量 (バイト単位)。|
|VertiPaqPaged|メモリ:VertiPaq ページ|Bytes|平均|メモリ内エンジン用に使用されているページ メモリの量 (バイト単位)。|
|ProcessingPoolJobQueueLength|処理プール ジョブ キューの長さ|Count|平均|処理スレッド プールのキュー内の非 I/O ジョブの数。|
|RowsConvertedPerSec|処理:1 秒あたりの変換行数|CountPerSecond|平均|処理中に変換された行の比率。|
|RowsReadPerSec|処理:1 秒あたりの読み取り行数|CountPerSecond|平均|すべてのリレーショナル データベースから読み取った行の比率。|
|RowsWrittenPerSec|処理:1 秒あたりの書き込み行数|CountPerSecond|平均|処理中に書き込まれた行の比率。|
|qpu_metric|QPU|Count|平均|QPU。 範囲は S1 で 0 ～ 100、S2 で 0 ～ 200、S4 で 0 ～ 400|
|QueryPoolBusyThreads|クエリ プール ビジー スレッド|Count|平均|クエリ スレッド プールのビジー スレッドの数。|
|SuccessfullConnectionsPerSec|成功した接続数 (秒単位)|CountPerSecond|平均|接続が正常に完了した割合。|
|CommandPoolBusyThreads|スレッド:コマンド プールのビジー状態のスレッド|Count|平均|コマンド スレッド プール内にあるビジー状態のスレッドの数。|
|CommandPoolIdleThreads|スレッド:コマンド プールのアイドル状態のスレッド|Count|平均|コマンド スレッド プール内にあるアイドル状態のスレッドの数。|
|LongParsingBusyThreads|スレッド:長時間解析を行っているビジー状態のスレッド|Count|平均|長時間解析を行っているスレッド プール内にあるビジー状態のスレッドの数。|
|LongParsingIdleThreads|スレッド:長時間解析を行っているアイドル状態のスレッド|Count|平均|長時間解析を行っているスレッド プール内にあるアイドル状態のスレッドの数。|
|LongParsingJobQueueLength|スレッド:長時間解析を行っているジョブのキューの長さ|Count|平均|長時間解析を行っているスレッド プールのキューに登録されているジョブの数。|
|ProcessingPoolIOJobQueueLength|スレッド:処理中のプール I/O ジョブ キューの長さ|Count|平均|処理中のスレッド プールのキューに登録されている I/O ジョブの数。|
|ProcessingPoolBusyIOJobThreads|スレッド:処理中のプールのビジー状態の I/O ジョブのスレッド|Count|平均|処理中のスレッド プール内で I/O ジョブを実行しているスレッドの数。|
|ProcessingPoolBusyNonIOThreads|スレッド:処理中のプールのビジー状態の I/O 以外のスレッド|Count|平均|処理中のスレッド プール内で I/O 以外のジョブを実行しているスレッドの数。|
|ProcessingPoolIdleIOJobThreads|スレッド:処理中のプールのアイドル状態の I/O ジョブのスレッド|Count|平均|処理中のスレッド プール内にある I/O ジョブのアイドル状態のスレッドの数。|
|ProcessingPoolIdleNonIOThreads|スレッド:処理中のプールのアイドル状態の I/O 以外のスレッド|Count|平均|I/O 以外のジョブ専用の、処理中のスレッド プール内にあるアイドル状態のスレッドの数。|
|QueryPoolIdleThreads|スレッド:クエリ プールのアイドル状態のスレッド|Count|平均|処理中のスレッド プール内にある I/O ジョブのアイドル状態のスレッドの数。|
|QueryPoolJobQueueLength|スレッド:クエリ プール ジョブ キューの長さ|Count|平均|クエリ スレッド プールのキューに登録されているジョブの数。|
|ShortParsingBusyThreads|スレッド:短時間解析を行っているビジー状態のスレッド|Count|平均|短時間解析を行っているスレッド プール内にあるビジー状態のスレッドの数。|
|ShortParsingIdleThreads|スレッド:短時間解析を行っているアイドル状態のスレッド|Count|平均|短時間解析を行っているスレッド プール内にあるアイドル状態のスレッドの数。|
|ShortParsingJobQueueLength|スレッド:短時間解析を行っているジョブ キューの長さ|Count|平均|短時間解析を行っているスレッド プールのキューに登録されているジョブの数。|
|TotalConnectionFailures|合計接続失敗数|Count|平均|失敗した接続試行数の合計。|
|TotalConnectionRequests|合計接続要求数|Count|平均|合計接続要求数。 |

## <a name="next-steps"></a>次の手順
[Azure Monitor の概要](../azure-monitor/overview.md)      
[Azure メトリックス エクスプローラーの概要](../azure-monitor/platform/metrics-getting-started.md)      
[Azure Monitor REST API のメトリック](/rest/api/monitor/metrics)
