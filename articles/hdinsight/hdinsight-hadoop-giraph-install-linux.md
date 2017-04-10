---
title: "Linux ベースの HDInsight (Hadoop) に Giraph をインストールして使用する | Microsoft Docs"
description: "スクリプト アクションを使用して Linux ベースの HDInsight クラスターに Giraph をインストールする方法について説明します。 スクリプト アクションでは、クラスター構成を変更するか、サービスとユーティリティをインストールすることで、クラスターを作成時にカスタマイズできます。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9fcac906-8f06-4002-9fe8-473e42f8fd0f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: ff27749800319517a8f635530f0f16b928692575
ms.lasthandoff: 03/25/2017


---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>HDInsight Hadoop クラスターに Giraph をインストールし、Giraph を使用して大規模なグラフを処理する

**Script Action** を使用してクラスターをカスタマイズして、Azure HDInsight の Hadoop の任意の種類のクラスターに Giraph をインストールできます。

このトピックでは、Script Action を使用して Giraph をインストールする方法を学習します。 Giraph をインストールした後は、大規模なグラフを処理する多くの一般的なアプリケーションで Giraph を使用する方法についても説明します。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a name="whatis"></a>Giraph とは

[Apache Giraph](http://giraph.apache.org/) は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。 グラフでは、オブジェクト間の関係 (インターネットのような大規模ネットワークでのルーター間の接続など) や、ソーシャル ネットワークでの人々の関係 (ソーシャル グラフとも呼ばれる) をモデル化します。 グラフの処理により、次のようなグラフ内のオブジェクト間の関係について推論できます。

* 現在の関係に基づいて潜在的な友人を識別する

* ネットワーク内の 2 台のコンピューター間の最短ルートを識別する

* Web ページのページ順位を計算する

> [!WARNING]
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
> 
> Giraph といったカスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) などのプロジェクト サイトもあります。


## <a name="what-the-script-does"></a>スクリプトの機能

このスクリプトは、次のアクションを実行します。

* Giraph を `/usr/hdp/current/giraph` にインストールする。

* `/example/jars/giraph-examples.jar` のように、`giraph-examples.jar` ファイルをクラスターの既定のストレージ (WASB) にコピーする。

## <a name="install"></a>スクリプト アクションを使用した Giraph のインストール

HDInsight クラスターに Giraph をインストールするサンプル スクリプトは、次の場所にあります。

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

このセクションでは、Azure ポータルを使用してクラスターを作成する際に、サンプル スクリプトを使用する方法について説明します。 

> [!NOTE]
> スクリプト アクションは、Azure PowerShell、Azure CLI、HDInsight .NET SDK、または Azure Resource Manager のテンプレートを使用して適用することもできます。 既に実行しているクラスターにスクリプト アクションを適用することもできます。 詳細については、 [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関する記事を参照してください。

1. [Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)に関するページに記載されている手順を使用して、クラスターの作成を開始します。ただし、作成を完了しないでください。

2. **[オプションの構成]** ブレードで **[スクリプト アクション]** を選択し、以下の情報を指定します。
   
   * **[名前]**: スクリプト アクションの表示名を入力します。

   * **[スクリプト URI]**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **[ヘッド]**: このオプションをオンにします。

   * **[ワーカー]**: オフのままにします。

   * **[ZOOKEEPER]**: オフのままにします。

   * **[パラメーター]**: このフィールドは空のままにします。

3. **[スクリプト アクション]** の下部で、**[選択]** を使用して構成を保存します。 最後に、**[オプションの構成]** ブレードの下部にある **[選択]** を使用して、オプションの構成情報を保存します。

4. 「[Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」の説明に従って、クラスターの作成を続行します。

## <a name="usegiraph"></a>HDInsight で Giraph を使用する方法

クラスターの作成が完了したら、次の手順を使用して、Giraph に含まれているサンプル SimpleShortestPathsComputation を実行します。 ここでは、グラフのオブジェクト間の最短パスを見つけるための基本的な [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) 実装を使用します。

1. SSH を使用して HDInsight クラスターに接続します。
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを使用して、**tiny_graph.txt** という名前の新しいファイルを作成します。
   
    ```
    nano tiny_graph.txt
    ```
   
    このファイルの内容として以下を使用します。
   
    ```
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```
   
    このデータは、`[source_id, source_value,[[dest_id], [edge_value],...]]` の形式を使用して、有向グラフ内のオブジェクト間の関係を示しています。 各行は、`source_id` オブジェクトと 1 つ以上の `dest_id` オブジェクトとの関係を表現しています。 `edge_value` (重み) は、`source_id` と `dest\_id` の間のつながりの強さまたは距離であると考えられます。
   
    この値 (重み) を使用し、オブジェクト間の距離に応じて線を引くと、先のデータは次の図のようになります。
   
    ![円で表した tiny_graph.txt (線はオブジェクト間の距離)](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. ファイルを保存するには、**Ctrl キーを押しながら X キー**を押し、**Y** キー、**Enter** キーの順に押してファイル名を確定します。

4. 次のコマンドを使用して、HDInsight クラスターのプライマリ ストレージにデータを格納します。
   
    ```
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. 次のコマンドを使用して、サンプル SimpleShortestPathsComputation を実行します。
   
    ```
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```
   
    このコマンドで使用されるパラメーターを次の表に示します。
   
   | パラメーター | 実行内容 |
   | --- | --- |
   | `jar /usr/hdp/current/giraph/giraph-examples.jar` |サンプルを含む jar ファイル。 |
   | `org.apache.giraph.GiraphRunner` |サンプルを開始するために使用するクラス。 |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |使用される例。 この場合は、ID 1 とグラフ内の他のすべての ID の間の最短パスを計算します。 |
   | `-ca mapred.job.tracker=headnodehost:9010` |クラスターのヘッド ノード。 |
   | `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` |入力データに使用する入力形式。 |
   | `-vip /example/data/tiny_graph.txt` |入力データ ファイル。 |
   | `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` |出力形式。 この場合は、ID と値をプレーンテキストとして出力します。 |
   | `-op /example/output/shortestpaths` |出力場所。 |
   | `-w 2` |使用する worker の数。 この場合は 2 です。 |
   
    これらのパラメーターと Giraph サンプルで使用されるその他のパラメーターの詳細については、 [Giraph のクイックスタート](http://giraph.apache.org/quick_start.html)のページを参照してください。

6. ジョブが完了すると、結果が **/example/out/shotestpaths** ディレクトリに格納されます。 出力ファイルの名前の先頭は **part-m-**、末尾はファイルの順番を示す数字です。 次のコマンドを使用して、出力を表示します。
   
    ```
    hdfs dfs -text /example/output/shortestpaths/*
    ```
   
    出力は次のようになります。
   
        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0
   
    SimpleShortestPathComputation サンプルは、オブジェクト ID 1 から開始して他のオブジェクトへの最短パスを見つけるようにハードコーディングされています。 したがって、出力は `destination_id distance` として読み取られます。distance はオブジェクト ID 1 とターゲット ID の間を結ぶエッジの値 (重み) です。
   
    これを視覚化して、ID 1 と他のすべてのオブジェクトの間で最短パスを結ぶことにより、結果を検証できます。 ID 1 と ID 4 の最短パスは 5 です。 これは、<span style="color:orange">ID 1 と 3</span>、<span style="color:red">ID 3 と 4</span> を結ぶ距離の合計です。
   
    ![オブジェクトを円で表し、最短パスで結んだ図](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>次のステップ

* [HDInsight クラスターに Hue をインストールして使用する](hdinsight-hadoop-hue-linux.md)。 Hue は、Pig ジョブや Hive ジョブの作成、実行、保存や、HDInsight クラスターの既定のストレージの参照を容易にする Web UI です。

* [HDInsight Hadoop クラスターに R をインストールする](hdinsight-hadoop-r-scripts-linux.md)。HDInsight Hadoop クラスターに R をインストールして使用するためのクラスター カスタマイズの使用法に関する手順が説明されています。 R は、統計計算用のオープン ソースの言語および環境です。 R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。 また、広範なグラフィカル機能も提供します。

* [HDInsight クラスターに Solr をインストールする](hdinsight-hadoop-solr-install-linux.md)。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Solr をインストールします。 Solr は、格納されたデータに対して強力な検索操作を実行することができます。


