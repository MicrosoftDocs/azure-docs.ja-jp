---
title: "HDInsight、Hadoop テクノロジ スタック、クラスターとは - Azure | Microsoft Docs"
description: "ビッグ データの分析用の Spark、Kafka、Hive、HBase を含む、HDInsight および Hadoop テクノロジ スタックおよびコンポーネントの概要。"
keywords: "Azure Hadoop, Hadoop Azure, Hadoop の紹介, Hadoop の概要, Hadoop テクノロジ スタック, Hadoop の概説, Hadoop 入門, Hadoop クラスターとは, Hadoop クラスターについて, Hadoop の使用目的"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: cgronlun
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: b413b6f1a6c73251dfdbe6bf9d23cdfa6510839a
ms.contentlocale: ja-jp
ms.lasthandoff: 07/22/2017

---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Azure HDInsight、Hadoop テクノロジ スタック、Hadoop クラスターの概要
 この記事では、Hadoop テクノロジ スタックのクラウド ディストリビューションである Azure HDInsight について概説します。 さらに、Hadoop クラスターとは何であるかや、いつ Hadoop クラスターを使用するかについても説明します。 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>HDInsight および Hadoop テクノロジ スタックとは 
Azure HDInsight は、[Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/) の Hadoop コンポーネントのクラウド ディストリビューションです。 [Apache Hadoop](http://hadoop.apache.org/) は、本来、コンピューターのクラスターでのビッグ データ セットの分散処理および分析のためのオープン ソース フレームワークでした。 


Hadoop テクノロジ スタックには、Apache Hive、HBase、Spark、Kafka、その他の多くの関連するソフトウェアおよびユーティリティが含まれます。 HDInsight で利用可能な Hadoop テクノロジ スタック コンポーネントを確認するには、[HDInsight で利用可能なコンポーネントとバージョン][component-versioning]に関する記事を参照してください。 HDInsight の Hadoop については、[HDInsight 向けの Azure の機能](https://azure.microsoft.com/services/hdinsight/)に関するページを参照してください。

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>Hadoop クラスターとは何か、いつ使用するか
*Hadoop* とは、次の機能を備えたクラスターの種類でもあります。

* ジョブのスケジュール設定およびリソース管理のための YARN
* 並列処理のための MapReduce
* Hadoop 分散ファイル システム (HDFS)
  
Hadoop クラスターは、保存されているデータのバッチ処理に最もよく使用されます。 HDInsight の他の種類のクラスターには、追加の機能があります。Spark は高速なメモリ内処理により人気が上がっています。 詳細については、「[HDInsight でのクラスターの種類](#overview)」を参照してください。

## <a name="what-is-big-data"></a>ビッグ データとは
ビッグ データは、次のような大量のデジタル情報を記述します。

* 工業設備からのセンサー データ
* Web サイトから収集された顧客アクティビティ
* Twitter のニュース フィード

収集されたビッグ データは、膨れ上がっています。その勢いはしだいに増し、形式も多様化しています。 ビッグ データの種類には、履歴データ (つまり、保存されたデータ) とリアルタイム データ (つまり、ソースからストリーミングされるデータ) があります。 

## <a name="overview"></a>HDInsight でのクラスターの種類
HDInsight には、特定のクラスターの種類のほか、コンポーネント、ユーティリティ、および言語の追加などのクラスター カスタマイズ機能が含まれています。

### <a name="spark-kafka-interactive-hive-hbase-customized-and-other-cluster-types"></a>Spark、Kafka、Interactive Hive、HBase、カスタマイズ、その他のクラスターの種類
HDInsight は、次のクラスターの種類を提供します。

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: [HDFS](#hdfs)、[YARN](#yarn) によるリソース管理、およびシンプルな [MapReduce](#mapreduce) プログラミング モデルを使用して、バッチ データを同時に処理および分析します。
* **[Apache Spark](http://spark.apache.org/)**: ビッグ データ分析アプリケーションのパフォーマンスを向上させるメモリ内処理をサポートする並列処理フレームワークです。Spark は、SQL、ストリーミング データ、機械学習に対応します。 [HDInsight での Apache Spark の概要](hdinsight-apache-spark-overview.md)に関する記事を参照してください。
* **[Apache HBase](http://hbase.apache.org/)**: Hadoop 上に構築された NoSQL データベースです。数十億行 x 数百万列に達する可能性のある大量の非構造化データや半構造化データへのランダム アクセスと厳密な整合性を実現します。 [HDInsight での HBase の概要](hdinsight-hbase-overview.md)に関する記事を参照してください。
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: 並列分散 R プロセスをホストおよび管理するためのサーバーです。 データ サイエンティスト、統計学者、R プログラマーは、HDInsight でのスケーラブルで分散型の分析手法にオンデマンドでアクセスできるようになります。 [HDInsight の R Server の概要](hdinsight-hadoop-r-server-overview.md)に関するページを参照してください。
* **[Apache Storm](https://storm.incubator.apache.org/)**: 大量のデータ ストリームを高速処理するための分散型リアルタイム計算システムです。 Storm は、HDInsight で管理されるクラスターとして提供されます。 「 [HDInsight (Hadoop) での Storm と HBase を使ったセンサー データの分析](hdinsight-storm-sensor-data-analysis.md)」を参照してください。
* **[Apache Interactive Hive プレビュー (別名: Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: 対話型で高速な Hive クエリを実現するメモリ内キャッシュです。 「[HDInsight での対話型 Hive の使用](hdinsight-hadoop-use-interactive-hive.md)」を参照してください。
* **[Apache Kafka](https://kafka.apache.org/)**: ストリーミング データ パイプラインおよびアプリケーションを構築するためのオープン ソースのプラットフォームです。 Kafka には、データ ストリームの発行とサブスクライブを可能にするメッセージ キュー機能も用意されています。 「[HDInsight での Apache Kafka の概要](hdinsight-apache-kafka-introduction.md)」を参照してください。

次の方法を使用してクラスターを構成することもできます。
* **[ドメイン参加クラスター プレビュー](hdinsight-domain-joined-introduction.md)**: Active Directory ドメインに参加しているクラスターです。アクセスを制御し、データのガバナンスを提供することができます。
* **[スクリプト アクションを使用するカスタム クラスター](hdinsight-hadoop-customize-cluster-linux.md)**: プロビジョニング中に実行されて追加のコンポーネントをインストールするスクリプトを使用するクラスターです。

### <a name="example-cluster-customization-scripts"></a>クラスターのカスタマイズ スクリプトの例
スクリプト アクションとは、クラスターのプロビジョニング中に実行される Linux の Bash スクリプトで、クラスター上に追加のコンポーネントをインストールするために使用できます。 

HDInsight チームによって提供されるスクリプトの例は次のとおりです。

* **[Hue](hdinsight-hadoop-hue-linux.md)**: クラスターとの情報のやり取りに使用される一連の Web アプリケーション。 Linux クラスターのみ。
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)**: モノまたは人の間の関係をモデル化するグラフ処理。
* **[Solr](hdinsight-hadoop-solr-install-linux.md)**: データに対してフルテキスト検索ができるエンタープライズ規模の検索プラットフォーム。

独自のスクリプト アクションを開発する方法の詳細については、「 [HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」を参照してください。

## <a name="components-and-utilities-on-hdinsight-clusters"></a>HDInsight クラスターのコンポーネントとユーティリティ
HDInsight クラスターには、次のコンポーネントとユーティリティが含まれています。

* **[Ambari](#ambari)**: クラスターのプロビジョニング、管理、監視、ユーティリティ。
* **[Avro](#avro)** (Microsoft .NET Library for Avro): Microsoft.NET 環境向けのデータ シリアル化。 
* **[Hive と HCatalog](#hive)**: SQL に似たクエリ処理、テーブルおよびストレージの管理レイヤー。
* **[Mahout](#mahout)**: スケーラブルな機械学習アプリケーション用。
* **[MapReduce](#mapreduce)**: Hadoop 分散処理およびリソース管理用のレガシ フレームワーク。 「[YARN](#yarn)」を参照してください。
* **[Oozie](#oozie)**: ワークフロー管理。
* **[Phoenix](#phoenix)**: HBase の上のリレーショナル データベース レイヤー。
* **[Pig](#pig)**- MapReduce 変換のためのシンプルなスクリプト作成。
* **[Sqoop](#sqoop)**- データのインポートとエクスポート。
* **[Tez](#tez)**: データ量の多い処理を効率的かつ大規模に実行可能。
* **[YARN](#yarn)**: Hadoop コア ライブラリの一部であるリソース管理。
* **[ZooKeeper](#zookeeper)**- 分散システムにおけるプロセスの調整。

> [!NOTE]
> 特定のコンポーネントとバージョン情報については、[HDInsight での Hadoop のコンポーネントとバージョン][component-versioning]に関するページを参照してください。
>
>

### <a name="ambari"></a>Ambari
Apache Ambari は、Apache Hadoop クラスターのプロビジョニング、管理、および監視を目的としています。 演算子ツールの直観的なコレクションおよび Hadoop の複雑さが見えないようにする信頼性の高い、一連の API が含まれており、クラスターの操作を単純化しています。 Linux 上の HDInsight クラスターは、Ambari Web UI と Ambari REST API の両方を提供します。 HDInsight クラスターの Ambari ビューでは、プラグイン UI 機能を使用できます。
[Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)および <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API リファレンス</a>に関するページを参照してください。

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)
Microsoft .NET Library for Avro は、Microsoft.NET 環境向けのシリアル化のために、Apache Avro のコンパクトなバイナリ データ交換形式を実装します。 言語に依存しないスキーマを定義して、ある言語でシリアル化されたデータを別の言語で読むことができるようにします。 この形式の詳細については、<a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro の仕様</a>をご覧ください。 Avro ファイルの形式は、分散 MapReduce プログラミング モデルをサポートしています。ファイル内の任意のポイントを検索し、特定のブロックから読み取りを開始できるという意味で、ファイルは "分割可能" です。 詳細については、[Microsoft .NET Library for Avro を使用したデータのシリアル化](hdinsight-dotnet-avro-serialization.md)に関する記事をご覧ください。 Linux ベースのクラスター サポートが予定されています。

### <a name="hdfs"></a>HDFS
Hadoop 分散ファイル システム (HDFS) は、YARN と MapReduce と共に Hadoop テクノロジの中核となるファイル システムです。 HDFS は、HDInsight の Hadoop クラスターの標準ファイル システムです。 [HDFS と互換性のあるストレージのデータのクエリ](hdinsight-hadoop-use-blob-storage.md)に関するページを参照してください。

### <a name="hive"></a>Hive と HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> は、Hadoop に組み込まれているデータ ウェアハウス ソフトウェアで、HiveQL と呼ばれる SQL に似た言語を使って分散ストレージ内の大量のデータセットに対するクエリと管理を可能にします。 Hive は、Pig と同様、MapReduce の上位にあって抽象化を担当するものであり、クエリを一連の MapReduce ジョブに変換します。 Hive は、Pig よりもリレーショナル データベース管理システムに近いため、より構造化されたデータに対して使用されます。 非構造化データを操作する場合は Pig が適しています。 「 [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)」をご覧ください。

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> は、Hadoop のテーブルおよびストレージを管理するレイヤーであり、ユーザーにデータのリレーショナル ビューを提供します。 HCatalog では、Hive SerDe (シリアライザー - デシリアライザー) で利用できるあらゆる形式のファイルを読み書きできます。

### <a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> は、Hadoop 上で実行される機械学習アルゴリズムのライブラリです。 統計の原則を使用して、機械学習アプリケーションは、データから学習すること、および過去の結果から将来の動作を決めることをシステムに教えます。 「 [HDInsight (Hadoop) で Apache Mahout を使用して映画のリコメンデーションを生成する](hdinsight-mahout.md)」を参照してください。

### <a name="mapreduce"></a>MapReduce
Hadoop MapReduce は、大規模なデータセットを並列でバッチ処理するアプリケーションを記述するための Hadoop 用のレガシー ソフトウェア フレームワークです。 MapReduce ジョブは、大規模なデータセットを分割し、データを処理のためにキーと値のペアに整理します。 MapReduce ジョブは [YARN](#yarn) で実行されます。 Hadoop Wiki の「<a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a>」を参照してください。

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> は Hadoop ジョブを管理するワークフローと調整システムです。 Hadoop スタックと統合されていて、MapReduce、Pig、Hive、Sqoop の Hadoop ジョブをサポートしています。 Java プログラムやシェル スクリプトのような、システム特有のジョブのスケジュールを設定するのに使用することもできます。 [Hadoop での Oozie の使用](hdinsight-use-oozie-linux-mac.md)に関するページを参照してください。

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> は、HBase 上のリレーショナル データベース レイヤーです。 Phoenix には JDBC ドライバーが含まれており、ユーザーはこれを使用して SQL テーブルを直接クエリおよび管理できます。 Phoenix は、クエリおよびその他のステートメントをネイティブ NoSQL の API 呼び出しに変換します。MapReduce を使用しないため、NoSQL ストア上でアプリケーションを高速化できます。 [HBase クラスターでの Apache Phoenix と SQuirreL の使用](hdinsight-hbase-phoenix-squirrel.md)に関する記事をご覧ください。

### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> は、Pig Latin と呼ばれるシンプルなスクリプト言語を使って大規模なデータセットに対して複雑な MapReduce 変換を実行することを可能にする高レベル プラットフォームです。 Pig は、Hadoop 内で実行できるように Pig Latin スクリプトを変換します。 ユーザー定義関数 (UDF) を作成して Pig Latin を拡張できます。 [Hadoop での Pig の使用](hdinsight-use-pig.md)に関するページを参照してください。

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> は、リレーショナル データベース (SQL など) やその他の構造化データ ストアと Hadoop との間でデータを効率的に一括転送するためのツールです。 「 [HDInsight の Hadoop での Sqoop の使用](hdinsight-use-sqoop.md)」を参照してください。

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> は、一般的なデータ処理で構成される複雑な非循環グラフを実行する Hadoop YARN 上に構築されたアプリケーション フレームワークです。 これは、MapReduce の後を継ぐ柔軟で強力なフレームワークであり、Hive などのデータ量の多い処理を、より効率的かつ大規模に実行することができます。 [Hive と HiveQL の使用に関するページの『パフォーマンスを改善するための Apache Tez の使用』](hdinsight-use-hive.md#usetez)を参照してください。

### <a name="yarn"></a>YARN
Apache YARN は次世代の MapReduce (MapReduce 2.0、または MRv2) であり、優れたスケーラビリティとリアルタイム処理によって MapReduce のバッチ処理を超えるデータ処理シナリオをサポートします。 YARN は、リソース管理および分散アプリケーション フレームワークを提供します。 MapReduce ジョブは YARN で実行されます。 <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop の次世代 MapReduce (YARN)</a> に関するページを参照してください。

### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> は、データ レジスタ (znode) の共有階層名前空間を使用して、大規模な分散システムのプロセスを調整します。 znode には、状態、場所、構成など、プロセスを調整するのに必要な少量のメタ情報が含まれます。 [ZooKeeper を HBase クラスターと Apache Phoenix と共に使用する](hdinsight-hbase-phoenix-squirrel-linux.md)例を参照してください。 

## <a name="programming-languages-on-hdinsight"></a>HDInsight のプログラミング言語
HDInsight クラスター (Spark、HBase、Kafka、Hadoop、およびその他のクラスター) は多数のプログラミング言語をサポートしますが、既定でインストールされないものがあります。 既定でインストールされないライブラリ、モジュール、またはパッケージは、[スクリプト アクションを使用してコンポーネントをインストール](hdinsight-hadoop-script-actions-linux.md)します。 

### <a name="default-programming-language-support"></a>既定のプログラミング言語のサポート
既定では、HDInsight クラスターは以下をサポートします。

* Java
* Python

[スクリプト アクション](hdinsight-hadoop-script-actions-linux.md)を使用して他の言語をインストールできます。

### <a name="java-virtual-machine-jvm-languages"></a>Java 仮想マシン (JVM) 言語
Java 以外の多くの言語を Java 仮想マシン (JVM) で実行できます。ただし、一部の言語では、クラスターに追加コンポーネントをインストールする必要があります。

次の JVM ベースの言語が HDInsight クラスターでサポートされます。

* Clojure
* Jython (Java 用の Python)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop 固有言語
HDInsight クラスターでは、Hadoop テクノロジ スタックに固有の次の言語をサポートしています。

* Pig ジョブ用の Pig Latin
* Hive ジョブ用の HiveQL と SparkSQL

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard と HDInsight Premium
HDInsight は、ビッグ データのクラウド サービスを Standard と Premium の 2 つのカテゴリで提供します。 HDInsight Standard では、組織がビッグ データのワークロードの実行に使用できる、エンタープライズ規模のクラスターが用意されています。 HDInsight Premium では、Standard 機能に加えて HDInsight クラスター用の高度な分析機能やセキュリティ機能が用意されています。 詳細については、 [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="microsoft-business-intelligence-and-hdinsight"></a>Microsoft ビジネス インテリジェンスおよび HDInsight
Power Query アドインまたは Microsoft Hive ODBC ドライバーを使用すれば、使い慣れたビジネス インテリジェンス (BI) ツールを HDInsight と連携して、データの取得、分析、レポート生成を行うことができます。

* [Power Query を使用した Excel から Hadoop への接続](hdinsight-connect-excel-power-query.md): Microsoft Power Query for Excel を使用して、HDInsight クラスターのデータを格納する Azure ストレージ アカウントに Excel を接続する方法を説明します。 Windows ワークステーションが必要です。 
* [Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続](hdinsight-connect-excel-hive-odbc-driver.md): Microsoft Hive ODBC ドライバーを使用して、HDInsight からデータをインポートする方法について説明します。 Windows ワークステーションが必要です。 
* [Microsoft Cloud プラットフォーム](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx)- Power BI for Office 365 の詳細、SQL Server 評価版のダウンロード、SharePoint Server 2013 と SQL Server BI のセットアップについて説明します。
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>次のステップ

* [HDInsight における Hadoop の概要](hdinsight-hadoop-linux-tutorial-get-started.md)- HDInsight Hadoop クラスターをプロビジョニングし、サンプルの Hive クエリを実行するためのクイック スタート チュートリアル。
* [HDInsight における Spark の概要](hdinsight-apache-spark-jupyter-spark-sql.md): Spark クラスターを作成してインタラクティブな Spark SQL クエリを実行するためのクイック スタート チュートリアル。
* [HDInsight における R Server の使用](hdinsight-hadoop-r-server-get-started.md): HDInsight Premium で R Server を使用するためのスタート ガイド。
* [HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md): Azure Portal、Azure CLI、または Azure PowerShell を使用して、HDInsight Hadoop クラスターをプロビジョニングする方法について説明します。


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
