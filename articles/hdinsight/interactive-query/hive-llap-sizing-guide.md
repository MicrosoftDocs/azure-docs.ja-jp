---
title: HDInsight Interactive Query クラスター (LLAP) サイズ設定ガイド
description: LLAP サイズ設定ガイド
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: a9b86f09ade0d437436779ef3e4a17fcdede2cf0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663690"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Azure HDInsight Interactive Query クラスター (Hive LLAP) サイズ設定ガイド

このドキュメントでは、標準的なワークロードで適切なパフォーマンスを実現するための HDInsight Interactive Query クラスター (Hive LLAP クラスター) のサイズ設定について説明します。 このドキュメントに示す推奨事項は一般的なガイドラインであり、ワークロードによっては個別にチューニングが必要となるので注意してください。 

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**HDInsight Interactive Query クラスター (LLAP) に対する Azure の既定の VM 種類**

| ノードの種類      | インスタンス | サイズ     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPU、56 GB RAM、400 GB SSD   |
| ワーカー   | **D14 v2**        | **16 vCPU、112 GB RAM、800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 vCPU、8 GB RAM、40 GB SSD       |

***注意事項: 推奨されるすべての構成値は、D14 v2 タイプのワーカー ノードに基づいています。***  

### <a name="configuration"></a>**構成:**    
| 構成キー      | 推奨値  | 説明 |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | ノード上のすべての YARN コンテナーに割り当てられるメモリの合計 (MB) | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | RM でのすべてのコンテナー要求に対する最大割り当て (MB)。 この値を超えるメモリ要求は有効になりません |
| yarn.scheduler.maximum-allocation-vcores | 12 |Resource Manager でのすべてのコンテナー要求に対する CPU コアの最大数。 この値を超える要求は有効になりません。 |
| yarn.nodemanager.resource.cpu-vcores | 12 | コンテナーに割り当てることができる NodeManager あたりの CPU コア数 |
| yarn.scheduler.capacity.root.llap.capacity | 80 (%) | LLAP キューに対する YARN 容量割り当て  |
| tez.am.resource.memory.mb | 4096 (MB) | tez AppMaster で使用されるメモリの量 (MB) |
| hive.server2.tez.sessions.per.default.queue | <number_of_worker_nodes> |hive.server2.tez.default.queues で指定された各キューのセッション数。 この数はクエリ コーディネーター (Tez AM) の数に対応します。 |
| hive.tez.container.size | 4096 (MB) | 指定された Tez コンテナーのサイズ (MB) |
| hive.llap.daemon.num.executors | 12 | LLAP デーモンあたりの Executor の数 | 
| hive.llap.io.threadpool.size | 12 | Executor のスレッド プール サイズ |
| hive.llap.daemon.yarn.container.mb | 77824 (MB) | 個々の LLAP デーモンで使用されるメモリの合計 (MB) (デーモンあたりのメモリ)
| hive.llap.io.memory.size | 235520 (MB) | SSD キャッシュが有効である場合の LLAP デーモンあたりのキャッシュ サイズ (MB) |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | マップ結合を行うためのメモリ サイズ (MB) |

### <a name="llap-daemon-size-estimations"></a>**LLAP デーモンのサイズ見積もり**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1.ノード上のすべてのコンテナーに対する YARN メモリ割り当ての合計の決定**    
構成: ***yarn.nodemanager.resource.memory-mb***  

この値は、各ノードの YARN コンテナーが使用できるメモリの最大合計 (MB) を示します。 指定する値は、当該ノードの物理メモリの合計容量より小さくする必要があります。   
ノード上のすべての YARN コンテナーに対するメモリ合計 = [物理メモリの合計] – [OS とその他サービス用のメモリ]  
この値は、使用可能な RAM サイズの 90% 以下に設定します。  
D14 v2 での推奨値は  **102400 MB** です。 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2.YARN コンテナー要求あたりの最大メモリ容量の決定**  
構成: ***yarn.scheduler.maximum-allocation-mb***

この値は、Resource Manager でのすべてのコンテナー要求に対する最大割り当て (MB) を示します。 指定された値より大きいメモリ要求は有効になりません。 Resource Manager では、*yarn.scheduler.minimum-allocation-mb* を増分単位としてコンテナーにメモリを割り当てることができ、*yarn.scheduler.maximum-allocation-mb* で指定されたサイズを超えることはできません。 指定する値は、*yarn.nodemanager.resource.memory-mb* で指定された、ノード上のすべてのコンテナーに割り当てられるメモリの合計以下にする必要があります。    
D14 v2 のワーカー ノードでの推奨値は、**102400 MB** です。

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3.YARN コンテナー要求あたりの最大仮想コア数の決定**  
構成: ***yarn.scheduler.maximum-allocation-vcores***  

この値は、Resource Manager でのすべてのコンテナー要求に対する仮想 CPU コアの最大数を示します。 この値を超える仮想コア数の要求は有効にはなりません。 これは YARN スケジューラのグローバル プロパティです。 LLAP デーモン コンテナーの場合、この値は使用可能な仮想コアの合計の 75% に設定できます。 残りの 25% は、NodeManager、DataNode、およびワーカー ノードで実行されているその他のサービスのために確保しておく必要があります。  
D14 v2 の VM には 16 個の仮想コアがあり、合計 16 個の仮想コアの 75% を LLAP デーモン コンテナーで使用できます。  
D14 v2 での推奨値は **12** です。  

#### <a name="4-number-of-concurrent-queries"></a>**4.同時クエリの数**  
構成: ***hive.server2.tez.sessions.per.default.queue***

この構成値により、並列で起動できる Tez セッションの数が決まります。 これらの Tez セッションは、"hive.server2.tez.default.queues" で指定されたキューごとに起動されます。 これは Tez AM (クエリ コーディネーター) の数に対応します。 ワーカー ノードの数と同じにすることをお勧めします。 Tez AM の数は、LLAP デーモン ノードの数より大きくなる可能性があります。 Tez AM の主な役割は、クエリの実行を調整し、クエリ プラン フラグメントを対応する LLAP デーモンに割り当てて実行することです。 より高いスループットを実現するには、この値は常に LLAP デーモン ノードの数の倍数にしてください。  

既定の HDInsight クラスターでは、4 つのワーカー ノードで 4 つの LLAP デーモンが実行されるため、推奨値は **4** です。  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5.Tez コンテナーおよび Tez アプリケーション マスターのサイズ**    
構成: ***tez.am.resource.memory.mb、hive.tez.container.size***  

*tez.am.resource.memory.mb* - Tez アプリケーション マスターのサイズを定義します。  
推奨値は **4096 MB** です。
   
*hive.tez.container.size* - Tez コンテナーに割り当てられるメモリ量を定義します。 この値は、YARN 最小コンテナー サイズ (*yarn.scheduler.minimum-allocation-mb*) と YARN 最大コンテナー サイズ (*yarn.scheduler.maximum-allocation-mb*) の間に設定する必要があります。 LLAP デーモンの Executor は、この値を使用して Executor あたりのメモリ使用量を制限します。  
推奨値は **4096 MB** です。  

#### <a name="6-llap-queue-capacity-allocation"></a>**6.LLAP キューの容量割り当て**   
構成: ***yarn.scheduler.capacity.root.llap.capacity***  

この値は、LLAP キューに割り当てる容量の割合を示します。 YARN キューの構成方法によっては、容量割り当ての値がワークロードごとに異なる可能性があります。 ワークロードが読み取り専用操作の場合は、これを容量の 90% のように高く設定しても機能します。 ただし、ワークロードがマネージド テーブルを使用する更新、削除、マージの混合操作である場合は、容量の 80% を LLAP キューに割り当てることをお勧めします。 残りの 20% の容量は、圧縮などのその他のタスクが既定のキューからコンテナーを割り当てるために使用できます。 これにより、既定のキュー内のタスクによって YARN リソースが使われることはなくなります。    
D14 v2 のワーカー ノードでの LLAP キューの推奨値は **80** です。   
(読み取り専用のワークロードの場合は、必要に応じて 90 まで引き上げることができます。)  

#### <a name="7-llap-daemon-container-size"></a>**7.LLAP デーモン コンテナーのサイズ**    
構成: ***hive.llap.daemon.yarn.container.mb***  
   
LLAP デーモンは、各ワーカー ノードで YARN コンテナーとして実行されます。 LLAP デーモン コンテナーの合計メモリ サイズは、次の要因によって決まります。    
*  YARN コンテナー サイズの構成 (yarn.scheduler.minimum-allocation-mb、yarn.scheduler.maximum-allocation-mb、yarn.nodemanager.resource.memory-mb)
*  ノード上の Tez AM の数
*  ノード上のすべてのコンテナーに対して構成された合計メモリと LLAP キューの容量  

Tez アプリケーション マスター (Tez AM) に必要なメモリは、次のように計算できます。  
HDInsight 対話型クラスターの場合、既定では、クエリ コーディネーターとして機能するワーカー ノードごとに Tez AM が 1 つ存在します。 Tez AM の数は、処理される同時実行クエリの数に基づいて構成できます。
Tez AM あたりのメモリは 4 GB にすることをお勧めします。

ノードあたりの Tez AM メモリ = [ ceil(Tez AM の数/LLAP デーモン ノードの数) ] x [ Tez AM コンテナー サイズ]  
D14 v2 の場合、既定の構成の Tez AM の数と LLAP デーモン ノードの数はそれぞれ 4 つです。  
ノードあたりの Tez AM メモリ = (ceil(4/4) x 4 GB) = 4 GB

各ワーカー ノードで LLAP キューに使用できるメモリの合計は、次のように計算できます。この値は、ノード上のすべての YARN コンテナーで使用できるメモリの総量 (*yarn.nodemanager.resource.memory-mb*) および LLAP キューに対して構成されている容量の割合 (*yarn.scheduler.capacity.root.llap.capacity*) によって決まります。  
ワーカー ノード上の LLAP キューの合計メモリ = ノード上のすべての YARN コンテナーで使用できる合計メモリ x LLAP キューに対する容量の割合。  
D14 v2 では、この値が [ 100 GB x 0.80 ] = 80 GB となります。

LLAP デーモン コンテナーのサイズは、次のように計算されます。

**LLAP デーモン コンテナーのサイズ = [ LLAP キューで使用できる合計メモリ] – [ ノードあたりの Tez AM メモリ ]**  
    
D14 v2 ワーカー ノード、HDI 4.0 の場合、推奨値は、(80 GB - 4 GB) = **76 GB**  です。  
(HDI 3.6 の場合は、スライダー AM 用に追加で最大 2 GB を予約する必要があるため、推奨値は **74 GB** となります。)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8.LLAP デーモンあたりの Executor の数の決定**  
構成: ***hive.llap.daemon.num.executors***、***hive.llap.io.threadpool.size***

***hive.llap.daemon.num.executors***:   
この構成は、LLAP デーモンあたりの、タスクを並列で実行できる Executor の数を制御します。 この値は、仮想コアの数、Executor ごとに割り当てられるメモリの量、および LLAP デーモンで使用できるメモリの合計量によって決まります。 通常は、この値をできるだけ仮想コアの数に近づけます。
D14 v2 の VM には仮想コアが 16 個あります。 ただし、使用可能な仮想コアの一部は NodeManager、DataNode、Metrics Monitor などの他のサービスに必要となるため、仮想コアをすべて使用できるわけではありません。 

Executor の数を調整する必要がある場合は、*hive.tez.container.size* の指定に従って Executor あたりのメモリを 4 GB にすることを検討するとともに、すべての Executor に必要なメモリの合計が、LLAP デーモン コンテナーで使用できるメモリの合計を超えないようにすることをお勧めします。  
この値は、そのノードで使用可能な合計仮想コア数の 75% まで構成できます。  
D14 v2 での推奨値は (.75 X 16) = **12** です。

***hive.llap.io.threadpool.size***:   
この値は、Executor のスレッド プール サイズを指定します。 Executor は指定したとおりに固定されるため、これは LLAP デーモンあたりの Executor の数と同じになります。   
D14 v2 での推奨値は **12** です。

#### <a name="9-determining-llap-daemon-cache-size"></a>**9.LLAP デーモンのキャッシュ サイズの決定**  
構成: ***hive.llap.io.memory.size***

LLAP デーモンのコンテナー メモリは、次のコンポーネントで構成されます。
*  ヘッド ルーム
*  Executor (Xmx) が使用するヒープ メモリ
*  デーモンごとのメモリ内キャッシュ (そのオフヒープ メモリのサイズ。SSD キャッシュが有効になっている場合は適用されません)
*  メモリ内キャッシュのメタデータ サイズ (SSD キャッシュが有効になっている場合のみ適用できます)

**ヘッドルーム サイズ**:このサイズは、Java VM のオーバーヘッド (メタスペース、スレッド スタック、gc データ構造など) に使用されるオフヒープ メモリの一部を示します。 一般に、このオーバーヘッドはヒープ サイズ (Xmx) の約 6% です。 さらに安全性を高める場合、この値は LLAP デーモンのメモリ サイズの合計の 6% として計算できます。  
D14 v2 の場合、推奨値は ceil(76 GB x 0.06) ~= **5 GB** です。  

**ヒープ サイズ (Xmx)** :すべての Executor で使用可能なヒープ メモリの量です。
合計ヒープ サイズ = Executor の数 x 4 GB  
D14 v2 の場合、この値は 12 x 4 GB = **48 GB** となります。  

SSD キャッシュが無効になっている場合は、LLAP デーモン コンテナーのサイズからヘッドルーム サイズとヒープ サイズを取り除いたあとに残るメモリの量がメモリ内キャッシュとなります。

SSD キャッシュが有効になっている場合は、キャッシュ サイズの計算が異なります。
*hive.llap.io.allocator.mmap* = true に設定すると、SSD キャッシュが有効になります。
SSD キャッシュを有効にすると、メモリの一部が SSD キャッシュのメタデータの格納に使用されます。 メタデータはメモリに格納されます。それは、SSD キャッシュ サイズの最大 10% になると予想されます。   
SSD キャッシュのメモリ内メタデータ サイズ = [ LLAP デーモンのコンテナー サイズ ] - [ ヘッドルーム + ヒープ サイズ ]  
D14 v2、HDI 4.0 の場合の SSD キャッシュのメモリ内メタデータ サイズ = [ 76 GB ] - [ 5 GB + 48 GB ] = **23 GB**  
D14 v2、HDI 3.6 の場合の SSD キャッシュのメモリ内メタデータ サイズ = [ 76 GB ] - [ 5 GB + 48 GB + 2 GB スライダー ] = **21 GB**

SSD キャッシュのメタデータの格納に使用できるメモリのサイズから、サポートできる SSD キャッシュのサイズを計算できます。  
SSD キャッシュのメモリ内メタデータのサイズ = SSD キャッシュのサイズの 10%       
SSD キャッシュのサイズ = SSD キャッシュのメモリ内メタデータのサイズ x 10  

D14 v2、HDI 4.0 の場合の推奨 SSD キャッシュ サイズ = 23 GB x 10 = **230 GB**  
D14 v2、HDI 3.6 の場合の推奨 SSD キャッシュサイズ = 21 GB x 10 = **210 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10.マップ結合メモリの調整**   
構成: ***hive.auto.convert.join.noconditionaltask.size***

このパラメーターを有効にするには、*hive.auto.convert.join.noconditionaltask* が有効になっていることを確認してください。
この構成により、ユーザーはマップ結合を行うためにメモリに格納できるテーブルのサイズを指定できます。 N 方向結合の n-1 テーブルまたはパーティションのサイズの合計が、構成されている値よりも小さい場合は、マップ結合が選択されます。 マップ結合に自動変換するためのしきい値を計算するには、LLAP Executor のメモリ サイズを使用する必要があります。
各 Executor には 4 GB のヒープ サイズがあると想定されますが、そのすべてをマップ結合に使用できるわけではありません。 ヒープ メモリの一部は、他の操作で並べ替えバッファー、シャッフル バッファー、ハッシュ テーブルなどにも使用されます。 そのため、マップ結合に割り当てることができるのは、4 GB のヒープ メモリの 50% です。  
注:場合によっては、ワークロードに合わせてこの値を調整する必要があります。 この値を低く設定しすぎると、自動変換機能が使用されない場合があります。 また、これを大きく設定しすぎると、メモリ不足例外や GC の一時停止が発生して、パフォーマンスが低下する可能性があります。  
D14 v2 で Executor ごとに 4 GB のメモリを使用する場合は、この値を **2048 MB** に設定することをお勧めします。


#### <a name="next-steps"></a>**次の手順**
これらの値を設定しても問題が解決しない場合は、次のいずれかにアクセスしてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。  

* ##### <a name="other-references"></a>**その他の参考資料**
  * [その他の LLAP プロパティの構成](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Hive サーバー ヒープ サイズの構成](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [LLAP でのマップ結合のメモリ サイズ設定](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Tez 実行エンジンのプロパティ](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Hive LLAP の詳細](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)  
