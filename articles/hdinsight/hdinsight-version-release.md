---
title: HDInsight 4.0 の概要 - Azure
description: HDInsight 3.6 と HDInsight 4.0 の機能、制限事項、アップグレードの推奨事項を比較します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 694acc0005e90d8299d46528f83ba68ea3fcf1c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931181"
---
# <a name="azure-hdinsight-40-overview"></a>Azure HDInsight 4.0 の概要

Azure HDInsight は、Apache Hadoop および Apache Spark に関してエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。 HDInsight 4.0 は、Apache Hadoop コンポーネントのクラウド ディストリビューションです。 この記事では、Azure HDInsight の最新のリリースとアップグレード方法に関する情報を提供します。

## <a name="whats-new-in-hdinsight-40"></a>HDInsight 4.0 の新機能

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 と低待機時間の分析処理

Apache Hive LLAP (低遅延分析処理) では、永続的クエリ サーバーとメモリ内キャッシュが使用されます。 このプロセスでは、リモート クラウド ストレージ内のデータに対する SQL クエリの結果がすばやく提供されます。 Hive LLAP では、Hive クエリのフラグメントを実行する永続的なデーモンのセットが使用されます。 LLAP でのクエリの実行は、コンテナーではなく LLAP デーモン内でワーカー タスクが実行している、LLAP を使用しない Hive と似ています。

Hive LLAP の利点は次のとおりです。

* パフォーマンスと適応性を損なうことなく、詳細な SQL 分析を実行できます。 複雑な結合、サブクエリ、ウィンドウ関数、並べ替え、ユーザー定義関数、複雑な集計などです。

* データが準備された場所と同じストレージ内のデータに対する対話型クエリにより、分析処理のためにストレージから別のエンジンにデータを移動する必要がありません。

* クエリ結果をキャッシュすることで、以前に計算されたクエリ結果を再利用できます。 このキャッシュにより、クエリに必要なクラスター タスクの実行に費やされる時間とリソースが節約されます。

### <a name="hive-dynamic-materialized-views"></a>Hive の動的なマテリアライズドビュー

Hive では、動的なマテリアライズドビュー、つまり関連するサマリーの事前計算がサポートされるようになっています。 ビューによって、データ ウェアハウスでのクエリ処理が高速化されます。 マテリアライズドビューは Hive にネイティブに格納でき、LLAP アクセラレーションをシームレスに使用できます。

### <a name="hive-transactional-tables"></a>Hive トランザクション テーブル

HDI 4.0 には Apache Hive 3 が含まれています。 Hive 3 では、Hive ウェアハウス内に存在するトランザクション テーブルに対して、原子性、一貫性、分離性、持続性への対応が要求されます。 ACID に準拠しているテーブルおよびテーブルのデータは、Hive によってアクセスされて管理されます。 作成、取得、更新、削除 (CRUD) テーブル内のデータは Optimized Row Column (ORC) ファイル形式である必要があります。 挿入のみのテーブルでは、すべてのファイル形式がサポートされます。 

> [!Note]
> ACID またはトランザクション サポートは、マネージド テーブルに対してのみ機能し、外部テーブルでは機能しません。 Hive 外部テーブルは、Hive によって基になるデータの変更が行われなくても外部パーティがテーブル データを読み書きできるように設計されています。 ACID テーブルの場合は、Hive によって基になるデータが圧縮とトランザクションを使用して変更される可能性があります。

ACID テーブルには次のような利点があります。

* ACID v2 では、ストレージ形式と実行エンジンの両方でパフォーマンスが向上しています

* データの更新を完全にサポートできるよう、ACID は既定で有効になります。

* 向上した ACID 機能により、行レベルでの更新および削除が可能です。

* パフォーマンスのオーバーヘッドはありません。

* バケット処理は必要ありません。

* Spark は、Hive Warehouse Connector を介して、Hive ACID テーブルを読み書きできます。

詳しくは、[Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html) に関するページをご覧ください。

### <a name="apache-spark"></a>Apache Spark

Apache Spark は、Hive Warehouse Connector を使用して更新可能なテーブルと ACID トランザクションを取得します。 Hive Warehouse Connector を使用すると、Spark で外部テーブルとして Hive トランザクション テーブルを登録でき、完全なトランザクション機能にアクセスできます。 以前のバージョンでは、テーブル パーティションの操作のみがサポートされていました。 Hive Warehouse Connector では、Streaming DataFrames もサポートされています。  このプロセスでは、Spark からトランザクション Hive テーブルおよびストリーミング Hive テーブルへの読み取りと書き込みがストリーミングされます。

Spark Executor は、Hive LLAP デーモンに直接接続してトランザクション方式でデータを取得および更新することができ、Hive でデータの制御を維持できます。

HDInsight 4.0 上の Apache Spark では、次のシナリオがサポートされています。

* レポートに使用されるのと同じトランザクション テーブルで機械学習モデルのトレーニングを実行します。
* ACID トランザクションを使用して、Spark ML から Hive テーブルに列を安全に追加します。
* Hive ストリーミング テーブルからの変更フィードで Spark ストリーミング ジョブを実行します。
* Spark Structured Streaming ジョブから直接、ORC ファイルを作成します。

誤って Spark から直接 Hive トランザクション テーブルにアクセスを試みても心配する必要はもうありません、 矛盾した結果、重複したデータ、データの破損の発生。 HDInsight 4.0 では、Spark のテーブルと Hive のテーブルは、個別の metastore に保持されます。 Hive トランザクション テーブルを Spark 外部テーブルとして明示的に登録するには、Hive Data Warehouse Connector を使用します。

詳しくは、[Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html) に関するページをご覧ください。

### <a name="apache-oozie"></a>Apache Oozie

HDI 4.0 に含まれる Apache Oozie 4.3.1 では以下の点が変更されています。

* Oozie では、Hive アクションが実行されなくなりました。 Hive CLI は削除され、BeeLine に置き換えられています。

* **job.properties** ファイルに除外パターンを含めることによって、共有ライブラリから不要な依存関係を除外できます。

詳しくは、[Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html) に関するページをご覧ください。

## <a name="how-to-upgrade-to-hdinsight-40"></a>HDInsight 4.0 にアップグレードする方法

運用環境に最新バージョンを実装する前に、コンポーネントを徹底的にテストします。 HDInsight 4.0 を使用して、アップグレード プロセスを開始できます。 不慮の事故を防ぐため、HDInsight 3.6 が既定のオプションです。

以前のバージョンの HDInsight から HDInsight 4.0 へのアップグレード パスはサポートされていません。 メタストアと BLOB のデータ形式が変更されたため、4.0 は以前のバージョンと互換性がありません。 新しい HDInsight 4.0 環境を現在の運用環境から分離しておくことが重要です。 HDInsight 4.0 を現在の環境にデプロイした場合、メタストアは恒久的にアップグレードされます。  

## <a name="limitations"></a>制限事項

* HDInsight 4.0 では、Apache Hive 用 MapReduce はサポートされていません。 代わりに Apache Tez を使用してください。 詳しくは、[Apache Tez](https://tez.apache.org/) に関するページをご覧ください。
* HDInsight 4.0 では Apache Storm がサポートされていません。
* HDInsight 4.0 では、ML Services クラスターの種類がサポートされていません。
* Hive View は、バージョン番号が 4.1 以上の HDInsight 4.0 クラスターでのみ利用できます。 この番号は、[Ambari Admin]、[バージョン] の順に選択すると見つかります。
* Apache Zeppelin のシェル インタープリターは、Spark および対話型クエリ クラスターではサポートされていません。
* Spark-LLAP クラスターでは LLAP を "*無効にする*" ことはできません。 LLAP をオフにすることだけができます。
* Azure Data Lake Storage Gen2 では、Jupyter Notebook を Spark クラスターに保存できません。
* Apache pig は既定では Tez で実行されますが、Mapreduce に変更することもできます
* 行および列のセキュリティに対する Spark SQL Ranger 統合は非推奨です
* HDInsight 4.0 では Spark 2.4 と Kafka 2.1 を利用できます。そのため、Spark 2.3 と Kafka 1.1 はサポートされなくなりました。 HDInsight 4.0 では、Spark 2.4 および Kafka 2.1 以降を使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

* [HBase 移行ガイド](./hbase/apache-hbase-migrate-new-version.md)
* [Hive 移行ガイド](./interactive-query/apache-hive-migrate-workloads.md)
* [Kafka 移行ガイド](./kafka/migrate-versions.md)
* [Spark 移行ガイド](./spark/migrate-versions.md)
* [Azure HDInsight のドキュメント](index.yml)
* [リリース ノート](hdinsight-release-notes.md)
