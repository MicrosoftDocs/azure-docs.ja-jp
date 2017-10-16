---
title: "Apache Hive と HiveQL とは - Azure HDInsight | Microsoft Docs"
description: "Apache Hive は、Hadoop 用のデータ ウェアハウス システムです。 Transact-SQL に似た HiveQL を使用して、Hive に格納されているデータを照会することができます。 このドキュメントでは、Azure HDInsight で Hive と HiveQL を使用する方法について説明します。"
keywords: "HiveQL, Hive とは, Hadoop HiveQL, Hive の使い方, Hive について, Hive の概要"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/03/2017
ms.author: larryfr
ms.openlocfilehash: 90422088897fd5aa10ebd6e76910c74ad5718b07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Azure HDInsight における Apache Hive と HiveQL

[Apache Hive](http://hive.apache.org/) は、Hadoop 用のデータ ウェアハウス システムです。 Hive を使用すると、データの集計、クエリ、および分析を行うことができます。 Hive クエリは、SQL に似たクエリ言語である HiveQL で記述します。

Hive では、大規模な非構造化データに構造を投影することができます。 構造を定義したら、HiveQL を使用することで、Java や MapReduce の知識がなくてもそのデータを照会できます。

HDInsight には、特定のワークロード用に調整されたいくつかのクラスター タイプがあります。 Hive クエリには、次のクラスター タイプが最も一般的に使用されます。

* __Interactive Query__: [Low Latency Analytical Processing (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) 機能を提供して対話型クエリの応答時間を向上させる Hadoop クラスター。 詳細については、[HDInsight での Interactive Query の使用](hdinsight-hadoop-use-interactive-hive.md)に関するドキュメントをご覧ください。

* __Hadoop__: バッチ処理ワークロード用に調整された Hadoop クラスター。 詳細については、[HDInsight での Hadoop の使用](hdinsight-hadoop-linux-tutorial-get-started.md)に関するドキュメントを参照してください。

* __Spark__: Apache Spark には、Hive を操作するための機能が組み込まれています。 詳細については、[HDInsight での Spark の使用](hdinsight-apache-spark-jupyter-spark-sql.md)に関するドキュメントを参照してください。

* __HBase__: HiveQL を使用して、HBase に格納されているデータを照会できます。 詳細については、[HDInsight での HBase の使用](hdinsight-hbase-tutorial-get-started-linux.md)に関するドキュメントを参照してください。

## <a name="how-to-use-hive"></a>Hive の使用方法

次の表に基づいて、HDInsight で Hive を使用する方法を参照してください。

| **方法** | **対話型** シェルの有無 | **バッチ** 処理の有無 | 使用する **クラスターのオペレーティング システム** | 使用元の **クライアントのオペレーティング システム** |
|:--- |:---:|:---:|:--- |:--- |
| [Hive ビュー](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |任意 (ブラウザー ベース) |
| [Beeline クライアント](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux、Unix、Mac OS X、または Windows |
| [REST API](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux または Windows* |Linux、Unix、Mac OS X、または Windows |
| [HDInsight Tools for Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux または Windows* |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux または Windows* |Windows |

> [!IMPORTANT]
> \* Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。
>
> Windows ベースの HDInsight クラスターを使用している場合は、ブラウザーから[クエリ コンソール](hdinsight-hadoop-use-hive-query-console.md)を使用するか、[リモート デスクトップ](hdinsight-hadoop-use-hive-remote-desktop.md)を使用して、Hive クエリを実行できます。

## <a name="hiveql-language-reference"></a>HiveQL 言語のリファレンス

HiveQL 言語リファレンスは、[言語マニュアル (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) から入手できます。

## <a name="hive-and-data-structure"></a>Hive とデータ構造

Hive は、構造化データおよび半構造化データの操作方法を理解します。 たとえば、フィールドが特定の文字で区切られたテキスト ファイルがあるとします。 次の HiveQL ステートメントは、スペースで区切られたデータからテーブルを作成します。

```hiveql
CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

また、Hive では、複雑なまたは不規則な構造化データのためのカスタム **シリアライザー/デシリアライザー (SerDe)** もサポートされます。 詳細については、「[HDInsight でのカスタム JSON SerDe の使用方法](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx)」というドキュメントを参照してください。

Hive でサポートされているファイル形式の詳細については、[言語マニュアル (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) を参照してください。

## <a name="hive-internal-tables-vs-external-tables"></a>Hive の内部テーブルと外部テーブル

Hive で作成できるテーブルには、次の 2 種類があります。

* __内部__: データは Hive データ ウェアハウスに格納されます。 データ ウェアハウスは、クラスターの既定のストレージの `/hive/warehouse/` に配置されます。

    内部テーブルは次の場合に使用します。

    * データが一時的である。
    * Hive でテーブルとデータのライフサイクルを管理したい。

* __外部__: データはデータ ウェアハウスの外部に格納されます。 データは、クラスターからアクセス可能な任意のストレージに格納できます。

    外部テーブルは次の場合に使用します。

    * Hive の外部でもデータが使用される。 たとえば、データ ファイルが別のプロセスによって更新される (ファイルがロックされない)。
    * テーブルを削除した後もデータを基になる場所に保持する必要がある。
    * 既定以外のストレージ アカウントなど、カスタムの場所が必要である。
    * データの形式、場所などが Hive 以外のプログラムによって管理される。

詳細については、ブログ記事「[Hive Internal and External Tables Intro (Hive の内部テーブルと外部テーブルの概要)][cindygross-hive-tables]」を参照してください。

## <a name="user-defined-functions-udf"></a>ユーザー定義関数 (UDF)

Hive は **ユーザー定義関数 (UDF)**で拡張することもできます。 UDF では、HiveQL で簡単にモデル化されない機能やロジックを実装することができます。 Hive での UDF の使用例については、以下のドキュメントを参照してください。

* [Java ユーザー定義関数と Hive の使用](hdinsight-hadoop-hive-java-udf.md)

* [Hive および Pig での Python ユーザー定義関数の使用](hdinsight-python.md)

* [Hive と Pig での C# ユーザー定義関数の使用](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [HDInsight にカスタムの Hive ユーザー定義関数を追加する方法](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [日付/時刻の形式を Hive タイムスタンプに変換する Hive ユーザー定義関数の例](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>サンプル データ

HDInsight の Hive には、`hivesampletable` という名前の内部テーブルがあらかじめ読み込まれています。 HDInsight には、Hive で使用できるサンプル データ セットも用意されています。 これらのデータ セットは、`/example/data` および `/HdiSamples` ディレクトリに格納されています。 これらのディレクトリは、クラスターの既定のストレージ内に存在します。

## <a id="job"></a>Hive クエリの例

次の HiveQL ステートメントは、`/example/data/sample.log` ファイルに列を投影します。

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

前の例では、HiveQL ステートメントは次のアクションを実行します。

* `set hive.execution.engine=tez;`: Tez を使用するように実行エンジンを設定します。 MapReduce ではなく Tez を使用すると、クエリ パフォーマンスを向上させることができます。 Tez の詳細については、「 [パフォーマンスを改善するための Apache Tez の使用方法](#usetez) 」セクションをご覧ください。

    > [!NOTE]
    > このステートメントは、Windows ベースの HDInsight クラスターの使用時にのみ必要です。 Linux ベースの HDInsight クラスターでは、Tez が既定の実行エンジンです。

* `DROP TABLE`: テーブルが既に存在する場合は、それを削除します。

* `CREATE EXTERNAL TABLE`: Hive に新しい**外部**テーブルを作成します。 外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に、元の形式で残されます。

* `ROW FORMAT`: データの形式を Hive に伝えます。 ここでは、各ログのフィールドは、スペースで区切られています。

* `STORED AS TEXTFILE LOCATION`: データの格納先 (`example/data` ディレクトリ) と、データがテキストとして格納されていることを Hive に伝えます。 データは 1 つのファイルに格納することも、ディレクトリ内の複数のファイルに分散することもできます。

* `SELECT`: 列 **t4** に値 **[ERROR]** が含まれているすべての行の数を選択します。 この値を含む行が 3 行あるため、このステートメントでは値 **3** が返されます。

* `INPUT__FILE__NAME LIKE '%.log'` - Hive は、ディレクトリ内のすべてのファイルにスキーマの適用を試みます。 このケースでは、ディレクトリにスキーマに一致しないファイルが含まれています。 結果にガベージ データが含まれないように、このステートメントを使用して、.log で終わるファイルのデータのみを返す必要があることを Hive に指示します。

> [!NOTE]
> 基になるデータが外部ソースによって更新されると考えられる場合は、外部テーブルを使用する必要があります。 たとえば、データの自動アップロード処理や MapReduce 操作の場合です。
>
> 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

外部テーブルではなく**内部**テーブルを作成するには、次の HiveQL を使用します。

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

これらのステートメントは次のアクションを実行します。

* `CREATE TABLE IF NOT EXISTS`: テーブルが存在しない場合は作成します。 **EXTERNAL** キーワードが使用されていないため、このステートメントは内部テーブルを作成します。 このテーブルは Hive データ ウェアハウスに格納され、完全に Hive によって管理されます。

* `STORED AS ORC`: Optimized Row Columnar (ORC) 形式でデータを格納します。 ORC は、Hive データを格納するための高度に最適化された効率的な形式です。

* `INSERT OVERWRITE ... SELECT`: **[ERROR]** を含む **log4jLogs** テーブルの行を選択し、**errorLogs** テーブルにデータを挿入します。

> [!NOTE]
> 外部テーブルとは異なり、内部デーブルを削除すると、基盤となるデータも削除されます。

## <a name="improve-hive-query-performance"></a>Hive クエリ パフォーマンスの向上

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) は、Hive などの大量のデータを扱うアプリケーションを同じ規模で遥かに効率的に実行可能にするフレームワークです。 Linux ベースの HDInsight クラスターでは、Tez は既定で有効になっています。

> [!NOTE]
> Tez は Windows ベースの HDInsight クラスターに対して現在既定でオフになっているため、有効にする必要があります。 Tez を活用するために、Hive クエリに次の値を設定する必要があります。
>
> `set hive.execution.engine=tez;`
>
> Linux ベースの HDInsight クラスターでは、Tez が既定のエンジンです。

[「Hive on Tez」設計ドキュメント](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)には、実装の選択肢および構成の調整に関する詳細が記載されています。

Tez を使用して実行したジョブのデバッグを支援するために、HDInsight には、Tez ジョブの詳細を表示できる次の Web UI が用意されています。

* [HDInsight で Ambari ビューを使用して Tez ジョブをデバッグする](hdinsight-debug-ambari-tez-view.md)

* [Windows ベースの HDInsight で Tez UI を使用して Tez ジョブをデバッグする](hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Low Latency Analytical Processing (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (Live Long and Process と呼ばれることもあります) は、クエリのメモリ内キャッシュを使用可能にする Hive 2.0 の新機能です。 LLAP により、Hive クエリは[特定のケースで Hive 1.x と比べて最大 26 倍高速化](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)されます。

HDInsight は、Interactive Query クラスター タイプの LLAP を提供します。 詳細については、[Interactive Query の使用](hdinsight-hadoop-use-interactive-hive.md)に関するドキュメントをご覧ください。

## <a name="hive-jobs-and-sql-server-integration-services"></a>Hive ジョブと SQL Server Integration Services

SQL Server Integration Services (SSIS) を使用して Hive ジョブを実行することができます。 Azure Feature Pack for SSIS には、HDInsight の Hive ジョブと連動する次のコンポーネントがあります。

* [Azure HDInsight Hive タスク][hivetask]

* [Azure サブスクリプション接続マネージャー][connectionmanager]

Azure Feature Pack for SSIS の詳細については、[こちら][ssispack]を参照してください。

## <a id="nextsteps"></a>次のステップ

これで、Hive と、HDInsight での Hadoop との使用方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [HDInsight へのデータのアップロード][hdinsight-upload-data]
* [HDInsight での Pig の使用][hdinsight-use-pig]
* [HDInsight での MapReduce ジョブの使用][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
