---
title: "インデックスの照会 (ポータル - Azure Search) | Microsoft Docs"
description: "Azure ポータルの Search エクスプローラーで検索クエリを発行します。"
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 07/10/2017
ms.author: ashmaka
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: dd68d8ed073bf7b8666ddef35a2f1f84df690b4b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="query-an-azure-search-index-using-search-explorer-in-the-azure-portal"></a>Azure Portal の Search エクスプローラーを使用して Azure Search インデックスを照会する
> [!div class="op_single_selector"]
> * [概要](search-query-overview.md)
> * [ポータル](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST ()](search-query-rest-api.md)
> 
> 

この記事では、Azure Portal の **Search エクスプローラー**を使用して Azure Search インデックスを照会する方法について説明します。 Search エクスプローラーを使用すると、サービス内の既存のインデックスに対して単純または完全な Lucene クエリ文字列を送信することができます。

## <a name="open-the-service-dashboard"></a>サービスのダッシュボードを開く
1. [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) の左側にあるジャンプ バーで **[すべてのリソース]** をクリックします。
2. Azure Search サービスを選択します。

## <a name="select-an-index"></a>インデックスを選択する

**[インデックス]** タイルから、検索するインデックスを選択します。

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Search エクスプローラーを開く

[Search エクスプローラー] タイルをクリックして、検索バーと結果のウィンドウを開きます。

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>検索を開始する

Search エクスプローラーを使用している場合は、[クエリ パラメーター](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)を指定してクエリを作成できます。

1. **[クエリ文字列]** で、クエリを入力し、**[検索]** をクリックします。 

   クエリ文字列は自動的に適切な要求 URL に解析され、Azure Search REST API に対して HTTP 要求を送信します。   
   
   任意の Lucene クエリ構文を使用して要求を作成できます。クエリ構文は、有効であれば単純でも完全でもかまいません。 `*` 文字は、空または未指定の検索に相当し、すべてのドキュメントを任意の順序で返します。

2. **[結果]** には、クエリの結果が未加工の JSON 形式で表示されます。これは、プログラムによって要求を発行したときに HTTP 応答本文で返されるペイロードと同じです。

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>次のステップ

クエリ構文のその他の情報や例については、次のリソースを参照してください。

 + [単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene クエリ構文例](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [OData フィルター式の構文](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
