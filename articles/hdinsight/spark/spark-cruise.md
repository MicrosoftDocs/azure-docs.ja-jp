---
title: Azure HDInsight で SparkCruise を使用して Apache Spark クエリを高速化する
description: SparkCruise 最適化プラットフォームを使用して Apache Spark クエリの効率を向上させる方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f07ea3007d2e72c179abe38cca56178b564bc231
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928284"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>Azure HDInsight での SparkCruise

このドキュメントでは、Apache Spark 計算を自動的に再利用して、クエリの効率を向上させる、Azure HDInsight の機能 *SparkCruise* について説明します。

## <a name="overview"></a>概要

Apache Spark などの分析プラットフォームで実行するクエリは、より小さいサブクエリを含むクエリ プランに分解されます。 これらのサブクエリは、複数のクエリのクエリ プランで繰り返し表示される場合があります。 それらは発生するたびに、再実行されて結果が返されます。 しかし、同じクエリを再実行すると効率が悪くなり、不要な計算コストが発生する可能性があります。

*SparkCruise* は、よく使われる計算を再利用して、クエリ全体の実行時間とデータ転送コストを削減することができるワークロード最適化プラットフォームです。 このプラットフォームでは、"*マテリアライズドビュー*" の概念が使用されています。これは、事前に計算された形式で結果が格納されるクエリです。 クエリ自体が後で再び表示されたときに、結果を最初から再計算するのではなく、これらの結果を再利用できます。

## <a name="setup-and-installation"></a>セットアップとインストール

SparkCruise は、Spark 2.3 または 2.4 を使用するすべての HDInsight 4.0 クラスターで使用できます。 SparkCruise ライブラリ ファイルは、HDInsight クラスターの `/opt/peregrine/` ディレクトリにインストールされます。 正常に機能するために *SparkCruise* には、次の構成プロパティが必要です。これらは既定で設定されています。

* `spark.sql.queryExecutionListeners` は `com.microsoft.peregrine.spark.listeners.PlanLogListener` に設定されています。これにより、クエリ プランのログが有効になります。
* `spark.sql.extensions` は `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` に設定されています。これにより、オンラインでの具体化と再利用に対してオプティマイザーの規則が有効になります。

## <a name="computation-reuse-in-spark-sql"></a>Spark SQL での計算の再利用

次のサンプル シナリオでは、*SparkCruise* を使用して Apache Spark クエリを最適化する方法を示します。 

1. Spark クラスターのヘッド ノードに SSH 接続します。
1. 「`spark-shell`.
1. 次のコード スニペットを実行します。これにより、クラスターでサンプル データを使用していくつかの基本的なクエリが実行されます。

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. *SparkCruise* クエリ分析ツールを使用して、Spark アプリケーション ログに格納されている前のクエリのクエリ プランを分析します。 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. 分析プロセスの出力を表示します。これはフィードバック ファイルです。 このフィードバック ファイルには、将来の Spark SQL クエリの注釈が含まれています。 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`analyze` コマンドは、クエリ プランを解析し、ワークロードの表形式表現を作成します。 このワークロード テーブルのクエリは、[HDInsight SparkCruise Samples](https://github.com/Azure-Samples/azure-sparkcruise-samples) リポジトリに含まれている *WorkloadInsights* ノートブックを使用して実行できます。 次に、`views` コマンドは、よく使用されるサブプラン式を識別し、将来の具体化と再利用のために興味深いサブプラン式を選択します。 出力は、今後の Spark SQL クエリの注釈を含むフィードバック ファイルです。 

`show` コマンドを実行すると、次のテキストのような出力が表示されます。

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

フィードバック ファイルには、エントリが `subplan-identifier [Materialize|Reuse] input/path/to/action` の形式で含まれています。 この例では、2 つの一意のシグネチャがあります。1 つは最初の 2 つの繰り返しクエリを表し、もう 1 つは最後の 2 つのクエリのフィルター述語を表します。 このフィードバック ファイルを使用すると、次のクエリが再送信されたときに、よく使われるサブプランが自動的に具体化されて再利用されるようになります。 

最適化をテストするには、別のサンプル クエリのセットを実行します。

1. 「`spark-shell`.
1. 次のコード スニペットを実行します。

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. もう一度フィードバック ファイルを表示して、再利用されたクエリの署名を確認します。

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`show` コマンドを実行すると、次のテキストのような出力が得られます。

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

クエリ内のリテラル値は `'11%'` から `'12%'` に変更されていますが、*SparkCruise* では、リテラル値やデータセットのバージョンの進化などのわずかなバリエーションを使用して、以前のクエリを新しいクエリに一致させることができます。 クエリに大きな変更が加えられた場合は、分析ツールを再実行して、再利用できる新しいクエリを特定できます。

バックグラウンドで、選択したサブプランを含む最初のクエリからサブプランを具体化するために *SparkCruise* によってサブクエリがトリガーされます。 後のクエリでは、再計算ではなく、具体化されたサブプランを直接読み取ることができます。 このワークロードでは、1 番目と 3 番目のクエリによってサブプランがオンラインで具体化されます。 よく使われるサブプランが具体化された後のクエリのプラン変更を確認できます。

次のクエリで再利用できる新しい具体化された部分式が 4 つあることがわかります。

## <a name="clean-up"></a>クリーンアップ

フィードバック ファイル、具体化されたサブプラン、およびクエリ ログは、Spark セッション間で保持されます。 これらのファイルを削除するには、次のコマンドを実行します。

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>次のステップ

* [ワークロードの分析情報ノートブックを使用して、SparkCruise の利点を確認する](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [Azure HDInsight IO キャッシュを使用して Apache Spark のワークロードのパフォーマンスを改善する](apache-spark-improve-performance-iocache.md)
* [HDInsight で Apache Spark ジョブを最適化する](./apache-spark-perf.md)
* [SparkCruise: Spark でのハンズフリー計算の再利用](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Azure HDInsight での Apache Spark のガイドライン](./spark-best-practices.md)
