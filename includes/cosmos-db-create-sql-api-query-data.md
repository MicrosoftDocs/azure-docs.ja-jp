---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 609b62312329b3a8f9f16d15458a0a47f5eb377f
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2020
ms.locfileid: "85115211"
---
データ エクスプローラーでクエリを使用して、データを取得しフィルター処理できます。

1. データ エクスプローラーの **[Items]\(項目\)** タブの上部で、既定のクエリ `SELECT * FROM c` を確認します。 このクエリは、コンテナーからすべてのドキュメントを取得して ID 順に表示します。 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="データ エクスプローラーの既定のクエリは SELECT * FROM c":::
   
1. クエリを変更するには、 **[フィルターの編集]** を選択し、既定のクエリを `ORDER BY c._ts DESC` に置き換えてから、 **[フィルターの適用]** を選択します。
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="ORDER BY c._ts DESC を追加し [フィルターの適用] をクリックすることで既定のクエリを変更":::

   この変更したクエリでは、ドキュメントがそのタイムスタンプの降順に一覧表示されるので、ここでは 2 番目のドキュメントが最初に表示されます。 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="クエリを ORDER BY c._ts DESC に変更し、[フィルターの適用] をクリックする":::

SQL 構文に慣れている場合は、サポートされている任意の [SQL クエリ](../articles/cosmos-db/sql-api-sql-query.md)をクエリ述語ボックスに入力できます。 さらに、データ エクスプローラーを使用して、サーバー側ビジネス ロジックのためのストアド プロシージャ、UDF、トリガーを作成することもできます。 

データ エクスプローラーは、API で利用可能なすべての組み込みのプログラムによるデータ アクセス機能への簡単な Azure portal アクセスを提供します。 また、ポータルを使用して、スループットのスケーリング、キーと接続文字列の取得、および Azure Cosmos DB アカウントのメトリックと SLA の確認を行うこともできます。 

