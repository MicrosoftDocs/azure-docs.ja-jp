---
title: HDInsight Interactive Query クラスター (LLAP) サイズ設定ガイド
description: LLAP サイズ設定ガイド
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 7df75077785c66215008e045ef0b1e451ba29f57
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931098"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Azure HDInsight Interactive Query クラスター (Hive LLAP) サイズ設定ガイド

このドキュメントでは、標準的なワークロードで適切なパフォーマンスを実現するための HDInsight Interactive Query クラスター (Hive LLAP クラスター) のサイズ設定について説明します。 このドキュメントに示す推奨事項は一般的なガイドラインであり、ワークロードによっては個別にチューニングが必要となるので注意してください。

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**HDInsight Interactive Query クラスター (LLAP) に対する Azure の既定の VM 種類**

| ノードの種類      | インスタンス | サイズ     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPU、56 GB RAM、400 GB SSD   |
| ワーカー   | **D14 v2**        | **16 vCPU、112 GB RAM、800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 vCPU、8 GB RAM、40 GB SSD       |

**_注: 推奨されるすべての構成値は、D14 v2 タイプのワーカー ノードに基づいています。_* _  

### <a name="_configuration"></a>_ *構成:* *    
| 構成キー      | 推奨値  | 説明 |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | ノード上のすべての YARN コンテナーに割り当てられるメモリの合計 (MB) | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | RM でのすべてのコンテナー要求に対する最大割り当て (MB)。 この値を超えるメモリ要求は有効になりません |
| yarn.scheduler.maximum-allocation-vcores | 12 |Resource Manager でのすべてのコンテナー要求に対する CPU コアの最大数。 この値を超える要求は有効になりません。 |
| yarn.nodemanager.resource.cpu-vcores | 12 | コンテナーに割り当てることができる NodeManager あたりの CPU コア数 |
| yarn.scheduler.capacity.root.llap.capacity | 85 (%) | LLAP キューに対する YARN 容量割り当て  |
| tez.am.resource.memory.mb | 4096 (MB) | tez AppMaster で使用されるメモリの量 (MB) |
| hive.server2.tez.sessions.per.default.queue | <number_of_worker_nodes> |hive.server2.tez.default.queues で指定された各キューのセッション数。 この数はクエリ コーディネーター (Tez AM) の数に対応します。 |
| hive.tez.container.size | 4096 (MB) | 指定された Tez コンテナーのサイズ (MB) |
| hive.llap.daemon.num.executors | 19 | LLAP デーモンあたりの Executor の数 | 
| hive.llap.io.threadpool.size | 19 | Executor のスレッド プール サイズ |
| hive.llap.daemon.yarn.container.mb | 81920 (MB) | 個々の LLAP デーモンで使用されるメモリの合計 (MB) (デーモンあたりのメモリ)
| hive.llap.io.memory.size | 242688 (MB) | SSD キャッシュが有効である場合の LLAP デーモンあたりのキャッシュ サイズ (MB) |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | マップ結合を行うためのメモリ サイズ (MB) |

### <a name="llap-architecturecomponents"></a>**LLAP アーキテクチャ/コンポーネント:**  

![`LLAP アーキテクチャ/コンポーネント`](./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png "LLAP アーキテクチャ/コンポーネント")

### <a name="llap-daemon-size-estimations"></a>**LLAP デーモンのサイズ見積もり** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1.ノード上のすべてのコンテナーに対する YARN メモリ割り当ての合計の決定**    
構成: **_yarn.nodemanager.resource.memory-mb_* _  

この値は、各ノードの YARN コンテナーが使用できるメモリの最大合計 (MB) を示します。 指定する値は、当該ノードの物理メモリの合計容量より小さくする必要があります。   
ノード上のすべての YARN コンテナーに対するメモリ合計 = (物理メモリの合計 – OS とその他サービス用のメモリ)  
この値は、使用可能な RAM サイズの 90% 以下に設定します。  
D14 v2 の場合、推奨値は _*102400 MB** です。 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2.YARN コンテナー要求あたりの最大メモリ容量の決定**  
構成: **_yarn.scheduler.maximum-allocation-mb_* _

この値は、Resource Manager でのすべてのコンテナー要求に対する最大割り当て (MB) を示します。 指定された値より大きいメモリ要求は有効になりません。 Resource Manager では、_yarn.scheduler.minimum-allocation-mb* を増分単位としてコンテナーにメモリを割り当てることができ、*yarn.scheduler.maximum-allocation-mb* で指定されたサイズを超えることはできません。 指定する値は、*yarn.nodemanager.resource.memory-mb* で指定された、ノード上のすべてのコンテナーに割り当てられるメモリの合計以下にする必要があります。    
D14 v2 のワーカー ノードでの推奨値は、**102400 MB** です。

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3.YARN コンテナー要求あたりの最大仮想コア数の決定**  
構成: **_yarn.scheduler.maximum-allocation-vcores_* _  

この値は、Resource Manager でのすべてのコンテナー要求に対する仮想 CPU コアの最大数を示します。 この値を超える仮想コア数の要求は有効にはなりません。 これは YARN スケジューラのグローバル プロパティです。 LLAP デーモン コンテナーの場合、この値は使用可能な仮想コアの合計の 75% に設定できます。 残りの 25% は、NodeManager、DataNode、およびワーカー ノードで実行されているその他のサービスのために確保しておく必要があります。  
D14 v2 の VM には 16 個の仮想コアがあり、合計 16 個の仮想コアの 75% を LLAP デーモン コンテナーで使用できます。  
D14 v2 での推奨値は _*12** です。  

#### <a name="4-number-of-concurrent-queries"></a>**4.同時クエリの数**  
構成: **_hive.server2.tez.sessions.per.default.queue_* _

この構成値により、並列で起動できる Tez セッションの数が決まります。 これらの Tez セッションは、"hive.server2.tez.default.queues" で指定されたキューごとに起動されます。 これは Tez AM (クエリ コーディネーター) の数に対応します。 ワーカー ノードの数と同じにすることをお勧めします。 Tez AM の数は、LLAP デーモン ノードの数より大きくなる可能性があります。 Tez AM の主な役割は、クエリの実行を調整し、クエリ プラン フラグメントを対応する LLAP デーモンに割り当てて実行することです。 より高いスループットを実現するには、この値は常に LLAP デーモン ノードの数の倍数にしてください。  

既定の HDInsight クラスターでは、4 つのワーカー ノードで 4 つの LLAP デーモンが実行されるため、推奨値は _*4** です。  

**Hive 構成変数の Ambari UI スライダー`hive.server2.tez.sessions.per.default.queue`:**

![`LLAP 同時クエリの最大数`](./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png "LLAP の同時クエリの最大数")

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5.Tez コンテナーおよび Tez アプリケーション マスターのサイズ**    
構成: **_tez.am.resource.memory.mb、hive.tez.container.size_* _  

_tez.am.resource.memory.mb* - Tez アプリケーション マスターのサイズを定義します。  
推奨値は **4096 MB** です。
   
*hive.tez.container.size* - Tez コンテナーに割り当てられるメモリ量を定義します。 この値は、YARN 最小コンテナー サイズ (*yarn.scheduler.minimum-allocation-mb*) と YARN 最大コンテナー サイズ (*yarn.scheduler.maximum-allocation-mb*) の間に設定する必要があります。 LLAP デーモンの Executor は、この値を使用して Executor あたりのメモリ使用量を制限します。  
推奨値は **4096 MB** です。  

#### <a name="6-llap-queue-capacity-allocation"></a>**6.LLAP キューの容量割り当て**   
構成: **_yarn.scheduler.capacity.root.llap.capacity_* _  

この値は、LLAP キューに割り当てる容量の割合を示します。 YARN キューの構成方法によっては、容量割り当ての値がワークロードごとに異なる可能性があります。 ワークロードが読み取り専用操作の場合は、これを容量の 90% のように高く設定しても機能します。 ただし、ワークロードがマネージド テーブルを使用する更新、削除、マージの混合操作である場合は、容量の 85% を LLAP キューに割り当てることをお勧めします。 残りの 15% の容量は、圧縮などのその他のタスクが既定のキューからコンテナーを割り当てるために使用できます。 これにより、既定のキュー内のタスクによって YARN リソースが使われることはなくなります。    

D14 v2 のワーカー ノードでの LLAP キューの推奨値は _*85** です。     
(読み取り専用のワークロードの場合は、必要に応じて 90 まで引き上げることができます。)  

#### <a name="7-llap-daemon-container-size"></a>**7.LLAP デーモン コンテナーのサイズ**    
構成: **_hive.llap.daemon.yarn.container.mb_* _  
   
LLAP デーモンは、各ワーカー ノードで YARN コンテナーとして実行されます。 LLAP デーモン コンテナーの合計メモリ サイズは、次の要因によって決まります。    
_  YARN コンテナー サイズの構成 (yarn.scheduler.minimum-allocation-mb、yarn.scheduler.maximum-allocation-mb、yarn.nodemanager.resource.memory-mb)
*  ノード上の Tez AM の数
*  ノード上のすべてのコンテナーに対して構成された合計メモリと LLAP キューの容量  

Tez アプリケーション マスター (Tez AM) に必要なメモリは、次のように計算できます。  
Tez AM はクエリ コーディネーターとして動作し、Tez AM の数は、処理される同時実行クエリの数に基づいて構成できます。 理論的には、ワーカー ノードごとに 1 つの Tez AM を検討できます。 ただし、1 つのワーカー ノードに複数の Tez AM が表示されることもあります。 計算上の目的により、Tez AM はすべての LLAP デーモン ノード/ワーカー ノードに均等に分布されていると見なします。
Tez AM あたりのメモリは 4 GB にすることをお勧めします。  

Tez AM の数 = Hive 構成で指定された値 ***hive.server2.tez.sessions.per.default.queue** _。  
LLAP デーモン ノードの数 = Ambari UI の環境変数 _*_num_llap_nodes_for_llap_daemons_*_ で指定。  
Tez AM のコンテナー サイズ = Tez 構成 _*_tez.am.resource.memory.mb_*_ で指定した値。  

ノードあたりの Tez AM メモリ = _ *(* * ceil **(** Tez AM の数 **/** LLAP デーモン ノードの数 **)** **x** Tez AM コンテナー サイズ **)**  
D14 v2 の場合、既定の構成の Tez AM の数と LLAP デーモン ノードの数はそれぞれ 4 つです。  
ノードあたりの Tez AM メモリ = (ceil(4/4) x 4 GB) = 4 GB

各ワーカー ノードで LLAP キューに使用できるメモリの合計は、次のように計算できます。  
この値は、ノード上のすべての YARN コンテナーで使用できるメモリの総量 (*yarn.nodemanager.resource.memory-mb*) および LLAP キューに対して構成されている容量の割合 (*yarn.scheduler.capacity.root.llap.capacity*) によって決まります。  
ワーカー ノード上の LLAP キューの合計メモリ = ノード上のすべての YARN コンテナーで使用できる合計メモリ x LLAP キューに対する容量の割合。  
D14 v2 では、この値が (100 GB x 0.85) = 85 GB となります。

LLAP デーモン コンテナーのサイズは、次のように計算されます。

**LLAP デーモン コンテナーのサイズ =  (ワーカー ノード上の LLAP キューのメモリ合計) – (ノードあたりの Tez AM メモリ) - (サービス マスターのコンテナー サイズ)**  
ワーカー ノードのいずれかで生成されたクラスターのサービス マスター (LLAP サービスのアプリケーション マスター) は 1 つだけです。 計算上の目的により、1 つのワーカー ノードにあるサービス マスターは 1 つと見なします。  
D14 v2 ワーカー ノード、HDI 4.0 の場合、推奨値は (85 GB - 4 GB - 1 GB)) = **80 GB**  です  
(HDI 3.6 の場合は、スライダー AM 用に追加で最大 2 GB を予約する必要があるため、推奨値は **79 GB** となります)。  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8.LLAP デーモンあたりの Executor の数の決定**  
構成: **_hive.llap.daemon.num.executors_* _、_*_hive.llap.io.threadpool.size_*_

_*_hive.llap.daemon.num.executors_*_:   
この構成は、LLAP デーモンあたりの、タスクを並列で実行できる Executor の数を制御します。 この値は、仮想コアの数、Executor ごとに使用されるメモリの量、および LLAP デーモン コンテナーで使用できるメモリの合計量によって決まります。    Executor の数は、ワーカー ノードあたりの使用可能な仮想コアの 120% にオーバーサブスクライブされる場合があります。 ただし、メモリ要件を満たしていない場合は、Executor ごとに必要なメモリと LLAP デーモン コンテナーのサイズに基づいて調整する必要があります。

各 Executor は 1 つの Tez コンテナーに相当し、4 GB のメモリ (Tez コンテナー サイズ) を使用します。 LLAP デーモン内のすべての Executor は、同じヒープ メモリを共有します。 すべての Executor がメモリ集中型の操作を同時に実行するわけではないと仮定すると、Executor ごとに Tez コンテナー サイズ (4 GB) の 75% が妥当です。 このように考え、並列処理を向上するために各 Executor のメモリを少なくする (3 GB など) ことで、Executor の数を増やすことができます。 ただし、対象のワークロードに合わせてこの設定を調整することをお勧めします。

D14 v2 の VM には仮想コアが 16 個あります。
D14 v2 の場合、Executor 数の推奨値は、Executor ごとに 3GB とすると、各ワーカー ノードで (16 個の仮想コア x 120%) ~= _ *19** となります。

**_hive.llap.io.threadpool.size_ *_:この値は、Executor のスレッド プール サイズを指定します。Executor は指定したとおりに固定されるため、これは LLAP デーモンあたりの Executor の数と同じになります。D14 v2 の場合、推奨値は _* 19** です。

#### <a name="9-determining-llap-daemon-cache-size"></a>**9.LLAP デーモンのキャッシュ サイズの決定**  
構成: **_hive.llap.io.memory.size_* _

LLAP デーモンのコンテナー メモリは、次のコンポーネントで構成されます。 _  ヘッド ルーム
*  Executor (Xmx) が使用するヒープ メモリ
*  デーモンごとのメモリ内キャッシュ (そのオフヒープ メモリのサイズ。SSD キャッシュが有効になっている場合は適用されません)
*  メモリ内キャッシュのメタデータ サイズ (SSD キャッシュが有効になっている場合のみ適用できます)

**ヘッドルーム サイズ**:このサイズは、Java VM のオーバーヘッド (メタスペース、スレッド スタック、gc データ構造など) に使用されるオフヒープ メモリの一部を示します。 一般に、このオーバーヘッドはヒープ サイズ (Xmx) の約 6% です。 さらに安全性を高める場合、この値は LLAP デーモンのメモリ サイズの合計の 6% として計算できます。  
D14 v2 の場合、推奨値は ceil(80 GB x 0.06) ~= **4 GB** です。  

**ヒープ サイズ (Xmx)** :すべての Executor で使用可能なヒープ メモリの量です。
合計ヒープ サイズ = Executor の数 x 3 GB  
D14 v2 の場合、この値は 19 x 3 GB = **57 GB**  

`Ambari environment variable for LLAP heap size:`

![`LLAP ヒープ サイズ`](./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png "LLAP ヒープ サイズ")

SSD キャッシュが無効になっている場合は、LLAP デーモン コンテナーのサイズからヘッドルーム サイズとヒープ サイズを取り除いたあとに残るメモリの量がメモリ内キャッシュとなります。

SSD キャッシュが有効になっている場合は、キャッシュ サイズの計算が異なります。
*hive.llap.io.allocator.mmap* = true に設定すると、SSD キャッシュが有効になります。
SSD キャッシュを有効にすると、メモリの一部が SSD キャッシュのメタデータの格納に使用されます。 メタデータはメモリに格納されます。それは、SSD キャッシュ サイズの最大 8% になると予想されます。   
SSD キャッシュのメモリ内メタデータ サイズ = LLAP デーモンのコンテナー サイズ - (ヘッドルーム + ヒープ サイズ)  
D14 v2、HDI 4.0 の場合の SSD キャッシュのメモリ内メタデータ サイズ = 80 GB - (4 GB + 57 GB) = **19 GB**  
D14 v2、HDI 3.6 の場合の SSD キャッシュのメモリ内メタデータ サイズ = 79 GB - (4 GB + 57 GB) = **18 GB**

SSD キャッシュのメタデータの格納に使用できるメモリのサイズから、サポートできる SSD キャッシュのサイズを計算できます。  
SSD キャッシュのメモリ内メタデータ サイズ = LLAP デーモンのコンテナー サイズ - (ヘッドルーム + ヒープ サイズ) = 19 GB     
SSD キャッシュのサイズ = SSD キャッシュのメモリ内メタデータのサイズ (19 GB) / 0.08 (8%)  

D14 v2、HDI 4.0 の場合の推奨 SSD キャッシュ サイズ = 19 GB / 0.08 ~= **237 GB**  
D14 v2、HDI 3.6 の場合の推奨 SSD キャッシュ サイズ = 18 GB / 0.08 ~= **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10.マップ結合メモリの調整**   
構成: **_hive.auto.convert.join.noconditionaltask.size_* _

このパラメーターを有効にするには、_hive.auto.convert.join.noconditionaltask* が有効になっていることを確認してください。
この構成は、Hive オプティマイザーによる MapJoin の選択に関するしきい値を決定します。Hive オプティマイザーは、他の Executor からのメモリのオーバーサブスクリプションによってインメモリ ハッシュ テーブルに余裕が生まれ、より多くのマップ結合変換が可能になると見なします。 Executor あたり 3 GB と考えると、このサイズは 3 GB までオーバーサブスクライブが可能ですが、他の操作によって、バッファーの並べ替え、バッファーのシャッフルなどにヒープ メモリが使用されることもあります。   
そのため、D14 v2 で Executor ごとに 3 GB のメモリを使用する場合は、この値を **2048 MB** に設定することをお勧めします。  

(注: 場合によっては、ワークロードに合わせてこの値を調整する必要があります。 この値を低く設定しすぎると、自動変換機能が使用されない場合があります。 また、これを大きく設定しすぎると、メモリ不足例外や GC の一時停止が発生して、パフォーマンスが低下する可能性があります。)  

#### <a name="11-number-of-llap-daemons"></a>**11.LLAP デーモンの数**
Ambari 環境変数: **_num_llap_nodes、num_llap_nodes_for_llap_daemons_* _  

_ *num_llap_nodes** - Hive LLAP サービスで使用されるノードの数を指定します。これには、LLAP デーモン、LLAP サービス マスター、Tez アプリケーション マスター (Tez AM) を実行するノードが含まれます。  

![`LLAP サービスのノード数`](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png "LLAP サービスのノード数")  

**num_llap_nodes_for_llap_daemons** - LLAP デーモンにのみ使用されるノードの数を指定します。 LLAP デーモン コンテナーのサイズは最大限適合するノードに設定され、結果的に、各ノードの LLAP デーモンは 1 つとなります。

![`LLAP デーモンのノード数`](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png "LLAP デーモンのノード数")

どちらの値も、Interactive Query クラスターのワーカー ノードの数と同じにしておくことをお勧めします。

### <a name="considerations-for-workload-management"></a>**ワークロード管理に関する考慮事項**  
LLAP のワークロード管理を有効にする場合は、ワークロード管理が想定どおり機能するのに十分な容量を確保してください。 ワークロード管理には、`llap` キューに加えて、カスタム YARN キューの構成が必要です。 ワークロードの要件に従って、クラスター リソースの総容量を LLAP キューとワークロード管理キューに割り当ててください。
ワークロード管理は、リソース プランがアクティブ化されたときに、Tez アプリケーション マスター (Tez AM) を生成します。
注意:

* リソース プランをアクティブ化することで生成される Tez AM は、`hive.server2.tez.interactive.queue` で指定されたワークロード管理キューからリソースを消費します。  
* Tez AM の数は、リソース プランで指定された `QUERY_PARALLELISM` の値によって変わります。  
* ワークロード管理がアクティブになると、LLAP キューの Tez AM は使用されません。 クエリ調整に使用されるのは、ワークロード管理キューの Tez AM のみです。 `llap` キュー内の Tez AM は、ワークロード管理が無効になっているときに使用されます。
 
次に例を示します。クラスターの合計容量である 100 GB のメモリは、LLAP、ワークロード管理、および既定のキューに次のように割り当てられます。
 - LLAP のキューの容量 = 70 GB
 - ワークロード管理のキューの容量 = 20 GB
 - 既定のキューの容量 = 10 GB

ワークロード管理のキューの容量が 20 GB の場合、リソース プランでは `QUERY_PARALLELISM` 値に 5 を指定できます。これは、コンテナー サイズが 4 GB の Tez AM をワークロード管理が 5 つ起動できることを意味します。 `QUERY_PARALLELISM` が容量を超えている場合は、一部の Tez AM が `ACCEPTED` 状態で応答を停止している可能性があります。 Hiveserver2 Interactive は、`RUNNING` 状態ではない Tez AM にクエリ フラグメントを送信することはできません。


#### <a name="next-steps"></a>**次の手順**
これらの値を設定しても問題が解決しない場合は、次のいずれかにアクセスしてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。  

* ##### <a name="other-references"></a>**その他の参考資料**
  * [その他の LLAP プロパティの構成](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Hive サーバー ヒープ サイズの構成](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [LLAP でのマップ結合のメモリ サイズ設定](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Tez 実行エンジンのプロパティ](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Hive LLAP の詳細](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)