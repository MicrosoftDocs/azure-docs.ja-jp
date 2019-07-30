---
title: Azure HDInsight での Apache HBase の概要
description: Hadoop 上に構築される NoSQL データベースである HDInsight の Apache HBase の概要 ユース ケースについて説明し、HBase を他の Hadoop クラスターと比較します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: e48a0c69dc04325c3f3c2ff7b73a26c6366816c9
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137460"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Azure HDInsight での Apache HBase の概要

[Apache HBase](https://hbase.apache.org/) は、[Google BigTable](https://cloud.google.com/bigtable/) をモデルにしている [Apache Hadoop](https://hadoop.apache.org/) 上に構築されるオープン ソースの NoSQL データベースです。 HBase は、大量の非構造化データと半構造化データに対するランダム アクセスと強力な一貫性を、列ファミリで整理されたスキーマなしのデータベースで実現します。

ユーザーの視点から見ると、HBase はデータベースに似ています。 データはテーブルの行および列内に格納され、行内のデータは列ファミリによってグループ化されます。 HBase は、列や列内に格納されるデータの型を使用前に定義する必要がないという意味で、スキーマレス データベースです。 オープン ソース コードは、直線的な拡張により何千ものノード上でペタバイト級のデータを扱うことができます。 また、Hadoop エコシステムの分散アプリケーションの利点であるデータの冗長性、バッチ処理などの機能を利用できます。

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Azure HDInsight での Apache HBase の実装方法

HDInsight HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。 クラスターは、[Azure Storage](./../hdinsight-hadoop-use-blob-storage.md) にデータを直接格納するよう構成されるため、待ち時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。 そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよびテレメトリ データを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。 HBase と Hadoop によって、Azure のビッグ データ プロジェクトは大きな一歩を踏み出しました。特に注目したいのは、リアルタイム アプリケーションが大規模なデータセットを処理できるようになったことです。

HDInsight の実装と HBase のスケールアウト アーキテクチャにより、テーブルの自動シャーディング、読み取りと書き込みの強力な一貫性、自動フェールオーバーなどが実現します。 また、メモリ内キャッシュを利用した読み取りと高スループットのストリーミングによる書き込みによって、パフォーマンスも拡張されています。 仮想ネットワーク内に HBase クラスターを作成することができます。 詳細については、[Azure Virtual Network での HDInsight クラスターの作成](./apache-hbase-provision-vnet.md)に関する記事を参照してください。

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>HDInsight HBase 内のデータの管理方法
HBase 内のデータは、HBase シェルから `create`、`get`、`put`、`scan` というコマンドを使用して管理できます。 データの書き込みは `put`、読み取りは `get` を使用します。 `scan` コマンドは、テーブルの複数の行からデータを取得するために使用します。 HBase の C# API を使用してデータを管理することもできます。これにより、HBase REST API 最上層のクライアント ライブラリを利用できます。 HBase データベースでは、[Apache Hive](https://hive.apache.org/) を使用してクエリを実行することもできます。 これらのプログラミング モデルの概要については、[HDInsight の Apache Hadoop 環境での Apache HBase の使用](./apache-hbase-tutorial-get-started-linux.md)に関する記事を参照してください。 コプロセッサも利用でき、データベースをホストするノードでデータが処理できます。

> [!NOTE]  
> Thrift は、HDInsight での HBase ではサポートされていません。

## <a name="scenarios-use-cases-for-apache-hbase"></a>シナリオ:Apache HBase のユース ケース
BigTable が開発され、さらにその機能を拡張する形で HBase が開発された本来の目的は、Web 検索に利用するためでした。 検索エンジンでは、言葉を、その言葉が含まれる Web ページにマッピングするインデックスを作成するために使用されています。 しかし、HBase が適する用途は他にも数多くあります。このセクションでは、その中からいくつかの使用事例を紹介します。

* キー値ストア
  
    HBase は、キー値ストアとして使用できるため、メッセージ システムの管理に適しています。 Facebook は、メッセージング システムに HBase を使用しており、インターネット通信データの保存と管理に効果を発揮しています。 WebTable は、HBase を使用して Web ページから抽出したテーブルを検索し管理します。
* センサー データ
  
    HBase は、さまざまなソースから次々と収集されるデータの取り込みに適しています。 たとえば、ソーシャル分析や時系列データベースに使用されており、対話型ダッシュボードに最新の傾向やカウンターを反映させたり、監査ログ システムを管理したりできます。 また、ブルームバーグのトレーダー用端末や Open Time Series Database (OpenTSDB) にも使用されています。OpenTSDB は、収集されたサーバー システムの健全性指標を格納してアクセスできるデータベースです。
* リアルタイム クエリ
  
    [Apache Phoenix](https://phoenix.apache.org/) は、Apache HBase 用の SQL クエリ エンジンです。 SQL を使用して HBase テーブルの照会と管理ができます。
* プラットフォームとしての HBase
  
    HBase をデータストアとして使用することで、アプリケーションは HBase の最上層で実行できます。 この例には、Phoenix、[OpenTSDB](http://opentsdb.net/)、Kiji、Titan があります。 アプリケーションを HBase と統合することもできます。 この例には、[Apache Hive](https://hive.apache.org/)、[Apache Pig](https://pig.apache.org/)、[Solr](https://lucene.apache.org/solr/)、[Apache Storm](https://storm.apache.org/)、[Apache Flume](https://flume.apache.org/)、[Apache Impala](https://impala.apache.org/)、[Apache Spark](https://spark.apache.org/)、[Ganglia](http://ganglia.info/)、[Apache Drill](https://drill.apache.org/) があります。

## <a name="next-steps"></a>次の手順

* [HDInsight の Apache Hadoop での Apache HBase の概要](./apache-hbase-tutorial-get-started-linux.md)
* [Azure Virtual Network での HDInsight クラスターの作成](./apache-hbase-provision-vnet.md)
* [HDInsight での Apache HBase レプリケーションの構成](apache-hbase-replication.md)
