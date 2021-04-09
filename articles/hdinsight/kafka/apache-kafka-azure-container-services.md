---
title: Azure Kubernetes Service で HDInsight 上の Kafka を使用する
description: Azure Kubernetes Service (AKS) でホストされているコンテナー イメージから、HDInsight 上の Kafka を使用する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 2216eb5893b77761f4d31c5819d152ceeb985abc
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869652"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Azure Kubernetes Service で HDInsight 上の Apache Kafka を使用する

Azure Kubernetes Service (AKS) で HDInsight クラスター上の [Apache Kafka](https://kafka.apache.org/) を使用する方法について説明します。 このドキュメント内の手順では、AKS でホストされている Node.js アプリケーションを使用して、Kafka との接続を検証します。 このアプリケーションでは、[kafka-node](https://www.npmjs.com/package/kafka-node) パッケージを使用して Kafka と通信します。 ブラウザー クライアントと、AKS でホストされているバックエンドとの間のイベント駆動型メッセージングには、[Socket.io](https://socket.io/) を使用します。

[Apache Kafka](https://kafka.apache.org) はオープン ソースの分散ストリーム プラットフォームで、リアルタイムのストリーミング データ パイプラインとアプリケーションの構築に使用できます。 Azure Kubernetes Service は、ホストされた Kubernetes 環境を管理し、コンテナー化されたアプリケーションをすばやく簡単にデプロイできるようにします。 Azure Virtual Network を使用することで、これら 2 つのサービスを接続することができます。

> [!NOTE]  
> このドキュメントは、Azure Kubernetes Service が HDInsight 上の Kafka と通信できるようにするための手順に主眼を置いたものです。 ドキュメント内で示す例はあくまでも、構成が機能していることを確認するための基本的な Kafka クライアントです。

## <a name="prerequisites"></a>前提条件

* [Azure CLI](/cli/azure/install-azure-cli)
* Azure サブスクリプション

このドキュメントでは、読者が次の Azure サービスの作成と使用に慣れていることを前提としています。

* HDInsight 上の Kafka
* Azure Kubernetes Service
* Azure 仮想ネットワーク

またこのドキュメントでは、読者が「[Azure Kubernetes Service tutorial (Azure Container Service チュートリアル)](../../aks/tutorial-kubernetes-prepare-app.md)」を学習済みであることを前提としています。 この記事では、コンテナー サービス、Kubernetes クラスター、コンテナー レジストリを作成し、`kubectl` ユーティリティを構成しています。

## <a name="architecture"></a>Architecture

### <a name="network-topology"></a>ネットワーク トポロジ

HDInsight と AKS はいずれも、コンピューティング リソースのコンテナーとして Azure Virtual Network を使用します。 HDInsight と AKS 間の通信を有効にするには、それらのネットワーク間の通信を有効にする必要があります。 このドキュメントの手順では、これらのネットワークに仮想ネットワーク ピアリングを使用します。 なお、その他の接続 (VPN など) を使用することもできます。 ピアリングの詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」ドキュメントを参照してください。

次の図は、このドキュメントで使用されるネットワーク トポロジを示したものです。

:::image type="content" source="./media/apache-kafka-azure-container-services/kafka-aks-architecture.png" alt-text="仮想ネットワーク内の HDInsight、別の仮想ネットワーク内の AKS、ピアリングを使用" border="false":::

> [!IMPORTANT]  
> ピアリングされたネットワーク間では名前解決が有効化されていないので、IP アドレス指定が使用されます。 既定では、Kafka on HDInsight はクライアント接続時に IP アドレスではなく、ホスト名を返すように構成されています。 このドキュメントの手順では、Kafka に変更を加えて、IP アドバタイズが使用されるようにします。

## <a name="create-an-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) を作成する

AKS クラスターがまだない場合は、次のいずれかのドキュメントを使用して、AKS クラスターの作成方法を確認してください。

* [Azure Kubernetes Service (AKS) クラスターのデプロイ - Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Azure Kubernetes Service (AKS) クラスターのデプロイ - CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> AKS では、**追加の** リソース グループへのインストール時に仮想ネットワークが作成されます。 追加のリソース グループは、**MC_resourceGroup_AKSclusterName_location** という名前付け規則に従います。  
> このネットワークは、次のセクションで HDInsight 用に作成するネットワークにピアリングされます。

## <a name="configure-virtual-network-peering"></a>仮想ネットワーク ピアリングを構成する

### <a name="identify-preliminary-information"></a>準備情報の特定

1. [Azure portal](https://portal.azure.com) で、AKS クラスター用の仮想ネットワークが含まれている追加の **リソース グループ** を探します。

2. リソース グループで、 __[仮想ネットワーク]__ リソースを選択します。 後で使うので名前をメモしておきます。

3. **[設定]** で、 __[アドレス空間]__ を選択します。 表示されたアドレス空間をメモします。

### <a name="create-virtual-network"></a>Create virtual network

1. HDInsight 用の仮想ネットワークを作成するには、 __[+ リソースの作成]__  >  __[ネットワーク]__  >  __[仮想ネットワーク]__ の順に移動します。

1. 特定のプロパティに対する以下のガイドラインを使用して、ネットワークを作成します。

    |プロパティ | 値 |
    |---|---|
    |アドレス空間|AKS クラスター ネットワークに使用されているものと重複しないアドレス空間を使用する必要があります。|
    |場所|AKS クラスターに使用した仮想ネットワークと同じ __場所__ を使用します。|

1. 仮想ネットワークが作成されるまで待ち、その後、次の手順に進みます。

### <a name="configure-peering"></a>ピアリングの構成

1. HDInsight ネットワークと AKS クラスター ネットワークの間のピアリングを構成するには、仮想ネットワークを選択し、 __[ピアリング]__ を選択します。

1. __[+ 追加]__ を選択し、次の値を使用してフォームに値を入力します。

    |プロパティ |値 |
    |---|---|
    |\<this VN> からリモート仮想ネットワークへのピアリングの名前|このピアリング構成の一意の名前を入力します。|
    |仮想ネットワーク|**AKS クラスター** 用の仮想ネットワークを選択します。|
    |\<AKS VN> から \<this VN> へのピアリングの名前|一意の名前を入力します。|

    その他のフィールドはすべて既定値のままにし、 __[OK]__ を選択してピアリングを構成します。

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>HDInsight 上に Apache Kafka クラスターを作成する

Kafka HDInsight クラスターを作成する際には、先ほど HDInsight 用に作成した仮想ネットワークに参加する必要があります。 Kafka クラスターの作成の詳細については、[Apache Kafka クラスターの作成](apache-kafka-get-started.md)に関するドキュメントを参照してください。

## <a name="configure-apache-kafka-ip-advertising"></a>Apache Kafka の IP アドバタイズを構成する

次の手順を使用して、ドメイン名の代わりに IP アドレスを提供するように Kafka を構成します。

1. Web ブラウザーを使用し、`https://CLUSTERNAME.azurehdinsight.net` にアクセスします。 CLUSTERNAME を HDInsight クラスター上の Kafka の名前に置き換えます。

    プロンプトが表示されたら、クラスターの HTTPS ユーザー名とパスワードを入力します。 クラスターの Ambari Web UI が表示されます。

2. Kafka に関する情報を表示するには、左にある一覧から __[Kafka]__ を選択します。

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-service.png" alt-text="Kafka が強調表示されているサービスの一覧" border="true":::

3. Kafka 構成を表示するには、上部中央の __[Configs (構成)]__ を選択します。

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-config1.png" alt-text="Apache Ambari サービスの構成" border="true":::

4. __kafka-env__ 構成を検索するには、右上の __[Filter (フィルター)]__ フィールドに「`kafka-env`」と入力します。

    :::image type="content" source="./media/apache-kafka-azure-container-services/search-for-kafka-env.png" alt-text="kafka-env の Kafka 構成" border="true":::

5. IP アドレスを提供するように Kafka を構成するには、次のテキストを __kafka-env-template__ フィールドの最後に追加します。

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka がリッスンするインターフェイスを構成するには、右上の __[Filter (フィルター)]__ フィールドに「`listeners`」と入力します。

7. すべてのネットワーク インターフェイスをリッスンするように Kafka を構成するには、 __[listeners (リスナー)]__ フィールドの値を `PLAINTEXT://0.0.0.0:9092`に変更します。

8. 構成を保存するには、 __[Save (保存)]__ ボタンを使用します。 変更を説明するテキスト メッセージを入力します。 変更が保存されたら、 __[OK]__ を保存します。

    :::image type="content" source="./media/apache-kafka-azure-container-services/save-configuration-button.png" alt-text="Apache Ambari の保存の構成" border="true":::

9. Kafka の再起動時にエラーが発生しないようにするため、 __[Service Actions (サービス アクション)__ ] ボタンを使用して __[Turn On Maintenance Mode (メンテナンス モードの有効化)]__ を選択します。 [OK] を選択して、この操作を完了します。

    :::image type="content" source="./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png" alt-text="[Turn On Maintenance Mode (メンテナンス モードの有効化)] が強調表示されているサービス アクション" border="true":::

10. Kafka を再起動するには、 __[Restart (再起動)]__ ボタンをクリックし、 __[Restart All Affected (影響を受けるものをすべて再起動)]__ を選択します。 再起動を確認し、操作が完了したら __[OK]__ ボタンを使用します。

    :::image type="content" source="./media/apache-kafka-azure-container-services/restart-required-button.png" alt-text="[Restart All Affected (影響を受けるものをすべて再起動)] が強調表示されている [Restart (再起動)] ボタン" border="true":::

11. メンテナンス モードを無効にするには、 __[Service Actions (サービス アクション)]__ ボタンをクリックし、 __[Turn Off Maintenance Mode (メンテナンス モードの無効化)]__ を選択します。 **[OK]** を選択して、この操作を完了します。

## <a name="test-the-configuration"></a>構成をテストする

この時点で、Kafka と Azure Kubernetes Service はピアリングされた仮想ネットワークを通じて通信できるようになっています。 この接続をテストするには、次の手順に従います。

1. テスト アプリケーションによって使用される Kafka トピックを作成します。 Kafka トピックを作成する方法については、[Apache Kafka クラスターの作成](apache-kafka-get-started.md)に関するドキュメントをご覧ください。

2. サンプル アプリケーションは [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) からダウンロードできます。

3. `index.js` ファイルを編集し、次の行を変更します。

    * `var topic = 'mytopic'`:`mytopic` を、このアプリケーションで使用される Kafka トピックの名前に置き換えます。
    * `var brokerHost = '176.16.0.13:9092`:`176.16.0.13` を、クラスターのいずれかのブローカー ホストの内部 IP アドレスに置き換えます。

        クラスター内のブローカー ホスト (workernodes) の内部 IP アドレスを取得する方法については、[Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes) に関するドキュメントを参照してください。 ドメイン名が `wn` で始まるいずれかのエントリの IP アドレスを選択します。

4. `src` ディレクトリのコマンドラインから、依存関係をインストールし、Docker を使用してデプロイ用のイメージを作成します。

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > このアプリケーションで必要となるパッケージはリポジトリにチェックインされるので、`npm` ユーティリティを使用してそれらをインストールする必要はありません。

5. Azure Container Registry (ACR) にログインし、loginServer 名を探します。

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Azure Container Registry 名がわからない場合や、Azure CLI で Azure Kubernetes Service を操作する方法がわからない場合は、[AKS チュートリアル](../../aks/tutorial-kubernetes-prepare-app.md)に関するドキュメントを参照してください。

6. ローカルの `kafka-aks-test` イメージを、ACR の loginServer にタグ付けします。 末尾には `:v1` を付加し、イメージのバージョンを示します。

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. イメージをレジストリにプッシュします。

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    この操作は、完了するまでに数分かかります。

8. Kubernetes マニフェスト ファイル (`kafka-aks-test.yaml`) を編集し、`microsoft` を、手順 4 で取得した ACR loginServer 名に置き換えます。

9. 次のコマンドを使用して、マニフェストからアプリケーション設定をデプロイします。

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. 次のコマンドを使用して、アプリケーションの `EXTERNAL-IP` をウォッチします。

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    外部 IP アドレスが割り当てられたら、__CTRL + C__ キーを使用してウォッチを終了します

11. Web ブラウザーを開き、サービスの外部 IP アドレスを入力します。 次の画像のようなページが表示されます。

    :::image type="content" source="./media/apache-kafka-azure-container-services/test-web-page-image1.png" alt-text="Apache Kafka テスト Web ページの画像" border="true":::

12. フィールドにテキストを入力し、 __[送信]__ ボタンを選択します。 データが Kafka に送信されます。 その後、アプリケーション内の Kafka コンシューマーによりメッセージが読み取られ、 __[Messages from Kafka]__ セクションに追加されます。

    > [!WARNING]  
    > 同じメッセージが複数返されることがあります。 この問題は通常、接続後にブラウザーを更新した場合や、アプリケーションへのブラウザー接続を複数開いた場合に発生します。

## <a name="next-steps"></a>次のステップ

次のリンクを使用することで、HDInsight で Apache Kafka を使用する方法を知ることができます。

* [HDInsight での Apache Kafka の使用](apache-kafka-get-started.md)

* [MirrorMaker を使用して HDInsight 上の Apache Kafka のレプリカを作成する](apache-kafka-mirroring.md)

* [HDInsight 上の Apache Kafka で Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)

* [HDInsight 上の Apache Kafka で Apache Spark を使用する](../hdinsight-apache-spark-with-kafka.md)

* [Azure Virtual Network 経由で Apache Kafka に接続する](apache-kafka-connect-vpn-gateway.md)
