---
title: アーキテクチャ:オンプレミスの Apache Hadoop から Azure HDInsight
description: オンプレミスの Hadoop クラスターを Azure HDInsight に移行する場合のアーキテクチャのベスト プラクティスについて説明します。
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 519dc53f6373ae1a9c8853d3fa90d137e9fa934b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435411"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>オンプレミスの Apache Hadoop クラスターを Azure HDInsight に移行する - アーキテクチャのベスト プラクティス

この記事では、Azure HDInsight システムのアーキテクチャに関する推奨事項について説明します。 これは、オンプレミスの Apache Hadoop システムを Azure HDInsight に移行する際に役立つベスト プラクティスを紹介するシリーズの一部です。

## <a name="use-multiple-workload-optimized-clusters"></a>ワークロードに最適化された複数のクラスターを使用する

多くのオンプレミスの Apache Hadoop デプロイは、多数のワークロードをサポートする 1 つの大規模なクラスターで構成されています。 この 1 つのクラスターは複雑になることがあり、すべてを連携させるために個々のサービスに対して妥協が必要になる場合があります。 オンプレミスの Hadoop クラスターを Azure HDInsight に移行するには、アプローチの変更が必要になります。

Azure HDInsight クラスターは、特定の種類の計算を利用するために設計されています。 ストレージは複数のクラスターとの間で共有できるため、ワークロードに最適化された複数のコンピューティング クラスターを作成してさまざまなジョブのニーズを満たすことができます。 クラスターの種類ごとに、それぞれ特定のワークロードに最適な構成があります。 次の表には、HDInsight でサポートされるクラスターの種類と対応するワークロードを示しています。

|ワークロード|HDInsight クラスターの種類|
|---|---|
|バッチ処理 (ETL/ELT)|Hadoop、Spark|
|データ ウェアハウス|Hadoop、Spark、対話型クエリ|
|IoT/ストリーミング|Kafka、Storm、Spark|
|NoSQL トランザクション処理|hbase|
|メモリ内キャッシュによる対話型でより高速なクエリ|Interactive Query|
|データ サイエンス|ML Services、Spark|

次の表は、HDInsight クラスターの作成に使用できる各種方法を示しています。

|ツール|ブラウザー ベース|コマンド ライン|REST API|SDK|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (ver 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](/dotnet/api/overview/azure/hdinsight)||||X|
|[Python SDK](/python/api/overview/azure/hdinsight)||||X|
|[Java SDK](/java/api/overview/azure/hdinsight)||||X|
|[Azure リソース マネージャーのテンプレート](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

詳細については、[HDInsight でのクラスターの種類](../hadoop/apache-hadoop-introduction.md)に関する記事を参照してください。

## <a name="use-transient-on-demand-clusters"></a>一時的なオンデマンド クラスターを使用する

HDInsight クラスターは、長期間未使用の状態が続くことがあります。 リソースのコストを節約できるように、HDInsight では、オンデマンドの一時的なクラスターがサポートされています。このようなクラスターは、ワークロードが正常に完了すると削除できます。

クラスターを削除しても、関連付けられているストレージ アカウントと外部メタデータは削除されません。 このクラスターは、同じストレージ アカウントと metastore を使用して、後で再作成することができます。

オンデマンドの HDInsight クラスターの作成は、Azure Data Factory を使用してスケジュールすることができます。 詳細については、「[Azure Data Factory を使用して HDInsight でオンデマンドの Apache Hadoop クラスターを作成する](../hdinsight-hadoop-create-linux-clusters-adf.md)」の記事を参照してください。

## <a name="decouple-storage-from-compute"></a>コンピューティングからストレージを切り離す

一般的なオンプレミスの Hadoop デプロイでは、データ ストレージとデータ処理に同じ一連のマシンを使用します。 これらは併置されているため、コンピューティングとストレージを一緒にスケーリングする必要があります。

HDInsight クラスターでは、ストレージをコンピューティングと併置する必要はなく、Azure Storage、Azure Data Lake Storage、またはその両方に配置することができます。 ストレージをコンピューティングから切り離すことには、次の利点があります。

- クラスター間でのデータ共有。
- データがクラスターに依存していないための一時的なクラスターの使用。
- ストレージ コストの削減。
- ストレージとコンピューティングの個別のスケーリング。
- リージョン間でのデータ レプリケーション。

コンピューティング クラスターは Azure リージョン内のストレージ アカウント リソースの近くに作成され、コンピューティングとストレージを切り離した場合のパフォーマンス コストを軽減します。 高速ネットワークにより、コンピューティング ノードは Azure Storage 内のデータに効率的にアクセスできます。

## <a name="use-external-metadata-stores"></a>外部のメタデータ ストアを使用する

HDInsight クラスターで動作する主なメタストアには、[Apache Hive](https://hive.apache.org/) と [Apache Oozie](https://oozie.apache.org/) の 2 つがあります。 Hive metastore は、Hadoop、Spark、LLAP、Presto、Apache Pig などのデータ処理エンジンで使用できる中央のスキーマ リポジトリです。 Oozie metastore には、スケジューリングの詳細と、進行中および完了した Hadoop ジョブの状態が格納されます。

HDInsight では、Hive metastore と Oozie metastore に Azure SQL Database を使用します。 HDInsight クラスターで metastore を設定する方法は 2 とおりあります。

1. 既定の metastore

    - 追加コストはありません。
    - metastore は、クラスターが削除されると削除されます。
    - metastore を異なるクラスター間で共有することはできません。
    - DTU の上限が 5 である基本的な Azure SQL DB が使用されます。

1. カスタムの外部 metastore

    - 外部の Azure SQL Database を metastore として指定します。
    - Hive スキーマ Oozie ジョブの詳細を含むメタデータを失うことなく、クラスターを作成および削除できます。
    - 1 つの metastore DB を異なる種類のクラスターで共有できます。
    - metastore は、必要に応じてスケールアップできます。
    - 詳細については、[Azure HDInsight での外部メタデータ ストアの使用](../hdinsight-use-external-metadata-stores.md)に関する記事を参照してください。

## <a name="best-practices-for-hive-metastore"></a>Hive metastore のベスト プラクティス

いくつかの HDInsight Hive metastore のベスト プラクティスを次に示します。

- カスタムの外部 metastore を使用して、コンピューティング リソースとメタデータを切り離します。
- 50 DTU と 250 GB のストレージを提供する S2 レベルの Azure SQL インスタンスから開始します。 ボトルネックを確認した場合は、データベースをスケール アップできます。
- ある HDInsight クラスター バージョン用に作成された metastore を別のバージョンのクラスターと共有しないでください。 異なるバージョンの Hive は異なるスキーマを使用します。 たとえば、1 つの metastore を Hive 1.2 クラスターと Hive 2.1 クラスターの両方で共有することはできません。
- カスタム metastore を定期的にバックアップします。
- metastore と HDInsight クラスターを同じリージョンで保持します。
- Azure portal や Azure Monitor ログなどの Azure SQL Database 監視ツールを使用して、metastore のパフォーマンスと可用性を監視します。
- 必要に応じて `ANALYZE TABLE` コマンドを実行し、表と列の統計を生成します。 たとえば、「 `ANALYZE TABLE [table_name] COMPUTE STATISTICS` 」のように入力します。

## <a name="best-practices-for-different-workloads"></a>さまざまなワークロードのベスト プラクティス

- 応答時間が改善された対話型 Hive クエリに LLAP クラスターを使用することを検討します。[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) は、クエリのメモリ内キャッシュを可能にする Hive 2.0 の新機能です。 LLAP により、Hive クエリは[特定のケースで Hive 1.x と比べて最大 26 倍高速化](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)されます。
- Hive ジョブの代わりに Spark ジョブを使用することを検討します。
- impala ベースのクエリを LLAP クエリに置き換えることを検討します。
- MapReduce ジョブを Spark ジョブに置き換えることを検討します。
- Spark Structured Streaming ジョブを使用して待ち時間の短い Spark バッチ ジョブを置き換えることを検討します。
- データのオーケストレーションに Azure Data Factory (ADF) 2.0 を使用することを検討します。
- クラスター管理に Ambari の使用を検討します。
- スクリプトを処理するために、データ ストレージをオンプレミスの HDFS から WASB、ADLS、または ADFS に変更します。
- Hive のテーブルと監査で Ranger の RBAC を使用することを検討します。
- MongoDB または Cassandra の代わりに CosmosDB を使用することを検討します。

## <a name="next-steps"></a>次のステップ

このシリーズの次の記事をお読みください。

- [オンプレミスから Azure HDInsight Hadoop への移行のためのインフラストラクチャのベスト プラクティス](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)