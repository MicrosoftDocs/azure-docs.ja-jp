---
title: Apache Ambari を使用したクラスター構成の最適化 - Azure HDInsight
description: Apache Ambari Web UI を使用して、HDInsight クラスターを構成および最適化します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: f0db36fa380d0d1bb7f2b581c4bf8fa1abfaadaf
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805382"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Apache Ambari を使用して HDInsight クラスター構成を最適化する

HDInsight は、大規模なデータ処理アプリケーションのための [Apache Hadoop](https://hadoop.apache.org/) クラスターを提供します。 これらの複雑なマルチノード クラスターの管理、監視、最適化は困難な場合があります。 [Apache Ambari](https://ambari.apache.org/) は、HDInsight Linux クラスターを管理および監視するための Web インターフェイスです。  Windows クラスターの場合は、[Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) を使用します。

Ambari Web UI の使用の概要については、[Apache Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)に関するページを参照してください

クラスターの資格情報を使用して Ambari (`https://CLUSTERNAME.azurehdidnsight.net`) にログインします。 初期画面に概要ダッシュボードが表示されます。

![Ambari ダッシュボード](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Ambari Web UI を使用して、ホスト、サービス、アラート、構成、ビューを管理できます。 Ambari を使用して、HDInsight クラスターの作成、サービスのアップグレード、スタックとバージョンの管理、ホストの使用停止または再稼働、クラスターへのサービスの追加を行うことはできません。

## <a name="manage-your-clusters-configuration"></a>クラスターの構成の管理

構成設定は、特定のサービスを調整する際に役立ちます。 サービスの構成設定を変更するには、**[Services]\(サービス\)** サイドバー (左側) でサービスを選択し、サービスの詳細ページで **[Configs]\(構成\)** タブに移動します。

![[Services]\(サービス\) サイドバー](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>NameNode の Java ヒープ サイズを変更する

NameNode の Java ヒープ サイズは、クラスターの負荷、ファイル数、ブロック数などの多くの要素によって決まります。 既定のサイズの 1 GB はほとんどのクラスターに適していますが、一部のワークロードではメモリを増減することが必要な場合があります。 

NameNode の Java ヒープ サイズを変更するには、次の手順を実行します。

1. [Services]\(サービス\) サイドバーで **[HDFS]** を選択し、**[Configs]\(構成\)** タブに移動します。

    ![HDFS の構成](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

1. **[NameNode Java heap size]\(NameNode の Java ヒープ サイズ\)** 設定を見つけます。 **フィルター** テキスト ボックスに特定の設定を入力して検索することもできます。 設定名の横の**ペン** アイコンをクリックします。

    ![[NameNode Java heap size]\(NameNode の Java ヒープ サイズ\)](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

1. テキスト ボックスに新しい値を入力し、**Enter** キーを押して変更を保存します。

    ![NameNode の Java ヒープ サイズの編集](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

1. NameNode の Java ヒープ サイズが 2 GB から 1 GB に変更されます。

    ![編集された NameNode の Java ヒープ サイズ](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. 構成画面の上部にある緑色の **[Save]\(保存\)** ボタンをクリックして変更を保存します。

    ![変更を保存する](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="apache-hive-optimization"></a>Apache Hive の最適化

以下のセクションでは、Apache Hive の全体的なパフォーマンスを最適化するための構成オプションについて説明します。

1. Hive 構成パラメーターを変更するには、[Services]\(サービス\) サイドバーで **[Hive]** を選択します。
1. **[Configs]\(構成\)** タブに移動します。

### <a name="set-the-hive-execution-engine"></a>Hive 実行エンジンを設定する

Hive は、[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) と [Apache TEZ](https://tez.apache.org/) の 2 つの実行エンジンを提供します。 Tez は MapReduce より高速です。 HDInsight Linux クラスターでは、既定の実行エンジンとして Tez を使用します。 実行エンジンを変更するには、次の手順を実行します。

1. Hive の **[Configs]\(構成\)** タブで、フィルター ボックスに「**execution engine**」と入力します。

    ![実行エンジンの検索](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

1. **[Optimization]\(最適化\)** プロパティの既定値は **Tez** です。

    ![[Optimization]\(最適化\) - [Tez]](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>マッパーを調整する

Hadoop では、1 つのファイルを複数のファイルに分割 (*"マップ"*) し、分割されたファイルの並列処理を試みます。 マッパーの数は分割数によって異なります。 次の 2 つの構成パラメーターは、Tez 実行エンジンの分割数を制御します。

* `tez.grouping.min-size`:グループ化された分割のサイズの下限であり、既定値は 16 MB (16,777,216 バイト) です。
* `tez.grouping.max-size`:グループ化された分割のサイズの上限であり、既定値は 1 GB (1,073,741,824 バイト) です。

パフォーマンスの目安として、これらのパラメーターの両方を減らすと待機時間が改善され、増やすとスループットが向上します。

たとえば、128 MB のデータ サイズに対して 4 つのマッパー タスクを設定するには、これらのパラメーターをそれぞれ 32 MB (33,554,432バイト) に設定します。

1. 制限パラメーターを変更するには、Tez サービスの **[Configs]\(構成\)** タブに移動します。 **[General]\(全般\)** パネルを展開し、`tez.grouping.max-size` パラメーターと `tez.grouping.min-size` パラメーターを見つけます。

1. 両方のパラメーターを **33,554,432** バイト(32 MB) に設定します。

    ![Tez のグループ化サイズ](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
これらの変更は、サーバーのすべての Tez ジョブに影響します。 最適な結果を得るために、適切なパラメーター値を選択してください。

### <a name="tune-reducers"></a>レジューサーを調整する

[Apache ORC](https://orc.apache.org/) と [Snappy](https://google.github.io/snappy/) は、どちらも高パフォーマンスを提供します。 ただし、Hive は既定でレジューサーの数が少なすぎるため、ボトルネックが発生する可能性があります。

たとえば、入力データ サイズが 50 GB であるとします。 このデータを ORC 形式にして Snappy で圧縮すると 1 GB になります。 Hive では、(マッパーに入力されたバイト数/`hive.exec.reducers.bytes.per.reducer`) で必要なレジューサーの数を見積もります。

既定の設定を使用した場合、この例は 4 レジューサーになります。

`hive.exec.reducers.bytes.per.reducer` パラメーターは、レジューサーごとに処理されるバイト数を指定します。 既定値は 64 MB です。 この値を調整して並列処理を増やすと、パフォーマンスが向上する場合があります。 また、値を小さくしすぎると、生成されるレジューサーが多すぎ、パフォーマンスに悪影響を及ぼす可能性があります。 このパラメーターは、特定のデータ要件、圧縮設定、その他の環境要因に基づきます。

1. パラメーターを変更するには、Hive の **[Configs]\(構成\)** タブに移動し、[Settings]\(設定\) ページで **[Data per Reducer]\(レジューサーごとのデータ\)** パラメーターを見つけます。

    ![[Data per Reducer]\(レジューサーごとのデータ\)](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
1. **[Edit]\(編集\)** を選択して値を 128 MB (134,217,728 バイト) に変更し、**Enter** キーを押して保存します。

    ![[Data per Reducer]\(レジューサーごとのデータ\) - 編集済み](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    入力サイズが 1,024 MB、レジューサーごとのデータが 128 MB とすると、8 レジューサー (1024/128) になります。

1. **[Data per Reducer]\(レジューサーごとのデータ\)** パラメーターの値が正しくない場合、多数のレジューサーが生成され、クエリのパフォーマンスに悪影響を及ぼす可能性があります。 レジューサーの最大数を制限するには、`hive.exec.reducers.max` を適切な値に設定します。 既定値は 1009 です。

### <a name="enable-parallel-execution"></a>並列実行を有効にする

Hive クエリは、1 つ以上のステージで実行されます。 独立したステージを並列実行できれば、クエリのパフォーマンスが向上します。

1.  クエリの並列実行を有効にするには、Hive の **[Configs]\(構成\)** タブに移動し、`hive.exec.parallel` プロパティを検索します。 既定値は false です。 値を true に変更し、**Enter** キーを押して値を保存します。
 
1.  並列実行するジョブの数を制限するには、`hive.exec.parallel.thread.number` プロパティを変更します。 既定値は 8 です。

    ![hive.exec.parallel](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>ベクター化を有効にする

Hive では行単位でデータを処理します。 ベクター化では、1 行ずつではなく、1,024 行のブロック単位でデータを処理するよう Hive に指示します。 ベクター化は、ORC ファイル形式にのみ適用されます。

1. ベクター化されたクエリ実行を有効にするには、Hive の **[Configs]\(構成\)** タブに移動し、`hive.vectorized.execution.enabled` パラメーターを検索します。 Hive 0.13.0 以降では、既定値は true です。
 
1. クエリの Reduce 側でベクター化された実行を有効にするには、`hive.vectorized.execution.reduce.enabled` パラメーターを true に設定します。 既定値は false です。

    ![Hive のベクター化された実行](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>コストベースの最適化 (CBO) を有効にする

既定では、Hive は一連のルールに従って、1 つの最適なクエリ実行プランを見つけます。 コストベースの最適化 (CBO) では、複数のクエリ実行プランを評価し、各プランにコストを割り当てて、最もコストがかからないクエリ実行プランを特定します。

CBO を有効にするには、Hive の **[Configs]\(構成\)** タブに移動し、`parameter hive.cbo.enable` を検索して、トグル ボタンを **[On]\(オン\)** に切り替えます。

![CBO の構成](./media/hdinsight-changing-configs-via-ambari/cbo.png)

CBO を有効にすると、次の追加の構成パラメーターによって Hive クエリのパフォーマンスが向上します。

* `hive.compute.query.using.stats`

    true に設定すると、Hive はその metastore に保存されている統計を使用して、`count(*)` のような単純なクエリに応答します。

    ![CBO の統計](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    CBO を有効にすると、列統計が作成されます。 Hive は metastore に保存されている列統計を使用して、クエリを最適化します。 列数が多い場合、各列の列統計のフェッチに時間がかかります。 false に設定すると、metastore からの列統計のフェッチが無効になります。

    ![Hive 統計セットの列統計](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    基本的なパーティション統計 (行数、データ サイズ、ファイル サイズなど) は metastore に保存されています。 true に設定すると、metastore からパーティション統計がフェッチされます。 false の場合、ファイル サイズはファイル システムからフェッチされ、行数は行スキーマからフェッチされます。

    ![Hive 統計セットのパーティション統計](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>中間圧縮を有効にする

マップ タスクによって、レジューサー タスクで使用される中間ファイルが作成されます。 中間圧縮により、中間ファイルのサイズが縮小されます。

通常、Hadoop ジョブでは I/O ボトルネックが発生します。 データを圧縮することで、I/O を高速化し、全体的なネットワーク転送速度を向上させることができます。

使用可能な圧縮の種類は次のとおりです。

| 形式 | ツール | アルゴリズム | ファイル拡張子 | 分割可能かどうか |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .gz | いいえ  |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | はい |
| LZO | Lzop | LZO | .lzo | はい (インデックス付きの場合) |
| Snappy | 該当なし | Snappy | Snappy | いいえ  |

原則として、分割可能な圧縮方法を使用することが重要です。そうしないと、作成されるマッパーがごく少数になります。 入力データがテキストの場合は、`bzip2` が最適なオプションです。 ORC 形式の場合、Snappy が最速の圧縮オプションです。

1. 中間圧縮を有効にするには、Hive の **[Configs]\(構成\)** タブに移動し、`hive.exec.compress.intermediate` パラメーターを true に設定します。 既定値は false です。

    ![hive.exec.compress.intermediate](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > 中間ファイルを圧縮する場合、コーデックに高圧縮の出力がなくても、CPU コストが低い圧縮コーデックを選択します。

1. 中間圧縮コーデックを設定するには、`mapred.map.output.compression.codec` カスタム プロパティを `hive-site.xml` ファイルまたは `mapred-site.xml` ファイルに追加します。

1. カスタム設定を追加するには、次の手順を実行します。

    a. Hive の **[Configs]\(構成\)** タブに移動し、**[Advanced]\(詳細設定\)** タブを選択します。

    b. **[Advanced]\(詳細設定\)** タブで、**[Custom hive-site]\(カスタム hive-site\)** ウィンドウを見つけて展開します。

    c. [Custom hive-site]\(カスタム hive-site\) ウィンドウの下部にある **[Add Property]\(プロパティの追加\)** リンクをクリックします。

    d. [Add Property]\(プロパティの追加\) ウィンドウで、キーとして `mapred.map.output.compression.codec`、値として `org.apache.hadoop.io.compress.SnappyCodec` を入力します。

    e. **[追加]** をクリックします。

    ![Hive のカスタム プロパティ](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    これで、Snappy 圧縮を使用して中間ファイルが圧縮されます。 プロパティが追加されると、[Custom hive-site]\(カスタム hive-site\) ウィンドウに表示されます。

    > [!NOTE]  
    > この手順により、`$HADOOP_HOME/conf/hive-site.xml` ファイルが変更されます。

### <a name="compress-final-output"></a>最終出力を圧縮する

Hive の最終出力を圧縮することもできます。

1. Hive の最終出力を圧縮するには、Hive の **[Configs]\(構成\)** タブに移動し、`hive.exec.compress.output` パラメーターを true に設定します。 既定値は false です。

1. 出力圧縮コーデックを選択するには、前のセクションの手順 3. で説明したように、[Custom hive-site]\(カスタム hive-site\) ウィンドウに `mapred.output.compression.codec` カスタム プロパティを追加します。

    ![Hive のカスタム プロパティ](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>予測実行を有効にする

予測実行では、実行速度の遅いタスク トラッカーを検出してブラックリストに登録するために、一定数の重複タスクを開始し、個々のタスクの結果を最適化することで、全体的なジョブ実行を改善します。

大量の入力を伴う実行時間の長い MapReduce タスクでは、予測実行を有効にしないでください。

* 予測実行を有効にするには、Hive の **[Configs]\(構成\)** タブに移動し、`hive.mapred.reduce.tasks.speculative.execution` パラメーターを true に設定します。 既定値は false です。

    ![hive.mapred.reduce.tasks.speculative.execution](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>動的パーティションを調整する

Hive では、個々のパーティションを事前に定義せずに、レコードをテーブルに挿入するときに動的パーティションを作成できます。 これは強力な機能ですが、多数のパーティションが作成され、パーティションごとに多数のファイルが作成される可能性があります。

1. Hive で動的パーティションを実行するには、`hive.exec.dynamic.partition` パラメーターの値が true (既定値) である必要があります。

1. 動的パーティション モードを *strict* に変更します。 strict モードでは、少なくとも 1 つのパーティションが静的である必要があります。 これにより、WHERE 句にパーティション フィルターがないクエリを防ぐことができます。つまり、*strict* はすべてのパーティションをスキャンするクエリを防ぎます。 Hive の **[Configs]\(構成\)** タブに移動し、`hive.exec.dynamic.partition.mode` を **strict** に設定します。 既定値は **nonstrict** です。
 
1. 作成する動的パーティションの数を制限するには、`hive.exec.max.dynamic.partitions` パラメーターを変更します。 既定値は 5000 です。
 
1. ノードあたりの動的パーティションの総数を制限するには、`hive.exec.max.dynamic.partitions.pernode` を変更します。 既定値は 2000 です。

### <a name="enable-local-mode"></a>ローカル モードを有効にする

ローカル モードでは、Hive は 1 つのマシンまたは場合によっては 1 つのプロセスでジョブのすべてのタスクを実行できます。 入力データが小さく、クエリのタスクを開始するオーバーヘッドがクエリの全体的な実行のかなりの割合を消費する場合、クエリのパフォーマンスが向上します。

ローカル モードを有効にするには、「[中間圧縮を有効にする](#enable-intermediate-compression)」の手順 3. で説明したように、[Custom hive-site]\(カスタム hive-site\) ウィンドウに `hive.exec.mode.local.auto` パラメーターを追加します。

![hive.exec.mode.local.auto](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>単一の MapReduce の MultiGROUP BY を設定する

このプロパティを true に設定すると、共通の group-by キーを使用する MultiGROUP BY クエリによって、単一の MapReduce ジョブが生成されます。  

この動作を有効にするには、「[中間圧縮を有効にする](#enable-intermediate-compression)」の手順 3. で説明したように、[Custom hive-site]\(カスタム hive-site\) ウィンドウに `hive.multigroupby.singlereducer` パラメーターを追加します。

![Hive で単一の MapReduce の MultiGROUP BY を設定](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Hive のその他の最適化

以下のセクションでは、設定可能な Hive 関連のその他の最適化について説明します。

#### <a name="join-optimizations"></a>結合の最適化

Hive の既定の結合の種類は "*シャッフル結合*" です。 Hive では、特別なマッパーが入力を読み取り、中間ファイルに結合キーと値のペアを生成します。 Hadoop は、シャッフル ステージでこれらのペアを並べ替えてマージします。 このシャッフル ステージはコストがかかります。 データに基づいて適切な結合を選択すると、パフォーマンスを大幅に向上させることができます。

| 結合の種類 | タイミング | 方法 | Hive の設定 | 説明 |
| -- | -- | -- | -- | -- |
| シャッフル結合 | <ul><li>既定の選択肢</li><li>常に動作</li></ul> | <ul><li>テーブルの 1 つの一部から読み取る</li><li>結合キーをバケット処理して並べ替える</li><li>各 Reduce にバケットを 1 つ送信する</li><li>結合は Reduce 側で実行される</li></ul> | Hive の重要な設定は不要 | 毎回動作する |
| マップ結合 | <ul><li>1 つのテーブルがメモリに収まる</li></ul> | <ul><li>小さなテーブルをメモリ ハッシュ テーブルに読み込む</li><li>大きなファイルの一部からストリーミングする</li><li>ハッシュ テーブルの各レコードを結合する</li><li>結合はマッパーが単独で実行する</li></ul> | `hive.auto.confvert.join=true` | 非常に高速だが制限がある |
| 並べ替え/マージ/バケット処理 | 両方のテーブルが次の状態の場合: <ul><li>同様に並べ替えられている</li><li>同様にバケット処理されている</li><li>並べ替え/バケット処理が行われた列で結合している</li></ul> | 各プロセス: <ul><li>各テーブルからバケットを読み取る</li><li>最小値を含む行を処理する</li></ul> | `hive.auto.convert.sortmerge.join=true` | 非常に効率的 |

#### <a name="execution-engine-optimizations"></a>実行エンジンの最適化

Hive 実行エンジンの最適化に関するその他の推奨事項を次に示します。

| Setting | 推奨 | HDInsight の既定値 |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = 安全性は高いが低速、false = 高速 | false |
| `tez.am.resource.memory.mb` | ほとんどの場合、上限は 4 GB | Auto-Tuned |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Apache Pig の最適化

Ambari Web UI から [Apache Pig](https://pig.apache.org/) プロパティを変更して、Pig クエリを調整できます。 Ambari から Pig プロパティを変更すると、`/etc/pig/2.4.2.0-258.0/pig.properties` ファイルの Pig プロパティが直接変更されます。

1. Pig プロパティを変更するには、Pig の **[Configs]\(構成\)** タブに移動し、**[Advanced pig-properties]\(高度な pig-properties\)** ウィンドウを展開します。

1. 変更するプロパティの値を見つけ、コメント解除して変更します。

1. ウィンドウの右上の **[Save]\(保存\)** をクリックして新しい値を保存します。 一部のプロパティでは、サービスの再起動が必要な場合があります。

    ![[Advanced pig-properties]\(高度な pig-properties\)](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]  
> セッション レベルの設定によって、`pig.properties` ファイルのプロパティ値がオーバーライドされます。

### <a name="tune-execution-engine"></a>実行エンジンを調整する

Pig スクリプトを実行するために、MapReduce と Tez の 2 つの実行エンジンを使用できます。 Tez は最適化されたエンジンであり、MapReduce よりもはるかに高速です。

1. 実行エンジンを変更するには、**[Advanced pig-properties]\(高度な pig-properties\)** ウィンドウで、`exectype` プロパティを見つけます。

1. 既定値は **MapReduce** です。 これを **Tez** に変更します。


### <a name="enable-local-mode"></a>ローカル モードを有効にする

Hive と同様に、データ量が比較的少ないジョブを高速化するにはローカル モードを使用します。

1. ローカル モードを有効にするには、`pig.auto.local.enabled` を **true** に設定します。 既定値は false です。

1. 入力データ サイズが `pig.auto.local.input.maxbytes` プロパティ値よりも小さいジョブは、小さなジョブと見なされます。 既定値は 1 GB です。


### <a name="copy-user-jar-cache"></a>ユーザー JAR キャッシュをコピーする

Pig では、UDF に必要な JAR ファイルを分散キャッシュにコピーして、タスク ノードで使用できるようにします。 これらの JAR は、頻繁に変更されることはありません。 有効にした場合、`pig.user.cache.enabled` 設定を使用して、JAR をキャッシュに配置し、同じユーザーが実行するジョブで再利用できます。 これにより、ジョブのパフォーマンスが少し向上します。

1. 有効にするには、`pig.user.cache.enabled` を true に設定します。 既定値は false です。

1. キャッシュされた JAR のベース パスを設定するには、`pig.user.cache.location` をベース パスに設定します。 既定では、 `/tmp`です。


### <a name="optimize-performance-with-memory-settings"></a>メモリ設定を使用してパフォーマンスを最適化する

次のメモリ設定は、Pig スクリプトのパフォーマンスの最適化に役立ちます。

* `pig.cachedbag.memusage`:バッグに割り当てられたメモリの量。 バッグとはタプルのコレクションです。 タプルとはフィールドの順序付けされたセットであり、フィールドとはデータの一部です。 バッグ内のデータが割り当てられたメモリ容量を超えた場合はディスクに書き込まれます。 既定値は 0.2 です。これは使用可能なメモリの 20% を表します。 このメモリは、アプリケーションのすべてのバッグで共有されます。

* `pig.spill.size.threshold`:この書き込みサイズのしきい値 (バイト単位) より大きいバッグがディスクに書き込まれます。 既定値は 5 MB です。


### <a name="compress-temporary-files"></a>一時ファイルを圧縮する

Pig では、ジョブの実行中に一時ファイルが生成されます。 一時ファイルを圧縮すると、ファイルの読み取り時やディスクへの書き込み時にパフォーマンスが向上します。 一時ファイルを圧縮するには、次の設定を使用します。

* `pig.tmpfilecompression`:true の場合は、一時ファイルの圧縮が有効になります。 既定値は false です。

* `pig.tmpfilecompression.codec`:一時ファイルの圧縮に使用する圧縮コーデック。 CPU 使用率を削減するために推奨される圧縮コーデックは [LZO](https://www.oberhumer.com/opensource/lzo/) と Snappy です。

### <a name="enable-split-combining"></a>分割結合を有効にする

有効にすると、マップ タスクを減らすために小さなファイルが結合されます。 これにより、多数の小さなファイルを使用するジョブの効率が向上します。 有効にするには、`pig.noSplitCombination` を true に設定します。 既定値は false です。


### <a name="tune-mappers"></a>マッパーを調整する

マッパーの数を制御するには、`pig.maxCombinedSplitSize` プロパティを変更します。 このプロパティは、1 つのマップ タスクで処理されるデータのサイズを指定します。 既定値は、ファイル システムの既定のブロック サイズです。 この値を増やすと、マッパー タスクの数が減少します。


### <a name="tune-reducers"></a>レジューサーを調整する

レジューサーの数は、`pig.exec.reducers.bytes.per.reducer` パラメーターに基づいて計算されます。 このパラメーターは、レジューサーごとに処理されるバイト数を指定します。既定値は 1 GB です。 レジューサーの最大数を制限するには、`pig.exec.reducers.max` プロパティを設定します。既定値は 999 です。


## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Ambari Web UI を使用した Apache HBase の最適化

[Apache HBase](https://hbase.apache.org/) の構成は、**[HBase Configs] (HBase の構成)** タブから変更されます。以下のセクションでは、HBase のパフォーマンスに影響する重要な構成設定について説明します。

### <a name="set-hbaseheapsize"></a>HBASE_HEAPSIZE を設定する

HBase のヒープ サイズは、"*リージョン*" サーバーと "*マスター*" サーバーが使用するヒープの最大量 (メガバイト単位) を示します。 既定値は 1,000 MB です。 クラスター ワークロードに合わせて、これを調整する必要があります。

1. 変更するには、HBase の **[Configs]\(構成\)** タブの **[Advanced HBase-env]\(高度な HBase-env\)** ウィンドウに移動し、`HBASE_HEAPSIZE` 設定を見つけます。

1. 既定値を 5,000 MB に変更します。

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>読み取り負荷の高いワークロードを最適化する

読み取り負荷の高いワークロードのパフォーマンスを向上させるには、次の構成が重要です。

#### <a name="block-cache-size"></a>ブロック キャッシュ サイズ

ブロック キャッシュは読み取りキャッシュです。 そのサイズは、`hfile.block.cache.size` パラメーターで制御されます。 既定値は 0.4 です。これは、リージョン サーバーのメモリ合計の 40% を表します。 ブロック キャッシュ サイズが大きいほど、ランダム読み取りが高速化されます。

1. このパラメーターを変更するには、HBase の **[Configs]\(構成\)** タブの **[Settings]\(設定\)** タブに移動し、**[% of RegionServer Allocated to Read Buffers]\(読み取りバッファーに割り当てられた RegionServer の割合 (%)\)** を見つけます。

    ![HBase のブロック キャッシュ サイズ](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
1. 値を変更するには、**[Edit]\(編集\)** アイコンを選択します。


#### <a name="memstore-size"></a>Memstore のサイズ

すべての編集内容は、*Memstore* と呼ばれるメモリ バッファーに保存されます。 これにより、1 つの操作でディスクに書き込むことができるデータの総量が増加し、最近の編集内容への以降のアクセスが高速化されます。 Memstore のサイズは、次の 2 つのパラメーターで定義します。

* `hbase.regionserver.global.memstore.UpperLimit`:結合された Memstore が使用できるリージョン サーバーの最大パーセンテージを定義します。

* `hbase.regionserver.global.memstore.LowerLimit`:結合された Memstore が使用できるリージョン サーバーの最小パーセンテージを定義します。

ランダム読み取りに最適化するには、Memstore の上限と下限を減らします。


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>ディスクからのスキャン時にフェッチされる行数

`hbase.client.scanner.caching` 設定では、スキャナーで `next` メソッドが呼び出されたときにディスクから読み取る行数を定義します。  既定値は 100 です。 この数が多いほど、クライアントからリージョン サーバーに対して行われるリモート呼び出しが減り、スキャンが高速化されます。 ただし、クライアントのメモリ負荷も増加します。

![HBase のフェッチされる行数](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> スキャナーでの次のメソッドの呼び出しまでの時間がスキャナーのタイムアウトよりも長くなるような値を設定しないでください。 スキャナーのタイムアウト期間は、`hbase.regionserver.lease.period` プロパティで定義します。


### <a name="optimize-write-heavy-workloads"></a>書き込み負荷の高いワークロードを最適化する

書き込み負荷の高いワークロードのパフォーマンスを向上させるには、次の構成が重要です。


#### <a name="maximum-region-file-size"></a>リージョンの最大ファイル サイズ

HBase では、*HFile* と呼ばれる内部ファイル形式でデータを保存します。 `hbase.hregion.max.filesize` プロパティは、リージョンの 1 つの HFile のサイズを定義します。  リージョンのすべての HFile の合計がこの設定より大きい場合、リージョンが 2 つのリージョンに分割されます。
 
![hbase.hregion.max.filesize](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

リージョンのファイル サイズが大きいほど、分割数が少なくなります。 ファイル サイズを増やして、書き込みパフォーマンスが最大になる値を指定できます。


#### <a name="avoid-update-blocking"></a>更新がブロックされないようにする

* Memstore が `hbase.hregion.memstore.flush.size` プロパティで定義されたサイズに達すると、ディスクにフラッシュされます。 既定のサイズは 128 MB です。

* Hbase のリージョンのブロック乗数は、`hbase.hregion.memstore.block.multiplier` で定義します。 既定値は 4 ですが、 最大許容値は 8 です。

* Memstore が (`hbase.hregion.memstore.flush.size`  *  `hbase.hregion.memstore.block.multiplier`) バイトになると、HBase は更新をブロックします。

    フラッシュ サイズとブロック乗数の既定値を使用した場合、Memstore のサイズが 128 * 4 = 512 MB になると更新がブロックされます。 更新のブロックの数を減らすには、`hbase.hregion.memstore.block.multiplier` の値を増やします。

![HBase のリージョンのブロック乗数](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Memstore のサイズを定義する

Memstore のサイズは、`hbase.regionserver.global.memstore.UpperLimit` パラメーターと `hbase.regionserver.global.memstore.LowerLimit` パラメーターで定義します。 これらを相互に等しい値に設定すると、書き込み中の一時停止が減り (フラッシュの頻度も増えるため)、書き込みパフォーマンスが向上します。


### <a name="set-memstore-local-allocation-buffer"></a>Memstore のローカル割り当てバッファーを設定する

Memstore のローカル割り当てバッファーの使用は、`hbase.hregion.memstore.mslab.enabled` プロパティで指定します。 有効 (true) にすると、負荷の高い書き込み操作中にヒープの断片化を防ぐことができます。 既定値は true です。
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>次の手順

* [Apache Ambari Web UI を使用して HDInsight クラスターを管理する](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
