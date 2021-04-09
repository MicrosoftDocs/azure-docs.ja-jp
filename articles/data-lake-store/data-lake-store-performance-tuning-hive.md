---
title: パフォーマンス チューニング - Azure Data Lake Storage Gen1 での Hive
description: HdInsight での Hive と Azure Data Lake Storage Gen1 のパフォーマンス チューニングについて説明します。 I/O 集中型のクエリでは、パフォーマンスが向上するように Hive をチューニングします。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 082d684ed0a29cb6bf2de9c506886b6d98cf174a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723783"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight の Hive と Azure Data Lake Storage Gen1 のパフォーマンス チューニング ガイダンス

既定の設定は、多種多様なユース ケースで適切なパフォーマンスを提供するように設定されています。  I/O 集中型クエリの場合、Hive は Azure Data Lake Storage Gen1 でパフォーマンスが高くなるように調整できます。  

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Data Lake Storage Gen1 アカウント**。 これを作成する手順については、[Azure Data Lake Storage Gen1 の使用開始](data-lake-store-get-started-portal.md)に関するページを参照してください。
* Data Lake Storage Gen1 アカウントにアクセスできる **Azure HDInsight クラスター**。 [Data Lake Storage Gen1 を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。
* **HDInsight での Hive の実行**。  HDInsight の Hive ジョブを実行する方法については、[HDInsight での Hive の使用](../hdinsight/hadoop/hdinsight-use-hive.md)に関する記事を参照してください。
* **Data Lake Storage Gen1 のパフォーマンス チューニング ガイドライン**。  一般的なパフォーマンスの概念については、[Data Lake Storage Gen1 のパフォーマンス チューニング ガイダンス](./data-lake-store-performance-tuning-guidance.md)を参照してください。

## <a name="parameters"></a>パラメーター

Data Lake Storage Gen1 のパフォーマンスを向上するためのチューニングに重要な設定を次に示します。

* **hive.tez.container.size** – 各タスクで使用されるメモリの量

* **tez.grouping.min-size** – 各マッパーの最小サイズ

* **tez.grouping.max-size** – 各マッパーの最大サイズ

* **hive.exec.reducer.bytes.per.reducer** – 各レジューサーのサイズ

**hive.tez.container.size** - このコンテナーのサイズは、各タスクで使用可能なメモリの量を決定します。  これは、Hive でのコンカレンシーを制御するための主要な入力です。  

**tez.grouping.min-size** – このパラメーターを使用して、各マッパーの最小サイズを設定できます。  Tez が選択したマッパーの数がこのパラメーターの値よりも小さい場合、Tez はここに設定された値を使用します。

**tez.grouping.max-size** – このパラメーターを使用して、各マッパーの最大サイズを設定できます。  Tez が選択したマッパーの数がこのパラメーターの値よりも大きい場合、Tez はここに設定された値を使用します。

**hive.exec.reducer.bytes.per.reducer** – このパラメーターは、各レジューサーのサイズを設定します。  既定では、各レジューサーは 256 MB です。  

## <a name="guidance"></a>ガイダンス

**hive.exec.reducer.bytes.per.reducer の設定** – データが圧縮されていない場合は、既定値で問題ありません。  圧縮されているデータの場合は、レジューサーのサイズを小さく必要があります。  

**hive.tez.container.size の設定** – 各ノードのメモリは yarn.nodemanager.resource.memory-mb によって指定されるため、既定で HDI クラスターに適切に設定されます。  YARN で適切なメモリを設定する方法の詳細については、こちらの[投稿](../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md)を参照してください。

I/O 集中型のワークロードでは、Tez コンテナーのサイズの削減による並列処理の増加からメリットを得ることができます。 これにより、コンテナーの数が増え、コンカレンシーが高まります。  ただし、一部の Hive クエリでは、大量のメモリ が必要です (例: MapJoin)。  タスクに十分なメモリがない場合は、実行時にメモリ不足例外が発生します。  メモリ不足例外が発生した場合は、メモリを増やす必要があります。   

実行される同時実行タスクの数または並列処理は、YARN メモリの総量によって制限されます。  YARN コンテナーの数は、実行できる同時実行タスクの数を決定します。  ノードごとの YARN メモリを確認するには、Ambari を参照することができます。  YARN に移動し、[Configs] \(構成) タブを表示します。YARN メモリは、このウィンドウに表示されます。  

> 合計 YARN メモリ = ノード数 * ノードあたり YARN メモリ、YARN コンテナー数 = 合計 YARN メモリ / Tez コンテナー サイズ

Data Lake Storage Gen1 を使用してパフォーマンスを向上させる鍵は、コンカレンシーをできるだけ高くすることです。  作成する必要があるタスクの数は Tez が自動的に計算するため、設定する必要はありません。   

## <a name="example-calculation"></a>計算例

たとえば、8 ノードの D14 クラスターがあるとします。  

> 合計 YARN メモリ = ノード数 * ノードあたり YARN メモリ、合計 YARN メモリ = 8 ノード * 96 GB = 768 GB、YARN コンテナー数 = 768 GB / 3072 MB = 256

## <a name="limitations"></a>制限事項

**Data Lake Storage Gen1 の調整** 

Data Lake Storage Gen1 によって提供される帯域幅の制限に達した場合は、タスクが失敗する可能性があります。 このことは、タスク ログで調整エラーを監視することで確認できます。  コンテナーのサイズを増やすことで、並列処理を軽減できます。  ジョブのためにより高いコンカレンシーが必要な場合は、お問い合わせください。

調整されているかどうかを確認するには、クライアント側でデバッグ ログを有効にする必要があります。 その方法は次のとおりです。

1. 次のプロパティを Hive 構成の log4j プロパティに置きます。これは、Ambari ビュー: log4j.logger.com.microsoft.azure.datalake.store=DEBUG から実行できます。この構成を有効にするには、すべてのノード/サービスを再起動します。

2. 調整されている場合は、Hive ログ ファイルに HTTP 429 のエラー コードが表示されます。 Hive ログ ファイルは /tmp/&lt;user&gt;/hive.log にあります。

## <a name="further-information-on-hive-tuning"></a>Hive のチューニングに関する他の情報

Hive クエリをチューニングする際に役立つ、いくつかのブログを次に示します。
* [HDInsight の Hadoop に対する Hive クエリの最適化](../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Azure HDInsight での Hive クエリ ファイルのエンコード](/archive/blogs/bigdatasupport/encoding-the-hive-query-file-in-azure-hdinsight)
* [HDInsight での Hive の最適化に関する刺激的なトーク](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)