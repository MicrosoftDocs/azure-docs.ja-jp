---
title: "HDInsight の HBase とは | Microsoft Docs"
description: "Hadoop 上に構築される NoSQL データベースである HDInsight の Apache HBase の概要 ユース ケースについて説明し、HBase を他の Hadoop クラスターと比較します。"
keywords: "bigtable、nosql、hbase とは"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: d2a76d53-133a-4849-a30c-88d9c794391c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 45fb85f83570ed7d79e70dc6fb8f0fdabb7ea191
ms.lasthandoff: 03/24/2017


---
# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>HDInsight の HBase とは: Hadoop で BigTable のような機能を提供する NoSQL データベース
Apache HBase は、オープン ソースの NoSQL データベースで、Hadoop 上に構築され、Google BigTable を模範にしています。 HBase は、大量の非構造化データと半構造化データに対するランダム アクセスと強力な一貫性を、列ファミリで整理されたスキーマなしのデータベースで実現します。

データはテーブルの行内に格納され、行内のデータは列ファミリによってグループ化されます。 HBase は、列や列内に格納されるデータの型を使用前に定義する必要がないという意味で、スキーマレス データベースです。 オープン ソース コードは、直線的な拡張により何千ものノード上でペタバイト級のデータを扱うことができます。 また、Hadoop エコシステムの分散アプリケーションの利点であるデータの冗長性、バッチ処理などの機能を利用できます。

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Azure HDInsight での HBase の実装方法
HDInsight HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。 クラスターは、Azure Storage にデータを直接格納するよう構成されるため、待ち時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。 そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよびテレメトリ データを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。 HBase と Hadoop によって、Azure のビッグ データ プロジェクトは大きな一歩を踏み出しました。特に注目したいのは、リアルタイム アプリケーションが大規模なデータセットを処理できるようになったことです。

HDInsight の実装と HBase のスケールアウト アーキテクチャにより、テーブルの自動シャーディング、読み取りと書き込みの強力な一貫性、自動フェールオーバーなどが実現します。 また、メモリ内キャッシュを利用した読み取りと高スループットのストリーミングによる書き込みによって、パフォーマンスも拡張されています。 仮想ネットワーク内に HBase クラスターを作成することができます。 詳細については、[Azure Virtual Network での HDInsight クラスターの作成][hbase-provision-vnet]に関する記事を参照してください。

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>HDInsight HBase 内のデータの管理方法
HBase 内のデータは、HBase シェルから `create`、`get`、`put`、`scan` というコマンドを使用して管理できます。 データの書き込みは `put`、読み取りは `get` を使用します。 `scan` コマンドは、テーブルの複数の行からデータを取得するために使用します。 HBase の C# API を使用してデータを管理することもできます。これにより、HBase REST API 最上層のクライアント ライブラリを利用できます。 HBase データベースは、Hive を使用して照会することもできます。 これらのプログラミング モデルの概要については、[HDInsight の Hadoop 環境での HBase の使用][hbase-get-started]に関する記事を参照してください。 コプロセッサも利用でき、データベースをホストするノードでデータが処理できます。

>
> [!NOTE]
> Thrift は、HDInsight での HBase ではサポートされていません。
>

## <a name="scenarios-use-cases-for-hbase"></a>シナリオ: HBase のユース ケース
BigTable が開発され、さらにその機能を拡張する形で HBase が開発された本来の目的は、Web 検索に利用するためでした。 検索エンジンでは、言葉を、その言葉が含まれる Web ページにマッピングするインデックスを作成するために使用されています。 しかし、HBase が適する用途は他にも数多くあります。このセクションでは、その中からいくつかの使用事例を紹介します。

* キー値ストア
  
    HBase は、キー値ストアとして使用できるため、メッセージ システムの管理に適しています。 Facebook は、メッセージング システムに HBase を使用しており、インターネット通信データの保存と管理に効果を発揮しています。 WebTable は、HBase を使用して Web ページから抽出したテーブルを検索し管理します。
* センサー データ
  
    HBase は、さまざまなソースから次々と収集されるデータの取り込みに適しています。 たとえば、ソーシャル分析や時系列データベースに使用されており、対話型ダッシュボードに最新の傾向やカウンターを反映させたり、監査ログ システムを管理したりできます。 また、ブルームバーグのトレーダー用端末や Open Time Series Database (OpenTSDB) にも使用されています。OpenTSDB は、収集されたサーバー システムの健全性指標を格納してアクセスできるデータベースです。
* リアルタイム クエリ
  
    [Phoenix](http://phoenix.apache.org/) は、Apache HBase 用の SQL クエリ エンジンで、JDBC ドライバーとしてアクセスされます。 SQL を使用して HBase テーブルの照会と管理ができます。
* プラットフォームとしての HBase
  
    HBase をデータストアとして使用することで、アプリケーションは HBase の最上層で実行できます。 たとえば、Phoenix、OpenTSDB、Kiji、Titan などがあります。 アプリケーションを HBase と統合することもできます。 Hive、Pig、Solr、Storm、Flume、Impala、Spark、Ganglia、Drill がその例です。

## <a name="next-steps"></a>次のステップ
* [HDInsight の Hadoop 環境での HBase の使用][hbase-get-started]
* [Azure Virtual Network での HDInsight クラスターの作成][hbase-provision-vnet]
* [HDInsight での HBase レプリケーションの構成](hdinsight-hbase-replication.md)
* [HDInsight 環境の HBase での Twitter のセンチメントのリアルタイム分析][hbase-twitter-sentiment]
* [HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションの Maven での構築][hbase-build-java-maven]

## <a name="see-also"></a>関連項目
* [Apache HBase](https://hbase.apache.org/)
* [Bigtable: A Distributed Storage System for Structured Data (Bigtable: 構造化データ用分散ストレージ システム)](http://research.google.com/archive/bigtable.html)

[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/

