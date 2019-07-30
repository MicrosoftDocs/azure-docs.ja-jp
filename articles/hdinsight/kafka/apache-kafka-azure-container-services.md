---
title: Azure Kubernetes Service で HDInsight 上の Kafka を使用する
description: Azure Kubernetes Service (AKS) でホストされているコンテナー イメージから、HDInsight 上の Kafka を使用する方法について説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: dc2c3e557f295bd3125c09d9b839716159dcf50b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446453"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Azure Kubernetes Service で HDInsight 上の Apache Kafka を使用する

Azure Kubernetes Service (AKS) で HDInsight クラスター上の [Apache Kafka](https://kafka.apache.org/) を使用する方法について説明します。 このドキュメント内の手順では、AKS でホストされている Node.js アプリケーションを使用して、Kafka との接続を検証します。 このアプリケーションでは、[kafka-node](https://www.npmjs.com/package/kafka-node) パッケージを使用して Kafka と通信します。 ブラウザー クライアントと、AKS でホストされているバックエンドとの間のイベント駆動型メッセージングには、[Socket.io](https://socket.io/) を使用します。

[Apache Kafka](https://kafka.apache.org) はオープン ソースの分散ストリーム プラットフォームで、リアルタイムのストリーミング データ パイプラインとアプリケーションの構築に使用できます。 Azure Kubernetes Service は、ホストされた Kubernetes 環境を管理し、コンテナー化されたアプリケーションをすばやく簡単にデプロイできるようにします。 Azure Virtual Network を使用することで、これら 2 つのサービスを接続することができます。

> [!NOTE]  
> このドキュメントは、Azure Kubernetes Service が HDInsight 上の Kafka と通信できるようにするための手順に主眼を置いたものです。 ドキュメント内で示す例はあくまでも、構成が機能していることを確認するための基本的な Kafka クライアントです。

## <a name="prerequisites"></a>前提条件

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Azure サブスクリプション

このドキュメントでは、読者が次の Azure サービスの作成と使用に慣れていることを前提としています。

* HDInsight 上の Kafka
* Azure Kubernetes Service
* Azure 仮想ネットワーク

またこのドキュメントでは、読者が「[Azure Kubernetes Service tutorial (Azure Container Service チュートリアル)](../../aks/tutorial-kubernetes-prepare-app.md)」を学習済みであることを前提としています。 この記事では、コンテナー サービス、Kubernetes クラスター、コンテナー レジストリを作成し、`kubectl` ユーティリティを構成しています。

## <a name="architecture"></a>アーキテクチャ

### <a name="network-topology"></a>ネットワーク トポロジ

HDInsight と AKS はいずれも、コンピューティング リソースのコンテナーとして Azure Virtual Network を使用します。 HDInsight と AKS 間の通信を有効にするには、それらのネットワーク間の通信を有効にする必要があります。 このドキュメントの手順では、これらのネットワークに仮想ネットワーク ピアリングを使用します。 なお、その他の接続 (VPN など) を使用することもできます。 ピアリングの詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」ドキュメントを参照してください。


次の図は、このドキュメントで使用されるネットワーク トポロジを示したものです。

![仮想ネットワーク内の HDInsight、別の仮想ネットワーク内の AKS、およびピアリングを使用して接続されたネットワーク](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> ピアリングされたネットワーク間では名前解決が有効化されていないので、IP アドレス指定が使用されます。 既定では、Kafka on HDInsight はクライアント接続時に IP アドレスではなく、ホスト名を返すように構成されています。 このドキュメントの手順では、Kafka に変更を加えて、IP アドバタイズが使用されるようにします。

## <a name="create-an-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) を作成する

AKS クラスターがまだない場合は、次のいずれかのドキュメントを使用して、AKS クラスターの作成方法を確認してください。

* [Azure Kubernetes Service (AKS) クラスターのデプロイ - Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Azure Kubernetes Service (AKS) クラスターのデプロイ - CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]  
> AKS では、インストール時に仮想ネットワークが作成されます。 このネットワークは、次のセクションで HDInsight 用に作成するネットワークにピアリングされます。

## <a name="configure-virtual-network-peering"></a>仮想ネットワーク ピアリングを構成する

1. [Azure Portal](https://portal.azure.com) から __[リソース グループ]__ を選択し、AKS クラスター用の仮想ネットワークが含まれているリソース グループを探します。 リソース グループ名は `MC_<resourcegroup>_<akscluster>_<location>` です。 `resourcegroup` と `akscluster` はそれぞれ、クラスターを作成したリソース グループの名前と、クラスターの名前になります。 `location` は、クラスターを作成した場所です。

2. リソース グループで、 __[仮想ネットワーク]__ リソースを選択します。

3. __[アドレス空間]__ を選択します。 表示されたアドレス空間をメモします。

4. HDInsight 用の仮想ネットワークを作成するには、 __[+ リソースの作成]__ 、 __[ネットワーク]__ 、 __[仮想ネットワーク]__ の順に選択します。

    > [!IMPORTANT]  
    > 新しい仮想ネットワークの値を入力する際には、AKS クラスター ネットワークに使用されているものと重複しないアドレス空間を使用する必要があります。

    AKS クラスターに使用した仮想ネットワークと同じ __場所__ を使用します。

    仮想ネットワークが作成されるまで待ち、その後、次の手順に進みます。

5. HDInsight ネットワークと AKS クラスター ネットワークの間のピアリングを構成するには、仮想ネットワークを選択し、 __[ピアリング]__ を選択します。 __[+ 追加]__ を選択し、次の値を使用してフォームに値を入力します。

   * __[名前]__ :このピアリング構成の一意の名前を入力します。
   * __仮想ネットワーク__:**AKS クラスター**用の仮想ネットワークを選択するには、このフィールドを使用します。

     その他のフィールドはすべて既定値のままにし、 __[OK]__ を選択してピアリングを構成します。

6. AKS クラスター ネットワークと HDInsight ネットワークの間のピアリングを構成するには、__AKS クラスターの仮想ネットワーク__ を選択し、 __[ピアリング]__ を選択します。 __[+ 追加]__ を選択し、次の値を使用してフォームに値を入力します。

   * __[名前]__ :このピアリング構成の一意の名前を入力します。
   * __仮想ネットワーク__:__HDInsight クラスター__ 用の仮想ネットワークを選択するには、このフィールドを使用します。

     その他のフィールドはすべて既定値のままにし、 __[OK]__ を選択してピアリングを構成します。

## <a name="install-apache-kafka-on-hdinsight"></a>HDInsight に Apache Kafka をインストールする

Kafka HDInsight クラスターを作成する際には、先ほど HDInsight 用に作成した仮想ネットワークに参加する必要があります。 Kafka クラスターの作成の詳細については、[Apache Kafka クラスターの作成](apache-kafka-get-started.md)に関するドキュメントを参照してください。

> [!IMPORTANT]  
> クラスターを作成する際には、__詳細設定__ を使用して、HDInsight 用に作成した仮想ネットワークに接続する必要があります。

## <a name="configure-apache-kafka-ip-advertising"></a>Apache Kafka の IP アドバタイズを構成する

次の手順を使用して、ドメイン名の代わりに IP アドレスを提供するように Kafka を構成します。

1. Web ブラウザーを使用し、 https://CLUSTERNAME.azurehdinsight.net にアクセスします。 __CLUSTERNAME__ を HDInsight クラスター上の Kafka の名前に置き換えます。

    プロンプトが表示されたら、クラスターの HTTPS ユーザー名とパスワードを入力します。 クラスターの Ambari Web UI が表示されます。

2. Kafka に関する情報を表示するには、左にある一覧から __[Kafka]__ を選択します。

    ![Kafka が強調表示されているサービスの一覧](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Kafka 構成を表示するには、上部中央の __[Configs (構成)]__ を選択します。

    ![Kafka の構成リンク](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. __kafka-env__ 構成を検索するには、右上の __[Filter (フィルター)]__ フィールドに「`kafka-env`」と入力します。

    ![kafka-env の Kafka 構成](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. IP アドレスを提供するように Kafka を構成するには、次のテキストを __kafka-env-template__ フィールドの最後に追加します。

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka がリッスンするインターフェイスを構成するには、右上の __[Filter (フィルター)]__ フィールドに「`listeners`」と入力します。

7. すべてのネットワーク インターフェイスをリッスンするように Kafka を構成するには、 __[listeners (リスナー)]__ フィールドの値を `PLAINTEXT://0.0.0.0:9092`に変更します。

8. 構成を保存するには、 __[Save (保存)]__ ボタンを使用します。 変更を説明するテキスト メッセージを入力します。 変更が保存されたら、 __[OK]__ を保存します。

    ![構成を保存するボタン](./media/apache-kafka-azure-container-services/save-button.png)

9. Kafka の再起動時にエラーが発生しないようにするため、 __[Service Actions (サービス アクション)__ ] ボタンを使用して __[Turn On Maintenance Mode (メンテナンス モードの有効化)]__ を選択します。 [OK] を選択して、この操作を完了します。

    ![[Turn On Maintenance Mode (メンテナンス モードの有効化)] が強調表示されているサービス アクション](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Kafka を再起動するには、 __[Restart (再起動)]__ ボタンをクリックし、 __[Restart All Affected (影響を受けるものをすべて再起動)]__ を選択します。 再起動を確認し、操作が完了したら __[OK]__ ボタンを使用します。

    ![[Restart All Affected (影響を受けるものをすべて再起動)] が強調表示されている [Restart (再起動)] ボタン](./media/apache-kafka-azure-container-services/restart-button.png)

11. メンテナンス モードを無効にするには、 __[Service Actions (サービス アクション)]__ ボタンをクリックし、 __[Turn Off Maintenance Mode (メンテナンス モードの無効化)]__ を選択します。 **[OK]** を選択して、この操作を完了します。

## <a name="test-the-configuration"></a>構成をテストする

この時点で、Kafka と Azure Kubernetes Service はピアリングされた仮想ネットワークを通じて通信できるようになっています。 この接続をテストするには、次の手順に従います。

1. テスト アプリケーションによって使用される Kafka トピックを作成します。 Kafka トピックを作成する方法については、[Apache Kafka クラスターの作成](apache-kafka-get-started.md)に関するドキュメントをご覧ください。

2. サンプル アプリケーションは [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) からダウンロードできます。

3. `index.js` ファイルを編集し、次の行を変更します。

    * `var topic = 'mytopic'`:`mytopic` を、このアプリケーションで使用される Kafka トピックの名前に置き換えます。
    * `var brokerHost = '176.16.0.13:9092`:`176.16.0.13` を、クラスターのいずれかのブローカー ホストの内部 IP アドレスに置き換えます。

        クラスター内のブローカー ホスト (workernodes) の内部 IP アドレスを取得する方法については、[Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) に関するドキュメントを参照してください。 ドメイン名が `wn` で始まるいずれかのエントリの IP アドレスを選択します。

4. `src` ディレクトリのコマンドラインから、依存関係をインストールし、Docker を使用してデプロイ用のイメージを作成します。

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > このアプリケーションで必要となるパッケージはリポジトリにチェックインされるので、`npm` ユーティリティを使用してそれらをインストールする必要はありません。

5. Azure Container Registry (ACR) にログインし、loginServer 名を探します。

    ```bash
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

    ![Web ページの画像](./media/apache-kafka-azure-container-services/test-web-page.png)

12. フィールドにテキストを入力し、 __[送信]__ ボタンを選択します。 データが Kafka に送信されます。 その後、アプリケーション内の Kafka コンシューマーによりメッセージが読み取られ、 __[Messages from Kafka]__ セクションに追加されます。

    > [!WARNING]  
    > 同じメッセージが複数返されることがあります。 この問題は通常、接続後にブラウザーを更新した場合や、アプリケーションへのブラウザー接続を複数開いた場合に発生します。

## <a name="next-steps"></a>次の手順

次のリンクを使用することで、HDInsight で Apache Kafka を使用する方法を知ることができます。

* [HDInsight での Apache Kafka の使用](apache-kafka-get-started.md)

* [MirrorMaker を使用して HDInsight 上の Apache Kafka のレプリカを作成する](apache-kafka-mirroring.md)

* [HDInsight 上の Apache Kafka で Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)

* [HDInsight 上の Apache Kafka で Apache Spark を使用する](../hdinsight-apache-spark-with-kafka.md)

* [Azure Virtual Network 経由で Apache Kafka に接続する](apache-kafka-connect-vpn-gateway.md)
