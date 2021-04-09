---
title: クラスター アドバイザーの推奨事項に合わせて最適化する
titleSuffix: Azure HDInsight
description: Azure HDInsight でのクラスター アドバイザーの推奨事項に合わせて Apache HBase を最適化します。
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943020"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Azure HDInsight での Apache HBase のアドバイザリ

この記事では、Azure HDInsight での Apache HBase のパフォーマンスを最適化するために役立ついくつかのアドバイザリについて説明します。 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>最後に書き込まれたデータを読み取るように HBase を最適化する

ユース ケースに、HBase から最後に書き込まれたデータの読み取りが含まれている場合は、このアドバイザリが役立ちます。 ハイ パフォーマンスのためには、HBase の読み取りがリモート ストレージではなく memstore で処理されることが最適です。

このクエリ アドバイザリは、テーブル内の特定の列ファミリの場合、75% を超える読み取りが memstore で処理されることを示しています。 この指標が示唆しているのは、memstore に対するフラッシュが行われたとしても、最近のファイルがアクセスされる必要があり、それがキャッシュ内に存在する必要があるということです。 データは、最近のデータへのシステムのアクセスが行われる memstore に、最初に書き込まれます。 内部 HBase フラッシャー スレッドによって、ある特定のリージョンが 128 M (既定) のサイズに達したことが検出され、フラッシュがトリガーされる可能性があります。 このシナリオは、memstore のサイズが 128 M に近いときに書き込まれた最新のデータにも発生します。 そのため、それらの最近のレコードを後で読み取るときに、memstore からではなくファイルの読み取りが必要になることがあります。 そのため、最近フラッシュされた最近のデータもキャッシュに存在するように最適化することをお勧めします。

キャッシュ内の最近のデータを最適化するには、次の構成設定を考慮してください。

1. `hbase.rs.cacheblocksonwrite` を `true` に設定します。 HDInsight HBase のこの既定の構成は `true` になっています。`false` にリセットされていないことを確認してください。

2. `hbase.hstore.compactionThreshold` の値を大きくして、圧縮が開始されないようにします。 既定値は `3` です。 これは、`10` のように、より大きな値に増やすことができます。

3. 手順 2 に従い、compactionThreshold を設定した場合は、`hbase.hstore.compaction.max` を `100` などの大きい値に変更し、`hbase.hstore.blockingStoreFiles` 構成の値を `300` などの大きい値に増やします。

4. 最新のデータしか読み取る必要がないことが確実である場合は、`hbase.rs.cachecompactedblocksonwrite` 構成を **オン** に設定します。 この構成により、圧縮が行われた場合でもデータをキャッシュに保持するようにシステムに指示します。 この構成は、ファミリ レベルで設定することもできます。 

   HBase シェルで、次のコマンドを実行して `hbase.rs.cachecompactedblocksonwrite` 構成を設定します。
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. テーブル内の特定のファミリに対してブロック キャッシュをオフにすることができます。 最新データの読み取りを行うファミリに対しては、確実に **オン** にします。 既定では、テーブル内のすべてのファミリに対してブロック キャッシュがオンになっています。 あるファミリのブロック キャッシュを無効にしてあり、それを有効にする必要があるときは、hbase シェルから alter コマンドを使用します。

   これらの構成は、常にデータがキャッシュで使用でき、最新のデータが圧縮されないようにするために役立ちます。 ご自身のシナリオで TTL が可能な場合は、date-tiered compaction の使用を検討してください。 詳細については、「[Apache HBase Reference Guide: Date Tiered Compaction](https://hbase.apache.org/book.html#ops.date.tiered)」 (Apache HBase リファレンス ガイド: Date Tiered Compaction) を参照してください。  

## <a name="optimize-the-flush-queue"></a>フラッシュ キューを最適化する

このアドバイザリは、HBase のフラッシュがチューニングを必要としている可能性があることを示しています。 フラッシュ ハンドラーの現在の構成が、フラッシュの速度低下を招くおそれがある書き込みトラフィックを処理できるほど十分強固にはなっていない可能性があります。

リージョン サーバーの UI で、フラッシュ キューが 100 を超えているかどうかを確認します。 このしきい値はフラッシュが低速であることを示しており、`hbase.hstore.flusher.count` の構成を調整しなければならない場合があります。 既定では、この値は 2 です。 最大フラッシャー スレッド数が 6 を超えて増加しないようにします。

また、リージョン数のチューニングについての推奨事項があるか確認してください。 ある場合は、リージョンのチューニングを試して、それがフラッシュの高速化に役立つかどうかを確認することをお勧めします。 そうでない場合は、フラッシャー スレッド数のチューニングが役立つことがあります。

## <a name="region-count-tuning"></a>リージョン数のチューニング

リージョン数のチューニング アドバイザリは、HBase で更新がブロックされていること、およびリージョン数がサポートされている最適なヒープ サイズを超えている可能性があることを示しています。 ヒープ サイズ、memstore のサイズ、リージョン数を調整できます。

シナリオの例:

- リージョン サーバーのヒープ サイズが 10 GB であるとします。 既定では、`hbase.hregion.memstore.flush.size` は `128M` です。 `hbase.regionserver.global.memstore.size` の既定値は `0.4` です。 つまり 10 GB のうち 4 GB が memstore に (グローバルに) 割り当てられます。

- すべてのリージョンに書き込み負荷が均等に分散されていると仮定します。すべてのリージョンが 128 MB まで増加すると仮定して、このセットアップのリージョンの最大数は `32` リージョンになります。 特定のリージョン サーバーが 32 個のリージョンを持つように構成されている場合、システムで更新をブロックしないことをお勧めします。

- これらの設定を適用すると、リージョンの数は 100 になります。 4 GB のグローバル memstore が 100 個のリージョンに分割されます。 これにより、実質的に各リージョンの memstore は 40 MB のみになります。 書き込みが均一である場合、システムで頻繁なフラッシュが行われ、40 MB 未満というオーダーの小さいサイズになります。 多数のフラッシャー スレッドがあると、フラッシュ速度 `hbase.hstore.flusher.count` が増加する可能性があります。

このアドバイザリは、フラッシュ スレッド数のチューニングと共に、サーバーごとのリージョンの数、ヒープ サイズ、グローバルな memstore のサイズの構成を再検討して、更新がブロックされないようにすると有効であることを示しています。

## <a name="compaction-queue-tuning"></a>圧縮キューのチューニング

HBase 圧縮キューが 2000 を超える場合、しかもそれが定期的に発生する場合は、圧縮スレッド数をより大きな値に引き上げることができます。

圧縮するファイルの数が多すぎると、ファイルと Azure ファイル システムとのやりとりのしくみに関連してヒープの使用量が増える可能性があります。 そのため、圧縮はできるだけ短時間で完了させることをお勧めします。 以前のクラスターの場合、調整に関連する圧縮の構成によって、圧縮速度が低下することがあります。

`hbase.hstore.compaction.throughput.lower.bound` と `hbase.hstore.compaction.throughput.higher.bound` の構成を確認します。 50 M と 100 M に既に設定されている場合は、そのままにしておきます。 ただし、それらの設定をもっと小さい値 (以前のクラスターの場合に該当) に構成した場合は、制限をそれぞれ 50 M と 100 M に変更します。

構成は `hbase.regionserver.thread.compaction.small` と `hbase.regionserver.thread.compaction.large` です (既定値はそれぞれ 1 です)。
この構成の最大値が 3 未満になるように上限を設定します。

## <a name="full-table-scan"></a>テーブル全体スキャン

フル テーブル スキャンのアドバイザリは、発行されたスキャンの 75% がテーブルまたはリージョンのフル スキャンであることを示しています。 コードからスキャンを呼び出す方法を再確認してクエリのパフォーマンスを向上させることができます。 次の方法を検討してください。

* 各スキャンに適切な開始行と停止行を設定します。

* **MultiRowRangeFilter** API を使用して、1 回のスキャン呼び出しで異なる範囲のクエリを実行できるようにします。 詳細については、[MultiRowRangeFilter API のドキュメント](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html)を参照してください。

* テーブルまたはリージョンのフル スキャンを必要とする場合は、それらのクエリにキャッシュを使用せずに済む可能性があるかどうかを確認します。これにより、キャッシュを使用する他のクエリで、ホットなブロックが無効にされないようにします。 スキャンでキャッシュが使用されないことを確実にするには、コードの **setCaching(false)** オプションを指定した **scan** API を使用します。 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>次のステップ

[Ambari を使用して Apache HBase を最適化する](../optimize-hbase-ambari.md)
