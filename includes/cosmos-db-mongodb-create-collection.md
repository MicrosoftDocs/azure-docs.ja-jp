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
ms.openlocfilehash: 8d12645bd302a7ea808f13c07e2a72f66846bad7
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2020
ms.locfileid: "85121347"
---
Azure portal でデータ エクスプローラー ツールを使用して、Azure Cosmos DB の MongoDB データベースとコンテナー用の API を作成できるようになりました。 

1. **[データ エクスプローラー]**  >  **[新しいコンテナー]** の順に選択します。 
    
    **[コンテナーの追加]** 領域が右端に表示されます。表示するには、右へスクロールする必要がある場合があります。

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="Azure portal の [データ エクスプローラー] の [コンテナーの追加] ウィンドウ":::

2. **[コンテナーの追加]** ページで、新しいコンテナーの設定を入力します。

    |設定|推奨値|説明
    |---|---|---|
    |**データベース ID**|db|新しいデータベースの名前として「*db*」と入力します。 データベース名は 1 文字以上 255 文字以内にする必要があります。`/, \\, #, ?` は使えず、末尾にスペースを入れることもできません。 **[Provision database throughput]\(データベース スループットをプロビジョニングする\)** オプションをオンにすると、データベースにプロビジョニングされたスループットをデータベース内のすべてのコンテナーにわたって共有できます。 このオプションは、コストの削減にも役立ちます。 |
    |**スループット**|400|スループットを 400 要求ユニット/秒 (RU/秒) のままにします。 待ち時間を短縮する場合、後でスループットをスケールアップできます。 また、[[自動スケーリング モード]](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale) を選択することもできます。これにより、必要に応じて動的に増減する RU/秒の範囲が提供されます。| 
    |**[コレクション ID]**|coll|新しいコンテナーの名前として「`coll`」と入力します。 コンテナー ID には、データベース名と同じ文字要件があります。|
    |**[ストレージ容量]**|固定 (10 GB)|このアプリケーションに対して *[固定 (10 GB)]* を選択します。 *[無制限]* を選択した場合は、`Shard Key` を作成する必要があります。この場合、挿入されたすべての項目が必要になります。|
    |**シャード キー**| /_id| この記事で説明されているサンプルでは、シャード キーは使用されないため、 */_id* に設定すると、自動的に生成された ID フィールドがシャード キーとして使用されます。 シャーディング (パーティション分割) の詳細については、「[Azure Cosmos DB でのパーティション分割](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)」をご覧ください。|
        
    **[OK]** を選択します。 新しいデータベースとコンテナーがデータ エクスプローラーに表示されます。
