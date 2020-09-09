---
title: Azure HDInsight の Interactive Query クラスターのサイズ設定ガイド
description: Azure HDInsight の Interactive Query のサイズ設定ガイド
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a81eff1dcf48996c319933aa4dd46170043b943b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663650"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Azure HDInsight の Interactive Query クラスターのサイズ設定ガイド

このドキュメントでは、適切なパフォーマンスを実現するための一般的なワークロードの HDInsight Interactive Query クラスター (LLAP) のサイズ設定について説明します。 このドキュメントに記載されている推奨事項は汎用的なものであり、特定のワークロードに特定のチューニングが必要な場合があります。

## <a name="default-vm-types-for-interactive-query"></a>Interactive Query の既定の VM の種類

| ノードの種類 | インスタンス | サイズ |
|---|---|---|
| Head | D13 v2 | 8 VCPUS、56-GB RAM、400 GB SSD |
| ワーカー | D14 v2 | 16 VCPUS、112-GB RAM、800 GB SSD |
| ZooKeeper | A4 v2 | 4 VCPUS、8-GB RAM、40 GB SSD |

## <a name="recommended-configurations"></a>推奨される構成

推奨される構成値は、D14 v2 タイプのワーカー ノードに基づいています。

| Key | 値 | 説明 |
|---|---|---|
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | ノード上のすべての YARN コンテナーに割り当てられた合計メモリ (MB 単位)。 |
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | RM でのすべてのコンテナー要求の最大割り当て (MB 単位)。 この値より大きいメモリ要求は有効になりません。 |
| yarn.scheduler.maximum-allocation-vcores | 12 |Resource Manager でのすべてのコンテナー要求に対する CPU コアの最大数。 この値より大きい要求は有効になりません。 |
| yarn.scheduler.capacity.root.llap.capacity | 90% | LLAP キューの YARN 容量割り当て。  |
| hive.server2.tez.sessions.per.default.queue | number_of_worker_nodes |hive.server2.tez.default.queues で指定された各キューのセッション数。 この数は、クエリ コーディネーター (Tez AM) の数に対応しています。 |
| tez.am.resource.memory.mb | 4096 (MB) | tez AppMaster によって使用されるメモリの量 (MB 単位)。 |
| hive.tez.container.size | 4096 (MB) | Tez コンテナーのサイズを MB 単位で指定します。 |
| hive.llap.daemon.num.executors | 12 | LLAP デーモンごとの Executor の数。 |
| hive.llap.io.threadpool.size | 12 | Executor のスレッド プール サイズ。 |
| hive.llap.daemon.yarn.container.mb | 86016 (MB) | 個々の LLAP デーモンによって使用される MB 単位のメモリの合計 (デーモンあたりのメモリ)。|
| hive.llap.io.memory.size | 409600 (MB) | SSD キャッシュが有効な場合の LLAP デーモンごとのキャッシュ サイズ (MB 単位)。 |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | マップ結合を実行するメモリ サイズ (MB 単位)。 |

## <a name="llap-daemon-size-estimations"></a>LLAP デーモン サイズの見積もり  

### <a name="yarnnodemanagerresourcememory-mb"></a>yarn.nodemanager.resource.memory-mb

この値は、各ノードの YARN コンテナーによって使用されるメモリの最大合計を MB 単位で示します。 この値は、このノードで YARN が使用できるメモリの量を指定します。そのため、この値はノードの合計メモリより小さくする必要があります。

この値を [ノードの合計物理メモリ] – [OS + 他のサービス用のメモリ] に設定します。

この値は、使用可能な RAM の 90% 以下に設定することをお勧めします。 D14 v2 の場合、推奨値は **102400 MB** です。

### <a name="yarnschedulermaximum-allocation-mb"></a>yarn.scheduler.maximum-allocation-mb

この値は、Resource Manager でのすべてのコンテナー要求の最大割り当てを MB 単位で示します。 指定された値より大きいメモリ要求は有効になりません。 Resource Manager は、`yarn.scheduler.minimum-allocation-mb` 単位でコンテナーにメモリを割り当てることだけができ、`yarn.scheduler.maximum-allocation-mb` によって指定されたサイズを超えることはできません。 この値は、ノードに割り当てられたメモリの合計 (`yarn.nodemanager.resource.memory-mb` によって指定されます) より大きくすることはできません。

D14 v2 ワーカー ノードの場合、推奨値は **102400 MB** です。

### <a name="yarnschedulermaximum-allocation-vcores"></a>yarn.scheduler.maximum-allocation-vcores

この構成は、Resource Manager のすべてのコンテナー要求に対する仮想 CPU コアの最大数を示します。 この構成より高い値を要求しても有効になりません。 この構成は、YARN スケジューラのグローバル プロパティです。 LLAP デーモン コンテナーの場合、この値は使用可能な仮想コア (VCORES) 合計の 75% に設定できます。 残りの 25% は、NodeManager、DataNode、およびワーカー ノードで実行されているその他のサービス用に予約されている必要があります。  

D14 v2 ワーカー ノードの場合、16 個の仮想コアがあり、16 個の仮想コアの 75% を指定できます。 そのため、LLAP デーモン コンテナーの推奨値は **12** です。

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>hive.server2.tez.sessions.per.default.queue

この構成値により、`hive.server2.tez.default.queues` によって指定されたキューごとに同時に起動する Tez セッションの数が決まります。 この値は Tez AM (クエリ コーディネーター) の数に対応します。 ノードごとに 1 つの Tez AM を持つワーカー ノードの数と同じにすることをお勧めします。 Tez AM の数は、LLAP デーモン ノードの数よりも大きくなる可能性があります。 主な役割は、クエリの実行を調整し、クエリ プランのフラグメントを対応する LLAP デーモンに割り当てて実行することです。 より高いスループットを実現するには、これを LLAP デーモン ノード数の倍数にすることをお勧めします。  

既定の HDInsight クラスターには 4 つのワーカー ノードで実行されている 4 つの LLAP デーモンがあるため、推奨値は **4** です。  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>tez.am.resource.memory.mb、hive.tez.container.size

`tez.am.resource.memory.mb` は、Tez アプリケーション マスターのサイズを定義します。  
推奨値は **4096 MB** です。

`hive.tez.container.size` は、Tez コンテナーに割り当てられるメモリの量を定義します。 この値は、YARN 最小コンテナー サイズ (`yarn.scheduler.minimum-allocation-mb`) と YARN 最大コンテナー サイズ (`yarn.scheduler.maximum-allocation-mb`) の間に設定する必要があります。  
**4096 MB** に設定することをお勧めします。  

一般的な規則では、コンテナー 1 つに 1 つのプロセッサと考え、この値をプロセッサごとのメモリ量より小さくします。 LLAP デーモンにメモリを割り当てる前に、ノード上の Tez AM のメモリを `Rreserve` します。 たとえば、ノードごとに 2 つの Tez AM (それぞれ 4 GB) を使用している場合は、2 つの Tez AM に対して 8 GB を予約し、LLAP デーモンに 90 GB 中の 82 GB を割り当てます。

### <a name="yarnschedulercapacityrootllapcapacity"></a>yarn.scheduler.capacity.root.llap.capacity

この値は、LLAP キューに割り当てられている容量の割合を示します。 HDInsights の Interactive Query クラスターは、LLAP キューの合計容量の 90% を割り当て、残りの 10% は他のコンテナー割り当ての既定のキューに設定されます。  
D14v2 ワーカー ノードの場合、LLAP キューの推奨値は **90** です。

### <a name="hivellapdaemonyarncontainermb"></a>hive.llap.daemon.yarn.container.mb

LLAP デーモンの合計メモリ サイズは、次のコンポーネントに依存します。

* YARN コンテナー サイズ (`yarn.scheduler.maximum-allocation-mb`、`yarn.scheduler.maximum-allocation-mb`、`yarn.nodemanager.resource.memory-mb`) の構成

* Executor によって使用されるヒープメモリ (Xmx)

    ヘッドルームのサイズを確保した後に使用可能な RAM の容量。  
    D14 v2、HDI 4.0 の場合 - この値は (86 GB - 6 GB) = 80 GB です。  
    D14 v2、HDI 3.6 の場合 - この値は (84 GB - 6 GB) = 78 GB です。

* デーモンごとのオフヒープのメモリ内キャッシュ (hive.llap.io.memory.size)

* ヘッドルーム

    これは、Java VM のオーバーヘッド (メタスペース、スレッド スタック、gc データ構造など) に使用されるオフヒープ メモリの一部です。 この部分は、ヒープ サイズ (Xmx) の約 6% であることが観測されています。 さらに安全性を高める場合は、LLAP デーモンのメモリ サイズの合計の 6% として計算できます。 SSD キャッシュが有効になっている場合、LLAP デーモンは使用可能なすべてのメモリ内領域をヒープに対してのみ使用できる可能性があるためです。  
    D14 v2 の場合、推奨値は ceil(86 GB x 0.06) ~= **6 GB** です。  

デーモンあたりのメモリ = [メモリ内キャッシュ サイズ] + [ヒープ サイズ] + [ヘッドルーム]。

これは、次のように算出されます。

ノードごとの Tez AM メモリ = [(Tez AM の数/LLAP デーモン ノードの数) * Tez AM のサイズ]。
LLAP デーモン コンテナー サイズ = [YARN 最大コンテナー メモリの 90%] – [ノードごとの Tez AM メモリ]。

D14 v2 ワーカー ノード、HDI 4.0 の場合、推奨値は (90 - (1/1 * 4 GB)) = **86 GB** です。
(HDI 3.6 の場合、推奨値は **84 GB** です。これは、スライダー AM に最大 2 GB を予約する必要があるためです。)  

### <a name="hivellapiomemorysize"></a>hive.llap.io.memory.size

この構成は、LLAP デーモンのキャッシュとして使用可能なメモリの量です。 LLAP デーモンは、SSD をキャッシュとして使用できます。 `hive.llap.io.allocator.mmap` = true に設定すると、SSD キャッシュが有効になります。 D14 v2 には最大 800 GB の SSD が付属しており、SSD キャッシュは既定で Interactive Query クラスター (LLAP) に対して有効になっています。 SSD 領域の 50% をオフヒープ キャッシュに使用するように構成されています。

D14 v2 の場合、推奨値は **409600 MB** です。  

SSD キャッシュが有効になっていない他の VM では、使用可能な RAM の一部を LLAP キャッシュ用に割り当てると、パフォーマンスが向上して便利です。 LLAP デーモンの合計メモリ サイズは次のように調整します。  

合計 LLAP デーモン メモリ = [LLAP キャッシュ サイズ] + [ヒープ サイズ] + [ヘッドルーム]。

ワークロードに最適なキャッシュ サイズとヒープ サイズに調整することをお勧めします。  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

この構成は、LLAP デーモンごとにタスクを並行して実行できる Executor の数を制御します。 この値は、使用可能な仮想コアの数、Executor ごとに割り当てられたメモリの量、および LLAP デーモンごとに使用可能な合計メモリのバランスです。 通常、この値は、コアの数にできるだけ近くします。

D14 v2 の場合、16 個の仮想コアを使用できますが、すべての仮想コアを指定することはできません。 ワーカー ノードは、使用可能な仮想コアの一部を必要とする NodeManager、DataNode、メトリック監視などの他のサービスも実行します。 この値は、そのノードで使用可能な合計仮想コア数の 75% まで構成できます。

D14 v2 の場合、推奨値は (0.75 X 16) = **12** です。

Executor ごとに最大 6 GB のヒープ領域を予約することをお勧めします。 使用可能な LLAP デーモンのサイズと、ノードあたりの使用可能な仮想コアの数に基づいて、Executor の数を調整します。  

### <a name="hivellapiothreadpoolsize"></a>hive.llap.io.threadpool.size

この値は、Executor のスレッド プール サイズを指定します。 Executor は指定されたとおりに固定されるため、LLAP デーモンあたりの実行数と同じになります。

D14 v2 の場合、この値は **12** に設定することをお勧めします。

この構成は `yarn.nodemanager.resource.cpu-vcores` の値を超えることはできません。

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>hive.auto.convert.join.noconditionaltask.size

このパラメーターを有効にするには、`hive.auto.convert.join.noconditionaltask` が有効になっていることを確認してください。 この構成により、ユーザーは、マップ結合のためにメモリに収めることができるテーブルのサイズを指定できます。 N 方向結合の `tables/partitions` の n-1 のサイズの合計が、構成されている値よりも小さい場合、マップ結合が選択されます。 マップ結合への自動変換のしきい値を計算するには、LLAP Executor のメモリ サイズを使用する必要があります。

D14 v2 の場合、この値は **2048 MB** に設定することをお勧めします。

この値が低く設定しすぎると自動変換機能が使用されない可能性があるので、ワークロードに適した値に調整することをお勧めします。 設定値が高すぎると、GC が一時停止し、クエリのパフォーマンスに悪影響を及ぼす可能性があります。

## <a name="next-steps"></a>次のステップ

* [ゲートウェイのガイドライン](gateway-best-practices.md)
* [Apache Tez メモリ チューニングの説明 - 操作手順](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [LLAP のマップ結合メモリのサイズ設定](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP - 1 ページ アーキテクチャの概要](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [Hive LLAP の詳細](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
