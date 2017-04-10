---
title: "Azure Data Lake Store Hive のパフォーマンス チューニング ガイドライン | Microsoft Docs"
description: "Azure Data Lake Store Hive のパフォーマンス チューニング ガイドライン"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: ab688288c7ae0d6bebdff5156fad6f7f5e9b4224
ms.openlocfilehash: e10bf8f7cbae2b81d22823ff74fe652c6bcb2da3
ms.lasthandoff: 02/07/2017


---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-store"></a>HDInsight と Azure Data Lake Store の Hive のパフォーマンス チューニング ガイダンス

既定の設定は、多種多様なユース ケースで適切なパフォーマンスを提供するように設定されています。  I/O 集中型のクエリでは、ADLS を使用してパフォーマンスを向上させるように Hive をチューニングできます。  

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Store アカウント**。 このアカウントを作成する手順については、「 [Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)
* Data Lake Store アカウントにアクセスできる **Azure HDInsight クラスター**。 [Data Lake Store を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。
* **HDInsight での Hive の実行**。  HDInsight での Hive ジョブの実行については、[HDInsight での Hive の使用に関するページ] (https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-use-hive) を参照してください。
* **ADLS のパフォーマンス チューニング ガイドライン**。  一般的なパフォーマンスの概念については、「[Data Lake Store のパフォーマンス チューニング ガイドライン](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)」を参照してください。

## <a name="parameters"></a>parameters

ADLS のパフォーマンスを向上させるためにチューニングする重要な設定を次に示します。

* **hive.tez.container.size** – 各タスクで使用されるメモリの量

* **tez.grouping.min-size** – 各マッパーの最小サイズ

* **tez.grouping.max-size** – 各マッパーの最大サイズ

* **hive.exec.reducer.bytes.per.reducer** – 各レジューサーのサイズ

**hive.tez.container.size** - このコンテナーのサイズは、各タスクで使用可能なメモリの量を決定します。  これは、Hive での同時実行を制御するための主要な入力です。  

**tez.grouping.min-size** – このパラメーターを使用して、各マッパーの最小サイズを設定できます。  Tez が選択したマッパーの数がこのパラメーターの値よりも小さい場合、Tez はここに設定された値を使用します。  

**tez.grouping.max-size** – このパラメーターを使用して、各マッパーの最大サイズを設定できます。  Tez が選択したマッパーの数がこのパラメーターの値よりも大きい場合、Tez はここに設定された値を使用します。  

**hive.exec.reducer.bytes.per.reducer** – このパラメーターは、各レジューサーのサイズを設定します。  既定では、各レジューサーは 256 MB です。  

## <a name="guidance"></a>ガイダンス

**hive.exec.reducer.bytes.per.reducer の設定** – データが圧縮されていない場合は、既定値で問題ありません。  圧縮されているデータの場合は、レジューサーのサイズを小さく必要があります。  

**hive.tez.container.size の設定** – 各ノードのメモリは yarn.nodemanager.resource.memory-mb によって指定されるため、既定で HDI クラスターに適切に設定されます。  YARN で適切なメモリを設定する方法の詳細については、こちらの[投稿](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom)を参照してください。

I/O 集中型のワークロードでは、Tez コンテナーのサイズの削減による並列処理の増加からメリットを得ることができます。 これにより、コンテナーの数が増え、同時実行性が高まります。  ただし、一部の Hive クエリでは、大量のメモリ が必要です (例: MapJoin)。  タスクに十分なメモリがない場合は、実行時にメモリ不足例外が発生します。  メモリ不足例外が発生した場合は、メモリを増やす必要があります。   

実行される同時実行タスクの数または並列処理は、YARN メモリの総量によって制限されます。  YARN コンテナーの数は、実行できる同時実行タスクの数を決定します。  ノードごとの YARN メモリを確認するには、Ambari を参照することができます。  YARN に移動し、[Configs (構成)] タブを表示します。  YARN メモリは、このウィンドウに表示されます。  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
ADLS を使用してパフォーマンスを向上させる鍵は、同時実行性をできるだけ高くすることです。  作成する必要があるタスクの数は Tez が自動的に計算するため、設定する必要はありません。   

## <a name="example-calculation"></a>計算例

たとえば、8 ノードの D14 クラスターがあるとします。  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>制限事項
**ADLS の調整** 

ADLS によって提供される帯域幅の限界に達すると、タスクの失敗が発生し始めます。 このことは、タスク ログの調整エラーを監視することで確認できます。  コンテナーのサイズを増やすことで、並列処理を軽減できます。  ジョブのためにより高い同時実行性が必要な場合は、お問い合わせください。   

調整されているかどうかを確認するには、クライアント側でデバッグ ログを有効にする必要があります。 その方法は次のとおりです。

1. 次のプロパティを Hive 構成の log4j プロパティに置きます。 これは、Ambari ビュー: log4j.logger.com.microsoft.azure.datalake.store=DEBUG から実行できます。この構成を有効にするには、すべてのノード/サービスを再起動します。

2. 調整されている場合は、Hive ログ ファイルに HTTP 429 のエラー コードが表示されます。 Hive ログ ファイルは /tmp/&lt;user&gt;/hive.log にあります。

## <a name="further-information-on-hive-tuning"></a>Hive のチューニングに関する他の情報

Hive クエリをチューニングする際に役立つ、いくつかのブログを次に示します。
* [HDInsight の Hadoop に対する Hive クエリの最適化](https://azure.microsoft.com/en-us/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Hive クエリ パフォーマンスのトラブルシューティング](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [HDInsight での Hive の最適化に関する刺激的なトーク](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)

