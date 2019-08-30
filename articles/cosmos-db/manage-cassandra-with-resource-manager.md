---
title: Azure Cosmos DB Cassandra API の Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB Cassandra API を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: beae89b3f8e21e2f56a1dbf2090b7a612ed5cab0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815115"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure Cosmos DB Cassandra API リソースを管理する

## Azure Cosmos アカウント、キースペース、およびテーブルを作成する<a id="create-resource"></a>

Azure Resource Manager テンプレートを使用して Azure Cosmos DB リソースを作成します。 このテンプレートは、キースペース レベルで 400 RU/秒のスループットを共有する 2 つのテーブルを含む Cassandra API の Azure Cosmos アカウントを作成します。 テンプレートをコピーして次に示すようにデプロイするか、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/)にアクセスして Azure portal からデプロイします。 テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

> [!NOTE]
> アカウント名は小文字とし、31 文字未満にする必要があります。

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

Azure CLI を使用して Resource Manager テンプレートをデプロイするには、スクリプトの **[コピー]** を実行し、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` コマンドは、新しく作成された Azure Cosmos アカウントをそのプロビジョニング後に表示します。 Cloud Shell を使用せずに、Azure CLI のローカルでインストールされたバージョンを使用する場合、「[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/)」の記事を参照してください。

## キースペースのスループット (RU/秒) の更新する<a id="keyspace-ru-update"></a>

次のテンプレートでは、キースペースのスループットが更新されます。 テンプレートをコピーして次に示すようにデプロイするか、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-keyspace-ru-update/)にアクセスして Azure portal からデプロイします。 テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

[!code-json[cosmosdb-cassandra-keyspace-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json)]

### <a name="deploy-keyspace-template-via-azure-cli"></a>Azure CLI を使用してキースペース テンプレートをデプロイする

Azure CLI を使用して Resource Manager テンプレートをデプロイするには、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-keyspace-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName throughput=$throughput
```

## テーブルのスループット (RU/秒) の更新する<a id="table-ru-update"></a>

次のテンプレートでは、テーブルのスループットが更新されます。 テンプレートをコピーして次に示すようにデプロイするか、[Azure クイック スタート ギャラリー](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra-table-ru-update/)にアクセスして Azure portal からデプロイします。 テンプレートをローカル コンピューターにダウンロードするか、新しいテンプレートを作成して、`--template-file` パラメーターでローカル パスを指定することもできます。

[!code-json[cosmosdb-cassandra-table-ru-update](~/quickstart-templates/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-template-via-azure-cli"></a>Azure CLI を使用してテーブル テンプレートをデプロイする

Azure CLI を使用して Resource Manager テンプレートをデプロイするには、 **[試してみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the keyspace name: ' keyspaceName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName keyspaceName=$keyspaceName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>次の手順

次にその他のリソースを示します。

- [Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)
- [Azure Cosmos DB リソース プロバイダー スキーマ](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Azure Resource Manager デプロイの一般的なエラーのトラブルシューティング](../azure-resource-manager/resource-manager-common-deployment-errors.md)
