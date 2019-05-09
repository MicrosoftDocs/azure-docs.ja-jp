---
title: HDInsight (Hadoop) に Giraph をインストールして使用する - Azure
description: スクリプト アクションを使用して Linux ベースの HDInsight クラスターに Giraph をインストールする方法について説明します。 スクリプト アクションでは、クラスター構成を変更するか、サービスとユーティリティをインストールすることで、クラスターを作成時にカスタマイズできます。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: a2f964915efda6ce83439c3c1970de58b0467456
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201723"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>HDInsight Hadoop クラスターに Apache Giraph をインストールし、Giraph を使用して大規模なグラフを処理する

HDInsight クラスターで Apache Giraph をインストールする方法について説明します。 HDInsight のスクリプト アクション機能では、bash スクリプトを実行してクラスターをカスタマイズできます。 スクリプトを使用して、クラスターの作成時および作成後にクラスターをカスタマイズできます。

> [!IMPORTANT]  
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a name="whatis"></a>Giraph とは

[Apache Giraph](https://giraph.apache.org/) は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。 グラフは、オブジェクト間のリレーションシップをモデル化します。 たとえば、インターネット、またはソーシャル ネットワーク上の人物間の関係など、大規模なネットワーク上のルーター間の接続をモデル化します。 グラフの処理により、次のようなグラフ内のオブジェクト間の関係について推論できます。

* 現在の関係に基づいて潜在的な友人を識別する

* ネットワーク内の 2 台のコンピューター間の最短ルートを識別する

* Web ページのページ順位を計算する

> [!WARNING]  
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> Giraph といったカスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 Microsoft サポートで問題を解決できる場合があります。 解決できない場合は、その技術に関して豊富な専門知識のあるオープン ソース コミュニティを参照する必要があります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)や [https://stackoverflow.com](https://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[https://apache.org](https://apache.org) に[Hadoop](https://hadoop.apache.org/) などのプロジェクト サイトもあります。


## <a name="what-the-script-does"></a>スクリプトの機能

このスクリプトは、次のアクションを実行します。

* Giraph を `/usr/hdp/current/giraph` にインストールする。

* `/example/jars/giraph-examples.jar` のように、`giraph-examples.jar` ファイルをクラスターの既定のストレージ (WASB) にコピーする。

## <a name="install"></a>スクリプト アクションを使用した Giraph のインストール

HDInsight クラスターに Giraph をインストールするサンプル スクリプトは、次の場所にあります。

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

このセクションでは、Azure ポータルを使用してクラスターを作成する際に、サンプル スクリプトを使用する方法について説明します。

> [!NOTE]  
> スクリプト アクションは、次の方法を使用して適用できます。
> * Azure PowerShell
> * Azure クラシック CLI
> * HDInsight .NET SDK
> * Azure Resource Manager のテンプレート
> 
> 既に実行しているクラスターにスクリプト アクションを適用することもできます。 詳細については、 [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関する記事を参照してください。

1. [Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)に関するページに記載されている手順を使用して、クラスターの作成を開始します。ただし、作成を完了しないでください。

2. **[オプションの構成]** セクションで **[スクリプト アクション]** を選択し、以下の情報を指定します。

   * **[名前]**: スクリプト アクションのフレンドリ名を入力します。

   * **[スクリプト URI]**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **[ヘッド]**: このエントリをオンにします。

   * **[ワーカー]**: このエントリはオフのままにします。

   * **[ZooKeeper]**: このエントリはオフのままにします。

   * **[パラメーター]**: このフィールドは空のままにします。

3. **[スクリプト アクション]** の下部で、**[選択]** を使用して構成を保存します。 最後に、**[オプションの構成]** セクションの下部にある **[選択]** を使用して、オプションの構成情報を保存します。

4. 「[Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」の説明に従って、クラスターの作成を続行します。

## <a name="usegiraph"></a>HDInsight で Giraph を使用する方法

クラスターが作成されたら、次の手順を使用して、Giraph に含まれているサンプル SimpleShortestPathsComputation を実行します。 この例では、グラフのオブジェクト間の最短パスを見つけるための基本的な [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) 実装を使用します。

1. SSH を使用して HDInsight クラスターに接続します。

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを使用して、**tiny_graph.txt** という名前のファイルを作成します。

    ```bash
    nano tiny_graph.txt
    ```

    このファイルの内容として、次のテキストを使用します。

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    このデータは、`[source_id, source_value,[[dest_id], [edge_value],...]]` の形式を使用して、有向グラフ内のオブジェクト間の関係を示しています。 各行は、`source_id` オブジェクトと 1 つ以上の `dest_id` オブジェクトとの関係を表現しています。 `edge_value` は、`source_id` と `dest\_id` の間のつながりの強さまたは距離であると考えられます。

    この値 (重み) を取り出し、オブジェクト間の距離として使用すると、データは次の図のようになります。

    ![円で表した tiny_graph.txt (線はオブジェクト間の距離)](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. ファイルを保存するには、**Ctrl キーを押しながら X キー**を押し、**Y** キー、**Enter** キーの順に押してファイル名を確定します。

4. 次のコマンドを使用して、HDInsight クラスターのプライマリ ストレージにデータを格納します。

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. 次のコマンドを使用して、サンプル SimpleShortestPathsComputation を実行します。

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    このコマンドで使用されるパラメーターを次の表に示します。

   | パラメーター | 実行内容 |
   | --- | --- |
   | `jar` |サンプルを含む jar ファイル。 |
   | `org.apache.giraph.GiraphRunner` |サンプルを開始するために使用するクラス。 |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |使用される例。 この例では、ID 1 とグラフ内の他のすべての ID の間の最短パスを計算します。 |
   | `-ca mapred.job.tracker` |クラスターのヘッド ノード。 |
   | `-vif` |入力データに使用する入力形式。 |
   | `-vip` |入力データ ファイル。 |
   | `-vof` |出力形式。 この例は、ID と値をプレーンテキストとして出力します。 |
   | `-op` |出力場所。 |
   | `-w 2` |使用する worker の数。 この例では 2 です。 |

    これらのパラメーターと Giraph サンプルで使用されるその他のパラメーターの詳細については、 [Giraph のクイックスタート](https://giraph.apache.org/quick_start.html)のページを参照してください。

6. ジョブが完了すると、結果が **/example/out/shotestpaths** ディレクトリに格納されます。 出力ファイルの名前の先頭は **part-m-**、末尾はファイルの順番を示す数字です。 次のコマンドを使用して、出力を表示します。

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    次のテキストのような出力が表示されます。

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation サンプルは、オブジェクト ID 1 から開始して他のオブジェクトへの最短パスを見つけるようにハードコーディングされています。 出力の形式は `destination_id` と `distance` です。 `distance` は、オブジェクト ID 1 とターゲット ID の間を結ぶエッジの値 (重み) です。

    このデータを視覚化して、ID 1 と他のすべてのオブジェクトの間で最短パスを結ぶことにより、結果を検証できます。 ID 1 と ID 4 の最短パスは 5 です。 この値は、<span style="color:orange">ID 1 と 3</span>、<span style="color:red">ID 3 と 4</span> を結ぶ距離の合計です。

    ![オブジェクトを円で表し、最短パスで結んだ図](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>次の手順

* [HDInsight クラスターに Hue をインストールして使用する](hdinsight-hadoop-hue-linux.md)。
