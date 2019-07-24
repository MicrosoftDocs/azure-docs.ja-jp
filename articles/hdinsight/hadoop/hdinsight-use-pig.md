---
title: Apache Pig の使用 - Azure HDInsight
description: HDInsight 上の Apache Hadoop で Pig を使用する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: 2b0f3a4394267a808b6e6e0a73abfaf1e698ff1c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445034"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>HDInsight 上の Apache Pig で Apache Pig を使用する

HDInsight で [Apache Pig](https://pig.apache.org/) を使用する方法について説明します。

Apache Pig は、*Pig Latin* として知られている手続き型言語を使用して Apache Hadoop 用のプログラムを作成するためのプラットフォームです。 Pig は、 *MapReduce* ソリューションを作成するために Java の代わりに使用され、Azure HDInsight に含まれています。 HDInsight で Pig を使用するさまざまな方法を次の表に示します。

| **方法**  | **対話型** シェルの有無 | **バッチ** 処理の有無 | 使用する **クラスターのオペレーティング システム** | 使用元の **クライアントのオペレーティング システム** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux、Unix、Mac OS X、または Windows |
| [REST API](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux または Windows |Linux、Unix、Mac OS X、または Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux または Windows |Windows (現時点) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux または Windows |Windows |

> [!IMPORTANT]  
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a id="why"></a>Apache Pig を使用する理由

Hadoop での MapReduce を使用したデータ処理における課題の 1 つは、map と reduce 関数のみを使用する処理ロジックの実装です。 複雑な処理では、多くの場合、望ましい結果を得るために、一緒にチェーンされている複数の MapReduce 操作に処理を分割する必要があります。

Pig では、データ フローに使用される一連の変換として処理を定義し、目的の出力を生成することができます。

Pig Latin 言語では、生の入力から 1 つ以上の変換を介して目的の出力を生成するデータ フローを記述できます。 Pig Latin プログラムはこの一般的なパターンに従います。

* **load**:操作対象のデータをファイル システムから読み取ります。

* **transform**:データを操作します。

* **dump または store**:データを画面に出力します。または、処理できるように保存します。

### <a name="user-defined-functions"></a>ユーザー定義関数

Pig Latin ではユーザー定義関数 (UDF) もサポートされています。これを使用して、Pig Latin でのモデル化が困難なロジックを実装する外部コンポーネントを呼び出すことができます。

Pig Latin の詳細については、「[Pig Latin Reference Manual 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html)」および「[Pig Latin Reference Manual 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html)」を参照してください。

Pig での UDF の使用例については、以下のドキュメントを参照してください。

* [HDInsight における Apache DataFu と Apache Pig の使用](apache-hadoop-use-pig-datafu-udf.md) - DataFu は Apache によって管理される便利な UDF のコレクションです。
* [HDInsight 上の Apache Pig と Apache Hive で Python を使用する](python-udf-hdinsight.md)
* [HDInsight 上の Apache Hive と Apache Pig で C# を使用する](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>サンプル データ

HDInsight にはさまざまなサンプル データ セットが用意されていて、`/example/data` および `/HdiSamples` ディレクトリに格納されています。 これらのディレクトリは、クラスターの既定のストレージにあります。 このドキュメントでの Pig の例では、`/example/data/sample.log` の *log4j* ファイルを使用します。

ファイル内の各ログは、タイプと重要度を表す `[LOG LEVEL]` フィールドを含むフィールド行で構成されています。以下に例を示します。

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

前の例では、ログ レベルは ERROR です。

> [!NOTE]  
> また、 [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) ログ ツールを使用して log4j ファイルを生成し、そのファイルを BLOB にアップロードすることもできます。 手順については、「[データを HDInsight にアップロードする方法](../hdinsight-upload-data.md)」を参照してください。 HDInsight と共に Azure ストレージの BLOB を使用する方法の詳細については、「[HDInsight での Azure BLOB ストレージの使用](../hdinsight-hadoop-use-blob-storage.md)」を参照してください。

## <a id="job"></a>サンプル ジョブ

次の Pig Latin ジョブでは、HDInsight クラスターの既定のストレージから `sample.log` ファイルを読み込みます。 次に、一連の変換を実行します。その結果、入力データの各ログ レベルの出現回数がカウントされます。 結果は STDOUT に書き込まれます。

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

次の図は、データへの各変換の要約を示しています。

![変換のグラフィカル表示][image-hdi-pig-data-transformation]

## <a id="run"></a>Pig Latin ジョブを実行する

HDInsight では、さまざまな方法を使用して Pig Latin ジョブを実行できます。 次の表を使用して、適切な方法を判別してから、該当するチュートリアルのリンクをクリックしてください。

| **方法**  | **対話型** シェルの有無 | **バッチ** 処理の有無 | 使用する **クラスターのオペレーティング システム** | 使用元の**クライアント** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux、Unix、Mac OS X、または Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux または Windows |Linux、Unix、Mac OS X、または Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux または Windows |Windows (現時点) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux または Windows |Windows |

> [!IMPORTANT]  
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a name="pig-and-sql-server-integration-services"></a>Pig および SQL Server Integration Services

SQL Server Integration Services (SSIS) を使用して Pig ジョブを実行することができます。 Azure Feature Pack for SSIS には、HDInsight の Pig ジョブと連動する次のコンポーネントがあります。

* [Azure HDInsight Pig タスク][pigtask]

* [Azure サブスクリプション接続マネージャー][connectionmanager]

Azure Feature Pack for SSIS の詳細については、[こちら][ssispack]を参照してください。

## <a id="nextsteps"></a>次のステップ
これで、HDInsight で Pig を使用する方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [HDInsight へのデータのアップロード](../hdinsight-upload-data.md)
* [HDInsight での Apache Hive の使用][hdinsight-use-hive]
* [HDInsight での Apache Sqoop の使用](hdinsight-use-sqoop.md)
* [HDInsight での Apache Oozie の使用](../hdinsight-use-oozie-linux-mac.md)
* [HDInsight での MapReduce ジョブの使用][hdinsight-use-mapreduce]

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
