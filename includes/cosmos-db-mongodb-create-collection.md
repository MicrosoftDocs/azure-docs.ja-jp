---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 37cdb6b466417add8dae69464304ce2f32247c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95556063"
---
Azure portal でデータ エクスプローラー ツールを使用して、Azure Cosmos DB の MongoDB データベースとコンテナー用の API を作成できるようになりました。 

1. **[データ エクスプローラー]**  >  **[新しいコンテナー]** の順に選択します。 
    
    **[コンテナーの追加]** 領域が右端に表示されます。表示するには、右へスクロールする必要がある場合があります。

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="Azure portal の [データ エクスプローラー] の [コンテナーの追加] ウィンドウ":::

2. **[コンテナーの追加]** ページで、新しいコンテナーの設定を入力します。

    |設定|推奨値|説明
    |---|---|---|
    |**データベース ID**|db|新しいデータベースの名前として「*db*」と入力します。 データベース名は 1 文字以上 255 文字以内にする必要があります。`/, \\, #, ?` は使えず、末尾にスペースを入れることもできません。 **[Provision database throughput]\(データベース スループットをプロビジョニングする\)** オプションをオンにすると、データベースにプロビジョニングされたスループットをデータベース内のすべてのコンテナーにわたって共有できます。 このオプションは、コストの削減にも役立ちます。 |
    |**スループット**|400|スループットを 400 要求ユニット/秒 (RU/秒) のままにします。 待ち時間を短縮する場合、後でスループットをスケールアップできます。 また、[[自動スケーリング モード]](../articles/cosmos-db/provision-throughput-autoscale.md) を選択することもできます。これにより、必要に応じて動的に増減する RU/秒の範囲が提供されます。| 
    |**[コレクション ID]**|coll|新しいコンテナーの名前として「`coll`」と入力します。 コンテナー ID には、データベース名と同じ文字要件があります。|
    |**[ストレージ容量]**|固定 (10 GB)|このアプリケーションに対して *[固定 (10 GB)]* を選択します。 *[無制限]* を選択した場合は、`Shard Key` を作成する必要があります。この場合、挿入されたすべての項目が必要になります。|
    |**シャード キー**| /_id| この記事で説明されているサンプルでは、シャード キーは使用されないため、 */_id* に設定すると、自動的に生成された ID フィールドがシャード キーとして使用されます。 シャーディング (パーティション分割) の詳細については、「[Azure Cosmos DB でのパーティション分割](../articles/cosmos-db/partitioning-overview.md)」をご覧ください。|
        
    **[OK]** を選択します。 新しいデータベースとコンテナーがデータ エクスプローラーに表示されます。