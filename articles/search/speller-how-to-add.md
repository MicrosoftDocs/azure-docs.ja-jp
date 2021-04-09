---
title: スペル チェックを追加する
titleSuffix: Azure Cognitive Search
description: クエリを実行する前に、クエリ パイプラインにスペル修正を追加して、クエリ用語の入力ミスを修正します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: be72cae8d109bfeccd60e8c821625f75357568e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232360"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Cognitive Search のクエリにスペル チェックを追加する

> [!IMPORTANT]
> スペル修正はパブリック プレビュー段階にあり、プレビュー REST API を介してのみ利用できます。 プレビュー機能は、[補足利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に基づいて、現状のまま提供されます。 最初のプレビュー期間中は、スペル チェックに対して料金は発生しません。 詳細については、[可用性と価格](semantic-search-overview.md#availability-and-pricing)に関するページを参照してください。

個々の検索クエリ用語が検索エンジンに到達する前に修正することで、リコールを改善できます。 **スペル チェック** パラメーターは、[simple](query-simple-syntax.md)、 [full](query-lucene-syntax.md)、および現在パブリック プレビュー中の新しい [semantic](semantic-how-to-query-request.md) オプションのすべての種類のクエリでサポートされています。

## <a name="prerequisites"></a>前提条件

+ 英語のコンテンツを含む既存の検索インデックス

+ クエリを送信するための検索クライアント

  検索クライアントは、クエリ要求でプレビューの REST API をサポートする必要があります。 [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md)、または自分で変更したコードを使用して、プレビュー API への REST 呼び出しを行うことができます。

+ スペル修正を使用する[クエリ要求](/rest/api/searchservice/preview-api/search-documents)には、"api-version=2020-06-30-Preview"、"speller=lexicon"、および "queryLanguage=en-us" があります。

  スペル チェックには queryLanguage が必要であり、現在 "en-us" が唯一の有効な値です。

> [!Note]
> スペル チェック パラメーターは、セマンティック検索を提供するのと同じリージョン内のすべての階層で使用できます。 このプレビュー機能にアクセスするためにサインアップする必要はありません。 詳細については、[可用性と価格](semantic-search-overview.md#availability-and-pricing)に関するページを参照してください。

## <a name="spell-correction-with-simple-search"></a>単純な検索でのスペル修正

次の例では、組み込みの hotels-sample index を使用して、単純な自由形式のテキスト クエリのスペル修正を示します。 スペル修正を行わない場合、クエリは 0 件の結果を返します。 修正すると、このクエリでは Johnson 家族向けリゾートについて 1 つの結果が返されます。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>完全な Lucene でのスペル修正

スペル修正は、分析される個々のクエリ用語に対して行われます。一部の Lucene クエリではスペル チェック パラメーターを使用できますが、それ以外では使用できない理由です。

+ テキスト分析をバイパスする互換性のないクエリ形式には、ワイルドカード、正規表現、あいまいがあります
+ 互換性のあるクエリ形式には、フィールド検索、近接、用語ブーストがあります

この例では、完全な Lucene 構文とスペル ミスがあるクエリ用語を使用して、Category フィールドに対して検索を使用します。 スペル チェックを含めると、"Suiite" の入力ミスが修正され、クエリが成功します。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>セマンティック検索でのスペル修正

このクエリでは、1 つを除くすべての用語に入力ミスがあるため、関連する結果を返すためのスペル修正が行われます。 詳細については、 [セマンティック クエリの作成](semantic-how-to-query-request.md)に関するページを参照してください。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>言語に関する注意点

スペル チェックに必要な queryLanguage パラメーターは、インデックス スキーマのフィールド定義に割り当てられている[言語アナライザー](index-add-language-analyzers.md)と一致している必要があります。 

+ queryLanguage により、スペル チェックに使用される辞書が決まります。これは、"queryType=semantic" を使用している場合に[セマンティック ランク付けアルゴリズム](semantic-answers.md)への入力としても使用されます。

+ 言語アナライザーは、検索インデックスで一致するドキュメントを見つけるために、インデックスの作成時とクエリの実行時に使用されます。 言語アナライザーを使用するフィールド定義の例として `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"` があります。

スペル チェックの使用時に最良の結果を得るには、queryLanguage が "en-us" の場合、言語アナライザーも英語のバリアント ("en.microsoft" または "en.lucene") である必要があります。

> [!NOTE]
> 言語に依存しないアナライザー (keyword、simple、standard、stop、whitespace、`standardasciifolding.lucene` など) は、queryLanguage 設定と競合しません。

クエリ要求では、設定した queryLanguage がスペル チェック、回答、キャプションにも同様に適用されます。 個々のパーツに対するオーバーライドはありません。

検索インデックスのコンテンツは複数の言語で構成できますが、通常、クエリ入力は 1 つの言語で行われます。 検索エンジンでは、queryLanguage、言語アナライザー、およびコンテンツが構成されている言語の互換性が確認されません。そのため、誤った結果が生成されないように、適切にクエリのスコープを設定してください。

## <a name="next-steps"></a>次のステップ

+ [セマンティック クエリの作成](semantic-how-to-query-request.md)
+ [基本的なクエリの作成](search-query-create.md)
+ [完全な Lucene クエリ構文の使用](query-Lucene-syntax.md)
+ [単純なクエリ構文の使用](query-simple-syntax.md)
+ [セマンティック検索の概要](semantic-search-overview.md)