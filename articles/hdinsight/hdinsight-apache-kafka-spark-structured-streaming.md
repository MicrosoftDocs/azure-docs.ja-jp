---
title: チュートリアル:Apache Kafka での Apache Spark 構造化ストリーミング - Azure HDInsight
description: Apache Spark ストリーミングを使用して、Apache Kafka 内外でデータを取得する方法について説明します。 このチュートリアルでは、Jupyter Notebook を使用して HDInsight の Spark からデータをストリーミングします。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e470f9d6f33e8b534be22132881f4806bed85f5d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078365"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>チュートリアル:HDInsight で Apache Kafka による Apache Spark 構造化ストリーミングを使用する

このチュートリアルでは、Azure HDInsight で [Apache Kafka](https://kafka.apache.org/) による [Apache Spark 構造化ストリーミング](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)を使用してデータを読み書きする方法について説明します。

Spark 構造化ストリーミングは、Spark SQL に組み込まれたストリーミング処理エンジンであり、 静的データに対してバッチ計算と同様にストリーミング計算を表現できるようになります。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Kafka での構造化ストリーミング
> * Kafka クラスターと Spark クラスターの作成
> * Spark への Notebook のアップロード
> * ノートブックを使用する
> * リソースのクリーンアップ

このドキュメントの手順を完了したら、余分に課金されないようにするためにクラスターは削除してください。

## <a name="prerequisites"></a>前提条件

* HDInsight の Spark での [Jupyter Notebook](https://jupyter.org/) の使用方法を熟知していること。 詳細については、[HDInsight の Apache Spark を使用したデータの読み込みとクエリの実行](spark/apache-spark-load-data-run-query.md)に関するドキュメントを参照してください。

* [Scala](https://www.scala-lang.org/) プログラミング言語の知識があること。 このチュートリアルで使用するコードは、Scala で記述されています。

* Kafka トピックの作成方法を熟知していること。 詳細については、[HDInsight の Apache Kafka のクイック スタート](kafka/apache-kafka-get-started.md)に関するドキュメントを参照してください。

> [!IMPORTANT]  
> このドキュメントの手順には、HDInsight の Spark クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループが必要です。 これらのクラスターは両方とも、Spark クラスターが Kafka クラスターと直接通信できるように、Azure Virtual Network 内に配置します。
> 
> 利便性のために、このドキュメントは、必要なすべての Azure リソースを作成できるテンプレートにリンクしています。 
>
> 仮想ネットワークでの HDInsight の使用方法の詳細については、[仮想ネットワークを使用した HDInsight の拡張](hdinsight-extend-hadoop-virtual-network.md)に関するドキュメントをご覧ください。

## <a name="structured-streaming-with-apache-kafka"></a>Apache Kafka での構造化ストリーミング

Spark 構造化ストリーミングは、Spark SQL エンジンを基盤とするストリーム処理エンジンです。 構造化ストリーミングを使用すると、バッチ クエリと同様にストリーミング クエリを記述できます。

次のコード スニペットは、Kafka からの読み取りとファイルへの保存を示しています。 最初のコード スニペットはバッチ操作であり、2 番目はストリーミング操作です。

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

どちらのスニペットでも、データは Kafka から読み取られ、ファイルに書き込まれます。 これらの例の違いは次のとおりです。

| Batch | ストリーミング |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

ストリーミング操作では、30000 ミリ秒後にストリームを停止する、`awaitTermination(30000)` も使用されています。 

Kafka で構造化ストリーミングを使用するには、プロジェクトに `org.apache.spark : spark-sql-kafka-0-10_2.11` パッケージの依存関係が必要です。 このパッケージのバージョンは、HDInsight の Spark のバージョンと一致する必要があります。 Spark 2.2.0 (HDInsight 3.6 で使用可能) の場合、さまざまなプロジェクトの種類の依存関係情報を [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar) で確認できます。

このチュートリアルで提供される Jupyter Notebook では、次のセルでこのパッケージの依存関係を読み込みます。

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka ブローカーへのアクセスは提供されていません。 Kafka を使用するものは、すべて同じ Azure 仮想ネットワークに属している必要があります。 このチュートリアルでは、Kafka クラスターと Spark クラスターの両方を同じ Azure 仮想ネットワーク内に配置します。 

次の図に、Spark と Kafka の間の通信フローを示します。

![Azure 仮想ネットワークにおける Spark クラスターと Kafka クラスターの図](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Kafka サービスは、仮想ネットワーク内の通信に制限されます。 SSH や Ambari など、クラスター上の他のサービスは、インターネット経由でアクセスできます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

Azure 仮想ネットワークを作成し、その仮想ネットワーク内に Kafka クラスターと Spark クラスターを作成するには、次の手順に従います。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager テンプレートは、**https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** にあります。

    このテンプレートでは、次のリソースを作成します。

   * HDInsight 3.6 クラスター上の Kafka。
   * HDInsight 3.6 クラスター上の Spark 2.2.0。
   * Azure Virtual Network (HDInsight クラスターを含む)

     > [!IMPORTANT]  
     > このチュートリアルで使用する構造化ストリーミング Notebook では、HDInsight 3.6 上に Spark 2.2.0 が必要です。 HDInsight 上で以前のバージョンの Spark を使用している場合は、ノートブックを使用するとエラーを受信します。

2. 次の情報に従って、**[カスタマイズされたテンプレート]** セクションの各エントリに入力します。

    | Setting | 値 |
    | --- | --- |
    | サブスクリプション | お使いの Azure サブスクリプション |
    | リソース グループ | リソースが含まれるリソース グループ。 |
    | Location | リソースが作成される Azure リージョン。 |
    | [Spark Cluster Name]\(Spark クラスター名\) | Spark クラスターの名前。 最初の 6 文字は、Kafka クラスターの名前と異なるものにする必要があります。 |
    | [Kafka Cluster Name]\(Kafka クラスター名\) | Kafka クラスターの名前。 最初の 6 文字は、Spark クラスターの名前と異なるものにする必要があります。 |
    | [Cluster Login User Name]\(クラスター ログイン ユーザー名\) | クラスターの管理者ユーザー名。 |
    | [クラスター ログイン パスワード] | クラスターの管理者ユーザー パスワード。 |
    | [SSH ユーザー名] | クラスター用に作成する SSH ユーザー。 |
    | [SSH パスワード] | SSH ユーザーのパスワード。 |
   
    ![カスタマイズされたテンプレートのスクリーンショット](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. **使用条件**を読み、**[上記の使用条件に同意する]** をオンにします。

4. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 

> [!NOTE]  
> クラスターの作成には最大で 20 分かかります。

## <a name="upload-the-notebook"></a>Notebook のアップロード

プロジェクトから HDInsight クラスター上の Spark へノートブックをアップロードするには、以下の手順を使用します。

1. [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming) からプロジェクトをダウンロードします。

1. Web ブラウザーで、Spark クラスターの Jupyter Notebook に接続します。 次の URL の`CLUSTERNAME` をお使いの __Spark__ クラスターの名前に置き換えます。

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    プロンプトが表示されたら、クラスターの作成時に使用したログイン (管理者) パスワードを入力します。

2. ページの右上隅の __[アップロード]__ ボタンを使用して、__spark-structured-streaming-kafka.ipynb__ ファイルをクラスターにアップロードします。 __[開く]__ を選択して、アップロードを開始します。

    ![[アップロード] ボタンを使用して、Notebook を選択してアップロード](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![KafkaStreaming.ipynb ファイルを選択する](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. ノートブックの一覧で __spark-structured-streaming-kafka.ipynb__ エントリを検索し、横にある __[アップロード]__ ボタンを選択します。

    ![ノートブックをアップロードするために KafkaStreaming.ipynb エントリの [アップロード] ボタンを使用する](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>ノートブックを使用する

ファイルをアップロードした後、__spark-structured-streaming-kafka.ipynb__ エントリを選択してノートブックを開きます。 HDInsight 上の Kafka で Spark 構造化ストリーミングを使用する方法については、ノートブックの指示に従ってください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したリソースをクリーンアップするために、リソース グループを削除することができます。 リソース グループを削除すると、関連付けられている HDInsight クラスター、およびリソース グループに関連付けられているその他のリソースも削除されます。

Azure Portal を使用してリソース グループを削除するには:

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、__[リソース グループ]__ を選択して、リソース グループの一覧を表示します。
2. 削除するリソース グループを見つけて、一覧の右側にある __[詳細]__ ボタン ([...]) を右クリックします。
3. __[リソース グループの削除]__ を選択し、確認します。

> [!WARNING]  
> HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。
> 
> HDInsight クラスター上の Kafka を削除すると、Kafka に格納されているすべてのデータが削除されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、[Apache Spark 構造化ストリーミング](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)を使用して、HDInsight の [Apache Kafka](https://kafka.apache.org/) からデータを読み書きする方法を説明しました。 Kafka で [Apache Storm](https://storm.apache.org/) を使用する方法については、次のリンクを参照してください。

> [!div class="nextstepaction"]
> [Apache Kafka で Apache Storm を使用する](hdinsight-apache-storm-with-kafka.md)
