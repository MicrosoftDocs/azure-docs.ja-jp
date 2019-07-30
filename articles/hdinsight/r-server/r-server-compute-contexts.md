---
title: HDInsight 上の ML Services 向けのコンピューティング コンテキスト オプション - Azure
description: HDInsight の ML Services でユーザーが使用できるさまざまなコンピューティング コンテキスト オプションについて説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: a2c66c5c4f1abe535eb51dba9101757ce6d26157
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444342"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>HDInsight 上の ML Services 向けのコンピューティング コンテキスト オプション

Azure HDInsight 上の ML Services は、コンピューティング コンテキストを設定することによって呼び出しの実行方法を制御します。 この記事では、HDInsight クラスターやエッジ ノードの複数のコア間で実行を並列化するかどうかとその方法を指定する際に利用できるオプションについて説明します。

クラスターのエッジ ノードは、クラスターへの接続と R スクリプトの実行に便利な場所です。 エッジ ノードでは、エッジ ノード サーバーのコア間で、RevoScaleR の並列化された分散関数を実行できます。 また、RevoScaleR の Hadoop Map Reduce または Apache Spark コンピューティング コンテキストを使用して、クラスターのノード間でこれらの関数を実行することもできます。

## <a name="ml-services-on-azure-hdinsight"></a>Azure HDInsight 上の ML Services
[Azure HDInsight の ML Services](r-server-overview.md) は、R ベースの分析を行うための最新の機能を備えています。 Apache Hadoop HDFS 内の [Azure BLOB](../../storage/common/storage-introduction.md "Azure Blob Storage") ストレージ アカウントのコンテナー、Data Lake Store、またはローカルの Linux ファイル システムに格納されているデータを使用します。 ML Services はオープン ソース R を基盤としているため、自ら構築する R ベースのアプリケーションで 8,000 を超えるオープン ソース R パッケージを活用できます。 また、[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler) (ML Services に付属する Microsoft のビッグ データ分析パッケージ) のルーチンも使用できます。  

## <a name="compute-contexts-for-an-edge-node"></a>エッジ ノードに対する計算コンテキスト
一般に、エッジ ノードの ML Services で実行される R スクリプトは、そのノードの R インタープリター内で実行されます。 RevoScaleR 関数を呼び出すステップは例外です。 RevoScaleR 呼び出しは、RevoScaleR コンピューティング コンテキストの設定方法によって決定されるコンピューティング環境で実行されます。  エッジ ノードから R スクリプトを実行する際に設定可能なコンピューティング コンテキストの値は次のとおりです。

- local sequential (*local*)
- local parallel (*localpar*)
- Map Reduce
- Spark

オプション *local* と *localpar* の違いは、**rxExec** 呼び出しを実行する方法のみです。 RevoScaleR の **numCoresToUse** オプションの使用を通じて別途指定されている (`rxOptions(numCoresToUse=6)` など) 場合を除き、どちらも、他の rx 関数呼び出しは使用可能なすべてのコアで並列に実行します。 並列実行は、パフォーマンスの面で最も有利なオプションです。

次の表は、呼び出しの実行方法を設定する各種コンピューティング コンテキスト オプションをまとめたものです。

| 計算コンテキスト  | 設定方法                      | 実行コンテキスト                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Local sequential | rxSetComputeContext('local')    | エッジ ノード サーバーのコアの実行の並列化 (順次実行される rxExec 呼び出しを除く) |
| Local parallel   | rxSetComputeContext('localpar') | エッジ ノード サーバーのコアの実行の並列化 |
| Spark            | RxSpark()                       | HDI クラスターのノード間での、Spark を介した分散実行の並列化 |
| Map Reduce       | RxHadoopMR()                    | HDI クラスターのノード間での Map Reduce を介した分散実行の並列化 |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>コンピューティング コンテキストの決定に関するガイドライン

並列実行の 3 つの方法のうち、どれを選ぶかは、分析作業の性質、データのサイズ、データの場所によって決まります。 どのコンピューティング コンテキストを使用すればよいかをはっきり示す基準はありません。 ただし、適切なものを選択したり、少なくともベンチマークの実行前に選択肢を絞り込んだりするのに役立つ、いくつかの基本原則はあります。 これらの基本原則を次に示します。

- ローカルの Linux ファイル システムは HDFS より処理が高速です。
- データがローカルで XDF にある場合、繰り返しの分析の処理が速くなります。
- テキスト データ ソースから少量のデータをストリーミングすることをお勧めします。 データ量が多い場合は、分析の前に XDF に変換してください。
- 分析用のエッジ ノードにデータをコピーまたはストリーミングする際に生じるオーバーヘッドは、データ量が非常に多いと管理できなくなります。
- ApacheSpark は、Hadoop での分析については Map Reduce より高速です。

これらの原則を考慮したうえで、コンピューティング コンテキストを選択するための、いくつかの一般的な経験則を以降のセクションで取り上げます。

### <a name="local"></a>ローカル
* 分析するデータが少量で、繰り返し分析が必要ない場合は、データを分析ルーチンに直接ストリーミングして、*local* または *localpar* を使用します。
* 分析するデータが少量または中規模の量で、繰り返し分析が必要である場合は、データをローカル ファイル システムにコピーして XDF にインポートし、*local* または *localpar* を使用して分析します。

### <a name="apache-spark"></a>Apache Spark
* 分析するデータが大量である場合、**RxHiveData** または **RxParquetData** を使用して Spark データフレームにインポートするか、ストレージに問題がなければ、HDFS の XDF にインポートし、Spark コンピューティング コンテキストを使用して分析を行います。

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop Map Reduce
* Map Reduce コンピューティング コンテキストは、一般に処理が低速になるため、Spark コンピューティング コンテキストを使用して解決不可能な問題が発生した場合のみ使用してください。  

## <a name="inline-help-on-rxsetcomputecontext"></a>rxSetComputeContext のインライン ヘルプ
RevoScaleR コンピューティング コンテキストの詳細と例については、次のようにして rxSetComputeContext メソッドの R のインライン ヘルプを参照してください。

    > ?rxSetComputeContext

「[Machine Learning Server documentation (Machine Learning Server ドキュメント)](https://docs.microsoft.com/machine-learning-server/)」の「[Distributed computing overview (分散コンピューティングの概要)](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing)」にも関連情報があります。

## <a name="next-steps"></a>次の手順
この記事では、HDInsight クラスターやエッジ ノードの複数のコア間で実行を並列化するかどうかとその方法を指定する際に利用できるオプションについて説明しました。 HDInsight クラスターで ML Services を使用する方法の詳細については、次のトピックを参照してください。

* [Apache Hadoop 向け ML サービスの概要](r-server-overview.md)
* [HDInsight 上の ML サービス向けの Azure Storage オプション](r-server-storage.md)

