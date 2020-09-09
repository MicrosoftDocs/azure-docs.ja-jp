---
title: Azure Cosmos DB Gremlin API の Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB Gremlin API を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 2ce6020d31f52a81450bfb7f8be499b13f2ce356
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86028196"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure Cosmos DB Gremlin API リソースを管理する

この記事では、ご利用の Azure Cosmos DB アカウント、データベース、およびグラフのデプロイと管理に役立つ Azure Resource Manager テンプレートの使用方法について説明します。

この記事に含まれているのは、Gremlin API アカウントの例のみです。他の種類の API アカウントでの例については、[Cassandra](manage-cassandra-with-resource-manager.md)、[SQL](manage-sql-with-resource-manager.md)、[MongoDB](manage-mongodb-with-resource-manager.md)、[Table](manage-table-with-resource-manager.md) 用の Azure Cosmos DB の API での Azure Resource Manager テンプレートの使用に関する記事を参照してください。

> [!IMPORTANT]
>
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新してテンプレートを再配置します。
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。

以下の Azure Cosmos DB リソースを作成するには、次のサンプル テンプレートを新しい json ファイルにコピーします。 必要に応じて、異なる名前と値を持つ同じリソースの複数のインスタンスをデプロイするときに使用するパラメーター json ファイルを作成することもできます。 Azure Resource Manager テンプレートをデプロイするには、[Azure portal](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)、[GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md) など、さまざまな方法があります。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-autoscale-provisioned-throughput"></a>自動スケーリングでプロビジョニングされたスループットを使用した Gremlin 用の Azure Cosmos DB アカウント

このテンプレートは、自動スケーリングのスループットでデータベースとグラフを含む Gremlin API の Azure Cosmos アカウントを作成します。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-standard-provisioned-throughput"></a>標準でプロビジョニングされたスループットを使用した Gremlin 用の Azure Cosmos DB アカウント

このテンプレートは、標準 (手動) スループットでデータベースとグラフを含む Gremlin API の Azure Cosmos アカウントを作成します。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="next-steps"></a>次のステップ

次にその他のリソースを示します。

* [Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)
* [Azure Cosmos DB リソース プロバイダー スキーマ](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Azure Resource Manager デプロイの一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)
