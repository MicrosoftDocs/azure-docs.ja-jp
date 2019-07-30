---
title: OData フルテキスト検索関数リファレンス - Azure Search
description: 'Azure Search クエリでの OData フルテキスト検索関数: search.ismatch および search.ismatchscoring。'
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081884"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>Azure Search での OData フルテキスト検索関数 - `search.ismatch` および `search.ismatchscoring`

Azure Search では、`search.ismatch` および `search.ismatchscoring` 関数を介して [OData フィルター式](query-odata-filter-orderby-syntax.md) のコンテキストでのフルテキスト検索がサポートされています。 これらの関数を使用すると、[Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents) の最上位の `search` パラメーターを使用しただけでは不可能な方法で、フルテキスト検索と厳密なブール値フィルタリングを組み合わせることができます。

> [!NOTE]
> `search.ismatch` および `search.ismatchscoring` 関数は、[Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents) のフィルター内でのみサポートされます。 これらは、[Suggest](https://docs.microsoft.com/rest/api/searchservice/suggestions) API または [Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) API ではサポートされていません。

## <a name="syntax"></a>構文

次の EBNF ([拡張バッカス・ナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) では、`search.ismatch` および `search.ismatchscoring` 関数の文法を定義しています。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 完全な EBNF については、「[Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)」をご覧ください。

### <a name="searchismatch"></a>search.ismatch

`search.ismatch` 関数では、フィルター式の一部としてフルテキスト検索クエリが評価されます。 検索クエリに一致するドキュメントは結果セットで返されます。 この関数の次のオーバーロードが利用できます。

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

次の表では、各パラメーターを定義しています。

| パラメーター名 | Type | 説明 |
| --- | --- | --- |
| `search` | `Edm.String` | 検索クエリ ([simple](query-simple-syntax.md) または [full](query-lucene-syntax.md) Lucene クエリ構文)。 |
| `searchFields` | `Edm.String` | 検索する検索可能フィールドのコンマ区切りリスト。既定値はインデックスのすべての検索可能フィールドになります。 `search` パラメーター内で[フィールド検索](query-lucene-syntax.md#bkmk_fields)を使用すると、このパラメーターで指定したフィールドはいずれも Lucene クエリ内のフィールド指定子によってオーバーライドされます。 |
| `queryType` | `Edm.String` | `'simple'` または `'full'`。既定値は `'simple'` です。 `search` パラメーターで使用されたクエリ言語を指定します。 |
| `searchMode` | `Edm.String` | `'all'` または `'any'`。既定値は `'any'` です。 ドキュメントを一致としてカウントするために、`search` パラメーター内の任意の検索語句またはすべての検索語句が一致する必要があるかどうかが示されます。 `search` パラメーター内で [Lucene ブール演算子](query-lucene-syntax.md#bkmk_boolean)を使用すると、それらはこのパラメーターよりも優先されます。 |

上記のパラメーターはすべて、それに対応する[Search API の検索要求パラメーター](https://docs.microsoft.com/rest/api/searchservice/search-documents)に等しくなります。

`search.ismatch` 関数からは `Edm.Boolean` 型の値が返されます。このため、ブール[論理演算子](search-query-odata-logical-operators.md)を使用して、それを他のフィルター サブ式と組み合わせることができます。

> [!NOTE]
> Azure Search では、ラムダ式内での `search.ismatch` または `search.ismatchscoring` の使用はサポートされていません。 つまり、フルテキスト検索の一致を、同じオブジェクトに対する厳密なフィルターの一致に関連付けることができるオブジェクトのコレクションに対してフィルターを記述することは不可能です。 この制限と例の詳細については、[Azure Search でのコレクション フィルターのトラブルシューティング](search-query-troubleshoot-collection-filters.md)に関する記事をご覧ください。 この制限が存在する理由の詳細については、[Azure Search でのコレクション フィルター](search-query-understand-collection-filters.md)に関する記事をご覧ください。


### <a name="searchismatchscoring"></a>search.ismatchscoring

`search.ismatchscoring` 関数では、`search.ismatch` 関数と同様に、パラメーターとして渡されたフルテキスト検索クエリに一致したドキュメントに対して `true` が返されます。 両者の違いは、`search.ismatchscoring` クエリに一致するドキュメント スコアは全体的なドキュメント スコアに貢献するが、`search.ismatch` の場合、ドキュメント スコアは変わらないという点にあります。 この関数の次のオーバーロードは `search.ismatch` のそれと等しいパラメーターで利用できます。

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

`search.ismatch` 関数と `search.ismatchscoring` 関数の両方を同じフィルター式で使用できます。

## <a name="examples"></a>例

"waterfront" という言葉の付いたドキュメントを探します。 このフィルターは `search=waterfront` を指定した[検索要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)と同じになります。

    search.ismatchscoring('waterfront')

"hostel" という言葉を含み、評価が 4 以上のドキュメントを探すか、"motel" という言葉を含み、評価が 5 のドキュメントを探します。 この要求は `search.ismatchscoring` 関数なしでは表現できないことに注意してください。

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

"luxury" という言葉のないドキュメントを探します。

    not search.ismatch('luxury')

"ocean view" というフレーズを含むか、評価が 5 のドキュメントを探します。 `search.ismatchscoring` クエリは `HotelName` フィールドと `Rooms/Description` フィールドに対してのみ実行されます。

論理和演算の 2 つ目の句にのみ一致するドキュメントも返されることに注意してください。`Rating` が 5 のホテルです。 式のスコア部分にこれらのドキュメントが一致しなかったことをはっきりさせるため、スコア 0 で返されます。

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

"hotel" という言葉と "airport" という言葉がホテルの説明で互いに 5 単語以内にあり、少なくとも一部の部屋で喫煙が許可されていないドキュメントを探します。 このクエリでは、[完全 Lucene クエリ言語](query-lucene-syntax.md)が使用されます。

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>次の手順  

- [Azure Search のフィルター](search-filters.md)
- [Azure Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
