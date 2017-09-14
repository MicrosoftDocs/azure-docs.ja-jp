---
title: "Azure HDInsight での Spark の概要 | Microsoft Docs"
description: "この記事では、HDInsight での Spark の概要と、HDInsight で Spark クラスターを使用できるさまざまなシナリオについて説明します。"
keywords: "Apache Spark とは,Spark クラスター,Spark の概要,HDInsight での Spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b8955acc83b0fbb0612e7042d62170ae8078b9ad
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="introduction-to-spark-on-hdinsight"></a>HDInsight での Spark の概要

この記事では、HDInsight での Spark の概要について説明します。 <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。 HDInsight の Spark クラスターは、Azure Storage (WASB) および Azure Data Lake Store と互換性があるため、 Azure に保存されている既存データを Spark クラスターを介して簡単に処理できます。

HDInsight で Spark クラスターを作成するときは、Spark をインストールおよび構成して Azure 計算リソースを作成します。 HDInsight で Spark クラスターを作成するのにかかる時間はわずか約 10 分です。 処理するデータは Azure Storage または Azure Data Lake Store に格納されます。 [HDInsight での Azure Storage の使用](hdinsight-hadoop-use-blob-storage.md)に関するページを参照してください。

**HDInsight で Spark クラスターを作成するには、**[HDInsight での Spark クラスターの作成と Jupyter を使用した対話型クエリの実行に関するクイックスタート ガイド](hdinsight-apache-spark-jupyter-spark-sql.md)のページを参照してください。


## <a name="what-is-apache-spark-on-azure-hdinsight"></a>Azure HDInsight での Apache Spark とは
Azure HDInsight の Spark クラスターでは、完全に管理された Spark サービスを利用できます。 以下の一覧は、HDInsight で Spark クラスターを作成する利点をまとめたものです。

| 機能 | Description |
| --- | --- |
| Spark クラスターの作成しやすさ |Azure Portal、Azure PowerShell、または HDInsight .NET SDK を使用すると、HDInsight に新しい Spark クラスターを数分で作成できます。 「 [HDInsight での Spark クラスターの概要](hdinsight-apache-spark-jupyter-spark-sql.md) |
| 使いやすさ |HDInsight の Spark クラスターには、Jupyter Notebook と Zeppelin Notebook が含まれています。 対話型のデータ処理と視覚化にこれらの Notebook を使用できます。|
| REST API |HDInsight の Spark クラスターには、ジョブの送信と監視をリモートで実行する REST API ベースの Spark ジョブ サーバーである [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server) が含まれています。 |
| Azure Data Lake Store のサポート | HDInsight の Spark クラスターは、プライマリ ストレージだけでなく、追加のストレージとして Azure Data Lake Store を使用するように構成できます (HDInsight 3.5 クラスターの場合のみ)。 Data Lake Store の詳細については、「 [Azure Data Lake Store の概要](../data-lake-store/data-lake-store-overview.md)」を参照してください。 |
| Azure サービスとの統合 |HDInsight の Spark クラスターには、Azure Event Hubs へのコネクタが付属しています。 Spark の一部として既に使用できる [Kafka](http://kafka.apache.org/)に加えて、Event Hubs を使用してストリーミング アプリケーションを作成できます。 |
| R Server のサポート | HDInsight Spark クラスターで R Server をセットアップして、Spark クラスターによって決められた速度で分散 R 計算を実行できます。 詳細については、「 [Get started using R Server on HDInsight (HDInsight で R Server を使ってみる)](hdinsight-hadoop-r-server-get-started.md)」をご覧ください。 |
| サード パーティ製 IDE との統合 | HDInsight には IntelliJ IDEA や Eclipse のような IDE 向けのプラグインが用意されており、このプラグインを使用してアプリケーションを作成し、HDInsight の Spark クラスターに送信することができます。 詳細については、[Azure Toolkit for IntelliJ IDEA の使用](hdinsight-apache-spark-intellij-tool-plugin.md)に関するページと [Azure Toolkit for Eclipse の使用](hdinsight-apache-spark-eclipse-tool-plugin.md)に関するページを参照してください。|
| 同時クエリ |HDInsight の Spark クラスターでは同時クエリがサポートされます。 これにより、1 人のユーザーからの複数のクエリまたは複数のユーザーおよびアプリケーションからの複数のクエリが、同じクラスター リソースを共有できます。 |
| SSD へのキャッシュ |データのキャッシュ先を、メモリまたはクラスター ノードに取り付けられている SSD から選択できます。 メモリへのキャッシュは、クエリのパフォーマンスは最高ですが、コストがかかります。SSD へのキャッシュは、メモリ内のデータセット全体を収めるのに必要なサイズのクラスターを作成する必要なしにクエリのパフォーマンスを向上できる優れたオプションです。 |
| BI ツールとの統合 |HDInsight の Spark クラスターには、データ分析用の [Power BI](http://www.powerbi.com/) や [Tableau](http://www.tableau.com/products/desktop) などの BI ツールへのコネクタが用意されています。 |
| 読み込み済みの Anaconda ライブラリ |HDInsight の Spark クラスターには、Anaconda ライブラリが事前にインストールされています。 [Anaconda](http://docs.continuum.io/anaconda/) は、機械学習、データ分析、視覚化などのための 200 個近いライブラリを提供します。 |
| 拡張性 |作成中にクラスター内のノード数を指定できますが、ワークロードに一致するようにクラスターを拡大、縮小できます。 すべての HDInsight クラスターでは、クラスター内のノード数を変更できます。 また、すべてのデータは Azure Storage または Data Lake Store に格納されるため、Spark クラスターはデータの損失なしで削除できます。 |
| 常時サポート |HDInsight の Spark クラスターでは、エンタープライズ レベルの 24 時間無休体制のサポートと、アップタイム 99.9% の SLA が提供されます。 |

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>HDInsight での Spark の使用例
HDInsight の Spark クラスターにより、以下に挙げる主なシナリオが実現できます。

### <a name="interactive-data-analysis-and-bi"></a>対話型のデータ分析と BI
[チュートリアルを見る](hdinsight-apache-spark-use-bi-tools.md)

HDInsight の Apache Spark では、Azure Storage または Azure Data Lake Store にデータが格納されます。 ビジネス エキスパートや重要な意思決定者は、そのデータを分析してレポートを作成し、分析されたデータから Microsoft Power BI を使用して対話型レポートを作成できます。 アナリストはクラスター ストレージ内の非構造化データや半構造化データから作業を開始し、そのデータのスキーマを Notebook を使用して定義してから、Microsoft Power BI を使用してデータ モデルを作成することができます。 また、HDInsight の Spark クラスターでは Tableau などの多数のサードパーティ製 BI ツールもサポートされるので、データ アナリストやビジネス エキスパート、重要な意思決定者にとって理想的なプラットフォームとなっています。

### <a name="spark-machine-learning"></a>Spark Machine Learning
[チュートリアルを見る: HVAC データを使用して建物の温度を予測する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[チュートリアルを見る: 食品検査の結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark には、Spark を基に作成された機械学習ライブラリである [MLlib](http://spark.apache.org/mllib/) が付属し、HDInsight の Spark クラスターから使用できます。 HDInsight の Spark クラスターには、機械学習用のさまざまなパッケージを含む Python ディストリビューションである Anaconda も含まれています。 これと Jupyter および Zeppelin Notebook の組み込みサポートを組み合わせることにより、機械学習アプリケーションを作成するうえで最高の環境が得られます。

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark のストリーミングおよびリアルタイム データ分析
[チュートリアルを見る](hdinsight-apache-spark-eventhub-streaming.md)

HDInsight の Spark クラスターには、リアルタイム分析ソリューションを構築するための豊富なサポートが用意されています。 Spark には既に Kafka、Flume、Twitter、ZeroMQ、TCP ソケットなどの多数のソースからデータを取り込むためのコネクタがありますが、HDInsight の Spark では Azure Event Hubs からデータを取り込むためのファーストクラスのサポートが追加されます。 Event Hubs は、Azure で最も広く使用されているキュー サービスです。 Event Hubs 向けのサポートがすぐに利用できることから、HDInsight の Spark クラスターは、リアルタイム分析パイプラインを構築するうえで理想的なプラットフォームです。

## <a name="next-steps"></a>Spark クラスターに含まれるコンポーネント
HDInsight の Spark クラスターには、クラスターで使用できる以下のコンポーネントが既定で含まれています。

* [Spark Core](https://spark.apache.org/docs/1.5.1/)。 Spark Core、Spark SQL、Spark ストリーミング API、GraphX、MLlib が含まれます。
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Zeppelin Notebook](http://zeppelin-project.org/)

HDInsight の Spark クラスターでは、Microsoft Power BI や Tableau などの BI ツールから HDInsight の Spark クラスターに接続するための [ODBC ドライバー](http://go.microsoft.com/fwlink/?LinkId=616229)も提供されます。

## <a name="where-do-i-start"></a>どこから始めるか
まずは HDInsight で Spark クラスターを作成します。 [HDInsight Linux での Spark クラスターの作成と Jupyter を使用した対話型クエリの実行に関するクイックスタート ガイド](hdinsight-apache-spark-jupyter-spark-sql.md)のページを参照してください。 

## <a name="next-steps"></a>次のステップ
### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)
* [Azure HDInsight における Apache Spark の既知の問題](hdinsight-apache-spark-known-issues.md)。

