---
title: Azure HDInsight での Apache HBase の概要
description: Hadoop 上に構築される NoSQL データベースである HDInsight の Apache HBase の概要 ユース ケースについて説明し、HBase を他の Hadoop クラスターと比較します。
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 98872cc1315d946c63825318d2b98460031e128a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942917"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Azure HDInsight での Apache HBase の概要

[Apache HBase](https://hbase.apache.org/) は、[Google BigTable](https://cloud.google.com/bigtable/) をモデルにした、Apache Hadoop 上に構築されるオープンソースの NoSQL データベースです。 HBase は、大量のデータに対するランダム アクセスと強力な一貫性を、スキーマレス データベースで実現します。 データベースは列ファミリによって整理されます。

ユーザーの視点から見ると、HBase はデータベースに似ています。 データはテーブルの行および列内に格納され、行内のデータは列ファミリによってグループ化されます。 HBase はスキーマレス データベースです。 列とデータ型は、使用前に未定義にすることができます。 オープン ソース コードは、直線的な拡張により何千ものノード上でペタバイト級のデータを扱うことができます。 また、Hadoop 環境の分散アプリケーションによって提供されるデータ冗長性やバッチ処理などの機能も利用できます。

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Azure HDInsight での Apache HBase の実装方法

HDInsight HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。 クラスターは、[Azure Storage](./../hdinsight-hadoop-use-blob-storage.md) にデータを直接格納するよう構成されるため、待ち時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きなエラスティック性が生まれます。 この特性により、大規模なデータセットを処理する対話的な Web サイトを構築できます。 このため、数百万ものエンドポイントからのセンサーおよびテレメトリ データを格納するサービスを構築したり、 Hadoop ジョブを使用してこのデータを分析したりできます。 HBase と Hadoop は、Azure でのビッグ データ プロジェクトの出発点として適しています。 これらのサービスを使用すると、リアルタイム アプリケーションで大規模なデータセットを処理できるようになります。

HDInsight の実装では、HBase のスケールアウト アーキテクチャを使用して、テーブルの自動シャーディングが実現されます。 また、読み取りと書き込みの厳密な整合性と、自動フェールオーバーも提供されます。 また、メモリ内キャッシュを利用した読み取りと高スループットのストリーミングによる書き込みによって、パフォーマンスも拡張されています。 仮想ネットワーク内に HBase クラスターを作成することができます。 詳細については、[Azure Virtual Network での HDInsight クラスターの作成](./apache-hbase-provision-vnet.md)に関する記事を参照してください。

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>HDInsight HBase 内のデータの管理方法

HBase 内のデータは、HBase シェルから `create`、`get`、`put`、`scan` というコマンドを使用して管理できます。 データの書き込みは `put`、読み取りは `get` を使用します。 `scan` コマンドは、テーブルの複数の行からデータを取得するために使用します。 HBase の C# API を使用してデータを管理することもできます。これにより、HBase REST API 最上層のクライアント ライブラリを利用できます。 HBase データベースでは、[Apache Hive](https://hive.apache.org/) を使用してクエリを実行することもできます。 これらのプログラミング モデルの概要については、[HDInsight の Apache Hadoop 環境での Apache HBase の使用](./apache-hbase-tutorial-get-started-linux.md)に関する記事を参照してください。 コプロセッサも利用でき、データベースをホストするノードでデータが処理できます。

> [!NOTE]  
> Thrift は、HDInsight での HBase ではサポートされていません。

## <a name="use-cases-for-apache-hbase"></a>Apache HBase のユース ケース

BigTable が開発され、さらにその機能を拡張する形で HBase が開発された本来の目的は、Web 検索に利用するためでした。 検索エンジンでは、言葉を、その言葉が含まれる Web ページにマッピングするインデックスを作成するために使用されています。 しかし、HBase が適する用途は他にも数多くあります。このセクションでは、その中からいくつかの使用事例を紹介します。

|シナリオ |説明 |
|---|---|
|キー値ストア|HBase は、キー値ストアとして使用できるため、メッセージ システムの管理に適しています。 Facebook は、メッセージング システムに HBase を使用しており、インターネット通信データの保存と管理に効果を発揮しています。 WebTable は、HBase を使用して Web ページから抽出したテーブルを検索し管理します。|
|センサー データ|HBase は、さまざまなソースから次々と収集されるデータの取り込みに適しています。 このデータには、ソーシャル分析と時系列が含まれます。 これにより、対話型ダッシュボードに最新の傾向やカウンターを反映させたり、監査ログ システムを管理したりできます。 ブルームバーグのトレーダー用ターミナルや Open Time Series Database (OpenTSDB) などがその例です。 OpenTSDB は、サーバー システムの正常性について収集されたメトリックを格納し、そのメトリックにアクセスできるようにします。|
|リアルタイム クエリ|[Apache Phoenix](https://phoenix.apache.org/) は、Apache HBase 用の SQL クエリ エンジンです。 JDBC ドライバーとしてアクセスされ、SQL を使用して HBase テーブルの照会と管理を行うことができます。|
|プラットフォームとしての HBase|HBase をデータストアとして使用することで、アプリケーションは HBase の最上層で実行できます。 この例には、Phoenix、OpenTSDB、`Kiji`、Titan があります。 アプリケーションを HBase と統合することもできます。 たとえば、次のようになります。[Apache Hive](https://hive.apache.org/)、Apache Pig、[Solr](https://lucene.apache.org/solr/)、Apache Storm、Apache Flume、[Apache Impala](https://impala.apache.org/)、Apache Spark、`Ganglia`、Apache Drill。|

## <a name="next-steps"></a>次のステップ

* [HDInsight の Apache Hadoop での Apache HBase の概要](./apache-hbase-tutorial-get-started-linux.md)
* [Azure Virtual Network での HDInsight クラスターの作成](./apache-hbase-provision-vnet.md)
* [HDInsight での Apache HBase レプリケーションの構成](apache-hbase-replication.md)
