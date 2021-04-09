---
title: Azure Cosmos DB Table API の Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB Table API を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: e8fb47cc54d520d44085c8d007a4dfe92972d1d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340585"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure Cosmos DB Table API リソースを管理する
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

この記事では、ご利用の Azure Cosmos DB アカウント、データベース、およびコンテナーのデプロイと管理に役立つ Azure Resource Manager テンプレートの使用方法について説明します。

この記事に含まれているのは、Table API アカウントの例のみです。他の種類の API アカウントでの例については、[Cassandra](./templates-samples-cassandra.md)、[Gremlin](./templates-samples-gremlin.md)、[MongoDB](./templates-samples-mongodb.md)、[SQL](./manage-with-templates.md) 用の Azure Cosmos DB の API での Azure Resource Manager テンプレートの使用に関する記事を参照してください。

> [!IMPORTANT]
>
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新してテンプレートを再配置します。
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。

以下の Azure Cosmos DB リソースを作成するには、次のサンプル テンプレートを新しい json ファイルにコピーします。 必要に応じて、異なる名前と値を持つ同じリソースの複数のインスタンスをデプロイするときに使用するパラメーター json ファイルを作成することもできます。 Azure Resource Manager テンプレートをデプロイするには、[Azure portal](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)、[GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md) など、さまざまな方法があります。

> [!TIP]
> Table API を使用する際に共有スループットを有効にするには、Azure portal でアカウント レベルのスループットを有効にします。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>自動スケーリングのスループットを使用した Table の Azure Cosmos アカウント

このテンプレートは、自動スケーリングのスループットで 1 つのテーブルを含む Table API の Azure Cosmos アカウントを作成します。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>標準プロビジョニング スループットを備える Table 用の Azure Cosmos アカウント

このテンプレートは、標準のスループットで 1 つのテーブルを含む Table API の Azure Cosmos アカウントを作成します。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>次のステップ

次にその他のリソースを示します。

* [Azure Resource Manager のドキュメント](../azure-resource-manager/index.yml)
* [Azure Cosmos DB リソース プロバイダー スキーマ](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Azure Resource Manager デプロイの一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)