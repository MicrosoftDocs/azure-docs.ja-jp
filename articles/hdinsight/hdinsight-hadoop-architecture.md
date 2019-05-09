---
title: Apache Hadoop のアーキテクチャ - Azure HDInsight
description: HDInsight クラスター上の Apache Hadoop ストレージおよび処理について説明します。
services: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 9f45b1603fe0e34bfdf6192fd85ecaf27311ae32
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718079"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>HDInsight の Apache Hadoop アーキテクチャ

[Apache Hadoop](https://hadoop.apache.org/) には、ストレージを提供する [HDFS (Apache Hadoop 分散ファイル システム)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) と、処理を提供する [YARN (Apache Hadoop Yet Another Resource Negotiator)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) の 2 つのコア コンポーネントが含まれています。 ストレージおよび処理機能により、クラスターは、目的のデータ処理を実行するための [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) プログラムを実行できるようになります。

> [!NOTE]  
> HDFS は通常、ストレージを提供するために、HDInsight クラスター内にはデプロイされません。 代わりに、HDFS と互換性のあるインターフェイス レイヤーが Hadoop コンポーネントによって使用されます。 実際のストレージ機能は、Azure Storage または Azure Data Lake Storage のどちらかによって提供されます。 Hadoop の場合、HDInsight クラスター上で実行されている MapReduce ジョブは HDFS が存在するかのように実行されるため、そのストレージ ニーズをサポートするための変更は必要ありません。 HDInsight 上の Hadoop では、ストレージはアウトソーシングされますが、YARN 処理はコア コンポーネントのままです。 詳細については、[Azure HDInsight の概要](hadoop/apache-hadoop-introduction.md)に関するページをご覧ください。

この記事では、YARN と、それが HDInsight 上でのアプリケーションの実行をどのように調整するかを紹介します。

## <a name="apache-hadoop-yarn-basics"></a>Apache Hadoop YARN の基本 

YARN は、Hadoop でのデータ処理を管理および調整します。 YARN には、クラスター内のノード上のプロセスとして実行される 2 つのコア サービスがあります。 

* ResourceManager 
* NodeManager

ResourceManager は、MapReduce ジョブなどのアプリケーションにクラスター コンピューティング リソースを付与します。 ResourceManager は、これらのリソースをコンテナーとして付与します。ここで、各コンテナーは CPU コアと RAM メモリの割り当てで構成されています。 クラスター内で使用可能なすべてのリソースを結合した後、コアとメモリをブロック単位で分散させた場合は、リソースの各ブロックがコンテナーになります。 クラスター内の各ノードには特定の数のコンテナー用の容量があるため、クラスターで使用可能なコンテナーの数には固定された制限があります。 コンテナー内のリソースの割り当ては構成可能です。 

MapReduce アプリケーションがクラスター上で実行されると、ResourceManager は、そのアプリケーションに実行するためのコンテナーを提供します。 ResourceManager は、実行中のアプリケーションの状態や使用可能なクラスター容量を追跡すると共に、アプリケーションが完了し、そのリソースを解放するのを追跡します。 

ResourceManager はまた、アプリケーションの状態を監視するために使用できる Web ユーザー インターフェイスを提供する Web サーバー プロセスも実行します。

ユーザーがクラスター上で実行される MapReduce アプリケーションを送信すると、そのアプリケーションは ResourceManager に送信されます。 ResourceManager は次に、使用可能な NodeManager ノード上のコンテナーを割り当てます。 NodeManager ノードは、そのアプリケーションが実際に実行される場所です。 最初に割り当てられたコンテナーは、ApplicationMaster と呼ばれる特殊なアプリケーションを実行します。 この ApplicationMaster は、送信されたアプリケーションの実行に必要なリソースを (以降のコンテナーの形式で) 取得することに責任を負います。 ApplicationMaster は、アプリケーションの段階 (Map 段階、Reduce 段階など) や、処理する必要があるデータ量の要因を検証します。 ApplicationMaster はその後、アプリケーションの代わりに ResourceManager にリソースを要求 (*ネゴシエート*) します。 ResourceManager は次に、クラスター内の NodeManagers からのリソースを、アプリケーションの実行に使用できるように ApplicationMaster に付与します。 

NodeManagers は、アプリケーションを構成するタスクを実行した後、それらの進行状況と状態を元の ApplicationMaster に報告します。 ApplicationMaster は次に、アプリケーションの状態を元の ResourceManager に報告します。 ResourceManager は、結果をすべてクライアントに返します。

## <a name="yarn-on-hdinsight"></a>HDInsight 上の YARN

すべての HDInsight クラスターの種類が YARN をデプロイします。 ResourceManager は、それぞれクラスター内の最初と 2 番目のヘッド ノードで実行されるプライマリおよびセカンダリ インスタンスを備えた高可用性でデプロイされます。 一度には ResourceManager の 1 つのインスタンスだけがアクティブになります。 NodeManager インスタンスは、クラスター内の使用可能なワーカー ノードにまたがって実行されます。

![HDInsight 上の YARN](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>次の手順

* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hadoop/hdinsight-use-mapreduce.md)
* [Azure HDInsight の概要](hadoop/apache-hadoop-introduction.md)
