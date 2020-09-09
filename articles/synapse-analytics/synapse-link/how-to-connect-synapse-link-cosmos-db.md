---
title: Azure Cosmos DB 用の Azure Synapse Link (プレビュー) に接続する
description: Azure Synapse Link を使用して Azure Cosmos DB を Synapse ワークスペースに接続する方法
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: fa33f2d9e0c4606587ca5ef65a3f4a021319431f
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85195047"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>Azure Cosmos DB 用の Azure Synapse Link (プレビュー) に接続する

この記事では、Azure Synapse Link を使用して Azure Synapse Analytics Studio から Azure Cosmos DB データベースにアクセスする方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure Cosmos DB データベースをワークスペースに接続する前に、以下が必要です。

> [!IMPORTANT]
> 現時点では、Azure Synapse Link for Azure Cosmos DB は、マネージド仮想ネットワークが有効になっていないワークスペースでサポートされています。 

* 既存の Azure Cosmos DB データベース。または、こちらの[クイックスタート](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)に従って新しいアカウントを作成します
* 既存の Synapse ワークスペース。または、こちらの[クイックスタート](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)に従って新しいワークスペースを作成します 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 分析ストアを有効にする

運用上のパフォーマンスに影響を与えることなく Azure Cosmos DB に大規模な分析を実行するには、Azure Cosmos DB 用の Synapse Link を有効にすることをお勧めします。 Synapse Link により、Azure Synapse のコンテナーと組み込みサポートに HTAP 機能が提供されます。

## <a name="navigate-to-synapse-studio"></a>Synapse Studio に移動する

Synapse ワークスペースから、 **[Synapse Studio の起動]** を選択します。 Synapse Studio のホーム ページで、[**データ] を選択します。これにより、**データ オブジェクト エクスプローラー**に移動します。

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Azure Cosmos DB データベースを Synapse ワークスペースに接続する

Azure Cosmos DB データベースの接続は、リンクされたサービスとして行われます。 Azure Cosmos DB のリンクされたサービスを使用すると、ユーザーは Azure Synapse Analytics 用の Apache Spark または SQL からデータを参照して探索したり、Azure Cosmos DB に読み書きしたりできます。

データ オブジェクト エクスプローラーから、次の手順を実行して Azure Cosmos DB データベースに直接接続できます。

1. [データ] の近くにある ***+*** アイコンを選択します
2. **[Connect to external data]\(外部データに接続する\)** を選択します
3. 接続先の API を選択します:MongoDB 用 SQL API または API
4. ***[続行]*** を選択します
5. リンクされたサービスに名前を付けます。 名前はオブジェクト エクスプローラーに表示され、データベースとコンテナーに接続するために Synapse ランタイムによって使用されます。 フレンドリ名を使用することをお勧めします。
6. **Azure Cosmos DB アカウント名**と**データベース名**を選択します
7. (省略可能) リージョンが指定されていない場合、Synapse ランタイム操作は、分析ストアが有効になっている最も近いリージョンにルーティングされます。 ただし、ユーザーを Azure Cosmos DB 分析ストアにアクセスさせるリージョンを手動で設定できます。 **[Additional connection properties]\(追加の接続プロパティ\)** 、 **[新規作成]** の順に選択します。 **[プロパティ名]** の下で、***PreferredRegions*** を作成し、 **[値]** を目的のリージョンに設定します (例:WestUS2、単語と数字の間にスペースはありません)
8. ***[作成]***

Azure Cosmos DB データベースは、[Azure Cosmos DB] セクションの **[リンク済み]** タブの下に表示されます。 Azure Cosmos DB では、次のアイコンによって、HTAP 対応のコンテナーと OLTP のみのコンテナーを区別できます。

**OLTP のみのコンテナー**:

![OLTP コンテナー](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**HTAP 対応のコンテナー**:

![HTAP コンテナー](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>コード生成アクションをすばやく操作する

コンテナーを右クリックすると、Spark または SQL ランタイムをトリガーするジェスチャの一覧が表示されます。 コンテナーへの書き込みは、Azure Cosmos DB のトランザクション ストアを通じて行われ、要求ユニットが使用されます。  

## <a name="next-steps"></a>次のステップ

* [Synapse と Azure Cosmos DB の間でサポートされている内容について学習する](./concept-synapse-link-cosmos-db-support.md)
* [Spark を使用して分析ストアにクエリを実行する方法を学習する](./how-to-query-analytical-store-spark.md)