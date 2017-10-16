---
title: "Azure Cosmos DB のスループットのプロビジョニング | Microsoft Docs"
description: "Azure Cosmos DB コンテナー、コレクション、グラフ、およびテーブルのプロビジョニング済みスループットを設定する方法について説明します。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: mimig
ms.openlocfilehash: d541bb19ba7e5ecb44c9fe91b1e232d4d9c2170e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>Azure Cosmos DB コンテナーのスループットの設定

Azure Portal またはクライアントの SDK を使用して、Azure Cosmos DB コンテナーのスループットを設定できます。 

次の表に、コンテナーで使用できるスループットを示します。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>単一パーティション コンテナー</strong></p></td>
            <td valign="top"><p><strong>パーティション分割コンテナー</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小スループット</p></td>
            <td valign="top"><p>400 要求ユニット/秒</p></td>
            <td valign="top"><p>2,500 要求ユニット/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大スループット</p></td>
            <td valign="top"><p>10,000 要求ユニット/秒</p></td>
            <td valign="top"><p>無制限</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Azure Portal を使用してスループットを設定するには

1. 新しいウィンドウで、[Azure Portal](https://portal.azure.com) を開きます。
2. 左側のバーで **[Azure Cosmos DB]** をクリックします。または、下部の **[その他のサービス]** をクリックし、**[データベース]** までスクロールして、**[Azure Cosmos DB]** をクリックします。
3. Azure Cosmos DB アカウントを選択します。
4. 新しいウィンドウで、ナビゲーション メニューの **[データ エクスプローラー (プレビュー)]** をクリックします。
5. 新しいウィンドウで、データベースとコンテナーを展開し、**[Scale & Settings]\(スケールと設定\)** をクリックします。
6. 新しいウィンドウで、**[スループット]** ボックスに新しいスループット値を入力し、**[保存]** をクリックします。

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-documentdb-api-for-net"></a>DocumentDB API for .NET を使用してスループットを設定するには

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>スループットについてよく寄せられる質問

**スループットを 400 RU/秒未満に設定することはできますか?**

Cosmos DB のシングル パーティション コレクションで使用できる最小スループットは 400 RU/秒です (パーティション分割コレクションの最小スループットは 2,500 RU/秒)。 要求ユニットは 100 RU/秒の間隔で設定されますが、スループットを 100 RU/秒または 400 RU/秒未満の値に設定することはできません。 コストを抑えて Cosmos DB の開発とテストを行いたい場合は、無料の [Azure Cosmos DB Emulator](local-emulator.md) を利用して無償でローカルにデプロイすることができます。 

**MongoDB API を使用してスループットを設定する方法**

スループットを設定するための MongoDB API 拡張機能はありません。 「[DocumentDB API for .NET を使用してスループットを設定するには](#set-throughput-sdk)」で示すように、DocumentDB API を使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

Cosmos DB を使用したプロビジョニングと地球規模での使用の詳細については、[Cosmos DB でのパーティション分割とスケーリング](partition-data.md)に関するページをご覧ください。
