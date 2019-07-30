---
title: HDInsight 4.0 の概要 - Azure
description: HDInsight 3.6 と HDInsight 4.0 の機能、制限事項、アップグレードの推奨事項を比較します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 77260eaa2e19135586e438576397b4647afe7ece
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483974"
---
# <a name="hdinsight-40-overview"></a>HDInsight 4.0 の概要

Azure HDInsight は、Azure 上でオープンソースの Apache Hadoop および Apache Spark の分析を行う、エンタープライズのお客様の間で最も人気のあるサービスの 1 つです。 HDInsight 4.0 は、Apache Hadoop コンポーネントのクラウド ディストリビューションです。 この記事では、Azure HDInsight の最新のリリースとアップグレード方法に関する情報を提供します。

## <a name="whats-new-in-hdinsight-40"></a>HDInsight 4.0 の新機能

### <a name="apache-hive-30-and-llap"></a>Apache Hive 3.0 と LLAP

Apache Hive LLAP (低遅延分析処理) では、永続的クエリ サーバーとメモリ内キャッシュを使用して、リモート クラウド ストレージ内のデータの SQL クエリ結果がすばやく提供されます。 Hive LLAP では、Hive クエリのフラグメントを実行する永続的なデーモンのセットが利用されます。 LLAP でのクエリの実行は、コンテナーではなく LLAP デーモン内でワーカー タスクが実行している、LLAP を使用しない Hive と似ています。

Hive LLAP の利点は次のとおりです。

* 複雑な結合、サブクエリ、ウィンドウ関数、並べ替え、ユーザー定義関数、複雑な集計などのディープ SQL 分析を、パフォーマンスとスケーラビリティを損なうことなく実行できます。

* データが準備された場所と同じストレージ内のデータに対する対話型クエリにより、分析処理のためにストレージから別のエンジンにデータを移動する必要がありません。

* クエリの結果をキャッシュすることで、前に計算されたクエリの結果を再利用でき、クエリに必要なクラスター タスクの実行に費やされる時間とリソースを節約できます。

### <a name="hive-dynamic-materialized-views"></a>Hive の動的な具体化されたビュー

Hive は、データ ウェアハウスでのクエリ処理を高速化するために使用される、動的な具体化されたビュー、つまり関連するサマリーの事前計算をサポートします。 具体化されたビューは Hive にネイティブに格納でき、LLAP アクセラレーションをシームレスに使用できます。

### <a name="hive-transactional-tables"></a>Hive トランザクション テーブル

HDI 4.0 に含まれる Apache Hive 3 では、Hive ウェアハウス内に存在するトランザクション テーブルに対して、原子性、一貫性、分離性、持続性 (ACID) への対応が要求されます。 ACID に準拠しているテーブルおよびテーブルのデータは、Hive によってアクセスされて管理されます。 作成、取得、更新、削除 (CRUD) テーブル内のデータは Optimized Row Column (ORC) ファイル形式である必要がありますが、挿入のみのテーブルはすべてのファイル形式をサポートします。

* ACID v2 では、ストレージ形式と実行エンジンの両方でパフォーマンスが向上しています 

* データの更新を完全にサポートできるよう、ACID は既定で有効になります。

* 向上した ACID 機能により、行レベルでの更新および削除が可能です。

* パフォーマンスのオーバーヘッドはありません。

* バケット処理は必要ありません。

* Spark は、Hive Warehouse Connector を介して、Hive ACID テーブルを読み書きできます。

詳しくは、[Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html) に関するページをご覧ください。

### <a name="apache-spark"></a>Apache Spark

Apache Spark は、Hive Warehouse Connector を使用して更新可能なテーブルと ACID トランザクションを取得します。 Hive Warehouse Connector を使用すると、Spark で外部テーブルとして Hive トランザクション テーブルを登録でき、完全なトランザクション機能にアクセスできます。 以前のバージョンでは、テーブル パーティションの操作のみがサポートされていました。 また、Hive Warehouse Connector は、Spark からトランザクションおよびストリーミング Hive テーブルの読み書きをストリーミングするための Streaming DataFrames もサポートしています。

Spark Executor は、Hive LLAP デーモンに直接接続してトランザクション方式でデータを取得および更新することができ、Hive でデータの制御を維持できます。

HDInsight 4.0 上の Apache Spark では、次のシナリオがサポートされています。

* レポートに使用されるのと同じトランザクション テーブルで機械学習モデルのトレーニングを実行します。
* ACID トランザクションを使用して、Spark ML から Hive テーブルに列を安全に追加します。
* Hive ストリーミング テーブルからの変更フィードで Spark ストリーミング ジョブを実行します。
* Spark Structured Streaming ジョブから直接、ORC ファイルを作成します。

誤って Spark から直接 Hive トランザクション テーブルにアクセスを試み、矛盾した結果、重複したデータ、データの破損が発生するのを心配する必要はもうありません。 HDInsight 4.0 では、Spark のテーブルと Hive のテーブルは、個別の metastore に保持されます。 Hive トランザクション テーブルを Spark 外部テーブルとして明示的に登録するには、Hive Data Warehouse Connector を使用します。

詳しくは、[Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html) に関するページをご覧ください。


### <a name="apache-oozie"></a>Apache Oozie

HDI 4.0 に含まれる Apache Oozie 4.3.1 では以下の点が変更されています。

* Oozie では、Hive アクションが実行されなくなりました。 Hive CLI は削除され、BeeLine に置き換えられています。

* **job.properties** ファイルに除外パターンを含めることによって、共有ライブラリから不要な依存関係を除外できます。

詳しくは、[Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html) に関するページをご覧ください。

## <a name="how-to-upgrade-to-hdinsight-40"></a>HDInsight 4.0 にアップグレードする方法

他のメジャー リリースと同様に、運用環境に最新バージョンを実装する前に、コンポーネントを徹底的にテストすることが重要です。 HDInsight 4.0 を指定してアップグレード プロセスを開始できますが、不慮の事故を防ぐため、HDInsight 3.6 が既定のオプションです。

以前のバージョンの HDInsight から HDInsight 4.0 へのアップグレード パスはサポートされていません。 metastore と BLOB のデータ形式が変更されたため、HDInsight 4.0 は以前のバージョンと互換性がありません。 新しい HDInsight 4.0 環境を現在の運用環境から分離しておくことが重要です。 HDInsight 4.0 を現在の環境にデプロイする場合、metastore がアップグレードされて、元に戻すことはできません。  

## <a name="limitations"></a>制限事項

* HDInsight 4.0 では、Apache Hive 用 MapReduce はサポートされていません。 代わりに Apache Tez を使用してください。 詳しくは、[Apache Tez](https://tez.apache.org/) に関するページをご覧ください。
* HDInsight 4.0 では、Apache Storm はサポートされていません。 
* HDInsight 4.0 では、Hive ビューは使用できなくなります。 
* Apache Zeppelin のシェル インタープリターは、Spark および対話型クエリ クラスターではサポートされていません。
* Spark-LLAP クラスターでは LLAP を "*無効にする*" ことはできません。 LLAP をオフにすることだけができます。
* Azure Data Lake Storage Gen2 では、Jupyter Notebook を Spark クラスターに保存できません。

## <a name="next-steps"></a>次の手順

* [Azure HDInsight のドキュメント](index.yml)
* [リリース ノート](hdinsight-release-notes.md)
