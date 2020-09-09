---
title: クイック スタート:Azure CLI を使用して Synapse ワークスペースを作成する
description: このガイドの手順に従って Azure CLI を使用し、Azure Synapse ワークスペースを作成します。
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 7f0bf7d409c5e47abaaa5b59271f55952d8ccff4
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871590"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Synapse ワークスペースを作成する

Azure CLI は、Azure リソースを管理するための、Azure のコマンド ライン エクスペリエンスです。 ブラウザーで、Azure Cloud Shell を使用して操作することができます。 また、macOS、Linux、または Windows 上にインストールし、コマンド ラインから実行することもできます。

このクイックスタートでは、Azure CLI を使用して Synapse ワークスペースを作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- 軽量で柔軟性の高いコマンド ライン JSON プロセッサ、[jq](https://stedolan.github.io/jq/download/) をダウンロードしてインストールします。
- [Azure Data Lake Storage Gen2 ストレージ アカウント](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > Azure Synapse ワークスペースは、選択した ADLS Gen2 アカウントの読み取りと書き込みを行うことができる必要があります。 加えて、プライマリ ストレージ アカウントとしてリンクするストレージ アカウントについては、「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account)」ページの説明に従って、その作成時に**階層型名前空間**を有効にする必要があります。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Azure CLI をローカルにインストールする

Azure CLI をローカルにインストールして使用する場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

Azure CLI をローカルで実行している場合、ログインして認証を行う必要があります。 Azure Cloud Shell を使用している場合、この手順は必要ありません。 Azure CLI にログインするには、`az login` を実行して、ブラウザー ウィンドウで認証を行います。

```azurecli
az login
```

Azure CLI を使用した認証について詳しくは、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」をご覧ください。

## <a name="install-azure-synapse-extension-for-azure-cli"></a>Azure CLI 用 Azure Synapse 拡張機能をインストールする

```azurecli
az extension add --name synapse
```

> [!WARNING]
> Azure CLI 用 Azure Synapse 拡張機能はプレビュー段階です。

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Azure CLI を使用して Azure Synapse ワークスペースを作成する

1. Azure Synapse ワークスペース用のリソースを作成するために、必要な環境変数を定義します。

    | 環境変数の名前 | 説明 |
    |---|---|---|
    |StorageAccountName| 既存の ADLS Gen2 ストレージ アカウントの名前。|
    |StorageAccountResourceGroup| 既存の ADLS Gen2 ストレージ アカウントのリソース グループの名前。 |
    |FileShareName| 既存のストレージ ファイル システムの名前。|
    |SynapseResourceGroup| Azure Synapse リソース グループの新しい名前を選択します。 |
    |リージョン| いずれかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/geographies/#overview)を選択します。 |
    |SynapseWorkspaceName| 新しい Azure Synapse ワークスペースの一意の名前を選択します。 |
    |SqlUser| 新しいユーザー名の値を選択します。|
    |SqlPassword| セキュリティで保護されたパスワードを選択します。|
    |||

2. Azure Synapse ワークスペースのコンテナーとしてリソース グループを作成します。
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. ADLS Gen 2 ストレージ アカウント キーを取得します。
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. ADLS Gen 2 ストレージ エンドポイント URL を取得します。
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. (オプション) ADLS Gen2 のストレージ アカウント キーとエンドポイントは、次のようにしていつでも確認できます。
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. Azure Synapse ワークスペースを作成します。
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. Azure Synapse ワークスペースの Web URL と開発 URL を取得します。
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. ご使用のマシンから Azure Synapse ワークスペースへのアクセスを許可するファイアウォール規則を作成します。

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. 環境変数 `WorkspaceWeb` に格納された Azure Synapse ワークスペースの Web URL アドレスを開いてワークスペースにアクセスします。

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [ ![Azure Synapse ワークスペース Web](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順に従って、Azure Synapse ワークスペースを削除します。
> [!WARNING]
> Azure Synapse ワークスペースを削除すると、分析エンジンと、含まれている SQL プールとワークスペース メタデータのデータベースに格納されているデータが削除されます。 以後、SQL または Apache Spark のエンドポイントには接続できません。 すべてのコード成果物が削除されます (クエリ、ノートブック、ジョブ定義、およびパイプライン)。
>
> ワークスペースを削除しても、そのワークスペースにリンクされている Data Lake Store Gen2 のデータには影響**しません**。

Azure Synapse ワークスペースを削除する場合は、次のコマンドを実行します。

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>次のステップ

次に、データの分析と探索を開始するために、[SQL プールを作成](quickstart-create-sql-pool-studio.md)するか、[Apache Spark プールを作成](quickstart-create-apache-spark-pool-studio.md)することができます。
