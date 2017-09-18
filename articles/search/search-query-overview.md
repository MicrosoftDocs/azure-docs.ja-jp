---
title: "Azure Search インデックスの照会 | Microsoft Docs"
description: "Azure Search の検索クエリを作成し、検索パラメーターを使用して検索結果のフィルター処理と並べ替えを行います。"
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 69205d7a-363f-4b92-a53f-6ca818a3d2c7
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 04/26/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: a22b82829df4659681940267e64c98d345453958
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017


---
# <a name="query-your-azure-search-index"></a>Azure Search インデックスの照会
> [!div class="op_single_selector"]
> * [概要](search-query-overview.md)
> * [ポータル](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Azure Search に検索要求を送信する際には、アプリケーションの検索ボックスに入力される実際の語句のほかにも、さまざまなパラメーターを指定できます。 これらのクエリ パラメーターを使用すると、[フルテキスト検索の機能](search-lucene-query-architecture.md)をよりきめ細かく制御できます。

以下の一覧で、Azure Search におけるクエリ パラメーターの一般的な使用方法について簡単に説明します。 クエリ パラメーターとその動作を網羅した説明については、[REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) および [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary) 向けの詳細ページを参照してください。

## <a name="types-of-queries"></a>クエリの種類
Azure Search では、非常に強力なクエリを作成できる多くのオプションが用意されています。 主に使用するクエリの種類は、`search` と `filter` の 2 種類です。 `search` クエリは、インデックスのすべての*検索可能*フィールドで 1 つ以上の語句を検索し、Google や Bing などの検索エンジンに期待するのと同様に機能します。 `filter` クエリは、インデックスのすべての *フィルター処理可能* フィールドでブール式を評価します。 `search` クエリとは異なり、`filter` クエリはフィールドの内容を厳密に照合します。つまり、文字列フィールドでは大文字と小文字が区別されます。

検索とフィルターは、一緒に使用することも、別々に使用することもできます。 一緒に使用した場合、フィルターが最初にインデックス全体に適用され、次にフィルター処理の結果に対して検索が実行されます。 フィルターはクエリのパフォーマンス向上に役立つ手法です。フィルターを使うと、検索クエリで処理が必要なドキュメントの数が減ります。

フィルター式の構文は、 [OData フィルター言語](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)のサブセットです。 検索クエリでは、以下で説明する[単純な構文](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)または [Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search)を使用できます。

### <a name="simple-query-syntax"></a>単純なクエリ構文
[単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) は、Azure Search で使用される既定のクエリ言語です。 単純なクエリ構文では、AND、OR、NOT、フレーズ、サフィックス、優先順位の演算子など、一般的な各種検索演算子がサポートされています。

### <a name="lucene-query-syntax"></a>Lucene クエリ構文
[Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search)では、[Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) の一部として開発された、広く採用されている表現性の高いクエリ言語を使用できます。

このクエリ構文を使うと、[フィールド スコープ クエリ](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)、[ファジー検索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)、[近接検索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)、[ターム ブースト](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)、[正規表現検索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)、[ワイルドカード検索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)、[構文の基本](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax)、[ブール演算子を使用したクエリ](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)などの機能を簡単に実現できます。

## <a name="ordering-results"></a>結果の並べ替え
検索クエリに対する結果を受け取る際、Azure Search から指定のフィールドの値に基づいて並べ替えられた結果が返されるようにすることができます。 既定では、Azure Search では、 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)から派生する各ドキュメントの検索スコアのランクに基づいて検索結果を並べ替えます。

検索スコア以外の値に基づいて並べ替えられた結果が Azure Search から返されるようにするには、 `orderby` 検索パラメーターを使います。 フィールド名を含める `orderby` パラメーターの値と、地理空間値の [`geo.distance()` 関数](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)の呼び出しを指定できます。 結果を昇順で要求する場合は各式の後に `asc` を指定し、降順で要求する場合は `desc` を指定します。 既定のランクは昇順です。

## <a name="paging"></a>ページング
Azure Search では、検索結果のページングを簡単に実装できます。 `top` および `skip` パラメーターを使用すると、管理しやすい並べ替えられたサブセットとして検索結果一式を取得できるようにする検索要求をスムーズに発行できます。また、このような検索結果により、検索 UI の利便性を簡単に高めることができます。 こうした結果の少数のサブセットを取得する際には、検索結果一式に含まれるドキュメントの数を取得することもできます。

検索結果のページングの詳細については、「 [Azure Search でのページ検索結果の表示方法](search-pagination-page-layout.md)」をご覧ください。

## <a name="hit-highlighting"></a>検索結果の強調表示
Azure Search では、検索クエリに一致する検索結果の特定の部分を正確に強調表示できます。これは、`highlight`、`highlightPreTag`、`highlightPostTag` の各パラメーターを使用して簡単に行えます。 一致するテキストを強調表示する*検索可能*フィールドを指定できるほか、Azure Search から返される一致テキストの先頭と末尾に追加する文字列タグを正確に指定することもできます。

## <a name="try-out-query-syntax"></a>クエリ構文を試してみる

構文の違いを理解する最善の方法は、クエリを実行して結果を確認することです。

+ Azure Portal で[検索エクスプローラー](search-explorer.md)を使用します。 [サンプル インデックス](search-get-started-portal.md)をデプロイすることで、ポータルのツールを使用してインデックスを簡単に照会できます。

+ [Fiddler](search-fiddler.md) または Chrome Postman を使用して、検索サービスにアップロードしたインデックスにクエリを発行します。 どちらのツールも、HTTP エンドポイントへの REST 呼び出しをサポートしています。 
