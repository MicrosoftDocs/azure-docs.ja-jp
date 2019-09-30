---
title: Azure CLI を使用して Apache Hadoop クラスターを作成する - Azure HDInsight
description: クロス プラットフォーム Azure CLI を使用して Azure HDInsight クラスターを作成する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: c26c0b16331ae01f7505e44cef3fe91b3282750b
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70809852"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Azure CLI を使用した HDInsight クラスターの作成

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

このドキュメントの手順では、Azure CLI を使用した HDInsight 3.6 クラスターの作成を順を追って説明します。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

Azure CLI。 Azure CLI をインストールしていない場合、手順については [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関する記事をご覧ください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>クラスターの作成

1. Azure サブスクリプションにログインします。 Azure Cloud Shell を使用する予定の場合は、コード ブロックの右上隅で **[試してみる]** を選択するだけです。 それ以外の場合は、下のコマンドを入力します。

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. 環境変数を設定します。 この記事での変数の使用は Bash に基づきます。 その他の環境では、若干の調整が必要となります。 クラスターの作成に使用できるパラメーターの完全な一覧については、[az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) を参照してください。

    |パラメーター | 説明 |
    |---|---|
    |`--size`| クラスター内のワーカー ノードの数。 この記事では、`--size` に渡される値として変数 `clusterSizeInNodes` を使用します。 |
    |`--version`| HDInsight クラスターのバージョン。 この記事では、`--version` に渡される値として変数 `clusterVersion` を使用します。 関連項目:[サポートされる HDInsight のバージョン](./hdinsight-component-versioning.md#supported-hdinsight-versions)。|
    |`--type`| hadoop、interactivehive、hbase、kafka、storm、spark、rserver、mlservices などの HDInsight クラスターの種類。  この記事では、`--type` に渡される値として変数 `clusterType` を使用します。 関連項目:[クラスターの種類と構成](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。|
    |`--component-version`|さまざまな Hadoop コンポーネントのバージョン。バージョンは 'component=version' の形式でスペースで区切られます。 この記事では、`--component-version` に渡される値として変数 `componentVersion` を使用します。 関連項目:[Hadoop コンポーネント](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)。|

    `RESOURCEGROUPNAME`、`LOCATION`、`CLUSTERNAME`、`STORAGEACCOUNTNAME`、および `PASSWORD` を目的の値に置き換えます。 必要に応じて、他の変数の値を変更します。 続いて CLI コマンドを入力します。

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. 次のコマンドを入力して、[リソース グループを作成します](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)。

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    グループの作成先として有効な場所は、`az account list-locations` コマンドで一覧表示できます。`name` 値に表示されるいずれかの場所を使用してください。

4. 次のコマンドを入力して、[Azure ストレージ アカウントを作成します](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)。

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. 次のコマンドを入力して、[Azure ストレージ アカウントからプライマリ キーを抽出し](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list)、それを変数に保存します。

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. 次のコマンドを入力して、[Azure ストレージ コンテナーを作成します](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)。

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. 次のコマンドを入力して、[HDInsight クラスターを作成します](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)。

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight クラスターにはさまざまな種類があり、それぞれに適したワークロードやテクノロジに対応しています。 複数の種類 (Storm と HBase など) を組み合わせたクラスターを作成することはできません。

    クラスターの作成処理は、完了までに数分かかる場合があります。 通常は約 15 です。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

記事を完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

次のコマンドのすべてまたは一部を入力して、リソースを削除します。

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](./hdinsight-hadoop-customize-cluster-linux.md#access-control)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure CLI を使用して HDInsight クラスターを作成したら、クラスターの使用方法について、以下のトピックを参照してください。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop クラスター

* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hadoop/hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase クラスター

* [HDInsight での Apache HBase の使用](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight での Apache HBase の Java アプリケーションの開発](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm クラスター

* [HDInsight での Apache Storm の Java トポロジの開発](storm/apache-storm-develop-java-topology.md)
* [HDInsight の Apache Storm での Python コンポーネントの使用](storm/apache-storm-develop-python-topology.md)
* [Deploy and monitor topologies with Apache Storm on HDInsight (HDInsight の Storm を使用したトポロジのデプロイと監視)](storm/apache-storm-deploy-monitor-topology-linux.md)
