---
title: "Azure HDInsight での Kafka に Apache Spark を使用する | Microsoft Docs"
description: "HDInsight で Spark を使用して、HDInsight クラスター上の Kafka に対するデータの読み取りと書き込みを行う方法について説明します。 この例では、Jupyter Notebook で Scala を使用し、HDInsight の Kafka にランダムなデータを書き込んだ後に、Spark ストリーミングを使用してそのデータを読み取ります。"
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
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: c56decc1f7603795e027ce20363c387c593999ae
ms.lasthandoff: 03/25/2017

---
# <a name="use-apache-spark-with-kafka-preview-on-hdinsight"></a>HDInsight での Kafka (プレビュー) に Apache Spark を使用する

Apache Spark は、Apache Kafka 内外へのデータのストリームに使用できます。 このドキュメントでは、HDInsight 上の Spark から Jupyter Notebook を使用して、Kafka の内外にデータをストリームする方法について説明します。

> [!NOTE]
> このドキュメントの手順では、HDInsight の Spark クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループを作成します。 これらのクラスターは両方とも、Spark クラスターが Kafka クラスターと直接通信できるように、Azure Virtual Network 内に配置します。
> 
> このドキュメントの手順を完了したら、余分に課金されないようにするためにクラスターは削除してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション

* SSH クライアント (`ssh` と `scp` コマンドが必要です) - 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

* [cURL](https://curl.haxx.se/) - HTTP 要求を行うためのクロス プラットフォーム ユーティリティ。

* [jq](https://stedolan.github.io/jq/) - JSON ドキュメントを解析するためのクロス プラットフォーム ユーティリティ。

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka ブローカーへのアクセスは提供されていません。 Kafka と通信するすべてのものは、Kafka クラスター内のノードと同じ Azure 仮想ネットワークに存在している必要があります。 この例では、Kafka クラスターと Spark クラスターの両方を Azure 仮想ネットワーク内に配置します。 次の図に、クラスター間の通信フローを示します。

![Azure 仮想ネットワークにおける Spark クラスターと Kafka クラスターの図](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 自体は仮想ネットワーク内の通信に制限されていますが、クラスターの SSH や Ambari などの他のサービスにはインターネット経由でアクセスすることができます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

Azure 仮想ネットワーク、Kafka、および Spark クラスターは手動で作成できますが、Azure Resource Manager テンプレートを使用する方が簡単です。 次の手順に従って、Azure 仮想ネットワーク、Kafka クラスター、および Spark クラスターを Azure サブスクリプションにデプロイします。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager テンプレートは **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet.json** にあります。

2. 次の情報に従って、**[カスタム デプロイ]** ブレードの各エントリに入力します。
   
    ![HDInsight のカスタム デプロイ](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **[リソース グループ]**: グループを作成するか、または既存のグループを選択します。 このグループに HDInsight クラスターが含まれます。

    * **[場所]**: 地理的に近い場所を選択します。 この場所は、__[設定]__ セクションの場所と一致する必要があります。

    * **[Base Cluster Name] (ベース クラスター名)**: この値は、Spark クラスターと Kafka クラスターのベース名として使用されます。 たとえば、「**hdi**」と入力すると、spark-hdi__ という名前の Spark クラスターと、**kafka-hdi** という名前の Kafka クラスターが作成されます。

    * **[Cluster Login User Name] (クラスター ログイン ユーザー名)**: Spark クラスターと Kafka クラスターの管理者のユーザー名。

    * **[クラスター ログイン パスワード]**: Spark クラスターと Kafka クラスターの管理者のユーザー パスワード。

    * **[SSH ユーザー名]**: Spark クラスターおよび Kafka クラスターの作成に使用する SSHユーザー。

    * **[SSH パスワード]**: Spark クラスターおよび Kafka クラスター用の SSH ユーザーのパスワード。

    * **[場所]**: クラスターが作成されるリージョン。

3. **使用条件**を読み、**[上記の使用条件に同意する]** をオンにします。

4. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 クラスターの作成には約 20 分かかります。

リソースが作成されると、クラスターと Web ダッシュボードが含まれているリソース グループのブレードにリダイレクトされます。

![Resource group blade for the vnet and clusters](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> 各 HDInsight クラスターの名前が **spark-BASENAME** および **kafka-BASENAME** であることに注目してください。BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。

## <a name="get-the-code"></a>コードの入手

このドキュメントで説明する例で使用するコードは、[https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) で入手できます。

## <a name="understand-the-code"></a>コードの理解

この例では、Scala アプリケーションを Jupyter Notebook で使用します。 Notebook のコードは、次のデータに依存します。

* __Kafka ブローカー__: Kafka クラスターの各ワーカー ノードでブローカー プロセスを実行します。 Kafka へのデータの書き込みを行うプロデューサー コンポーネントには、ブローカーの一覧が必要です。

* __Zookeeper ホスト__: Kafka からデータを使用する (読み取る) ときに、Kafka クラスターの Zookeeper ホストを使用します。

* __トピック名__: データの書き込みと読み取りが行われるトピックの名前。 この例では、`sparktest` という名前のトピックを指定します。

Kafka ブローカーと Zookeeper ホストの情報を取得する方法の詳細については、「[Kafka ホスト情報](#kafkahosts)」のセクションを参照してください。

Notebook のコードは次のタスクを実行します。

* `sparktest` という名前の Kafka トピックからデータを読み取るコンシューマーを作成し、データ内の各単語をカウントして、単語およびカウントを `wordcounts` という名前の一時テーブルに格納します。

* `sparktest` という名前の Kafka トピックにランダムな文を書き込むプロデューサーを作成します。

* `wordcounts` テーブルからデータを選択してカウントを表示します。

プロジェクト内の各セルには、コードの機能を説明するコメントやテキスト セクションが含まれています。

##<a id="kafkahosts"></a>Kafka ホスト情報

HDInsight 上の Kafka で動作するアプリケーションを作成する場合は、まず Kafka クラスターで使用する Kafka ブローカーと Zookeeper ホストの情報を取得する必要があります。 この情報は、クライアント アプリケーションによって Kafka との通信に使用されます。

> [!NOTE]
> Kafka ブローカーと Zookeeper ホストには、インターネット経由で直接アクセスすることはできません。 Kafka を使用するすべてのアプリケーションは、Kafka クラスター上、または Kafka クラスターと同じ Azure Virtual Network 内で実行する必要があります。 今回の例では、同じ仮想ネットワーク内に配置された HDInsight クラスター上の Spark で実行します。

開発環境からは、次のコマンドを使用してブローカーと Zookeeper の情報を取得します。 __PASSWORD__ は、クラスターの作成時に使用したログイン (管理者) パスワードに置き換えます。 __BASENAME__ は、クラスターの作成時に使用したベース名に置き換えます。

* __Kafka ブローカー__情報を取得するには：

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'

    > [!IMPORTANT]
    > Windows PowerShell からこのコマンドを使用すると、シェルの引用符に関するエラー メッセージが表示される場合があります。 その場合は、次のコマンドを使用します: `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")'`

* __Zookeeper ホスト__情報を取得するには：

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'
    
    > [!IMPORTANT]
    > Windows PowerShell からこのコマンドを使用すると、シェルの引用符に関するエラー メッセージが表示される場合があります。 その場合は、次のコマンドを使用します: `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")'`

どちらのコマンドでも、次のテキストのような情報が返されます。

* __Kafka ブローカー__: `wn0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092`

* __Zookeeper ホスト__: `zk0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk2-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181`

> [!IMPORTANT]
> このドキュメントのいくつかの手順で使用するため、この情報は保存してください。

## <a name="use-the-jupyter-notebook"></a>Jupyter Notebook の使用

この例の Jupyter Notebook を使用するには、Spark クラスター上の Jupyter Notebook サーバーに Jupyter Notebook をアップロードする必要があります。 次の手順で、Notebook をアップロードします。

1. Web ブラウザーで次の URL を使用して、Spark クラスター上の Jupyter Notebook サーバーに接続します。 __BASENAME__ は、クラスターの作成時に使用したベース名に置き換えます。

        https://spark-BASENAME.azurehdinsight.net/jupyter

    プロンプトが表示されたら、クラスターの作成時に使用したログイン (管理者) パスワードを入力します。

2. ページの右上隅の __[アップロード]__ ボタンを使用して、`KafkaStreaming.ipynb` ファイルをアップロードします。 [ファイル ブラウザー] ダイアログ ボックスでファイルを選択し、__[開く]__ を選択します。 

    ![[アップロード] ボタンを使用して、Notebook を選択してアップロード](./media/hdinsight-apache-spark-with-kafka/upload-button.png)

    ![KafkaStreaming.ipynb ファイルを選択する](./media/hdinsight-apache-spark-with-kafka/select-notebook.png)

3. Notebook の一覧で __KafkaStreaming.ipynb__ エントリを検索し、横にある __[Upload]__ ボタンを選択します。

    ![KafkaStreaming.ipynb エントリの横にある [アップロード] ボタンを使用して、Notebook サーバーにアップロード](./media/hdinsight-apache-spark-with-kafka/upload-notebook.png)

4. ファイルがアップロードされたら、__KafkaStreaming.ipynb__ エントリを選択して Notebook を開きます。 この例を完了するには、Notebook の指示に従ってください。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

このドキュメントの手順では両方のクラスターを同じ Azure リソース グループに作成したため、Azure Portal でこのリソース グループを削除するだけで済みます。 グループを削除することにより、このドキュメントに従って作成したすべてのリソース、Azure Virtual Network、クラスターで使用したストレージ アカウントが削除されます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Spark を使用して Kafka に対するデータの読み取りと書き込みを行う方法について説明しました。 次のリンクを使用することで、Kafka のその他の活用方法を知ることができます。

* [HDInsight での Apache Kafka の使用](hdinsight-apache-kafka-get-started.md)
* [MirrorMaker を使用した HDInsight での Kafka のレプリカの作成](hdinsight-apache-kafka-mirroring.md)
* [HDInsight での Kafka に Apache Storm を使用する](hdinsight-apache-storm-with-kafka.md)


