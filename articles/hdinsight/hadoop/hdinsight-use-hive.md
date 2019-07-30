---
title: Apache Hive と HiveQL とは - Azure HDInsight
description: Apache Hive は、Apache Hadoop 用のデータ ウェアハウス システムです。 Transact-SQL に似た HiveQL を使用して、Hive に格納されているデータを照会することができます。 このドキュメントでは、Azure HDInsight で Hive と HiveQL を使用する方法について説明します。
keywords: HiveQL, Hive とは, Hadoop HiveQL, Hive の使い方, Hive について, Hive の概要
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/06/2019
ms.openlocfilehash: ea8f14a7013a937ddd77baf0f50b8dca09cabad6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076327"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Azure HDInsight における Apache Hive と HiveQL

[Apache Hive](https://hive.apache.org/) は、Apache Hadoop 用のデータ ウェアハウス システムです。 Hive を使用すると、データの集計、クエリ、および分析を行うことができます。 Hive クエリは、SQL に似たクエリ言語である HiveQL で記述します。

Hive では、大規模な非構造化データに構造を投影することができます。 構造を定義したら、HiveQL を使用することで、Java や MapReduce の知識がなくてもそのデータを照会できます。

HDInsight には、特定のワークロード用に調整されたいくつかのクラスター タイプがあります。 Hive クエリには、次のクラスター タイプが最も一般的に使用されます。

* __対話型クエリ__:[Low Latency Analytical Processing (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) 機能を提供して対話型クエリの応答時間を向上させる Hadoop クラスター。 詳細については、[HDInsight での Interactive Query の使用](../interactive-query/apache-interactive-query-get-started.md)に関するドキュメントをご覧ください。

* __Hadoop__:バッチ処理ワークロード用に調整された Hadoop クラスター。 詳細については、[HDInsight での Apache Hadoop の使用](../hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するドキュメントを参照してください。

* __Spark__:Apache Spark には、Hive を操作するための機能が組み込まれています。 詳細については、「[HDInsight での Apache Spark の開始](../spark/apache-spark-jupyter-spark-sql.md)」をご覧ください。

* __HBase__:HiveQL を使用して、Apache HBase に格納されているデータを照会できます。 詳細については、「[HDInsight での Apache HBase の開始](../hbase/apache-hbase-tutorial-get-started-linux.md)」をご覧ください。

## <a name="how-to-use-hive"></a>Hive の使用方法

HDInsight で Hive を使用するさまざまな方法を次の表に示します。

| **方法** | **対話型**クエリ | **バッチ** 処理の有無 | 使用元の **クライアントのオペレーティング システム** |
|:--- |:---:|:---:|:--- |:--- |
| [HDInsight Tools for Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ | Linux、Unix、Mac OS X、または Windows |
| [HDInsight Tools for Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Hive ビュー](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |任意 (ブラウザー ベース) |
| [Beeline クライアント](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux、Unix、Mac OS X、または Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux、Unix、Mac OS X、または Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |


## <a name="hiveql-language-reference"></a>HiveQL 言語のリファレンス

HiveQL 言語のリファレンスは、[言語マニュアル](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)で入手できます。

## <a name="hive-and-data-structure"></a>Hive とデータ構造

Hive は、構造化データおよび半構造化データの操作方法を理解します。 たとえば、フィールドが特定の文字で区切られたテキスト ファイルがあるとします。 次の HiveQL ステートメントは、スペースで区切られたデータからテーブルを作成します。

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

また、Hive では、複雑なまたは不規則な構造化データのためのカスタム **シリアライザー/デシリアライザー (SerDe)** もサポートされます。 詳細については、「[HDInsight でのカスタム JSON SerDe の使用方法](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)」というドキュメントを参照してください。

Hive でサポートされるファイル形式の詳細については、[言語マニュアル (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) をご覧ください。

### <a name="hive-internal-tables-vs-external-tables"></a>Hive の内部テーブルと外部テーブル

Hive で作成できるテーブルには、次の 2 種類があります。

* __内部__:データは Hive データ ウェアハウスに格納されます。 データ ウェアハウスは、クラスターの既定のストレージの `/hive/warehouse/` に配置されます。

    次のいずれかの条件に当てはまる場合は、内部テーブルを使用します。

    * データが一時的である。
    * Hive でテーブルとデータのライフサイクルを管理したい。

* __外部__:データはデータ ウェアハウスの外部に格納されます。 データは、クラスターからアクセス可能な任意のストレージに格納できます。

    次のいずれかの条件に当てはまる場合は、外部テーブルを使用します。

    * Hive の外部でもデータが使用される。 たとえば、データ ファイルが別のプロセスによって更新される (ファイルがロックされない)。
    * テーブルを削除した後もデータを基になる場所に保持する必要がある。
    * 既定以外のストレージ アカウントなど、カスタムの場所が必要である。
    * データの形式、場所などが Hive 以外のプログラムによって管理される。

詳細については、[Hive の内部テーブルと外部テーブルの概要](https://blogs.msdn.microsoft.com/cindygross/2013/02/05/hdinsight-hive-internal-and-external-tables-intro/)に関するブログ記事を参照してください。

## <a name="user-defined-functions-udf"></a>ユーザー定義関数 (UDF)

Hive は **ユーザー定義関数 (UDF)** で拡張することもできます。 UDF では、HiveQL で簡単にモデル化されない機能やロジックを実装することができます。 Hive での UDF の使用例については、以下のドキュメントを参照してください。

* [Java ユーザー定義関数と Apache Hive の使用](../hadoop/apache-hadoop-hive-java-udf.md)

* [Python ユーザー定義関数と Apache Hive の使用](../hadoop/python-udf-hdinsight.md)

* [C# ユーザー定義関数と Apache Hive の使用](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight にカスタムの Apache Hive ユーザー定義関数を追加する方法](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [日付/時刻の形式を Apache Hive タイムスタンプに変換する Hive ユーザー定義関数の例](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>サンプル データ

HDInsight の Hive には、`hivesampletable` という名前の内部テーブルがあらかじめ読み込まれています。 HDInsight には、Hive で使用できるサンプル データ セットも用意されています。 これらのデータ セットは、`/example/data` および `/HdiSamples` ディレクトリに格納されています。 これらのディレクトリは、クラスターの既定のストレージ内に存在します。

## <a id="job"></a>Hive クエリの例

次の HiveQL ステートメントは、`/example/data/sample.log` ファイルに列を投影します。

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
    GROUP BY t4;
```

前の例では、HiveQL ステートメントは次のアクションを実行します。


* `DROP TABLE`:テーブルが既に存在する場合は、それを削除します。

* `CREATE EXTERNAL TABLE`:新しい**外部**テーブルを Hive に作成します。 外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に、元の形式で残されます。

* `ROW FORMAT`:データの形式を Hive に伝えます。 ここでは、各ログのフィールドは、スペースで区切られています。

* `STORED AS TEXTFILE LOCATION`:データの格納先 (`example/data` ディレクトリ) と、データがテキストとして格納されていることを Hive に伝えます。 データは 1 つのファイルに格納することも、ディレクトリ内の複数のファイルに分散することもできます。

* `SELECT`:列 **t4** に値 **[ERROR]** が含まれているすべての行の数を選択します。 この値を含む行が 3 行あるため、このステートメントでは値 **3** が返されます。

* `INPUT__FILE__NAME LIKE '%.log'` - Hive は、ディレクトリ内のすべてのファイルにスキーマの適用を試みます。 このケースでは、ディレクトリにスキーマに一致しないファイルが含まれています。 結果にガベージ データが含まれないように、このステートメントを使用して、.log で終わるファイルのデータのみを返す必要があることを Hive に指示します。

> [!NOTE]  
> 基になるデータが外部ソースによって更新されると考えられる場合は、外部テーブルを使用する必要があります。 たとえば、データの自動アップロード処理や MapReduce 操作の場合です。
>
> 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

外部テーブルではなく**内部**テーブルを作成するには、次の HiveQL を使用します。

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

これらのステートメントは次のアクションを実行します。

* `CREATE TABLE IF NOT EXISTS`:テーブルが存在しない場合は作成します。 **EXTERNAL** キーワードが使用されていないため、このステートメントは内部テーブルを作成します。 このテーブルは Hive データ ウェアハウスに格納され、完全に Hive によって管理されます。

* `STORED AS ORC`:Optimized Row Columnar (ORC) 形式でデータを格納します。 ORC は、Hive データを格納するための高度に最適化された効率的な形式です。

* `INSERT OVERWRITE ... SELECT`: **[ERROR]** を含む **log4jLogs** テーブルの行を選択し、**errorLogs** テーブルにそのデータを挿入します。

> [!NOTE]  
> 外部テーブルとは異なり、内部デーブルを削除すると、基盤となるデータも削除されます。

## <a name="improve-hive-query-performance"></a>Hive クエリ パフォーマンスの向上

### <a id="usetez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) は、Hive などの大量のデータを扱うアプリケーションを同じ規模で遥かに効率的に実行可能にするフレームワークです。 Tez は、既定で有効になっています。  [「Apache Hive on Tez」設計ドキュメント](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)には、実装の選択肢および構成の調整に関する詳細が記載されています。

### <a name="low-latency-analytical-processing-llap"></a>Low Latency Analytical Processing (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (Live Long and Process と呼ばれることもあります) は、クエリのメモリ内キャッシュを使用可能にする Hive 2.0 の新機能です。 LLAP により、Hive クエリは[特定のケースで Hive 1.x と比べて最大 26 倍高速化](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)されます。

HDInsight は、Interactive Query クラスター タイプの LLAP を提供します。 詳細については、[Interactive Query の使用](../interactive-query/apache-interactive-query-get-started.md)に関するドキュメントをご覧ください。

## <a name="scheduling-hive-queries"></a>Hive クエリのスケジュール設定

いくつかのサービスを使用して、スケジュール設定されたワークフローまたはオンデマンドのワークフローの一部として Hive クエリを実行できます。

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory では、Data Factory パイプラインの一部として HDInsight を使用できます。 パイプラインから Hive を使用する方法の詳細については、[Azure Data Factory での Hive アクティビティを使用したデータ変換](../../data-factory/transform-data-using-hadoop-hive.md)に関するドキュメントをご覧ください。

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive ジョブと SQL Server Integration Services

SQL Server Integration Services (SSIS) を使用して Hive ジョブを実行することができます。 Azure Feature Pack for SSIS には、HDInsight の Hive ジョブと連動する次のコンポーネントがあります。

* [Azure HDInsight Hive タスク](https://docs.microsoft.com/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Azure サブスクリプション接続マネージャー](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-subscription-connection-manager)

詳細については、[Azure Feature Pack](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis) のドキュメントをご覧ください。

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie は Hadoop ジョブを管理するワークフローおよび調整システムです。 Hive で Apache Oozie を使用する方法の詳細については、[Oozie を使用したワークフローの定義および実行](../hdinsight-use-oozie-linux-mac.md)に関するドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順

これで、Hive と、HDInsight での Hadoop との使用方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [HDInsight へのデータのアップロード](../hdinsight-upload-data.md)
* [HDInsight 上の Apache Hive と Apache Pig で Python ユーザー定義関数 (UDF) を使用する](./python-udf-hdinsight.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)
