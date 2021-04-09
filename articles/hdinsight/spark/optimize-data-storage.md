---
title: Apache Spark のデータ ストレージを最適化する - Azure HDInsight
description: Azure HDInsight で Apache Spark が使用するデータ ストレージを最適化する方法について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 10f99bdc4a5d418ae1b432a6799c5979e473c5ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98926987"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Apache Spark のデータ ストレージの最適化

この記事では、Azure HDInsight で効率的に Apache Spark ジョブを実行するためにデータ ストレージを最適化する戦略について説明します。

## <a name="overview"></a>概要

Spark では、csv、json、xml、parquet、orc、avro など、多くの形式がサポートされています。 Spark は、外部データ ソースを使用して他の多くの形式をサポートするように拡張できます。詳細については、[Apache Spark パッケージ](https://spark-packages.org)を参照してください。

パフォーマンスのために最適な形式は *Snappy で圧縮* した Parquet であり、これが Spark 2.x の既定値です。 Parquet はデータを列形式で格納し、Spark で高度に最適化されています。

## <a name="choose-data-abstraction"></a>データ抽象化の選択

以前のバージョンの Spark では、データの抽象化に RDD が使用されていましたが、Spark 1.3 と 1.6 では、DataFrames と DataSets がそれぞれ導入されました。 次の相対的な利点を考慮してください。

* **DataFrames**
    * ほとんどの状況で最適な選択肢。
    * Catalyst を介してクエリを最適化。
    * ステージ全体のコード生成。
    * ダイレクト メモリ アクセス
    * ガベージ コレクション (GC) のオーバーヘッドが低い。
    * コンパイル時のチェックやドメイン オブジェクトのプログラミングがないため、開発者にとっては DataSets ほど使いやすくない。
* **DataSets**
    * パフォーマンスへの影響が許容範囲内である複雑な ETL パイプラインに適している。
    * パフォーマンスへの影響が非常に大きい可能性のある集計には適さない。
    * Catalyst を介してクエリを最適化。
    * ドメイン オブジェクトのプログラミングとコンパイル時のチェックを提供することにより、開発者にっとって使いやすい。
    * シリアル化/逆シリアル化のオーバーヘッドを追加。
    * GC のオーバーヘッドが高い。
    * ステージ全体のコード生成を中断する。
* **RDD**
    * 新しいカスタム RDD を構築する必要がある場合を除き、RDD を使用する必要がない。
    * Catalyst を介したクエリ最適化がない。
    * ステージ全体のコード生成がない。
    * GC のオーバーヘッドが高い。
    * Spark 1.x のレガシ API を使用する必要がある。

## <a name="select-default-storage"></a>既定のストレージの選択

新しい Spark クラスターを作成する際は、クラスターの既定のストレージとして Azure Blob Storage または Azure Data Lake Storage を選択することができます。 どちらのオプションにも一時的なクラスター用の長期ストレージの利点があります。 そのため、クラスターを削除しても、データは自動的には削除されません。 一時的なクラスターを再作成して、引き続きデータにアクセスできます。

| ストアの種類 | ファイル システム | 速度 | 一時的 | 例 |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:** //url/ | **Standard** | はい | 一時的なクラスター |
| Azure Blob Storage (セキュア) | **wasbs:** //url/ | **Standard** | はい | 一時的なクラスター |
| Azure Data Lake Storage Gen 2| **abfs:** //url/ | **より高速** | はい | 一時的なクラスター |
| Azure Data Lake Storage Gen 1| **adl:** //url/ | **より高速** | はい | 一時的なクラスター |
| ローカルの HDFS | **hdfs:** //url/ | **最も高速** | いいえ | 24 時間 365 日の対話型クラスター |

ストレージ オプションの詳細については、「[Azure HDInsight クラスターで使用するストレージ オプションを比較する](../hdinsight-hadoop-compare-storage-options.md)」を参照してください。

## <a name="use-the-cache"></a>キャッシュの使用

Spark は、`.persist()`、`.cache()`、`CACHE TABLE` などのさまざまな方法で使用できる、独自のネイティブ キャッシュ メカニズムを備えています。 このネイティブ キャッシュは、小さいデータ セットと、中間結果をキャッシュする必要がある ETL パイプラインで有効です。 ただし、Spark ネイティブ キャッシュは、現在のところパーティション分割では適切に機能しません。キャッシュされたテーブルがパーティション分割のデータを保持しないためです。 より汎用的で信頼性の高いキャッシュの手法は、*ストレージ レイヤーのキャッシュ* です。

* Spark のネイティブ キャッシュ (非推奨)
    * 小さいデータセットに適している。
    * パーティション分割では機能しない (Spark の将来の リリースで変更の可能性あり)。

* ストレージ レベルのキャッシュ (推奨)
    * [IO キャッシュ](apache-spark-improve-performance-iocache.md)機能を使用して HDInsight に実装できます。
    * メモリ内と SSD のキャッシュを使用。

* ローカルの HDFS (推奨)
    * `hdfs://mycluster` パス。
    * SSD のキャッシュを使用。
    * クラスターを削除するとキャッシュされたデータが失われるため、キャッシュの再構築が必要。

## <a name="optimize-data-serialization"></a>データのシリアル化の最適化

Spark ジョブは分散されるため、パフォーマンスを最適にするためには適切なデータのシリアル化が重要です。  Spark には 2 つのシリアル化のオプションがあります。

* Java シリアル化は既定値です。
* `Kryo` シリアル化は新しい形式であり、シリアル化が Java よりも高速で、よりコンパクトになる可能性があります。  `Kryo` ではプログラムでクラスを登録する必要があり、まだシリアル化可能なすべての種類がサポートされているわけではありません。

## <a name="use-bucketing"></a>バケットの使用

バケットは、データのパーティション分割に似ています。 ただし、各バケットは、1 つだけではなく一連の列の値を保持できます。 この方法は、製品識別子などの大量 (数百万以上) の値でパーティション分割する場合に適しています。 バケットは、行のバケット キーをハッシュすることで決定されます。 バケット化したテーブルは、バケット化と並べ替えの方法についてのメタデータを格納するため、固有の最適化を提供します。

いくつかの高度なバケット機能を、次に示します。

* バケットのメタ情報に基づくクエリの最適化
* 最適化された集計。
* 最適化された結合

パーティション分割とバケットは同時に使用することができます。

## <a name="next-steps"></a>次のステップ

* [Apache Spark のデータ処理を最適化する](optimize-cluster-configuration.md)
* [Apache Spark 用にメモリ使用量を最適化する](optimize-memory-usage.md)
* [Apache Spark のクラスター構成を最適化する](optimize-cluster-configuration.md)
