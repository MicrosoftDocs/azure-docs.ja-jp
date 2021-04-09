---
title: Azure HDInsight で Apache Ambari を使用して Apache Hive を最適化する
description: Apache Ambari Web UI を使用して、Apache Hive を構成および最適化します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 69a4e769677b6f0200f4157305a3a125f82ee76d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864819"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight で Apache Ambari を使用して Apache Hive を最適化する

Apache Ambari は、HDInsight クラスターを管理および監視するための Web インターフェイスです。 Ambari Web UI の概要については、「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

以下のセクションでは、Apache Hive の全体的なパフォーマンスを最適化するための構成オプションについて説明します。

1. Hive 構成パラメーターを変更するには、[Services]\(サービス\) サイドバーで **[Hive]** を選択します。
1. **[Configs]\(構成\)** タブに移動します。

## <a name="set-the-hive-execution-engine"></a>Hive 実行エンジンを設定する

Hive は、Apache Hadoop MapReduce と Apache TEZ の 2 つの実行エンジンを提供します。 Tez は MapReduce より高速です。 HDInsight Linux クラスターでは、既定の実行エンジンとして Tez を使用します。 実行エンジンを変更するには、次の手順を実行します。

1. Hive の **[Configs]\(構成\)** タブで、フィルター ボックスに「**execution engine**」と入力します。

    :::image type="content" source="./media/optimize-hive-ambari/ambari-search-execution.png" alt-text="Apache Ambari の実行エンジンの検索" border="true":::

1. **[Optimization]\(最適化\)** プロパティの既定値は **Tez** です。

    :::image type="content" source="./media/optimize-hive-ambari/optimization-apache-tez.png" alt-text="最適化 - Apache Tez エンジン" border="true":::

## <a name="tune-mappers"></a>マッパーを調整する

Hadoop では、1 つのファイルを複数のファイルに分割 ( *"マップ"* ) し、分割されたファイルの並列処理を試みます。 マッパーの数は分割数によって異なります。 次の 2 つの構成パラメーターは、Tez 実行エンジンの分割数を制御します。

* `tez.grouping.min-size`:グループ化された分割のサイズの下限であり、既定値は 16 MB (16,777,216 バイト) です。
* `tez.grouping.max-size`:グループ化された分割のサイズの上限であり、既定値は 1 GB (1,073,741,824 バイト) です。

パフォーマンスの目安として、これらのパラメーターの両方を減らすと待機時間が改善され、増やすとスループットが向上します。

たとえば、128 MB のデータ サイズに対して 4 つのマッパー タスクを設定するには、これらのパラメーターをそれぞれ 32 MB (33,554,432バイト) に設定します。

1. 制限パラメーターを変更するには、Tez サービスの **[Configs]\(構成\)** タブに移動します。 **[General]\(全般\)** パネルを展開し、`tez.grouping.max-size` パラメーターと `tez.grouping.min-size` パラメーターを見つけます。

1. 両方のパラメーターを **33,554,432** バイト(32 MB) に設定します。

    :::image type="content" source="./media/optimize-hive-ambari/apache-tez-grouping-size.png" alt-text="Apache Ambari の Tez のグループ化サイズ" border="true":::

これらの変更は、サーバーのすべての Tez ジョブに影響します。 最適な結果を得るために、適切なパラメーター値を選択してください。

## <a name="tune-reducers"></a>レジューサーを調整する

Apache ORC と Snappy は、どちらもハイ パフォーマンスを提供します。 ただし、Hive は既定でレジューサーの数が少なすぎるため、ボトルネックが発生する可能性があります。

たとえば、入力データ サイズが 50 GB であるとします。 このデータを ORC 形式にして Snappy で圧縮すると 1 GB になります。 Hive では、(マッパーに入力されたバイト数/`hive.exec.reducers.bytes.per.reducer`) で必要なレジューサーの数を見積もります。

既定の設定を使用した場合、この例は 4 レジューサーになります。

`hive.exec.reducers.bytes.per.reducer` パラメーターは、レジューサーごとに処理されるバイト数を指定します。 既定値は 64 MB です。 この値を調整して並列処理を増やすと、パフォーマンスが向上する場合があります。 また、値を小さくしすぎると、生成されるレジューサーが多すぎ、パフォーマンスに悪影響を及ぼす可能性があります。 このパラメーターは、特定のデータ要件、圧縮設定、その他の環境要因に基づきます。

1. パラメーターを変更するには、Hive の **[Configs]\(構成\)** タブに移動し、[Settings]\(設定\) ページで **[Data per Reducer]\(レジューサーごとのデータ\)** パラメーターを見つけます。

    :::image type="content" source="./media/optimize-hive-ambari/ambari-data-per-reducer.png" alt-text="Apache Ambari のレジューサーごとのデータ" border="true":::

1. **[Edit]\(編集\)** を選択して値を 128 MB (134,217,728 バイト) に変更し、**Enter** キーを押して保存します。

    :::image type="content" source="./media/optimize-hive-ambari/data-per-reducer-edited.png" alt-text="Ambari のレジューサーごとのデータ - 編集済み" border="true":::
  
    入力サイズが 1,024 MB、レジューサーごとのデータが 128 MB とすると、8 レジューサー (1024/128) になります。

1. **[Data per Reducer]\(レジューサーごとのデータ\)** パラメーターの値が正しくない場合、多数のレジューサーが生成され、クエリのパフォーマンスに悪影響を及ぼす可能性があります。 レジューサーの最大数を制限するには、`hive.exec.reducers.max` を適切な値に設定します。 既定値は 1009 です。

## <a name="enable-parallel-execution"></a>並列実行を有効にする

Hive クエリは、1 つ以上のステージで実行されます。 独立したステージを並列実行できれば、クエリのパフォーマンスが向上します。

1. クエリの並列実行を有効にするには、Hive の **[Configs]\(構成\)** タブに移動し、`hive.exec.parallel` プロパティを検索します。 既定値は false です。 値を true に変更し、**Enter** キーを押して値を保存します。

1. 並列実行するジョブの数を制限するには、`hive.exec.parallel.thread.number` プロパティを変更します。 既定値は 8 です。

    :::image type="content" source="./media/optimize-hive-ambari/apache-hive-exec-parallel.png" alt-text="Apache Hive の実行の並列表示" border="true":::

## <a name="enable-vectorization"></a>ベクター化を有効にする

Hive では行単位でデータを処理します。 ベクター化では、1 行ずつではなく、1,024 行のブロック単位でデータを処理するよう Hive に指示します。 ベクター化は、ORC ファイル形式にのみ適用されます。

1. ベクター化されたクエリ実行を有効にするには、Hive の **[Configs]\(構成\)** タブに移動し、`hive.vectorized.execution.enabled` パラメーターを検索します。 Hive 0.13.0 以降では、既定値は true です。

1. クエリの Reduce 側でベクター化された実行を有効にするには、`hive.vectorized.execution.reduce.enabled` パラメーターを true に設定します。 既定値は false です。

    :::image type="content" source="./media/optimize-hive-ambari/hive-vectorized-execution.png" alt-text="Apache Hive のベクター化された実行" border="true":::

## <a name="enable-cost-based-optimization-cbo"></a>コストベースの最適化 (CBO) を有効にする

既定では、Hive は一連のルールに従って、1 つの最適なクエリ実行プランを見つけます。 コストベースの最適化 (CBO) は、クエリを実行するために複数のプランを評価します。 また、各プランにコストが割り当てられ、クエリの実行にかかるコストが最も低いプランが決定されます。

CBO を有効にするには、 **[Hive]**  >  **[Configs]\(構成\)**  >  **[Settings]\(設定\)** に移動し、 **[Enable Cost Based Optimizer]\(コスト ベースのオプティマイザー を有効にする\)** を探して、トグル ボタンを **[オン]** に切り替えます。

:::image type="content" source="./media/optimize-hive-ambari/hdinsight-cbo-config.png" alt-text="HDInsight のコストベースのオプティマイザー" border="true":::

CBO を有効にすると、次の追加の構成パラメーターによって Hive クエリのパフォーマンスが向上します。

* `hive.compute.query.using.stats`

    true に設定すると、Hive はその metastore に保存されている統計を使用して、`count(*)` のような単純なクエリに応答します。

    :::image type="content" source="./media/optimize-hive-ambari/hive-compute-query-using-stats.png" alt-text="Apache Hive の統計を使用したクエリのコンピューティング" border="true":::

* `hive.stats.fetch.column.stats`

    CBO を有効にすると、列統計が作成されます。 Hive は metastore に保存されている列統計を使用して、クエリを最適化します。 列数が多い場合、各列の列統計のフェッチに時間がかかります。 false に設定すると、metastore からの列統計のフェッチが無効になります。

    :::image type="content" source="./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png" alt-text="Apache Hive の統計セットの列統計" border="true":::

* `hive.stats.fetch.partition.stats`

    基本的なパーティション統計 (行数、データ サイズ、ファイル サイズなど) は metastore に保存されています。 true に設定すると、metastore からパーティション統計がフェッチされます。 false の場合、ファイルのサイズはファイル システムからフェッチされます。 行の数は、行スキーマからフェッチされます。

    :::image type="content" source="./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png" alt-text="Hive 統計セットのパーティション統計" border="true":::

## <a name="enable-intermediate-compression"></a>中間圧縮を有効にする

マップ タスクによって、レジューサー タスクで使用される中間ファイルが作成されます。 中間圧縮により、中間ファイルのサイズが縮小されます。

通常、Hadoop ジョブでは I/O ボトルネックが発生します。 データを圧縮することで、I/O を高速化し、全体的なネットワーク転送速度を向上させることができます。

使用可能な圧縮の種類は次のとおりです。

| Format | ツール | アルゴリズム | ファイル拡張子 | 分割可能かどうか |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | DEFLATE | `.gz` | いいえ |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | はい |
| LZO | `Lzop` | LZO | `.lzo` | はい (インデックス付きの場合) |
| Snappy | 該当なし | Snappy | Snappy | いいえ |

原則として、分割可能な圧縮方法を使用することが重要です。そうしないと、作成されるマッパーが少数になります。 入力データがテキストの場合は、`bzip2` が最適なオプションです。 ORC 形式の場合、Snappy が最速の圧縮オプションです。

1. 中間圧縮を有効にするには、Hive の **[Configs]\(構成\)** タブに移動し、`hive.exec.compress.intermediate` パラメーターを true に設定します。 既定値は false です。

    :::image type="content" source="./media/optimize-hive-ambari/hive-exec-compress-intermediate.png" alt-text="'Hive exec compress intermediate'" border="true":::

    > [!NOTE]  
    > 中間ファイルを圧縮する場合、コーデックに高圧縮の出力がなくても、CPU コストが低い圧縮コーデックを選択します。

1. 中間圧縮コーデックを設定するには、`mapred.map.output.compression.codec` カスタム プロパティを `hive-site.xml` ファイルまたは `mapred-site.xml` ファイルに追加します。

1. カスタム設定を追加するには、次の手順を実行します。

    a. **[Hive]**  >  **[Configs]\(構成\)**  >  **[Advanced]\(詳細\)**  >  **[Custom hive-site]\(カスタム hive-site\)** に移動します。

    b. [Custom hive-site]\(カスタム hive-site\) ウィンドウの下部にある **[Add Property...]\(プロパティの追加...\)** を選択します。

    c. [Add Property]\(プロパティの追加\) ウィンドウで、キーとして `mapred.map.output.compression.codec`、値として `org.apache.hadoop.io.compress.SnappyCodec` を入力します。

    d. **[追加]** を選択します。

    :::image type="content" source="./media/optimize-hive-ambari/hive-custom-property.png" alt-text="'Apache Hive のカスタム プロパティの追加'" border="true":::

    この設定により、Snappy 圧縮を使用して中間ファイルが圧縮されます。 プロパティが追加されると、[Custom hive-site]\(カスタム hive-site\) ウィンドウに表示されます。

    > [!NOTE]  
    > この手順により、`$HADOOP_HOME/conf/hive-site.xml` ファイルが変更されます。

## <a name="compress-final-output"></a>最終出力を圧縮する

Hive の最終出力を圧縮することもできます。

1. Hive の最終出力を圧縮するには、Hive の **[Configs]\(構成\)** タブに移動し、`hive.exec.compress.output` パラメーターを true に設定します。 既定値は false です。

1. 出力圧縮コーデックを選択するには、前のセクションの手順 3. で説明したように、[Custom hive-site]\(カスタム hive-site\) ウィンドウに `mapred.output.compression.codec` カスタム プロパティを追加します。

    :::image type="content" source="./media/optimize-hive-ambari/hive-custom-property2.png" alt-text="Apache Hive のカスタム プロパティの追加 2" border="true":::

## <a name="enable-speculative-execution"></a>予測実行を有効にする

予測実行では、実行速度の遅いタスクトラッカーを検出して拒否一覧に登録するために、特定の数の重複タスクが起動されます。 個々のタスクの結果を最適化することで、全体的なジョブの実行を改善します。

大量の入力を伴う実行時間の長い MapReduce タスクでは、予測実行を有効にしないでください。

* 予測実行を有効にするには、Hive の **[Configs]\(構成\)** タブに移動し、`hive.mapred.reduce.tasks.speculative.execution` パラメーターを true に設定します。 既定値は false です。

    :::image type="content" source="./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png" alt-text="`Hive mapred reduce tasks speculative execution`" border="true":::

## <a name="tune-dynamic-partitions"></a>動的パーティションを調整する

Hive では、個々のパーティションを事前に定義せずに、レコードをテーブルに挿入するときに動的パーティションを作成できます。 これは強力な機能です。 ただし、これにより多数のパーティションが作成される可能性があります。 また、各パーティションには多数のファイルが含まれます。

1. Hive で動的パーティションを実行するには、`hive.exec.dynamic.partition` パラメーターの値が true (既定値) である必要があります。

1. 動的パーティション モードを *strict* に変更します。 strict モードでは、少なくとも 1 つのパーティションが静的である必要があります。 この設定により、WHERE 句にパーティション フィルターがないクエリを防ぐことができます。つまり、*strict* はすべてのパーティションをスキャンするクエリを防ぎます。 Hive の **[Configs]\(構成\)** タブに移動し、`hive.exec.dynamic.partition.mode` を **strict** に設定します。 既定値は **nonstrict** です。

1. 作成する動的パーティションの数を制限するには、`hive.exec.max.dynamic.partitions` パラメーターを変更します。 既定値は 5000 です。

1. ノードあたりの動的パーティションの総数を制限するには、`hive.exec.max.dynamic.partitions.pernode` を変更します。 既定値は 2000 です。

## <a name="enable-local-mode"></a>ローカル モードを有効にする

Hive のローカル モードでは、1 台のコンピューター上のジョブのすべてのタスクを実行できます。 または、場合によっては 1 つのプロセスで実行されます。 この設定により、入力データが小さい場合にクエリのパフォーマンスが向上します。 また、クエリのタスクを開始するオーバーヘッドがクエリの全体的な実行のかなりの割合を消費する場合、クエリのパフォーマンスが向上します。

ローカル モードを有効にするには、「[中間圧縮を有効にする](#enable-intermediate-compression)」の手順 3. で説明したように、[Custom hive-site]\(カスタム hive-site\) ウィンドウに `hive.exec.mode.local.auto` パラメーターを追加します。

:::image type="content" source="./media/optimize-hive-ambari/hive-exec-mode-local-auto.png" alt-text="Apache Hive の実行モードのローカル オート" border="true":::

## <a name="set-single-mapreduce-multigroup-by"></a>単一の MapReduce の MultiGROUP BY を設定する

このプロパティを true に設定すると、共通の group-by キーを使用する MultiGROUP BY クエリによって、単一の MapReduce ジョブが生成されます。  

この動作を有効にするには、「[中間圧縮を有効にする](#enable-intermediate-compression)」の手順 3. で説明したように、[Custom hive-site]\(カスタム hive-site\) ウィンドウに `hive.multigroupby.singlereducer` パラメーターを追加します。

:::image type="content" source="./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png" alt-text="Hive で単一の MapReduce の MultiGROUP BY を設定" border="true":::

## <a name="additional-hive-optimizations"></a>Hive のその他の最適化

以下のセクションでは、設定可能な Hive 関連のその他の最適化について説明します。

### <a name="join-optimizations"></a>結合の最適化

Hive の既定の結合の種類は "*シャッフル結合*" です。 Hive では、特別なマッパーが入力を読み取り、中間ファイルに結合キーと値のペアを生成します。 Hadoop は、シャッフル ステージでこれらのペアを並べ替えてマージします。 このシャッフル ステージはコストがかかります。 データに基づいて適切な結合を選択すると、パフォーマンスを大幅に向上させることができます。

| 結合の種類 | タイミング | 方法 | Hive の設定 | 説明 |
| --- | --- | --- | --- | --- |
| シャッフル結合 | <ul><li>既定の選択肢</li><li>常に動作</li></ul> | <ul><li>テーブルの 1 つの一部から読み取る</li><li>結合キーをバケット処理して並べ替える</li><li>各 Reduce にバケットを 1 つ送信する</li><li>結合は Reduce 側で実行される</li></ul> | Hive の重要な設定は不要 | 毎回動作する |
| マップ結合 | <ul><li>1 つのテーブルがメモリに収まる</li></ul> | <ul><li>小さなテーブルをメモリ ハッシュ テーブルに読み込む</li><li>大きなファイルの一部からストリーミングする</li><li>ハッシュ テーブルの各レコードを結合する</li><li>結合はマッパーが単独で実行する</li></ul> | `hive.auto.confvert.join=true` | 高速だが制限がある |
| 並べ替え/マージ/バケット処理 | 両方のテーブルが次の状態の場合: <ul><li>同様に並べ替えられている</li><li>同様にバケット処理されている</li><li>並べ替え/バケット処理が行われた列で結合している</li></ul> | 各プロセス: <ul><li>各テーブルからバケットを読み取る</li><li>最小値を含む行を処理する</li></ul> | `hive.auto.convert.sortmerge.join=true` | 効率性 |

### <a name="execution-engine-optimizations"></a>実行エンジンの最適化

Hive 実行エンジンの最適化に関するその他の推奨事項を次に示します。

| 設定 | 推奨 | HDInsight の既定値 |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | True = 安全性は高いが低速、false = 高速 | false |
| `tez.am.resource.memory.mb` | ほとんどの場合、上限は 4 GB | Auto-Tuned |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="next-steps"></a>次のステップ

* [Apache Ambari Web UI を使用して HDInsight クラスターを管理する](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Azure HDInsight で Apache Hive クエリを最適化する](./hdinsight-hadoop-optimize-hive-query.md)
* [クラスターを最適化する](./optimize-hive-ambari.md)
* [Apache HBase を最適化する](./optimize-hbase-ambari.md)
* [Apache Pig を最適化する](./optimize-pig-ambari.md)
