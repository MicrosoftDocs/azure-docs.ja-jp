---
title: Apache Kafka に対する Apache Spark ストリーミング - Azure HDInsight
description: Apache Spark を使用して、Apache Kafka に対して DStreams によるデータの送信または受信ストリーミングを行う方法について説明します。 この例では、Jupyter Notebook を使用して HDInsight 上で Spark からデータをストリーミングします。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 6ef11e9c7907f57b3b8de0a042e1035bce638cf4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863277"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>HDInsight 上の Apache Kafka を用いた Apache Spark ストリーミング (DStream) の例

[Apache Spark](https://spark.apache.org/) を使用して、HDInsight 上の [Apache Kafka](https://kafka.apache.org/) に対して [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html) による送信または受信ストリーミングを行う方法について説明します。 この例では、Spark クラスター上で実行する [Jupyter Notebook](https://jupyter.org/) を使用します。

> [!NOTE]  
> このドキュメントの手順では、HDInsight の Spark クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループを作成します。 これらのクラスターは両方とも、Spark クラスターが Kafka クラスターと直接通信できるように、Azure Virtual Network 内に配置します。
>
> このドキュメントの手順を完了したら、余分に課金されないようにするためにクラスターは削除してください。

> [!IMPORTANT]  
> この例では DStreams を使用します。DStreams は古い Spark ストリーミング テクノロジです。 新しい Spark ストリーミング機能を使用する例については、[Apache Kafka を使った Spark 構造化ストリーミング](hdinsight-apache-kafka-spark-structured-streaming.md)に関するドキュメントを参照してください。

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight 上の Apache Kafka では、パブリック インターネット経由の Kafka ブローカーへのアクセスは提供されません。 Kafka と通信するすべてのものは、Kafka クラスター内のノードと同じ Azure 仮想ネットワークに存在している必要があります。 この例では、Kafka クラスターと Spark クラスターの両方を Azure 仮想ネットワーク内に配置します。 次の図に、クラスター間の通信フローを示します。

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png" alt-text="Azure 仮想ネットワークにおける Spark クラスターと Kafka クラスターの図" border="false":::

> [!NOTE]  
> Kafka 自体は仮想ネットワーク内の通信に制限されていますが、クラスターの SSH や Ambari などの他のサービスにはインターネット経由でアクセスすることができます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

Azure 仮想ネットワーク、Kafka、および Spark クラスターは手動で作成できますが、Azure Resource Manager テンプレートを使用する方が簡単です。 次の手順に従って、Azure 仮想ネットワーク、Kafka クラスター、および Spark クラスターを Azure サブスクリプションにデプロイします。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

   Azure Resource Manager テンプレートは、 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** にあります。

   > [!WARNING]
   > HDInsight で Kafka の可用性を保証するには、クラスターに少なくとも 3 つのワーカー ノードが必要です。 このテンプレートは、3 つのワーカー ノードが含まれる Kafka クラスターを作成します。

   このテンプレートは、Kafka と Spark の両方の HDInsight 3.6 クラスターを作成します。

1. 以下の情報を使用して、 **[カスタム デプロイ]** セクションに各エントリを入力します。

   |プロパティ |値 |
   |---|---|
   |Resource group|グループを作成するか、または既存のグループを選択します。|
   |Location|地理的に近い場所を選択します。|
   |Base Cluster Name (ベース クラスター名)|この値は、Spark クラスターと Kafka クラスターのベース名として使用されます。 たとえば、「**hdistreaming**」と入力すると、__spark-hdistreaming__ という名前の Spark クラスターと、**kafka-hdistreaming** という名前の Kafka クラスターが作成されます。|
   |[Cluster Login User Name]\(クラスター ログイン ユーザー名\)|Spark クラスターと Kafka クラスターの管理者のユーザー名。|
   |[クラスター ログイン パスワード]|Spark クラスターと Kafka クラスターの管理者のユーザー パスワード。|
   |[SSH ユーザー名]|Spark クラスターと Kafka クラスターの作成に使用する SSH ユーザー。|
   |[SSH パスワード]|Spark クラスターと Kafka クラスター用の SSH ユーザーのパスワード。|

   :::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png" alt-text="HDInsight カスタム デプロイ パラメーター":::

1. **使用条件** を読み、 **[上記の使用条件に同意する]** をオンにします。

1. 最後に、 **[購入]** を選択します。 クラスターの作成には約 20 分かかります。

リソースが作成されると、概要ページが表示されます。

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png" alt-text="vnet とクラスターのリソース グループ概要":::

> [!IMPORTANT]  
> 各 HDInsight クラスターの名前が **spark-BASENAME** および **kafka-BASENAME** であることに注目してください。BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。

## <a name="use-the-notebooks"></a>ノートブックを使用する

このドキュメントで説明した例のコードは、[https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) で入手できます。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

このドキュメントの手順では両方のクラスターを同じ Azure リソース グループに作成したため、Azure Portal でこのリソース グループを削除するだけで済みます。 グループを削除することにより、このドキュメントに従って作成したすべてのリソース、Azure Virtual Network、クラスターで使用したストレージ アカウントが削除されます。

## <a name="next-steps"></a>次のステップ

この例では、Spark を使用して Kafka に対するデータの読み取りと書き込みを行う方法について説明しました。 次のリンクを使用することで、Kafka のその他の活用方法を知ることができます。

* [HDInsight での Apache Kafka の使用](kafka/apache-kafka-get-started.md)
* [MirrorMaker を使用して HDInsight 上の Apache Kafka のレプリカを作成する](kafka/apache-kafka-mirroring.md)
* [HDInsight 上の Apache Kafka で Apache Storm を使用する](hdinsight-apache-storm-with-kafka.md)
