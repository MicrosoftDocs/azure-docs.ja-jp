---
title: Azure Cosmos DB Cassandra API の Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB Cassandra API を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: eb746e8105717883b029586db2bfce8e31290e93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340670"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure Cosmos DB Cassandra API リソースを管理する
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

この記事では、ご利用の Azure Cosmos DB アカウント、キースペース、およびテーブルのデプロイと管理に役立つ Azure Resource Manager テンプレートの使用方法について説明します。

この記事に含まれているのは、Cassandra API アカウントの例のみです。他の種類の API アカウントでの例については、[SQL](templates-samples-sql.md)、[Gremlin](templates-samples-gremlin.md)、[MongoDB](templates-samples-mongodb.md)、[Table](templates-samples-table.md) 用の Azure Cosmos DB の API での Azure Resource Manager テンプレートの使用に関する記事を参照してください。

> [!IMPORTANT]
>
> * アカウント名は、44 文字 (すべて小文字) に制限されています。
> * スループットの値を変更するには、RU/秒を更新してテンプレートを再配置します。
> * Azure Cosmos アカウントに対して場所の追加または削除を行う場合、他のプロパティを同時に変更することはできません。 これらの操作は個別に行う必要があります。

以下の Azure Cosmos DB リソースを作成するには、次のサンプル テンプレートを新しい json ファイルにコピーします。 必要に応じて、異なる名前と値を持つ同じリソースの複数のインスタンスをデプロイするときに使用するパラメーター json ファイルを作成することもできます。 Azure Resource Manager テンプレートをデプロイするには、[Azure portal](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)、[GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md) など、さまざまな方法があります。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>自動スケーリングでプロビジョニングされたスループットを使用した Cassandra 用の Azure Cosmos アカウント

このテンプレートは、一貫性とフェールオーバーのためのオプションを備えた 2 つのリージョンで Azure Cosmos アカウントを作成します。キースペースとテーブルは自動スケーリングのスループット用に構成されています。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>標準でプロビジョニングされたスループットを使用した Cassandra 用の Azure Cosmos アカウント

このテンプレートは、一貫性とフェールオーバーのためのオプションを備えた 2 つのリージョンで Azure Cosmos アカウントを作成します。キースペースとテーブルは標準のスループット用に構成されています。 このテンプレートは、Azure クイックスタート テンプレート ギャラリーからのワンクリック デプロイでも使用できます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>次のステップ

次にその他のリソースを示します。

* [Azure Resource Manager のドキュメント](../azure-resource-manager/index.yml)
* [Azure Cosmos DB リソース プロバイダー スキーマ](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Azure Resource Manager デプロイの一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)