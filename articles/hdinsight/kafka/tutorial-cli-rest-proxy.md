---
title: チュートリアル:Azure CLI を使用して Apache Kafka REST プロキシ対応のクラスターを HDInsight に作成する
description: Azure HDInsight で Kafka REST プロキシを使用して Apache Kafka 操作を実行する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 44951fc19f36bb6652caf79ded96484bcc4b38f1
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503142"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>チュートリアル:Azure CLI を使用して Apache Kafka REST プロキシ対応のクラスターを HDInsight に作成する

このチュートリアルでは、Azure コマンド ライン インターフェイス (CLI) を使って Apache Kafka の [REST プロキシ対応](./rest-proxy.md)クラスターを Azure HDInsight に作成する方法を説明します。 Azure HDInsight は、全範囲に対応した、オープンソースのエンタープライズ向けマネージド分析サービスです。 Apache Kafka は、オープンソースの分散ストリーミング プラットフォームです。 発行/サブスクライブ メッセージ キューと同様の機能を備えているため、メッセージ ブローカーとして多く使われています。 Kafka REST プロキシを使用すると、HTTP 経由の [REST API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) を使用して Kafka クラスターを操作することができます。 Azure CLI は、Azure リソースを管理するための、Microsoft のクロスプラットフォーム コマンド ライン エクスペリエンスです。

Apache Kafka API は、同じ仮想ネットワーク内のリソースによってのみアクセスできます。 SSH を使って直接クラスターにアクセスできます。 他のサービス、ネットワーク、または仮想マシンを Apache Kafka に接続するには、まず、仮想ネットワークを作成してから、ネットワーク内にリソースを作成する必要があります。 詳細については、[仮想ネットワークを使用した Apache Kafka への接続](./apache-kafka-connect-vpn-gateway.md)に関するページを参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Kafka REST プロキシの前提条件
> * Azure CLI を使用して Apache Kafka クラスターを作成する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure AD に登録されたアプリケーション。 Kafka REST プロキシを操作するために記述するクライアント アプリケーションは、このアプリケーションの ID とシークレットを使用して Azure に対する認証を行います。 詳細については、[Microsoft ID プラットフォームにアプリケーションを登録する](../../active-directory/develop/quickstart-register-app.md)方法に関するページを参照してください。

* 登録済みのアプリケーションをメンバーとして含む Azure AD セキュリティ グループ。 このセキュリティ グループは、REST プロキシの操作を許可するアプリケーションを制御するために使用されます。 Azure AD グループの作成方法の詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

* Azure CLI。 バージョンは 2.0.79 以上であることが必要です。 「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka クラスターを作成する

1. Azure サブスクリプションにサインインします。

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. 環境変数を設定します。 このチュートリアルでの変数の使用は Bash に基づきます。 その他の環境では、若干の調整が必要となります。

    |変数 | 説明 |
    |---|---|
    |resourceGroupName|RESOURCEGROUPNAME を新しいリソース グループの名前に置き換えます。|
    |location|LOCATION をクラスターの作成先リージョンに置き換えます。 有効な場所の一覧については、`az account list-locations` コマンドを使用してください。|
    |clusterName|CLUSTERNAME を、新しいクラスターのグローバルに一意な名前に置き換えます。|
    |storageAccount|STORAGEACCOUNTNAME を新しいストレージ アカウントの名前に置き換えます。|
    |httpPassword|PASSWORD をクラスター ログイン (**admin**) のパスワードに置き換えます。|
    |sshPassword|PASSWORD を Secure Shell のユーザー名 (**sshuser**) のパスワードに置き換えます。|
    |securityGroupName|SECURITYGROUPNAME を、Kafka REST プロキシのクライアント AAD セキュリティ グループ名に置き換えます。 `az-hdinsight-create` の `--kafka-client-group-name` パラメーターには、この変数が渡されます。|
    |securityGroupID|SECURITYGROUPID を、Kafka REST プロキシのクライアント AAD セキュリティ グループ ID に置き換えます。 `az-hdinsight-create` の `--kafka-client-group-id` パラメーターには、この変数が渡されます。|
    |storageContainer|クラスターによって使用されるストレージ コンテナー。このチュートリアルでは、現状のままにしておいてください。 この変数は、クラスターの名前を使用して設定されます。|
    |workernodeCount|クラスターのワーカー ノードの数。このチュートリアルでは、現状のままにしておいてください。 高可用性を保証するために、Kafka には最低 3 つのワーカー ノードが必要です|
    |clusterType|HDInsight クラスターの種類。このチュートリアルでは、現状のままにしておいてください。|
    |clusterVersion|HDInsight クラスターのバージョン。このチュートリアルでは、現状のままにしておいてください。 Kafka REST プロキシには、バージョン 4.0 以上のクラスターが必要です。|
    |componentVersion|Kafka のバージョン。このチュートリアルでは、現状のままにしておいてください。 Kafka REST プロキシには、バージョン 2.1 以上のコンポーネントが必要です。|

    変数を適切な値に更新してください。 続いて環境変数を設定する CLI コマンドを入力します。

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. 次のコマンドを入力して、[リソース グループを作成します](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)。

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. 次のコマンドを入力して、[Azure ストレージ アカウントを作成します](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)。

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. 次のコマンドを入力して、[Azure Storage アカウントからプライマリ キーを抽出し](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list)、それを変数に保存します。

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. 次のコマンドを入力して、[Azure ストレージ コンテナーを作成します](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)。

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [HDInsight クラスターを作成します](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)。 コマンドを入力する前に、次のパラメーターに注意してください。

    1. Kafka クラスターに必要なパラメーターは次のとおりです。

        |パラメーター | 説明|
        |---|---|
        |--type|この値は **Kafka** にする必要があります。|
        |--workernode-data-disks-per-node|ワーカー ノードごとに使用するデータ ディスクの数。 HDInsight Kafka はデータ ディスクでのみサポートされます。 このチュートリアルでは、値として **2** を使用します。|

    1. Kafka REST プロキシに必要なパラメーターは次のとおりです。

        |パラメーター | 説明|
        |---|---|
        |--kafka-management-node-size|ノードのサイズ。 このチュートリアルでは、値として **Standard_D4_v2** を使用します。|
        |--kafka-client-group-id|Kafka REST プロキシのクライアント AAD セキュリティ グループ ID。 この値は、変数 **$securityGroupID** から渡されます。|
        |--kafka-client-group-name|Kafka REST プロキシのクライアント AAD セキュリティ グループ名。 この値は、変数 **$securityGroupName** から渡されます。|
        |--version|HDInsight クラスターのバージョンは 4.0 以上であることが必要です。 この値は、変数 **$clusterVersion** から渡されます。|
        |--component-version|Kafka のバージョンは 2.1 以上であることが必要です。 この値は、変数 **$componentVersion** から渡されます。|
    
        REST プロキシなしでクラスターを作成したい場合は、`az hdinsight create` コマンドから `--kafka-management-node-size`、`--kafka-client-group-id`、`--kafka-client-group-name` を除外してください。

    1. 既存の仮想ネットワークがある場合は、パラメーター `--vnet-name` と `--subnet`、およびその値を追加します。

    次のコマンドを入力して、クラスターを作成します。

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    クラスターの作成処理は、完了までに数分かかる場合があります。 通常は約 15 です。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

記事を完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

次のコマンドのすべてまたは一部を入力して、リソースを削除します。

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>次のステップ

Azure CLI を使用して Apache Kafka REST プロキシ対応のクラスターを Azure HDInsight に作成できたら、Python コードを使用して REST プロキシを対話的に操作してみましょう。

> [!div class="nextstepaction"]
> [サンプル アプリケーションの作成](./rest-proxy.md#client-application-sample)
