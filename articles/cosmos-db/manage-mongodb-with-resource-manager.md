---
title: MongoDB 用 Azure Cosmos DB API の Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、MongoDB 用 Azure Cosmos DB API を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: b0a6bf3dd6585b600ff7283f6fd47cb87b68feda
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86028182"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure Cosmos DB MongoDB API リソースを管理する

この記事では、ご利用の MongoDB API の Azure Cosmos DB アカウント、データベース、およびコレクションのデプロイと管理に役立つ Azure Resource Manager テンプレートの使用方法について説明します。

この記事に含まれているのは、MongoDB 用の Azure Cosmos DB の API の例のみです。他の種類の API アカウントでの例については、[Cassandra](manage-cassandra-with-resource-manager.md)、[Gremlin](manage-gremlin-with-resource-manager.md)、[SQL](manage-sql-with-resource-manager.md)、[Table](manage-table-with-resource-manager.md) 用の Azure Cosmos DB の API での Azure Resource Manager テンプレートの使用に関する記事を参照してください。

> [!IMPORTANT]
>
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新してテンプレートを再配置します。
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。

以下の Azure Cosmos DB リソースを作成するには、次のサンプル テンプレートを新しい json ファイルにコピーします。 必要に応じて、異なる名前と値を持つ同じリソースの複数のインスタンスをデプロイするときに使用するパラメーター json ファイルを作成することもできます。 Azure Resource Manager テンプレートをデプロイするには、[Azure portal](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)、[GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md) など、さまざまな方法があります。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>自動スケーリングでプロビジョニングされたスループットを使用した MongoDB 用の Azure Cosmos アカウント

このテンプレートは、データベース レベルで自動スケーリングのスループットを共有する 2 つのコレクションを含む MongoDB API (3.2 または 3.6) の Azure Cosmos アカウントを作成します。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>標準でプロビジョニングされたスループットを使用した MongoDB 用の Azure Cosmos アカウント

このテンプレートは、データベース レベルで 400 RU/秒の標準 (手動) スループットを共有する 2 つのコレクションを含む MongoDB API (3.2 または3.6) の Azure Cosmos アカウントを作成します。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>次のステップ

次にその他のリソースを示します。

* [Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)
* [Azure Cosmos DB リソース プロバイダー スキーマ](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Azure Resource Manager デプロイの一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)
