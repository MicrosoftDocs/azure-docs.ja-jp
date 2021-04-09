---
title: OData の order-by リファレンス
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のクエリで order-by を使用するための構文と言語のリファレンス ドキュメント。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/05/2020
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
ms.openlocfilehash: 83ab2c6b97435ace0d2bc508cbf522600391b60b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88926832"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search での OData $orderby 構文

 [OData **$orderby** パラメーター](query-odata-filter-orderby-syntax.md)を使用すれば、Azure Cognitive Search での検索結果にカスタムの並べ替え順序を適用することができます。 この記事では、 **$orderby** の構文について詳しく説明します。 検索結果を表示するときに **$orderby** を使用する方法に関するより一般的な情報については、「[Azure Cognitive Search での検索結果の操作方法](search-pagination-page-layout.md)」を参照してください。

## <a name="syntax"></a>構文

**$orderby** パラメーターは、最大 32 個の **order-by 句** をコンマで区切ったリストを受け取ります。 order-by 句の構文は、次の EBNF ([拡張バッカス・ナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) によって記述されます。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Cognitive Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 完全な EBNF については、[Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)に関するページをご覧ください。

各句にはソート基準を指定し、必要に応じてその後に並べ替え方向 (昇順の場合は `asc`、降順の場合は `desc`) を指定します。 方向を指定しない場合、既定値は昇順となります。 フィールドに null 値がある場合、null 値は、並べ替えが `asc` の場合は最初に、並べ替えが `desc` の場合は最後に表示されます。

並べ替え基準は `sortable` フィールドのパスとすることも、[`geo.distance`](search-query-odata-geo-spatial-functions.md) 関数または [`search.score`](search-query-odata-search-score-function.md) 関数の呼び出しとすることもできます。

複数のドキュメントで並べ替え基準が同じであり、`search.score` 関数が使用されない場合 (たとえば、数値の `Rating` フィールドで並べ替えるとき、3 つすべてのドキュメントの評価が 4 である場合)、同点にはドキュメント スコアで順位が付けられ、降順で表示されます。 ドキュメント スコアが同じ場合 (たとえば、要求にフルテキスト検索クエリが指定されていない)、同点のドキュメントには相対的な順序付けが確定しません。

複数の並べ替え基準を指定できます。 式の順序によって最終的な並べ替え順序が決められます。 たとえば、スコア別に降順で並べ替えた後、評価で並べ替える場合、構文は `$orderby=search.score() desc,Rating desc` のようになります。

**$orderby** の `geo.distance` の構文は **$filter** の場合と同じになります。 **$orderby** で `geo.distance` を使用するとき、それが適用されるフィールドは `Edm.GeographyPoint` 型にする必要があり、また `sortable` である必要があります。

**$orderby** の `search.score` の構文は `search.score()` です。 関数 `search.score` には、いかなるパラメーターも取得されません。

## <a name="examples"></a>例

基本料金別にホテルを昇順で並べ替えます。

```odata-filter-expr
    $orderby=BaseRate asc
```

評価別にホテルを降順で並べ替え、その後、基本料金別に昇順で並べ替えます (昇順は既定値です)。

```odata-filter-expr
    $orderby=Rating desc,BaseRate
```

評価別にホテルを降順で並べ替え、その後、特定の座標からの距離別に昇順で並べ替えます。

```odata-filter-expr
    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

search.score と評価に基づいて降順でホテルを並べ替え、その後、特定の座標からの距離別に昇順で並べ替えます。 2 つのホテルで関連性スコアおよび評価が同じ場合、距離的に近い方が先に表示されるようにします。

```odata-filter-expr
    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>次の手順  

- [Azure Cognitive Search での検索結果の操作方法](search-pagination-page-layout.md)
- [Azure Cognitive Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)