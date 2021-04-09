---
title: OData コレクション フィルターの概要
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のクエリで OData コレクション フィルターが動作するしくみについて説明します。これには、コレクションに固有の制限や動作が含まれます。
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
ms.openlocfilehash: 6af0f2b5221a737687578e939c14cecf3be14509
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88932918"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Azure Cognitive Search での OData コレクション フィルターの概要

Azure Cognitive Search でコレクションのフィールドの [フィルター処理を行う](query-odata-filter-orderby-syntax.md)には、[`any` および `all` 演算子](search-query-odata-collection-operators.md)を **ラムダ式** と共に使用します。 ラムダ式は **範囲変数** を参照するブール式です。 `any` および `all` 演算子はほとんどのプログラミング言語の `for` ループに似ており、範囲変数がループ変数、ラムダ式がループ本体の役割を果たします。 範囲変数は、ループの反復処理の間、コレクションの "現在" の値になります。

少なくとも、概念的にはそのように動作します。 実際には、Azure Cognitive Search は、`for` ループの動作とはまったく異なる方法でフィルターを実装します。 この違いがユーザーには認識されないのが理想ですが、特定の状況ではそうなりません。 最終的な結果として、ラムダ式を作成するときに従う必要がある規則があります。

この記事では、Azure Cognitive Search がフィルターを実行する方法を調査することによって、コレクション フィルターの規則が存在する理由について説明します。 複雑なラムダ式で高度なフィルターを作成する場合、この記事はフィルターで可能性のあることとその理由を理解するのに役立ちます。

コレクション フィルターの規則がどのようなものであるか (例を含む) については、「[Azure Cognitive Search での OData コレクション フィルターのトラブルシューティング](search-query-troubleshoot-collection-filters.md)」を参照してください。

## <a name="why-collection-filters-are-limited"></a>コレクション フィルターに制限がある理由

コレクションのすべての種類に対してフィルターのすべての機能がサポートされていないことについては、3 つの基になる理由があります。

1. 特定のデータ型では、特定の演算子のみがサポートされています。 たとえば、`lt` や `gt` などを使ってブール値 `true` と `false` を比較しても意味がありません。
1. Azure Cognitive Search は、型 `Collection(Edm.ComplexType)` のフィールドに対する **相関検索** をサポートしていません。
1. Azure Cognitive Search は、逆インデックスを使用して、すべての種類のデータ (コレクションを含む) に対するフィルターを実行します。

最初の理由は、単に、OData 言語と EDM 型システムの定義方法の結果によるものです。 後の 2 つについては、この記事の残りの部分で詳しく説明します。

## <a name="correlated-versus-uncorrelated-search"></a>相関検索と非相関検索

複合オブジェクトのコレクションに対して複数のフィルター条件を適用するとき、条件は "*、コレクション内の各オブジェクト*" に適用されるため、条件は **相関** されます。 たとえば、次のフィルターでは、料金が 100 未満のデラックス ルームが 1 室以上あるホテルが返されます。

```odata-filter-expr
    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)
```

フィルター処理が "*相関されない*" 場合、上記のフィルターでは、1 つの部屋がデラックスで、別の部屋が基本料金 100 未満であるホテルが返される可能性があります。 ラムダ式の両方の句が同じ範囲変数 `room` に適用されるので、それでは意味がありません。 これが、そのようなフィルターが相関される理由です。

ただし、フルテキスト検索では、特定の範囲変数を参照する方法はありません。 フィールド検索を使って、次のような[完全な Lucene クエリ](query-lucene-syntax.md)を発行するものとします。

```odata-filter-expr
    Rooms/Type:deluxe AND Rooms/Description:"city view"
```

この場合、1 つの部屋がデラックスで、別の部屋の説明に "city view" という語句が含まれるホテルが返される可能性があります。 たとえば、`Id` が `1` である以下のドキュメントは、クエリに一致します。

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

その理由は、次の表に示すように、`Rooms/Type` ではドキュメント全体の `Rooms/Type` フィールドの分析されたすべての語句が参照され、`Rooms/Description` フィールドについても同様であるためです。

フルテキスト検索に対して `Rooms/Type` が格納される方法:

| `Rooms/Type` での語句 | ドキュメント ID |
| --- | --- |
| deluxe | 1、2 |
| standard | 1 |

フルテキスト検索に対して `Rooms/Description` が格納される方法:

| `Rooms/Description` での語句 | ドキュメント ID |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| garden | 1 |
| large | 1 |
| motel | 2 |
| room | 1、2 |
| standard | 1 |
| suite | 1 |
| view | 1 |

したがって、基本的に "部屋の `Type` が 'Deluxe Room' と等しく、**その同じ部屋** の `BaseRate` が 100 未満であるドキュメントと一致する" ことを示す上記のフィルターとは異なり、検索クエリでは "`Rooms/Type` に語句 'deluxe' が含まれ、`Rooms/Description` に語句 'city view' が含まれるドキュメントと一致する" ことが示されます。 後者の場合、フィールドを相関できる個別の部屋の概念はありません。

> [!NOTE]
> 相関検索に対するサポートが Azure Cognitive Search に追加されることを希望する場合は、[このユーザーの声の項目](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)に投票してください。

## <a name="inverted-indexes-and-collections"></a>逆インデックスとコレクション

複雑なコレクションに対するラムダ式での制限は、`Collection(Edm.Int32)` や `Collection(Edm.GeographyPoint)` などの単純なコレクションに対する制限よりはるかに少ないことにお気付きかもしれません。 これは、Azure Cognitive Search が複雑なコレクションをサブドキュメントの実際のコレクションとして格納するのに対して、単純なコレクションはまったくコレクションとして格納されないためです。

たとえば、オンライン小売店用のインデックスでの `seasons` のようなフィルター可能な文字列コレクション フィールドについて考えます。 このインデックスには、次のようなドキュメントがアップロードされる可能性があります。

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

`seasons` フィールドの値は **逆インデックス** と呼ばれる構造に格納され、次のようになります。

| 項目 | ドキュメント ID |
| --- | --- |
| spring | 1、2 |
| summer | 1 |
| fall | 1、2 |
| winter | 2、3 |

このデータ構造は、特定の語句はどのドキュメントに含まれているか、という 1 つの質問に対して非常に高速に回答するように設計されています。 この質問に答える処理は、コレクションのループというより、単純な等値チェックに近いものです。 実際、文字列コレクションの場合に、Azure Cognitive Search が `any` に対するラムダ式の内部の比較演算子として `eq` のみを許可するのはこのためです。

このような等価性を踏まえて、次に、`or` を使用して同じ範囲変数でどのように複数の等価性チェックを組み合わせることができるかを見てみましょう。 それは、代数と[量指定子の分配則](https://en.wikipedia.org/wiki/Existential_quantification#Negation)に従って動作します。 次の式

```odata-filter-expr
    seasons/any(s: s eq 'winter' or s eq 'fall')
```

は以下に匹敵します。

```odata-filter-expr
    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')
```

そして、2 つの各 `any` サブ式を、逆インデックスを使って効率的に実行できます。 また、[量指定子の否定法則](https://en.wikipedia.org/wiki/Existential_quantification#Negation)により、次の式

```odata-filter-expr
    seasons/all(s: s ne 'winter' and s ne 'fall')
```

は以下に匹敵します。

```odata-filter-expr
    not seasons/any(s: s eq 'winter' or s eq 'fall')
```

これが、`ne` および `and` で `all` を使用できる理由です。

> [!NOTE]
> 詳細はこのドキュメントの範囲を超えますが、これらの同じ原則が、[地理空間ポイントのコレクションに対する距離と交差のテスト](search-query-odata-geo-spatial-functions.md)に拡張されます。 これが、`any` で次のようになる理由です。
>
> - `geo.intersects` を否定することはできません
> - `geo.distance` は、`lt` または `le` を使って比較する必要があります
> - 式を結合するには、`and` ではなく `or` を使う必要があります
>
> `all` には逆の規則が適用されます。

`lt`、`gt`、`le`、`ge` の各演算子がサポートされるデータ型のコレクションをフィルター処理するときは、さまざまな式が許可されます (`Collection(Edm.Int32)` など)。 具体的には、基になる比較式が `and` を使って **範囲比較** に結合されている限り、`any` において`and` だけでなく `or` を使うことができ、それがさらに `or` を使って結合されます。 ブール式のこの構造は[選言標準形 (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) と呼ばれ、"ORs of ANDs" としても知られています。 逆に、これらのデータ型に対する `all` のラムダ式は、[連言標準形 (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) ("ANDs of ORs" としても知られます) になっている必要があります。 Azure Cognitive Search は、文字列に対する高速な用語参照と同様に逆インデックスを使用して効率的に実行できるため、このような範囲の比較を許可します。

まとめると、ラムダ式で許可されることの経験則は次のようになります。

- `any` の内部では、等値、範囲比較、`geo.intersects`、`lt` または `le` での `geo.distance` の比較など、"*ポジティブ チェック*" が常に許可されます (距離のチェックでは "近さ" は等価性と同じように考えます)。
- `any` の内部では、`or` が常に許可されます。 `and` は、範囲チェックを表現できるデータ型に対してだけ、ORs of ANDs (DNF) を使用する場合にのみ使用できます。
- `all` の内部では、規則は逆になります。"*ネガティブ チェック*" のみが許可され、`and` を常に使用でき、`or` は ANDs of ORs (CNF) として表される範囲チェックに対してだけ使用できます。

実際には、これらはいずれにしても使用する可能性が最も高いフィルターの種類です。 それでも、可能なことの境界を理解しておくと役に立ちます。

許可されるフィルターと許可されないフィルターの種類の具体的な例については、「[有効なコレクション フィルターを記述する方法](search-query-troubleshoot-collection-filters.md#bkmk_examples)」をご覧ください。

## <a name="next-steps"></a>次のステップ  

- [Azure Cognitive Search での OData コレクション フィルターのトラブルシューティング](search-query-troubleshoot-collection-filters.md)
- [Azure Cognitive Search のフィルター](search-filters.md)
- [Azure Cognitive Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)