---
title: Azure HDInsight で Apache Ambari を使用して Apache HBase を最適化する
description: Apache Ambari Web UI を使用して、Apache HBase を構成および最適化します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: 7e54b1347e4c67b99ba87b15c2c15d9d28244ce7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864773"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight で Apache Ambari を使用して Apache HBase を最適化する

Apache Ambari は、HDInsight クラスターを管理および監視するための Web インターフェイスです。 Ambari Web UI の概要については、「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

Apache HBase の構成は、**HBase の [Configs]\(構成\)** タブから変更されます。以下のセクションでは、HBase のパフォーマンスに影響する重要な構成設定について説明します。

## <a name="set-hbase_heapsize"></a>HBASE_HEAPSIZE を設定する

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*マスター*" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

HBase のヒープ サイズは、"*リージョン*" サーバーと "*マスター*" サーバーが使用するヒープの最大量 (メガバイト単位) を示します。 既定値は 1,000 MB です。 クラスター ワークロードに合わせて、この値を調整する必要があります。

1. 変更するには、HBase の **[Configs]\(構成\)** タブの **[Advanced HBase-env]\(高度な HBase-env\)** ウィンドウに移動し、`HBASE_HEAPSIZE` 設定を見つけます。

1. 既定値を 5,000 MB に変更します。

    :::image type="content" source="./media/optimize-hbase-ambari/ambari-hbase-heapsize.png" alt-text="'Apache Ambari の HBase メモリのヒープ サイズ'" border="true":::

## <a name="optimize-read-heavy-workloads"></a>読み取り負荷の高いワークロードを最適化する

読み取り負荷の高いワークロードのパフォーマンスを向上させるには、次の構成が重要です。

### <a name="block-cache-size"></a>ブロック キャッシュ サイズ

ブロック キャッシュは読み取りキャッシュです。 そのサイズは、`hfile.block.cache.size` パラメーターで制御されます。 既定値は 0.4 です。これは、リージョン サーバーのメモリ合計の 40% を表します。 ブロック キャッシュ サイズが大きいほど、ランダム読み取りが高速化されます。

1. このパラメーターを変更するには、HBase の **[Configs]\(構成\)** タブの **[Settings]\(設定\)** タブに移動し、 **[% of RegionServer Allocated to Read Buffers]\(読み取りバッファーに割り当てられた RegionServer の割合 (%)\)** を見つけます。

    :::image type="content" source="./media/optimize-hbase-ambari/hbase-block-cache-size.png" alt-text="Apache HBase のメモリ ブロック キャッシュ サイズ" border="true":::

1. 値を変更するには、 **[Edit]\(編集\)** アイコンを選択します。

### <a name="memstore-size"></a>Memstore のサイズ

すべての編集内容は、*Memstore* と呼ばれるメモリ バッファーに保存されます。 このバッファーにより、1 回の操作でディスクに書き込まれるデータの総量が増加します。 また、最近の編集へのアクセスを高速化します。 Memstore のサイズは、次の 2 つのパラメーターで定義します。

* `hbase.regionserver.global.memstore.UpperLimit`:結合された Memstore が使用できるリージョン サーバーの最大パーセンテージを定義します。

* `hbase.regionserver.global.memstore.LowerLimit`:結合された Memstore が使用できるリージョン サーバーの最小パーセンテージを定義します。

ランダム読み取りに最適化するには、Memstore の上限と下限を減らします。

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>ディスクからのスキャン時にフェッチされる行数

`hbase.client.scanner.caching` 設定では、スキャナーで `next` メソッドが呼び出されたときにディスクから読み取る行数を定義します。  既定値は 100 です。 この数が多いほど、クライアントからリージョン サーバーに対して行われるリモート呼び出しが減り、スキャンが高速化されます。 ただし、この設定により、クライアントのメモリ負荷も増加します。

:::image type="content" source="./media/optimize-hbase-ambari/hbase-num-rows-fetched.png" alt-text="Apache HBase のフェッチされる行数" border="true":::

> [!IMPORTANT]  
> スキャナーでの次のメソッドの呼び出しまでの時間がスキャナーのタイムアウトよりも長くなるような値を設定しないでください。 スキャナーのタイムアウト期間は、`hbase.regionserver.lease.period` プロパティで定義します。

## <a name="optimize-write-heavy-workloads"></a>書き込み負荷の高いワークロードを最適化する

書き込み負荷の高いワークロードのパフォーマンスを向上させるには、次の構成が重要です。

### <a name="maximum-region-file-size"></a>リージョンの最大ファイル サイズ

HBase では、*HFile* と呼ばれる内部ファイル形式でデータを保存します。 `hbase.hregion.max.filesize` プロパティは、リージョンの 1 つの HFile のサイズを定義します。  リージョンのすべての HFile の合計がこの設定より大きい場合、リージョンが 2 つのリージョンに分割されます。

:::image type="content" source="./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png" alt-text="'Apache HBase の HRegion 最大ファイル サイズ'" border="true":::

リージョンのファイル サイズが大きいほど、分割数が少なくなります。 ファイル サイズを増やして、書き込みパフォーマンスが最大になる値を指定できます。

### <a name="avoid-update-blocking"></a>更新がブロックされないようにする

* Memstore が `hbase.hregion.memstore.flush.size` プロパティで定義されたサイズに達すると、ディスクにフラッシュされます。 既定のサイズは 128 MB です。

* HBase リージョンのブロックの乗数は、`hbase.hregion.memstore.block.multiplier` で定義されています。 既定値は 4 ですが、 最大許容値は 8 です。

* Memstore が (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) バイトになると、HBase は更新をブロックします。

    フラッシュ サイズとブロック乗数の既定値を使用した場合、Memstore のサイズが 128 * 4 = 512 MB になると更新がブロックされます。 更新のブロックの数を減らすには、`hbase.hregion.memstore.block.multiplier` の値を増やします。

:::image type="content" source="./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png" alt-text="Apache HBase のリージョンのブロック乗数" border="true":::

## <a name="define-memstore-size"></a>Memstore のサイズを定義する

Memstore のサイズは、`hbase.regionserver.global.memstore.upperLimit` パラメーターと `hbase.regionserver.global.memstore.lowerLimit` パラメーターで定義します。 これらを相互に等しい値に設定すると、書き込み中の一時停止が減り (フラッシュの頻度も増えるため)、書き込みパフォーマンスが向上します。

## <a name="set-memstore-local-allocation-buffer"></a>Memstore のローカル割り当てバッファーを設定する

Memstore のローカル割り当てバッファーの使用は、`hbase.hregion.memstore.mslab.enabled` プロパティで指定します。 有効 (true) にすると、この設定により、負荷の高い書き込み操作中にヒープの断片化を防ぐことができます。 既定値は、true です。

:::image type="content" source="./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png" alt-text="hbase.hregion.memstore.mslab.enabled" border="true":::

## <a name="next-steps"></a>次のステップ

* [Apache Ambari Web UI を使用して HDInsight クラスターを管理する](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [クラスターを最適化する](./hdinsight-changing-configs-via-ambari.md)
* [Apache Hive を最適化する](./optimize-hive-ambari.md)
* [Apache Pig を最適化する](./optimize-pig-ambari.md)
