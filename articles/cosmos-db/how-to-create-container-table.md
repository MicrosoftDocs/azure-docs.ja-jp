---
title: Azure Cosmos DB Table API にコンテナーを作成する
description: Azure portal、.NET、Java、Python、Node.js、その他の SDK を使用して、Azure Cosmos DB Table API にコンテナーを作成する方法について学習します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: fde3145e7bd7f4e53ae7a0c44f02e066c28ec785
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101580"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API にコンテナーを作成する
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

この記事では、Azure Cosmos DB Table API にコンテナーを作成するいくつかの方法について説明します。 Azure portal、Azure CLI、PowerShell、またはサポートされている SDK を使用して、コンテナーを作成する方法が示されます。 この記事では、コンテナーを作成してパーティション キーを指定し、スループットをプロビジョニングする方法を紹介します。

この記事では、Azure Cosmos DB Table API にコンテナーを作成するいくつかの方法について説明します。 別の API を使用している場合、コンテナーを作成するには、[MongoDB の API](how-to-create-container-mongodb.md)、[Cassandra API](how-to-create-container-cassandra.md)、[Gremlin API](how-to-create-container-gremlin.md)、[SQL API](how-to-create-container.md) に関する記事を参照してください。

> [!NOTE]
> コンテナーを作成するときは、同じ名前で大文字と小文字が異なる 2 つのコンテナーを作成しないようにしてください。 これは、Azure プラットフォームの一部で大文字と小文字が区別されないため、このような名前のコンテナーでテレメトリとアクションの混同や衝突が発生する可能性があるためです。

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Azure portal を使用した作成

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-table-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいテーブル]** を選択します。 次に、以下の詳細を指定します。

   * テーブル ID を入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Table API の [テーブルの追加] ダイアログ ボックスのスクリーンショット":::

> [!Note]
> Table API では、新しい行を追加するたびにパーティション キーが指定されます。

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Azure CLI を使用して作成する

[Azure CLI を使用して Table API テーブルを作成します](./scripts/cli/table/create.md)。 すべての Azure Cosmos DB API にわたる Azure CLI の全サンプルの一覧については、「[Azure Cosmos DB の Azure CLI サンプル](cli-samples.md)」を参照してください。

## <a name="create-using-powershell"></a>PowerShell を使用して作成する

[PowerShell を使用して Table API テーブルを作成します](./scripts/powershell/table/create.md)。 すべての Azure Cosmos DB API にわたる PowerShell の全サンプルの一覧については、「[Azure Cosmos DB 用 Azure PowerShell サンプル](powershell-samples.md)」を参照してください

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [Azure Cosmos アカウントの使用](./account-databases-containers-items.md)