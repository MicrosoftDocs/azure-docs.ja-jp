---
title: マッピングデータフローでの JSON
description: Azure Data Factory のマッピング データ フローには、階層で JSON ドキュメントを処理するための組み込み機能があります
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/30/2019
ms.openlocfilehash: 153c7a1003c68526c960644bebcc4800e92edc3c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928325"
---
# <a name="mapping-data-flow-json-handling"></a>マッピング データ フローでの JSON の処理

## <a name="creating-json-structures-in-derived-column"></a>派生列での JSON 構造の作成

派生列式エディターを使用して複合列をデータ フローに追加できます。 派生列変換に新しい列を追加し、青いボックスをクリックして式ビルダーを開きます。 列を複合にするには、手動で JSON 構造を入力するか、UX を使用してサブ列を対話的に追加します。

### <a name="using-the-expression-builder-ux"></a>式ビルダー UX の使用

出力スキーマのサイド ウィンドウで、列の上にマウス ポインターを移動し、プラス記号のアイコンをクリックします。 列を複合型にするために、 **[Add subcolumn]\(サブ列の追加\)** を選択します。

![サブ列の追加](media/data-flow/addsubcolumn.png "サブ列の追加")

同じ方法で、さらに列とサブ列を追加できます。 複合でない各フィールドについては、式エディターで右側に式を追加できます。

![複合列](media/data-flow/complexcolumn.png "複合列")

### <a name="entering-the-json-structure-manually"></a>手動による JSON 構造の入力

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

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>完全な階層定義のサンプル手動スクリプト
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

データ フローでソースとして JSON データセットを使用すると、5 つの追加設定を行うことができます。 これらの設定は、 **[Source Options]\(ソース オプション\)** タブの **[JSON settings]\(JSON 設定\)** アコーディオンにあります。  

![JSON 設定](media/data-flow/json-settings.png "JSON 設定")

### <a name="default"></a>Default

既定では、JSON データは次の形式で読み取られます。

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>1 つのドキュメント

**[Single document]\(1 つのドキュメント\)** を選択した場合、マッピング データ フローでは、各ファイルから 1 つの JSON ドキュメントが読み取られます。 

``` json
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

**[Unquoted column names]\(引用符で囲まれていない列名\)** を選択した場合、マッピング データ フローでは、引用符で囲まれていない JSON 列が読み取られます。 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>コメントあり

JSON データに C または C++ スタイルのコメントが含まれている場合は、 **[コメントあり]** を選択します。

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>一重引用符付き

JSON フィールドと値に二重引用符ではなく単一引用符を使用している場合は、 **[Single quoted]\(単一引用符付き\)** を選択します。

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>円記号によるエスケープ

JSON データ内の文字をエスケープするためにバックスラッシュを使用している場合は、 **[Single quoted]\(単一引用符付き\)** を選択します。

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>高階関数

高階関数とは、1 つ以上の関数を引数として受け取る関数です。 配列操作を可能にする、マッピング データ フローでサポートされている高階関数の一覧を次に示します。

### <a name="filter"></a>filter
指定された述語を満たさない要素を配列から除外します。 filter は、述語関数の 1 つの要素への参照を #item として予期します。

#### <a name="examples"></a>例
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
指定された式を使用して、配列の各要素を新しい要素にマップします。 map は、式関数の 1 つの要素への参照を #item として予期します。

#### <a name="examples"></a>例
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>reduce
配列内の要素を累積します。 reduce は、最初の式関数のアキュムレータと 1 つの要素への参照を #acc および #item として予期し、結果の値を 2 番目の式関数で使用される #result として予期します。

#### <a name="examples"></a>例
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>sort
指定された述語関数を使用して配列を並べ替えます。 sort は、式関数の 2 つの連続する要素への参照を #item1 および #item2 として予期します。

#### <a name="examples"></a>例
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>contains
指定された配列内のいずれかの要素が、指定された述語で true と評価される場合に true を返します。 contains は、述語関数の 1 つの要素への参照を #item として予期します。

#### <a name="examples"></a>例
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>次の手順

* [派生列変換を使用した階層構造の構築](data-flow-derived-column.md)に関する記事を参照してください。
