---
title: OData フィルター リファレンス - Azure Search
description: Azure Search クエリでのフィルター構文に関する OData 言語リファレンスです。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081916"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Azure Search での OData $filter 構文

Azure Search では、[OData フィルター式](query-odata-filter-orderby-syntax.md)を使用して、フルテキスト検索語句の他に追加の条件が検索クエリに適用されます。 この記事では、フィルターの構文について詳しく説明します。 フィルターとは何か、またフィルターをどのように使用して特定のクエリのシナリオを実現するのかについて、より一般的な情報は、「[Azure Search のフィルター](search-filters.md)」を参照してください。

## <a name="syntax"></a>構文

OData 言語のフィルターはブール式です。そしてブール式は、次の EBNF ([拡張バッカス・ナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) に示すいくつかの種類の式のいずれかとすることができます。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 完全な EBNF については、「[Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)」をご覧ください。

ブール式の種類は次のとおりです。

- `any` または `all` を使用したコレクション フィルター式。 これらによってコレクション フィールドにフィルター条件が適用されます。 詳細については、[Azure Search での OData コレクション演算子](search-query-odata-collection-operators.md)に関するページを参照してください。
- `and`、`or`、および `not` の演算子を使用して他のブール式を結合する論理式。 詳細については、[Azure Search での OData 論理演算子](search-query-odata-logical-operators.md)に関するページを参照してください。
- `eq`、`ne`、`gt`、`lt`、`ge`、および `le` の演算子を使用してフィールドまたは範囲変数と定数値とを比較する比較式。 詳細については、[Azure Search での OData 比較演算子](search-query-odata-comparison-operators.md)に関するページを参照してください。 比較式はまた、`geo.distance` 関数を使用して地理空間座標間の距離を比較する場合も使用されます。 詳細については、[Azure Search での OData 地理空間関数](search-query-odata-geo-spatial-functions.md)に関するページを参照してください。
- ブール型リテラル: `true` および `false`。 これらの定数はプログラムでフィルターを生成する場合に役立つ可能性がありますが、それ以外の場合は実際には使用されない傾向にあります。
- 次のような Boolean 関数の呼び出し
  - `geo.intersects`: 指定されたポイントが指定された多角形内にあるかどうかをテストします。 詳細については、[Azure Search での OData 地理空間関数](search-query-odata-geo-spatial-functions.md)に関するページを参照してください。
  - `search.in`: フィールドまたは範囲変数を、値のリスト内の各値と比較します。 詳細については、Azure Search での [OData `search.in` 関数](search-query-odata-search-in-function.md)に関するページを参照してください。
  - `search.ismatch` および `search.ismatchscoring`: フィルター コンテキスト内でフルテキスト検索操作を実行します。 詳細については、[Azure Search での OData フルテキスト検索関数](search-query-odata-full-text-search-functions.md)に関するページを参照してください。
- `Edm.Boolean` 型のフィールド パスまたは範囲変数。 たとえば、ご利用のインデックスに `IsEnabled` というブール フィールドが含まれている場合に、このフィールドが `true` であるすべてのドキュメントを取得するときは、フィルター式を単に `IsEnabled` という名前にすることができます。
- かっこで囲まれたブール式。 かっこを使用することは、フィルター内で操作の順序を明示的に決定するのに役立ちます。 OData 演算子の既定の優先順位の詳細については、次のセクションを参照してください。

### <a name="operator-precedence-in-filters"></a>フィルターでの演算子の優先順位

サブ式をかっこで囲まずにフィルター式を記述した場合、Azure Search では一連の演算子の優先順位規則に従って式が評価されます。 これらの規則は、どの演算子を使用してサブ式が結合されているかに基づいています。 次の表に、優先順位の最も高いものから最も低いものへの順に演算子のグループを一覧表示します。

| Group | 演算子 |
| --- | --- |
| 論理演算子 | `not` |
| 比較演算子 | `eq`、`ne`、`gt`、`lt`、`ge`、`le` |
| 論理演算子 | `and` |
| 論理演算子 | `or` |

上記の表で上位にある演算子は、他の演算子よりもオペランドに "より緊密にバインドされます"。 たとえば、`and` は `or` よりも優先順位が高く、比較演算子はそのどちらよりも優先順位が高いため、次の 2 つの式は等価です。

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not` 演算子はすべての中で最も高い優先順位を持ちます (比較演算子よりもさらに優先度が高くなります)。 そのため、フィルターを次のように作成しようとした場合:

    not Rating gt 5

次のようなエラー メッセージが表示されます。

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

このエラーが発生するのは、演算子が `Rating` フィールド (`Edm.Int32` 型) にのみ関連付けられていて、比較式全体には関連付けられていないためです。 修正するには、`not` のオペランドをかっこで囲みます。

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>フィルター サイズの制限

Azure Search に送信できるフィルター式のサイズと複雑性には制限があります。 この上限はおおよそ、フィルター式の句の数に基づきます。 目安として、数百の句がある場合、上限を超える危険性があります。 サイズが無制限のフィルターを生成しない方法でアプリケーションを設計することをお勧めします。

> [!TIP]
> 等価比較の長い論理和演算ではなく、[ `search.in` 関数](search-query-odata-search-in-function.md) を使用すると、関数呼び出しが 1 つの句としてカウントされるため、フィルター句の制限を回避するのに役立ちます。

## <a name="examples"></a>例

基本料金が $200 未満の部屋が少なくとも 1 つある 4 つ星以上のホテルをすべて探します。

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

"Sea View Motel" 以外で 2010 年以降に改装されているホテルをすべて探します。

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

2010 年以降に改装されたホテルをすべて探します。 datetime リテラルには、太平洋標準時のタイム ゾーン情報が含まれています。  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

駐車場を備え、すべての部屋が禁煙になっているホテルをすべて探します。

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- または -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

高級ホテルをすべて探すか、駐車場を備えた 5 つ星以上のホテルをすべて探します。  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

少なくとも 1 つの部屋に "wifi" というタグが付いたホテルをすべて検索します (各部屋のタグは `Collection(Edm.String)` フィールドに格納されています)。  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

何らかの部屋が用意されているすべてのホテルを探します。  

    $filter=Rooms/any()

部屋が用意されていないすべてのホテルを探します。

    $filter=not Rooms/any()

指定された参照ポイントの 10 キロメートル内にあるホテルをすべて探します (`Location` は型 `Edm.GeographyPoint` のフィールドです)。

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

多角形として表現された所与のビューポート内にあるホテルをすべて探します (`Location` は型 Edm.GeographyPoint のフィールドです)。 多角形は閉じられている必要があります。つまり、最初と最後のポイント セットを同じにする必要があります。 また、[ポイントは反時計回り順にリストする必要があります](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

[説明] フィールドが null になっているホテルをすべて探します。 フィールドが null になるのは、それが設定されていない場合、または明示的に null に設定されている場合です。  

    $filter=Description eq null

"Sea View motel" または "Budget hotel" と同じ名前のすべてのホテルを探します。 これらの語句にはスペースを含めます。スペースは既定の区切り記号です。 3 番目の文字列パラメーターとして、単一引用符で囲まれた代替区切り記号を指定できます。  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

"Sea View motel" または "Budget hotel" と同じ名前のすべてのホテルを探します。区切り記号として '|' を使用します。  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

すべての部屋に "wifi" または "tub" というタグが付いているすべてのホテルを探します。

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

タグ内の 'heated towel racks' (タオル ウォーマー ラック) や 'hairdryer included' (ヘアドライヤーあり) など、コレクション内の語句との一致を探します。

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

"waterfront" という言葉の付いたドキュメントを探します。 このフィルターは `search=waterfront` を指定した[検索要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)と同じになります。

    $filter=search.ismatchscoring('waterfront')

"hostel" という言葉を含み、評価が 4 以上のドキュメントを探すか、"motel" という言葉を含み、評価が 5 のドキュメントを探します。 この要求は、`or` を使用してフルテキスト検索とフィルター操作を組み合わせたものであるため、`search.ismatchscoring` 関数なしでは表現できませんでした。

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

"luxury" という言葉のないドキュメントを探します。

    $filter=not search.ismatch('luxury')

"ocean view" というフレーズを含むか、評価が 5 のドキュメントを探します。 `search.ismatchscoring` クエリは `HotelName` フィールドと `Description` フィールドに対してのみ実行されます。 論理和演算の 2 つ目の句にのみ一致するドキュメントも返されることに注意してください。`Rating` が 5 のホテルです。 式のスコア部分にこれらのドキュメントが一致しなかったことをはっきりさせるため、スコア 0 で返されます。

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

説明の中で "hotel" という言葉と "airport" という言葉の間隔が 5 単語以内であり、かつ全室禁煙であるホテルを探します。 このクエリでは、[完全 Lucene クエリ言語](query-lucene-syntax.md)が使用されます。

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>次の手順  

- [Azure Search のフィルター](search-filters.md)
- [Azure Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
