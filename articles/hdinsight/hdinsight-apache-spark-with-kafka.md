---
title: "Kafka に対する Apache Spark ストリーミング - Azure HDInsight | Microsoft Docs"
description: "Apache Spark を使用して、DStreams による Apache Kafka 内外へのデータのストリームを行う方法について説明します。 この例では、Jupyter ノートブックを使用して HDInsight 上の Spark からデータをストリームします。"
keywords: kafka example,kafka zookeeper,spark streaming kafka,spark streaming kafka example
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/13/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 81fa319f6fb94bdabacd8f68d14b9a1063a9749a
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017

---
# <a name="apache-spark-streaming-dstream-example-with-kafka-preview-on-hdinsight"></a>HDInsight 上の Kafka (プレビュー) を用いた Apache Spark ストリーミング (DStream) の例

Apache Spark を使用して、DStreams による HDInsight 上の Apache Kafka 内外へのデータのストリームを行う方法について説明します。 この例では、Spark クラスター上で実行する Jupyter ノートブックを使用します。
> [!NOTE]
> このドキュメントの手順では、HDInsight の Spark クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループを作成します。 これらのクラスターは両方とも、Spark クラスターが Kafka クラスターと直接通信できるように、Azure Virtual Network 内に配置します。
>
> このドキュメントの手順を完了したら、余分に課金されないようにするためにクラスターは削除してください。

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka ブローカーへのアクセスは提供されていません。 Kafka と通信するすべてのものは、Kafka クラスター内のノードと同じ Azure 仮想ネットワークに存在している必要があります。 この例では、Kafka クラスターと Spark クラスターの両方を Azure 仮想ネットワーク内に配置します。 次の図に、クラスター間の通信フローを示します。

![Azure 仮想ネットワークにおける Spark クラスターと Kafka クラスターの図](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 自体は仮想ネットワーク内の通信に制限されていますが、クラスターの SSH や Ambari などの他のサービスにはインターネット経由でアクセスすることができます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

Azure 仮想ネットワーク、Kafka、および Spark クラスターは手動で作成できますが、Azure Resource Manager テンプレートを使用する方が簡単です。 次の手順に従って、Azure 仮想ネットワーク、Kafka クラスター、および Spark クラスターを Azure サブスクリプションにデプロイします。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager テンプレートは **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v2.1.json** にあります。

    > [!WARNING]
    > HDInsight で Kafka の可用性を保証するには、クラスターに少なくとも 3 つのワーカー ノードが必要です。 このテンプレートは、3 つのワーカー ノードが含まれる Kafka クラスターを作成します。

    このテンプレートは、Kafka と Spark の両方の HDInsight 3.6 クラスターを作成します。

2. 次の情報に従って、**[カスタム デプロイ]** ブレードの各エントリに入力します。
   
    ![HDInsight のカスタム デプロイ](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **[リソース グループ]**: グループを作成するか、または既存のグループを選択します。 このグループに HDInsight クラスターが含まれます。

    * **[場所]**: 地理的に近い場所を選択します。

    * **[Base Cluster Name] \(ベース クラスター名)**: この値は、Spark クラスターと Kafka クラスターのベース名として使用されます。 たとえば、「**hdi**」と入力すると、spark-hdi__ という名前の Spark クラスターと、**kafka-hdi** という名前の Kafka クラスターが作成されます。

    * **[Cluster Login User Name] \(クラスター ログイン ユーザー名)**: Spark クラスターと Kafka クラスターの管理者のユーザー名。

    * **[クラスター ログイン パスワード]**: Spark クラスターと Kafka クラスターの管理者のユーザー パスワード。

    * **[SSH ユーザー名]**: Spark クラスターおよび Kafka クラスターの作成に使用する SSHユーザー。

    * **[SSH パスワード]**: Spark クラスターおよび Kafka クラスター用の SSH ユーザーのパスワード。

3. **使用条件**を読み、**[上記の使用条件に同意する]** をオンにします。

4. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 クラスターの作成には約 20 分かかります。

リソースが作成されると、クラスターと Web ダッシュボードが含まれているリソース グループのブレードにリダイレクトされます。

![Resource group blade for the vnet and clusters](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> 各 HDInsight クラスターの名前が **spark-BASENAME** および **kafka-BASENAME** であることに注目してください。BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。

## <a name="use-the-notebooks"></a>ノートブックを使用する

このドキュメントで説明する例で使用するコードは、[https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) で入手できます。

`README.md` ファイルの手順に従って、この例を完了します。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

このドキュメントの手順では両方のクラスターを同じ Azure リソース グループに作成したため、Azure Portal でこのリソース グループを削除するだけで済みます。 グループを削除することにより、このドキュメントに従って作成したすべてのリソース、Azure Virtual Network、クラスターで使用したストレージ アカウントが削除されます。

## <a name="next-steps"></a>次のステップ

この例では、Spark を使用して Kafka に対するデータの読み取りと書き込みを行う方法について説明しました。 次のリンクを使用することで、Kafka のその他の活用方法を知ることができます。

* [HDInsight での Apache Kafka の使用](hdinsight-apache-kafka-get-started.md)
* [MirrorMaker を使用した HDInsight での Kafka のレプリカの作成](hdinsight-apache-kafka-mirroring.md)
* [HDInsight での Kafka に Apache Storm を使用する](hdinsight-apache-storm-with-kafka.md)


