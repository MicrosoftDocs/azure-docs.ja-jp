---
title: クイック スタート:Azure CLI を使用した Azure HDInsight での Apache Spark クラスターの作成
description: このクイック スタートでは、Azure CLI を使って、Azure HDInsight に Apache Spark クラスターを作成する方法を示します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 72bdab9d7fb5c3019d97ffc4c92257c49ec2b8e5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066244"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure HDInsight 内に Apache Spark クラスターを作成する

このクイックスタートでは、Azure CLI を使って Azure HDInsight に Apache Spark クラスターを作成する方法を説明します。 Apache Spark により、メモリ内処理を使用した、高速のデータ分析とクラスター コンピューティングが可能になります。 [Azure コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) は、Azure リソースを管理するための、Microsoft のクロスプラットフォーム コマンド ライン エクスペリエンスです。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

Azure CLI。 Azure CLI をインストールしていない場合、手順については [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関する記事をご覧ください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Apache Spark クラスターの作成

1. Azure サブスクリプションにサインインします。 Azure Cloud Shell を使用する予定の場合は、コード ブロックの右上隅で **[試してみる]** を選択するだけです。 それ以外の場合は、下のコマンドを入力します。

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. 環境変数を設定します。 このクイックスタートでの変数の使用は Bash に基づきます。 その他の環境では、若干の調整が必要となります。 下のコード スニペットの RESOURCEGROUPNAME、LOCATION、CLUSTERNAME、STORAGEACCOUNTNAME、および PASSWORD は、目的の値に置き換えてください。 続いて環境変数を設定する CLI コマンドを入力します。

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. 次のコマンドを入力して、リソース グループを作成します。

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. 次のコマンドを入力して、Azure ストレージ アカウントを作成します。

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. 次のコマンドを入力して、Azure ストレージ アカウントからプライマリ キーを抽出し、それを変数に保存します。

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. 次のコマンドを入力して、Azure ストレージ コンテナーを作成します。

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. 次のコマンドを入力して、Apache Spark クラスターを作成します。

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

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

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

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure CLI を使って Azure HDInsight に Apache Spark クラスターを作成する方法を説明しました。  HDInsight Spark クラスターを使用してサンプル データに対話型のクエリを実行する方法については、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Apache Spark への対話型クエリの実行](./apache-spark-load-data-run-query.md)
