---
title: Azure Cosmos DB 用の Azure Synapse Link に接続する
description: Azure Synapse Link を使用して Azure Cosmos DB データベースを Azure Synapse ワークスペースに接続する方法について学習します。
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: acomet
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 1c2dedfcbfd937215320c90d63c60d2e868939b7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868598"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB 用の Azure Synapse Link に接続する

この記事では、Azure Synapse Link を使用して Azure Synapse Analytics Studio から Azure Cosmos DB データベースにアクセスする方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure Cosmos DB データベースをワークスペースに接続する前に、次が必要です。

* 既存の Azure Cosmos DB データベース。または、[Azure Cosmos アカウントを管理する方法のクイックスタート](../../cosmos-db/how-to-manage-database-account.md)の手順に従って新しいアカウントを作成します。
* 既存の Azure Synapse ワークスペース。または、「[クイックスタート: Synapse ワークスペースを作成する](../quickstart-create-workspace.md)」の手順に従って新しいワークスペースを作成します。

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Azure Cosmos DB データベース アカウントで Synapse Link を有効にする

運用上のパフォーマンスに影響を与えることなく Azure Cosmos DB に大規模な分析を実行するには、Azure Cosmos DB 用の Synapse Link を有効にすることをお勧めします。 Synapse Link により、Azure Synapse のコンテナーと組み込みサポートに HTAP 機能が提供されます。

## <a name="go-to-synapse-studio"></a>Synapse Studio に移動する

対象の Azure Synapse ワークスペースから、 **[Synapse Studio の起動]** を選択します。 Synapse Studio のホーム ページで、 **[データ]** を選択します。これにより、データ オブジェクト エクスプローラーに移動します。

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Azure Cosmos DB データベースを Azure Synapse ワークスペースに接続する

Azure Cosmos DB データベースの接続は、リンクされたサービスとして行われます。 Azure Cosmos DB のリンクされたサービスを使用すると、Azure Synapse Analytics 用の Apache Spark または SQL からデータを参照して探索したり、Azure Cosmos DB に読み書きしたりできます。

データ オブジェクト エクスプローラーから Azure Cosmos DB データベースに直接接続するには、次の手順を実行します。

1. **[データ]** の近くにある **+** アイコンを選択します。
1. **[Connect to external data]\(外部データに接続する\)** を選択します。
1. 接続する API (**SQL API**、**MongoDB 用 API** など) を選択します。
1. **[続行]** をクリックします。
1. フレンドリ名を使用して、リンクされたサービスに名前を付けます。 この名前は、データ オブジェクト エクスプローラーに表示され、データベースとコンテナーに接続するために Azure Synapse ランタイムによって使用されます。
1. **Azure Cosmos DB アカウント名** と **データベース名** を選択します。
1. (省略可能) リージョンが指定されていない場合、Azure Synapse ランタイム操作は、分析ストアが有効になっている最も近いリージョンにルーティングされます。 また、ユーザーが Azure Cosmos DB 分析ストアにアクセスするために使用するリージョンを手動で設定することもできます。 **[Additional connection properties]\(追加の接続プロパティ\)** を選択し、 **[新規]** を選択します。 **[プロパティ名]** に「**PreferredRegions**」と入力します。 **[値]** を任意のリージョンに設定します (例: **WestUS2**)。 (単語と数字の間にスペースはありません)。
1. **［作成］** を選択します

Azure Cosmos DB データベースは、 **[Azure Cosmos DB]** セクションの **[リンク済み]** タブの下に表示されます。 Azure Cosmos DB では、次のアイコンによって、HTAP 対応のコンテナーと OLTP のみのコンテナーを区別できます。

**OLTP のみのコンテナー**:

![OLTP コンテナー アイコンを示す視覚化。](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**HTAP 対応のコンテナー**:

![HTAP コンテナー アイコンを示す視覚化。](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>コード生成アクションをすばやく操作する

コンテナーを右クリックすると、Spark または SQL ランタイムをトリガーするジェスチャの一覧が表示されます。 コンテナーへの書き込みは、Azure Cosmos DB のトランザクション ストアを通じて行われ、要求ユニットが使用されます。  

## <a name="next-steps"></a>次のステップ

* [Azure Synapse と Azure Cosmos DB の間でサポートされている内容について学習する](./concept-synapse-link-cosmos-db-support.md)
* [Azure Cosmos DB 分析ストアのプライベート エンドポイントを構成する](../../cosmos-db/analytical-store-private-endpoints.md)
* [Spark を使用して分析ストアにクエリを実行する方法を学習する](./how-to-query-analytical-store-spark.md)