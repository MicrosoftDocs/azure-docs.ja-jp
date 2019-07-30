---
title: OData の search.in 関数リファレンス - Azure Search
description: Azure Search クエリにおける OData の search.in 関数。
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
ms.openlocfilehash: a61291e547021077341a5f1b3db7422afa5b9440
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449978"
---
# <a name="odata-searchin-function-in-azure-search"></a>Azure Search における OData の `search.in` 関数

[OData フィルター式](query-odata-filter-orderby-syntax.md)の一般的なシナリオは、各ドキュメントの 1 つのフィールドが多数の可能な値のいずれかと等しいかどうかを調べることです。 たとえば、一部のアプリケーションでは、1 つ以上のプリンシパル ID を含むフィールドを、クエリを発行するユーザーを表すプリンシパル ID のリストと照合することで、[セキュリティによるトリミング](search-security-trimming-for-azure-search.md)を実装しています。 このようなクエリを記述する 1 つの方法として、[`eq`](search-query-odata-comparison-operators.md) および [`or`](search-query-odata-logical-operators.md) 演算子を使用します。

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

ただし、`search.in` 関数を使用すると、より簡潔に記述できます。

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> `search.in` を使用すると、より簡潔で読みやすくなるだけでなく、[パフォーマンス上の利点](#bkmk_performance)も得られます。また、フィルターに含める値が数百または数千になる場合に、[フィルターの特定のサイズ制限](search-query-odata-filter.md#bkmk_limits)を回避することもできます。 そのため、等値式のより複雑な論理和演算ではなく、`search.in` を使用することを強くお勧めします。

> [!NOTE]
> OData 標準のバージョン 4.01 では、Azure Search の `search.in` 関数と同様に動作する [`in` 演算子](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)が最近導入されました。 ただし、Azure Search ではこの演算子はサポートされていないので、代わりに `search.in` 関数を使用する必要があります。

## <a name="syntax"></a>構文

次の EBNF ([拡張バッカス・ナウア記法](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) では、`search.in` 関数の文法を定義しています。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

対話型の構文ダイアグラムも利用できます。

> [!div class="nextstepaction"]
> [Azure Search の OData 構文ダイアグラム](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 完全な EBNF については、「[OData expression syntax reference for Azure Search (Azure Search の OData 式構文リファレンス)](search-query-odata-syntax-reference.md)」をご覧ください。

`search.in` 関数では、指定された文字列フィールドまたは範囲変数が、指定されたリストの値のいずれかと等しいかどうかをテストします。 変数とリストの各値が等しいかどうかは、`eq` 演算子の場合と同様に、大文字と小文字を区別して判断されます。 そのため、`search.in(myfield, 'a, b, c')` のような式は、`search.in` の方がパフォーマンスが良いという点を除き、`myfield eq 'a' or myfield eq 'b' or myfield eq 'c'` に等しくなります。

`search.in` 関数には、次の 2 つのオーバーロードがあります。

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

次の表では、各パラメーターを定義しています。

| パラメーター名 | Type | 説明 |
| --- | --- | --- |
| `variable` | `Edm.String` | 文字列フィールド参照 (`search.in` が `any` または `all` 式内で使用されている場合は、文字列コレクション フィールドの範囲変数)。 |
| `valueList` | `Edm.String` | `variable` パラメーターと照合する値の区切りリストを含む文字列。 `delimiters` パラメーターが指定されていない場合、既定の区切り記号はスペースとコンマです。 |
| `delimiters` | `Edm.String` | `valueList` パラメーターを解析するときに、各文字が区切りとして扱われる文字列。 このパラメータの既定値は `' ,'` です。これは、間にスペースやコンマがある値は、区切られることを意味します。 値にスペースやコンマが含まれているため、それ以外の区切りを使用する必要がある場合は、このパラメーターで `'|'` などの代替区切り記号を指定できます。 |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>`search.in` のパフォーマンス

`search.in` を使用する場合で、2 つ目のパラメーターに数百または数千単位の値リストが含まれるとき、応答時間が 1 秒以下になることが期待できます。 `search.in` に渡すことができる項目の数は、最大要求サイズによって制限されますが、明示的な上限はありません。 ただし、値の数が増えると、それだけ待ち時間が長くなります。

## <a name="examples"></a>例

"Sea View motel" または "Budget hotel" と同じ名前のすべてのホテルを探します。 語句には、既定の区切り記号であるスペースを含めます。 3 番目の文字列パラメーターとして、単一引用符で囲まれた代替区切り記号を指定できます。  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

"Sea View motel" または "Budget hotel" と同じ名前のすべてのホテルを探します。区切り記号として '|' を使用します。

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

"wifi" または "tub" というタグが付いた客室があるすべてのホテルを探します。

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

タグ内の 'heated towel racks' (タオル ウォーマー ラック) や 'hairdryer included' (ヘアドライヤーあり) など、コレクション内の語句との一致を探します。

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

"motel" または "cabin" というタグがないすべてのホテルを探します。

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>次の手順  

- [Azure Search のフィルター](search-filters.md)
- [Azure Search の OData 式言語の概要](query-odata-filter-orderby-syntax.md)
- [Azure Search の OData 式構文リファレンス](search-query-odata-syntax-reference.md)
- [ドキュメントの検索 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
