---
title: Azure Cosmos DB Core (SQL API) の Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 52fa5957cdbc3ae73ebc4b36e04582741199414a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340602"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure Resource Manager テンプレート
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、コア (SQL) API アカウント用の Azure Resource Manager テンプレートの例のみを示します。 [Cassandra](templates-samples-cassandra.md)、[Gremlin](templates-samples-gremlin.md)、[MongoDB](templates-samples-mongodb.md)、[Table](templates-samples-table.md) の API の例もあります。

## <a name="core-sql-api"></a>コア (SQL) API

|**テンプレート**|**説明**|
|---|---|
|[自動スケーリングのスループットで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-with-templates.md#create-autoscale) | このテンプレートは、データベースとコンテナーの 2 つのリージョンに、自動スケーリングのスループットでコア (SQL) API アカウントを作成します。 |
|[分析ストアで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-with-templates.md#create-analytical-store) | このテンプレートでは、分析 TTL を有効にして構成されたコンテナーと、手動または自動スケールのスループットを使用するオプションで、1 つのリージョンにコア (SQL) API アカウントを作成します。 |
|[標準 (手動) のスループットで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-with-templates.md#create-manual) | このテンプレートは、データベースとコンテナーの 2 つのリージョンに、標準のスループットでコア (SQL) API アカウントを作成します。 |
|[ストアド プロシージャ、トリガー、および UDF を使用して Azure Cosmos アカウント、データベース、およびコンテナーを作成する](manage-with-templates.md#create-sproc) | このテンプレートは、コンテナーのストアド プロシージャ、トリガー、および UDF を使用して、2 つのリージョンに コア (SQL) API アカウントを作成します。 |
|[既存の Azure Cosmos アカウントに対するプライベート エンドポイントを作成する](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  このテンプレートは、既存の仮想ネットワークにある既存の Azure Cosmos Core (SQL) API アカウントのプライベート エンドポイントを作成します。 |
|[Free レベルの Azure Cosmos アカウントを作成する](manage-with-templates.md#free-tier) |  このテンプレートは、free レベルで Azure Cosmos DB Core (SQL) API アカウントを作成します。 |

リファレンス ドキュメントについては、[Azure Cosmos DB 用 Azure Resource Manager リファレンス](/azure/templates/microsoft.documentdb/allversions)のページを参照してください。
