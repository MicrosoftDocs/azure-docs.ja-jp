---
title: Azure CLI を使用した Azure Cosmos DB リソースの管理
description: Azure CLI を使用し、Azure Cosmos DB のアカウント、データベース、コンテナーを作成します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f9d8bf9161343e4b36a3c16209873962b69d8af5
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615206"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Azure CLI を使用した Azure Cosmos リソースの管理

次のガイドでは、Azure CLI を使用して Azure Cosmos DB のアカウント、データベース、コンテナーの管理を自動化するための共通のコマンドについて説明します。 Azure Cosmos DB CLI のすべてのコマンドのリファレンス ページは、[Azure CLI リファレンス](https://docs.microsoft.com/cli/azure/cosmosdb)で確認できます。 [Azure Cosmos DB 向け Azure CLI サンプル](cli-samples.md)にも、MongoDB、Gremlin、Cassandra、Table API で Cosmos DB のアカウント、データベース、コンテナーを作成し、管理する方法などを含むその他の例があります。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

SQL API で Azure Cosmos DB アカウントを作成し、米国東部と米国西部でセッション整合性を指定するには、次のコマンドを実行します。

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Azure Cosmos アカウントの名前は小文字にする必要があります。

## <a name="create-a-database"></a>データベースを作成する

Cosmos データベースを作成するには、次のコマンドを実行します。

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>コンテナーを作成する

1 秒あたりの RU が 400 でパーティション キーを持つ Cosmos コンテナーを作成するには、次のコマンドを実行します。

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>コンテナーのスループットの変更

Cosmos コンテナーのスループットを毎秒 1000 RU に変更するには、次のコマンドを実行します。

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>アカウント キーの一覧表示

Cosmos アカウントのキーを取得するには、次のコマンドを実行します。

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>接続文字列の一覧表示

Cosmos アカウントの接続文字列を取得するには、次のコマンドを実行します。

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>アカウント キーの再生成

Cosmos アカウントの主キーを新しく生成するには、次のコマンドを実行します。

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、次をご覧ください。

- [Azure CLI のインストール](/cli/azure/install-azure-cli)
- [Azure CLI リファレンス](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Azure Cosmos DB の追加 Azure CLI サンプル](cli-samples.md)
