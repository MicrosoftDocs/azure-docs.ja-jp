---
title: Azure Cosmos DB の Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB を作成および構成します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "83586152"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure Resource Manager テンプレート

次の表は、Azure Cosmos DB 用の Azure Resource Manager テンプレートのリンク一覧です。

## <a name="core-sql-api"></a>コア (SQL) API

|**テンプレート**|**説明**|
|---|---|
|[自動スケーリングのスループットで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-sql-with-resource-manager.md#create-autoscale) | このテンプレートは、データベースとコンテナーの 2 つのリージョンに、自動スケーリングのスループットでコア (SQL) API アカウントを作成します。 |
|[分析ストアで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-sql-with-resource-manager.md#create-analytical-store) | このテンプレートでは、分析 TTL を有効にして構成されたコンテナーと、手動または自動スケールのスループットを使用するオプションで、1 つのリージョンにコア (SQL) API アカウントを作成します。 |
|[標準 (手動) のスループットで Azure Cosmos アカウント、データベース、コンテナーを作成する](manage-sql-with-resource-manager.md#create-manual) | このテンプレートは、データベースとコンテナーの 2 つのリージョンに、標準のスループットでコア (SQL) API アカウントを作成します。 |
|[ストアド プロシージャ、トリガー、および UDF を使用して Azure Cosmos アカウント、データベース、およびコンテナーを作成する](manage-sql-with-resource-manager.md#create-sproc) | このテンプレートは、コンテナーのストアド プロシージャ、トリガー、および UDF を使用して、2 つのリージョンに コア (SQL) API アカウントを作成します。 |
|[既存の Azure Cosmos アカウントに対するプライベート エンドポイントを作成する](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  このテンプレートは、既存の仮想ネットワークにある既存の Azure Cosmos Core (SQL) API アカウントのプライベート エンドポイントを作成します。 |
|[Free レベルの Azure Cosmos アカウントを作成する](manage-sql-with-resource-manager.md#free-tier) |  このテンプレートは、free レベルで Azure Cosmos DB Core (SQL) API アカウントを作成します。 |

## <a name="mongodb-api"></a>MongoDB API

|**テンプレート**|**説明**|
|---| ---|
|[自動スケーリングのスループットで Azure Cosmos アカウント、データベース、コレクションを作成する](manage-mongodb-with-resource-manager.md#create-autoscale) | このテンプレートは、データベースレベルの自動スケーリングのスループットを共有する 2 つのコンテナーを持つ 2 つのリージョンで MongoDB 用 Azure Cosmos DB の API を使用してアカウントを作成します。 |
|[標準 (手動) のスループットで Azure Cosmos アカウント、データベース、コレクションを作成する](manage-mongodb-with-resource-manager.md#create-manual) | このテンプレートは、データベースレベルの標準のスループットを共有する 2 つのコンテナーを持つ 2 つのリージョンで MongoDB 用 Azure Cosmos DB の API を使用してアカウントを作成します。 |

## <a name="cassandra-api"></a>Cassandra API

|**テンプレート**|**説明**|
|---| ---|
|[自動スケーリングのスループットで Azure Cosmos アカウント、キースペース、テーブルを作成する](manage-cassandra-with-resource-manager.md#create-autoscale) | このテンプレートは、キースペースとテーブルの 2 つのリージョンに、自動スケーリングのスループットで Cassandra API アカウントを作成します。 |
|[標準 (手動) のスループットで Azure Cosmos アカウント、キースペース、テーブルを作成する](manage-cassandra-with-resource-manager.md#create-manual) | このテンプレートは、キースペースとテーブルの 2 つのリージョンに、手動のスループットで Cassandra API アカウントを作成します。 |

## <a name="gremlin-api"></a>Gremlin API

|**テンプレート**|**説明**|
|---| ---|
|[自動スケーリングのスループットで Azure Cosmos アカウント、データベース、グラフを作成する](manage-gremlin-with-resource-manager.md#create-autoscale) | このテンプレートは、データベースとグラフの 2 つのリージョンに、自動スケーリングのスループットで Gremlin API アカウントを作成します。 |
|[標準 (手動) のスループットで Azure Cosmos アカウント、データベース、グラフを作成する](manage-gremlin-with-resource-manager.md#create-manual) | このテンプレートは、データベースとグラフの 2 つのリージョンに、標準のスループットで Gremlin API アカウントを作成します。 |

## <a name="table-api"></a>テーブル API

|**テンプレート**|**説明**|
|---| ---|
|[自動スケーリングのスループットで Azure Cosmos アカウント、テーブルを作成する](manage-table-with-resource-manager.md#create-autoscale) | このテンプレートは、2 つのリージョンおよび 1 つのテーブルに、自動スケーリングのスループットで Table API アカウントを作成します。 |
|[標準 (手動) のスループットで Azure Cosmos アカウント、テーブルを作成する](manage-table-with-resource-manager.md#create-manual) | このテンプレートは、2 つのリージョンおよび 1 つのテーブルに、標準のスループットで Table API アカウントを作成します。 |

リファレンス ドキュメントについては、[Azure Cosmos DB 用 Azure Resource Manager リファレンス](/azure/templates/microsoft.documentdb/allversions)のページを参照してください。
