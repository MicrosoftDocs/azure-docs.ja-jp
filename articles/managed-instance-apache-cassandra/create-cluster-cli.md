---
title: クイックスタート - CLI を使用して Azure Managed Instance for Apache Cassandra クラスターを作成する
description: Azure CLI を使用して Azure Managed Instance for Apache Cassandra クラスターを作成するには、このクイックスタートをご利用ください。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: ef8ef85dde11eb991f14201286dc1a086df71dc8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588591"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>クイックスタート: Azure CLI を使用して Azure Managed Instance for Apache Cassandra クラスターを作成する (プレビュー)

Azure Managed Instance for Apache Cassandra は、マネージドなオープンソースの Apache Cassandra データセンターのデプロイ操作とスケーリング操作を自動化します。 このサービスは、ハイブリッド シナリオの促進と継続的保守管理の削減に役立ちます。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このクイックスタートでは、Azure CLI コマンドを使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法を紹介します。 また、データセンターの作成のほか、データセンター内のノードのスケールアップとスケールダウンについても取り上げます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* セルフホステッドまたはオンプレミス環境に接続された [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)。 オンプレミス環境を Azure に接続する方法の詳細については、「[オンプレミス ネットワークの Azure への接続](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)」の記事を参照してください。

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!IMPORTANT]
> この記事では、Azure CLI バージョン 2.17.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>マネージド インスタンス クラスターを作成する

1. [Azure ポータル](https://portal.azure.com/)

1. Azure CLI でサブスクリプション ID を設定します。

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. 次に、仮想ネットワークと専用サブネットをご使用のリソース グループに作成します。

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. その仮想ネットワークとサブネットに、マネージド インスタンスで必要となるいくつかの特殊なアクセス許可を適用します。 `az role assignment create` コマンドを使用します。`<subscription ID>`、`<resource group name>`、`<VNet name>`、`<subnet name>` は、適切な値に置き換えてください。

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > 前のコマンドの `assignee` と `role` の値は固定値です。コマンドに記載されているように、これらの値を正確に入力してください。 そうしないと、クラスターの作成時にエラーが発生します。 このコマンドの実行中にエラーが発生した場合は、実行するためのアクセス許可がない可能性があります。管理者に連絡してアクセス許可を求めてください。

1. 次に、[az managed-cassandra cluster create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create) コマンドを使用して、新しく作成した仮想ネットワークにクラスターを作成します。 `delegatedManagementSubnetId` 変数の値を使用して次のコマンドを実行します。

   > [!NOTE]
   > 以下で指定する `delegatedManagementSubnetId` 変数の値は、上記のコマンドで指定した `--scope` の値とまったく同じです。

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. 最後に、[az managed-cassandra datacenter create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create) コマンドを使用して、3 つのノードを含むクラスターのデータセンターを作成します。

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. データセンターの作成後、データセンター内のノードをスケールアップまたはスケールダウンしたい場合は、[az managed-cassandra datacenter update](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update) コマンドを実行します。 `node-count` パラメーターの値は、必要な値に変更してください。

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>クラスターへの接続

Azure Managed Instance for Apache Cassandra によって作成されるノードには、パブリック IP アドレスが割り当てられていません。 新しく作成した Cassandra クラスターに接続するには、仮想ネットワーク内に別のリソースを作成する必要があります。 たとえばアプリケーション リソースや、Apache のオープンソース クエリ ツール [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) がインストールされている仮想マシン リソースです。 Ubuntu 仮想マシンは、[Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)を使用してデプロイできます。 デプロイ後、SSH を使用してそのマシンに接続し、CQLSH をインストールします。次のコマンドはその例です。

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>トラブルシューティング

Virtual Network にアクセス許可を適用するときにエラー (例えば、"*Cannot find user or service principal in graph database for 'e5007d2c-4b13-4a74-9b6a-605d99f03501' ('e5007d2c-4b13-4a74-9b6a-605d99f03501' に対するユーザーまたはサービス プリンシパルがグラフ データベース内で見つかりません)* " など) が発生した場合は、Azure portal から同じアクセス許可を手動で適用できます。 ポータルからアクセス許可を適用するには、既存の仮想ネットワークの **[アクセス制御 (IAM)]** ペインにアクセスし、"Azure Cosmos DB" のロール割り当てを "ネットワーク管理者" ロールに追加します。 "Azure Cosmos DB" を検索したときに 2 つのエントリが表示される場合は、次の図に示すように両方のエントリを追加します。 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="アクセス許可を適用する" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Azure Cosmos DB のロールの割り当ては、デプロイの目的にのみ使用されます。 Azure Managed Instance for Apache Cassandra には、Azure Cosmos DB に対するバックエンドの依存関係はありません。  

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、`az group delete` コマンドを使用して、リソース グループとマネージド インスタンス、およびすべての関連リソースを削除できます。

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure CLI を使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法について学習しました。 これで、クラスターの操作を開始できます。

> [!div class="nextstepaction"]
> [Azure Databricks でマネージド Apache Spark クラスターをデプロイする](deploy-cluster-databricks.md)
