---
title: OData の select リファレンス
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のクエリの検索結果で返すフィールドの明示的な選択のための構文と言語のリファレンス。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 54b6ae227fc4b3b951717799660543c02874dda0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88919660"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search での OData $select 構文

 [OData **$select** パラメーター](query-odata-filter-orderby-syntax.md)を使用すると、Azure Cognitive Search の検索結果に含めるフィールドを選択できます。 この記事では、**$select** の構文について詳しく説明します。 検索結果を表示するときに **$select** を使用する方法に関するより一般的な情報については、「[Azure Cognitive Search での検索結果の操作方法](search-pagination-page-layout.md)」を参照してください。

## <a name="syntax"></a>構文

**$select** パラメーターによって、各ドキュメントのどのフィールドがクエリ結果セットに返されるかが決まります。 次の EBNF ([拡張バッカスナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) に **$select** パラメーターの文法が定義されています。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Cognitive Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 完全な EBNF については、[Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)に関するページをご覧ください。

**$select** パラメーターには 2 つの形式があります。

1. 1 つのアスタリスク (`*`)。取得できるすべてのフィールドを返すことを指定します。
1. 複数のフィールド パスをコンマで区切った一覧。返すフィールドを指定します。

2 つ目の形式を使用するときは、一覧の取得できるフィールドのみを指定できます。

サブフィールドを明示的に指定せずに複合フィールドを列挙すると、取得できるすべてのサブフィールドがクエリ結果セットに含まれます。 たとえば、インデックスに、`Street`、`City`、および `Country` というサブフィールドがある `Address` フィールドがあり、いずれも取得できるとします。 **$select** に `Address` を指定した場合、クエリ結果には 3 つのサブフィールドすべてが含まれます。

## <a name="examples"></a>例

結果に `HotelId`、`HotelName`、および `Rating` の最上位フィールドを含め、`Address` の `City` サブフィールドを含めます。

```odata-filter-expr
    $select=HotelId, HotelName, Rating, Address/City
```

結果の例は次のようになります。

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

結果に `HotelName` 最上位フィールドを含め、`Rooms` コレクションに `Address` のすべてのサブフィールドと各オブジェクトの `Type` および `BaseRate` のサブフィールドを含めます。

```odata-filter-expr
    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate
```

結果の例は次のようになります。

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>次の手順  

- [Azure Cognitive Search での検索結果の操作方法](search-pagination-page-layout.md)
- [Azure Cognitive Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)