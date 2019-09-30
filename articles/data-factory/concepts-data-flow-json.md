---
title: Azure Data Factory の Mapping Data Flow における JSON の概念
description: Data Factory の Mapping Data Flow には、階層で JSON ドキュメントを処理するための組み込み機能があります
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: cde42dda47d54c03c50895bc625f99c9350b53e3
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210513"
---
# <a name="mapping-data-flow-json-handling"></a>Mapping Data Flow での JSON の処理

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="creating-json-structures-in-expression-editor"></a>式エディターでの JSON 構造の作成
### <a name="derived-column-transformation"></a>派生列変換
データ フローへの複合列の追加は、派生列式エディターを使用すると簡単になります。 新しい列を追加してエディターを開くと、手動での JSON 構造の入力または UI を使用した対話形式でのサブ列の追加の 2 つのオプションがあります。

#### <a name="interactive-ui-json-design"></a>対話型 UI での JSON の設計
出力スキーマ側のウィンドウから、`+` メニューを使用して新しいサブ列を追加できます。![サブ列の追加](media/data-flow/addsubcolumn.png "サブ列の追加")

そこから、新しい列とサブ列を同じ方法で追加できます。 複合でない各フィールドについては、式エディターで右側に式を追加できます。

![複合列](media/data-flow/complexcolumn.png "複合列")

#### <a name="manual-json-design"></a>手動による JSON の設計
JSON 構造を手動で追加するには、新しい列を追加し、エディターで式を入力します。 式は、次の一般的な形式に従います。
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
"complexColumn" という名前の列に対してこの式が入力された場合、次の JSON としてシンクに書き込まれます。
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-dsl"></a>サンプルの手動 DSL
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>ソース形式のオプション
### <a name="default"></a>Default
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>1 つのドキュメント
* オプション 1
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* オプション 2
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>引用符で囲まれていない列名
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>コメントあり
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>一重引用符付き
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>円記号によるエスケープ
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

# <a name="higher-order-functions"></a>高階関数
## <a name="filter"></a>filter
指定された述語を満たさない要素を配列から除外します。 filter は、述語関数の 1 つの要素への参照を #item として予期します。

### <a name="examples"></a>例
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>map
指定された式を使用して、配列の各要素を新しい要素にマップします。 map は、式関数の 1 つの要素への参照を #item として予期します。

### <a name="examples"></a>例
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>reduce
配列内の要素を累積します。 reduce は、最初の式関数のアキュムレータと 1 つの要素への参照を #acc および #item として予期し、結果の値を 2 番目の式関数で使用される #result として予期します。

### <a name="examples"></a>例
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>sort
指定された述語関数を使用して配列を並べ替えます。 sort は、式関数の 2 つの連続する要素への参照を #item1 および #item2 として予期します。

### <a name="examples"></a>例
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>contains
指定された配列内のいずれかの要素が、指定された述語で true と評価される場合に true を返します。 contains は、述語関数の 1 つの要素への参照を #item として予期します。

### <a name="examples"></a>例
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>次の手順

* [派生列変換を使用した階層構造の構築](data-flow-derived-column.md)に関する記事を参照してください。
