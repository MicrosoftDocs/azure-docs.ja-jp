---
title: "Compute context options for R Server on HDInsight (HDInsight での R Server の計算コンテキストのオプション) | Microsoft Docs"
description: "HDInsight の R Server でユーザーが使用できるさまざまなコンピューティング コンテキスト オプションについて説明します。"
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 841e70fa3a80bbeb7e2281246bac2f99c0de899f
ms.openlocfilehash: 169743012b1f50d67d5eafdb279e706719752eb8
ms.lasthandoff: 11/22/2016


---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>HDInsight の R Server (プレビュー) の計算コンテキストのオプション
Azure HDInsight の Microsoft R Server は、R ベースの分析を行うための最新の機能を備えています。 HDFS 内の [Azure Blob](../storage/storage-introduction.md "Azure Blob Storage") ストレージ アカウントのコンテナー、Data Lake Store、またはローカルの Linux ファイル システムに格納されているデータを使用します。 R Server はオープン ソース R を基盤としているため、自ら構築する R ベースのアプリケーションで 8,000 を超えるオープン ソース R パッケージを活用できます。 また、 [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR")(R Server に付属する Microsoft のビッグ データ分析パッケージ) のルーチンも活用できます。  

クラスターのエッジ ノードは、クラスターへの接続と R スクリプトの実行に便利な場所です。 エッジ ノードでは、エッジ ノード サーバーのコア間で、ScaleR の並列化された分散関数を実行できます。 また、ScaleR の Hadoop Map Reduce または Spark コンピューティング コンテキストを使用して、クラスターのノード間でこれらの関数を実行することもできます。

## <a name="compute-contexts-for-an-edge-node"></a>エッジ ノードに対する計算コンテキスト
一般に、エッジ ノードの R Server で実行される R スクリプトは、そのノードの R インタープリター内で実行されます。 ScaleR 関数を呼び出すステップは例外です。 ScaleR 呼び出しは、ScaleR コンピューティング コンテキストの設定方法によって決定されるコンピューティング環境で実行されます。  エッジ ノードから R スクリプトを実行する際に設定可能なコンピューティング コンテキストの値は、local sequential ("local")、local parallel ("localpar")、Map Reduce、および Spark です。

オプション "local" と "localpar" の違いは、rxExec 呼び出しを実行する方法のみです。 ScaleR の numCoresToUse オプションの使用を通じて別途指定されている (rxOptions(numCoresToUse=6) など) 場合を除き、どちらも、他の rx 関数呼び出しは使用可能なすべてのコアで並列に実行します。 さまざまなコンピューティング コンテキスト オプションを以下にまとめました。

| 計算コンテキスト  | 設定方法                      | 実行コンテキスト                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Local sequential | rxSetComputeContext(‘local’)    | エッジ ノード サーバーのコアの実行の並列化 (順次実行される rxExec 呼び出しを除く) |
| Local parallel   | rxSetComputeContext(‘localpar’) | エッジ ノード サーバーのコアの実行の並列化 |
| Spark            | RxSpark()                       | HDI クラスターのノード間での、Spark を介した分散実行の並列化 |
| Map Reduce       | RxHadoopMR()                    | HDI クラスターのノード間での Map Reduce を介した分散実行の並列化 |

パフォーマンスの向上を目的として実行の並列化を行う場合は、3 つのオプションを利用できます。 どのオプションを選択するかは、分析作業の性質、データのサイズ、データの場所によって決まります。

## <a name="guidelines-for-deciding-on-a-compute-context"></a>コンピューティング コンテキストの決定に関するガイドライン
現時点では、どのコンピューティング コンテキストを使用すればよいかをはっきり示す基準はありません。 ただし、適切なものを選択したり、少なくともベンチマークの実行前に選択肢を絞り込んだりするのに役立つ、いくつかの基本原則はあります。 これらの基本原則を次に示します。

1. ローカルの Linux ファイル システムは HDFS より処理が高速です。
2. データがローカルで XDF にある場合、繰り返しの分析の処理が速くなります。
3. テキスト データ ソースから少量のデータをストリーミングすることをお勧めします。データ量が多い場合は、分析の前に XDF に変換してください。
4. 分析用のエッジ ノードにデータをコピーまたはストリーミングする際に生じるオーバーヘッドは、データ量が非常に多いと管理できなくなります。
5. Spark は、Hadoop での分析については Map Reduce より高速です。

これらの原則を考慮した上で、コンピューティング コンテキストを選択するための次のような一般的な経験則があります。

### <a name="local"></a>ローカル
* 分析するデータが少量で、繰り返し分析が必要ない場合は、データを分析ルーチンに直接ストリーミングして、"local" または "localpar" を使用します。
* 分析するデータが少量または中規模の量で、繰り返し分析が必要である場合は、データをローカル ファイル システムにコピーして XDF にインポートし、"local" または "localpar" を使用して分析します。

### <a name="hadoop-spark"></a>Hadoop Spark
* 分析するデータが大量である場合、RxHiveData または RxParquetData を使用して Spark データフレームにインポートするか、記憶域に問題がなければ、HDFS の XDF にインポートし、"Spark" を使用して分析を行います。

### <a name="hadoop-map-reduce"></a>Hadoop Map Reduce
* 一般に処理が低速になるため、Spark コンピューティング コンテキストを使用して解決不可能な問題が発生した場合のみ使用してください。  

## <a name="inline-help-on-rxsetcomputecontext"></a>rxSetComputeContext のインライン ヘルプ
ScaleR コンピューティング コンテキストの詳細と例については、次のようにして rxSetComputeContext メソッドの R のインライン ヘルプを参照してください。

    > ?rxSetComputeContext

[MSDN の R Server](https://msdn.microsoft.com/library/mt674634.aspx "R Server on MSDN") ライブラリで入手可能な [ScaleR 分散コンピューティング ガイド](https://msdn.microsoft.com/microsoft-r/scaler-distributed-computing)を参照することもできます。

## <a name="next-steps"></a>次のステップ
この記事では、R Server を含む新しい HDInsight クラスターの作成方法について説明しました。 また、SSH セッションからの R コンソールの基本的な使用方法についても説明しました。 これで、次の記事を参照して、HDInsight で R Server を使用する他の方法を確認できます。

* [R Server for Hadoop の概要](hdinsight-hadoop-r-server-overview.md)
* [R Server for Hadoop の使用](hdinsight-hadoop-r-server-get-started.md)
* [HDInsight へ RStudio Server を追加する (クラスター作成時に追加されていない場合)](hdinsight-hadoop-r-server-install-r-studio.md)
* [HDInsight の R Server 向けの Azure Storage オプション](hdinsight-hadoop-r-server-storage.md)


