---
title: Azure Maps Web SDK でのデータ ドリブンのスタイルの式 |Microsoft Docs
description: Azure Maps Web SDK でデータ ドリブンのスタイルの式を使用する方法。
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 507af54b8b4c2e7c67538a1a25a040c7ee5fdfd5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976311"
---
# <a name="data-driven-style-expressions-web-sdk"></a>データ ドリブンのスタイルの式 (Web SDK)

式を使用すると、データ ソース内の各図形で定義されたプロパティを監視するスタイル設定オプションにビジネス ロジックを適用できます。 また、式は、データ ソースまたはレイヤーでデータをフィルター処理するのにも使用できます。 式は、if ステートメントなどの条件付きロジックで構成でき、文字列、論理演算子、数学演算子によるデータの操作にも使用できます。 

データ ドリブンのスタイルを使用すると、スタイル設定関連のビジネス ロジックを実装するために必要なコードの量を削減できます。 レイヤーを併用すると、式は個々のスレッドでのレンダリング時に評価されます。この場合、UI スレッドでのビジネス ロジックの評価時と比較して、パフォーマンスが向上します。

次のビデオでは、Azure Maps Web SDK でのデータ ドリブンのスタイル処理の概要を示します。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

式は JSON 配列として表されます。 配列内の式の最初の要素は、式の演算子の名前を指定する文字列です。 たとえば、"+" や "case" です。 次の要素 (存在する場合) は、式に対する引数です。 各引数は、リテラル値 (文字列、数値、ブール値、`null`)、または別の式の配列です。 次の疑似コードでは、式の基本構造を定義します。 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK では、単独で、または他の式と組み合わせて使用できる、さまざまな種類の式がサポートされています。

| 式の種類 | 説明 |
|---------------------|-------------|
| [集計式](#aggregate-expression) | データのセットに対して処理され、`DataSource` の `clusterProperties` オプションと共に使用できる計算を定義する式です。 |
| [ブール式](#boolean-expressions) | ブール式により、ブール値の比較を評価するためにブール演算子式のセットが提供されます。 |
| [色の式](#color-expressions) | 色の式を使用すると、色の値の作成と操作が容易になります。 |
| [条件式](#conditional-expressions) | 条件式では、if ステートメントのようなロジック操作が提供されます。 |
| [データ式](#data-expressions) | 機能内のプロパティ データへのアクセスを提供します。 |
| [補間式とステップ式](#interpolate-and-step-expressions) | 補間式とステップ式は、補間曲線またはステップ関数に沿って値を計算するために使用できます。 |
| [レイヤー固有の式](#layer-specific-expressions) | 1 つのレイヤーにのみ適用できる特殊な式。 |
| [数式](#math-expressions) | 式のフレームワーク内でデータ ドリブンの計算を実行する数学演算子を提供します。 |
| [文字列演算子式](#string-operator-expressions) | 文字列演算子式では、連結や大文字と小文字の変換など、文字列の変換操作を実行します。 |
| [型式](#type-expressions) | 型式では、文字列、数値、ブール値などのさまざまなデータ型をテストおよび変換するためのツールを提供します。 |
| [変数バインド式](#variable-binding-expressions) | 変数バインド式では、計算結果を変数に格納して、格納された値を再計算することなく、式内の別の場所で繰り返し参照することができます。 |
| [ズーム式](#zoom-expression) | レンダリング時に、マップの現在のズーム レベルを取得します。 |

このドキュメントのすべての例では、次の機能を使用して、各種の式を使用できるさまざまな方法を示します。 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>データ式

データ式では、機能内のプロパティ データへのアクセスを提供します。 

| 式 | 戻り値の型 | 説明 |
|------------|-------------|-------------|
| `['at', number, array]` | object | 配列から項目を取得します。 |
| `['geometry-type']` | string | 機能の geometry 型: Point、MultiPoint、LineString、MultiLineString、Polygon、MultiPolygon を取得します。 |
| `['get', string]` | value | 現在の機能のプロパティからプロパティ値を取得します。 要求されたプロパティがない場合は、null が返されます。 |
| `['get', string, object]` | value | 指定されたオブジェクトのプロパティからプロパティ値を取得します。 要求されたプロパティがない場合は、null が返されます。 |
| `['has', string]` | ブール値 | 機能のプロパティに、指定されたプロパティがあるかどうかを判断します。 |
| `['has', string, object]` | ブール値 | オブジェクトのプロパティに、指定されたプロパティがあるかどうかを判断します。 |
| `['id']` | value | 機能の ID がある場合は取得します。 |
| `['length', string | array]` | number | 文字列または配列の長さを取得します。 |

**例**

機能のプロパティには、`get` 式を使用して式内で直接アクセスできます。 次の例では、機能の "zoneColor" 値を使用して、バブル レイヤーの色のプロパティを指定します。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

すべてのポイント機能に `zoneColor` プロパティがある場合、上記の例は問題なく機能しますが、プロパティがない場合、色は "黒" にフォールバックする可能性があります。 フォールバックの色を変更するには、`case` 式と `has` 式を組み合わせて使用して、プロパティが存在するかどうかをチェックします。プロパティが存在しない場合は代わりにフォールバックの色が返されます。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

バブルとシンボルのレイヤーでは、既定でデータ ソース内のすべての図形の座標がレンダリングされます。 これは、多角形または線の頂点を強調表示するために実行できます。 レイヤーの `filter` オプションを使用すると、ブール式内で `['geometry-type']` 式を使用して、レンダリングする機能の geometry 型を制限できます。 次の例では、`Point` 機能のみがレンダリングされるようにバブル レイヤーを制限できます。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

次の例では、`Point` 機能と `MultiPoint` 機能の両方をレンダリングできます。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

同様に、線レイヤーでは多角形のアウトラインがレンダリングされます。 線レイヤーでこの動作を無効にするには、`LineString` 機能と `MultiLineString` 機能のみを許可するフィルターを追加します。  

## <a name="math-expressions"></a>数式

数式では、式のフレームワーク内でデータ ドリブンの計算を実行する数学演算子を提供します。

| 式 | 戻り値の型 | 説明 |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | 指定された数値の合計が計算されます。 |
| `['-', number]` | number | 0 から、指定された数値が減算されます。 |
| `['-', number, number]` | number | 1 番目の数値から 2 番目の数値が減算されます。 |
| `['*', number, number, …]` | number | 指定された数値が乗算されます。 |
| `['/', number, number]` | number | 1 番目の数値が 2 番目の数値で除算されます。 |
| `['%', number, number]` | number | 1 番目の数値を 2 番目の数値で除算した際の剰余が計算されます。 |
| `['^', number, number]` | number | 1 番目の値を 2 番目の数値で累乗した値が計算されます。 |
| `['abs', number]` | number | 指定された数値の絶対値が計算されます。 |
| `['acos', number]` | number | 指定された数値のアークコサインが計算されます。 |
| `['asin', number]` | number | 指定された数値のアークサインが計算されます。 |
| `['atan', number]` | number | 指定された数値のアークタンジェントが計算されます。 |
| `['ceil', number]` | number | 数値が次の整数に切り上げられます。 |
| `['cos', number]` | number | 指定された数値のコサインが計算されます。 |
| `['e']` | number | 数理定数 `e` が返されます。 |
| `['floor', number]` | number | この数値が前の整数に切り下げられます。 |
| `['ln', number]` | number | 指定された数値の自然対数が計算されます。 |
| `['ln2']` | number | 数理定数 `ln(2)` が返されます。 |
| `['log10', number]` | number | 指定された数値の 10 を底とする対数が計算されます。 |
| `['log2', number]` | number | 指定された数値の 2 を底とする対数が計算されます。 |
| `['max', number, number, …]` | number | 指定された数値セット内の最大数が計算されます。 |
| `['min', number, number, …]` | number | 指定された数値セット内の最小数が計算されます。 |
| `['pi']` | number | 数理定数 `PI` が返されます。 |
| `['round', number]` | number | 数値が最も近い整数に丸められます。 端数を含む値は、ゼロから離れる方向に丸められます。 たとえば、`['round', -1.5]` は -2 に評価されます。 |
| `['sin', number]` | number | 指定された数値のサインが計算されます。 |
| `['sqrt', number]` | number | 指定された数値の平方根が計算されます。 |
| `['tan', number]` | number | 指定された数値のタンジェントが計算されます。 |

## <a name="aggregate-expression"></a>集計式

集計式では、データのセットに対して処理され、`DataSource` の `clusterProperties` オプションと共に使用できる計算を定義します。 これらの式の出力は、数値またはブール値である必要があります。 

集計式には 3 つの値があります。演算子の値と初期値、そして集計操作を適用するためにデータ内の各機能からプロパティを取得する式です。 この式の書式は次のとおりです。

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- 演算子:クラスター内の各ポイントについて `mapExpression` によって計算されるすべての値に適用される式関数。 サポートされている演算子: 
    - 数値の場合: `+`、`*`、`max`、`min`
    - ブール値の場合: `all`、`any`
- initialValue:最初の計算値が集計される初期値。
- mapExpression:データ セット内の各ポイントに適用される式。

**例**

データ セット内のすべての機能に、数値である `revenue` プロパティがある場合。 データ セットから作成されたクラスター内のすべてのポイントの合計収益は、次の集計式を使用して計算できます: `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>ブール式

ブール式により、ブール値の比較を評価するためにブール演算子式のセットが提供されます。

値を比較する際に、比較は厳密に型指定されます。 異なる型の値は、常に等しくないと見なされます。 解析時に型が異なるとがわかった場合は、無効と見なされ、解析エラーが生成されます。 

| 式 | 戻り値の型 | 説明 |
|------------|-------------|-------------|
| `['! ', boolean]` | ブール値 | 論理否定。 入力が `false` の場合は `true` が返され、入力が `true` の場合は `false` が返されます。 |
| `['!= ', value, value]` | ブール値 | 入力値が等しくない場合は `true` が返され、それ以外の場合は `false` が返されます。 |
| `['<', value, value]` | ブール値 | 最初の入力が厳密に 2 番目の入力未満の場合は `true` が返され、それ以外の場合は `false` が返されます。 引数は、両方とも文字列、または両方とも数値である必要があります。 |
| `['<=', value, value]` | ブール値 | 最初の入力が 2 番目の入力以下の場合は `true` が返され、それ以外の場合は `false` が返されます。 引数は、両方とも文字列、または両方とも数値である必要があります。 |
| `['==', value, value]` | ブール値 | 入力値が等しい場合は `true` が返され、それ以外の場合は `false` が返されます。 引数は、両方とも文字列、または両方とも数値である必要があります。 |
| `['>', value, value]` | ブール値 | 最初の入力が厳密に 2 番目の入力より大きい場合は `true` が返され、それ以外の場合は `false` が返されます。 引数は、両方とも文字列、または両方とも数値である必要があります。 |
| `['>=' value, value]` | ブール値 | 最初の入力が 2 番目の入力以上の場合は `true` が返され、それ以外の場合は `false` が返されます。 引数は、両方とも文字列、または両方とも数値である必要があります。 |
| `['all', boolean, boolean, …]` | ブール値 | すべての入力が `true` の場合は `true` が返され、それ以外の場合は `false` が返されます。 |
| `['any', boolean, boolean, …]` | ブール値 | いずれかの入力が `true` の場合は `true` が返され、それ以外の場合は `false` が返されます。 |

## <a name="conditional-expressions"></a>条件式

条件式では、if ステートメントのようなロジック操作が提供されます。

次の式では、入力データに対して条件付きロジック操作を実行します。 たとえば、`case` 式では "if/then/else" ロジックを提供する一方、`match` 式は "switch ステートメント" に似ています。 

### <a name="case-expression"></a>case 式

`case` 式は、ロジック (if/then/else) などの if ステートメントを提供する一種の条件式です。 この種類の式では、一連のブール条件を通過し、true となる最初のブール条件の出力値が返されます。

次の疑似コードでは、`case` 式の構造が定義されます。 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**例**

次の例では、さまざまなブール条件を通過し、`true` に評価されるものが見つかったら、その関連値が返されます。 `true` に評価されるブール条件がない場合は、フォールバック値が返されます。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>match 式

`match` 式は、ロジックなどの switch ステートメントを提供する一種の条件式です。 入力には、文字列または数値を返す `['get', 'entityType']` などの任意の式を使用できます。 各ラベルは、1 つのリテラル値、または値がすべて文字列またはすべて数値であるリテラル値の配列である必要があります。 配列内のいずれかの値が一致する場合、入力は一致します。 各ラベルは一意である必要があります。 入力の型がラベルの型と一致しない場合、結果はフォールバック値になります。

次の疑似コードでは、`match` 式の構造が定義されます。 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**例**

次の例では、バブル レイヤー内のポイント機能の `entityType` プロパティを確認し、一致を検索します。 一致が見つかると、その指定値が返されるか、フォールバック値が返されます。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

次の例では、配列を使用して、すべて同じ値を返すラベルのセットを一覧表示します。 この方法は、各ラベルを個々に一覧表示するよりもはるかに効率的です。 この場合、`entityType` プロパティが "restaurant" または "grocery_store" であれば、色 "red" が返されます。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

次の例では、match 式を使用して "配列内" または "配列に含まれる" タイプのフィルター処理を実行します。この場合、許可される ID のリストに含まれる ID 値を持つデータがフィルター処理されます。 フィルターを含む式を使用するときは、結果がブール値である必要があります。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>coalesce 式

`coalesce` 式では、一連の式を通過し、最初の null 以外の値を取得したら、その値が返されます。 

次の疑似コードでは、`coalesce` 式の構造が定義されます。 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**例**

次の例では、`coalesce` 式を使用して、シンボル レイヤーの `textField` オプションを設定します。 `title` プロパティが機能内にないか、`null` に設定されている場合は、式で `subtitle` プロパティの検索が試行されます。このプロパティがないか `null` である場合は、空の文字列にフォールバックされます。 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>型式

型式では、文字列、数値、ブール値などのさまざまなデータ型をテストおよび変換するためのツールを提供します。

| 式 | 戻り値の型 | 説明 |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | array \| object | リテラル配列またはオブジェクト値が返されます。 配列またはオブジェクトが式として評価されないようにするには、この式を使用します。 この操作は、式で配列またはオブジェクトを返さなければならない場合に必要となります。 |
| `['to-boolean', value]` | ブール値 | 入力値をブール値に変換します。 入力が空の文字列、`0`、`false`、`null`、`NaN` である場合は、結果は `false` になり、それ以外の場合は `true` になります。 |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | 入力値が色に変換されます。 複数の値が指定されている場合は、最初の正常な変換が取得されるまで、それぞれの値が順に評価されます。 いずれの入力も変換できない場合、式はエラーになります。 |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | 可能な場合は、入力値を数値に変換します。 入力が `null` または `false` の場合、結果は 0 になります。 入力が `true` の場合、結果は 1 になります。 入力が文字列である場合は、ECMAScript 言語仕様の [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) 文字列関数を使用して、数値に変換されます。 複数の値が指定されている場合は、最初の正常な変換が取得されるまで、それぞれの値が順に評価されます。 いずれの入力も変換できない場合、式はエラーになります。 |
| `['to-string', value]` | string | 入力値が文字列に変換されます。 入力が `null` の場合、結果は `""` になります。 入力がブール値の場合、結果は `"true"` または `"false"` になります。 入力が数値である場合は、ECMAScript 言語仕様の [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) 数値関数を使用して、文字列に変換されます。 入力が色である場合は、CSS RGBA 色文字列 `"rgba(r,g,b,a)"` に変換されます。 それ以外の場合、入力は ECMAScript 言語仕様の [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) 関数を使用して、文字列に変換されます。 |
| `['typeof', value]` | string | 指定された値の型を示す文字列が返されます。 |

> [!TIP]
> `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` のようなエラー メッセージがブラウザー コンソールに表示される場合は、コード内のいずれかの場所に、最初の値の文字列が欠落した配列を含む式が存在することを意味します。 配列を返す式を設定する場合は、配列を `literal` 式でラップします。 次の例では、`match` 式を使用して、ポイント機能の `entityType` プロパティの値に基づき 2 つのオフセット値から選択することで、シンボル レイヤーのアイコン `offset` オプションを設定します。これは、2 つの数値を含む配列である必要があります。
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>色の式

色の式を使用すると、色の値の作成と操作が容易になります。

| 式 | 戻り値の型 | 説明 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | `0` から `255` の範囲でなければならない *red*、*green*、*blue* コンポーネント、およびアルファ コンポーネント `1` から、色の値を作成します。 いずれかのコンポーネントが範囲外である場合、式はエラーとなります。 |
| `['rgba', number, number, number, number]` | color | `0` から `255` の範囲でなければならない *red*、*green*、*blue* コンポーネント、および `0` から `1` の範囲内のアルファ コンポーネントから、色の値を作成します。 いずれかのコンポーネントが範囲外である場合、式はエラーとなります。 |
| `['to-rgba']` | \[number, number, number, number\] | 入力色の *red*、*green*、*blue*、*アルファ* コンポーネントを含む 4 つの要素の配列が、この順序で返されます。 |

**例**

次の例では、*red* 値 `255`、および `2.5` を `temperature` プロパティの値で乗算して計算された *green* 値と *blue* 値を含む、色の RGB 値が作成されます。 温度の変化に応じて、色は網掛けの異なる *red* に変化します。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>文字列演算子式

文字列演算子式では、連結や大文字と小文字の変換など、文字列の変換操作を実行します。 

| 式 | 戻り値の型 | 説明 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | 複数の文字列を連結します。 各値は文字列である必要があります。 必要に応じて、`to-string` 型式を使用して、他の型の値を文字列に変換します。 |
| `['downcase', string]` | string | 指定された文字列を小文字に変換します。 |
| `['upcase', string]` | string | 指定された文字列を大文字に変換します。 |

**例**

次の例では、ポイント機能の `temperature` プロパティが文字列に変換され、その末尾に "°F" が連結されます。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

上記の式では、次の図に示すように、テキスト "64°F" が上にオーバーレイされたピンをマップ上にレンダリングします。

<center>

![文字列演算子式の例](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>補間式とステップ式

補間式とステップ式は、補間曲線またはステップ関数に沿って値を計算するために使用できます。 これらの式では、入力として数値を返す式が包含されします (たとえば、`['get',  'temperature']`)。 入力値は、入力値と出力値のペア ("分岐点" と呼ばれる) に対して評価され、補間曲線またはステップ関数に最適な値が判断されます。 各分岐点の入力値は数値で、昇順である必要があります。 出力値は、数値、数値の配列、または色である必要があります。

### <a name="interpolate-expression"></a>補間式

`interpolate` 式を使用すると、分岐点値間を補間することで、継続的で滑らかな値のセットを計算できます。 色の値を返す `interpolate` 式により、結果値の選択元となる色のグラデーションが生成されます。

`interpolate` 式で使用できる補間メソッドには、次の 3 種類があります。
 
* `['linear']` - 分岐点のペア間を直線的に補間します。
* `['exponential', base]` - 分岐点間を指数関数的に補間します。 `base` 値により、出力の増加率が制御されます。 この値が大きくなるほど、出力は範囲の上限値に偏るように増加します。 `base` 値が 1 に近づくと、より直線的に増加する出力が生成されます。
* `['cubic-bezier', x1, y1, x2, y2]` - 指定された制御点により定義された [3 次ベジエ曲線](https://developer.mozilla.org/docs/Web/CSS/timing-function)を使用して補間します。

これらの異なる種類の補間の外観の例を次に示します。 

| Linear  | 指数 | 3 次ベジエ |
|---------|-------------|--------------|
| ![線形補間グラフ](media/how-to-expressions/linear-interpolation.png) | ![指数補間グラフ](media/how-to-expressions/exponential-interpolation.png) | ![3 次ベジエ補間グラフ](media/how-to-expressions/bezier-curve-interpolation.png) |

次の疑似コードでは、`interpolate` 式の構造が定義されます。 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**例**

次の例では、`linear interpolate` 式を使用して、ポイント機能の `temperature` プロパティに基づきバブル レイヤーの `color` プロパティが設定されます。 `temperature` 値が 60 未満の場合は "blue"、60 以上 70 未満の場合は "yellow"、70 以上 80 未満の場合は "orange"、80 以上の場合は "red"が返されます。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

次の図では、上記の式に対して色を選択する方法を示します。
 
<center>

![補間式の例](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>ステップ式

`step` 式を使用すると、分岐点により定義された[区分定数関数](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)を評価することで、非連続的な階段状の結果値を計算できます。 

次の疑似コードでは、`step` 式の構造が定義されます。 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

ステップ式では、入力値の直前の分岐点の出力値が返されます。または、入力値が最初の分岐点より小さい場合、最初の入力値が返されます。 

**例**

次の例では、`step` 式を使用して、ポイント機能の `temperature` プロパティに基づきバブル レイヤーの `color` プロパティが設定されます。 `temperature` 値が 60 未満の場合は "blue"、60 以上 70 未満の場合は "yellow"、70 以上 80 未満の場合は "orange"、80 以上の場合は "red"が返されます。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

次の図では、上記の式に対して色を選択する方法を示します。
 
<center>

![ステップ式の例](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>レイヤー固有の式

特定のレイヤーにのみ適用される特殊な式。

### <a name="heat-map-density-expression"></a>ヒート マップ密度式

ヒート マップ密度式では、ヒート マップ レイヤー内の各ピクセルに対してヒート マップ密度値を取得します。この式は `['heatmap-density']` として定義されます。 この値は `0` と `1` の間の数値で、`interpolation` 式または `step` 式と組み合わせて使用して、ヒート マップの色分けに使用される色のグラデーションが定義されます。 この式は、ヒート マップ レイヤーの[色のオプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color)でのみ使用できます。

> [!TIP]
> 補間式のインデックス 0 の色、または段階色の既定の色では、データのない領域の色が定義されます。これは背景色を定義するために使用できます。 多くの場合、この値は透明または半透明の黒に設定することが好まれます。 

**例**

この例では、線形補間式を使用して、ヒート マップをレンダリングするための滑らかな色のグラデーションを作成します。 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

滑らかなグラデーションを使用してヒート マップを色分けするのに加えて、`step` 式を使用して、範囲のセット内で色を指定することができます。 ヒート マップの色分けに `step` 式を使用すると、密度は、等高線またはレーダー スタイル マップのような範囲に視覚的に分割されます。  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

詳細については、[ヒート マップ レイヤーの追加](map-add-heat-map-layer.md)に関するドキュメントを参照してください。

### <a name="line-progress-expression"></a>線形進行状況の式

線形進行状況の式では、線レイヤー内のグラデーション線に沿って進行状況を取得します。この式は `['line-progress']` として定義されます。 この値は 0 から 1 の数値で、`interpolation` 式または `step` 式と組み合わせて使用されます。 この式は、線レイヤーの [strokeGradient オプション]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient)でのみ使用できます。 

> [!NOTE]
> 線レイヤーの `strokeGradient` オプションでは、データ ソースの `lineMetrics` オプションが `true` に設定される必要があります。

**例**

次の例では、`['line-progress']` 式を使用して、線のストロークに色のグラデーションを適用します。

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[実際に操作できる例をご覧ください](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>テキスト フィールドの書式指定式

シンボル レイヤーの `textOptions` プロパティの `textField` オプションでテキスト フィールドの書式指定式を使用すると、混合のテキスト書式設定を提供できます。 この式では、入力文字列と書式設定オプションのセットを指定できます。 この式の各入力文字列には、次のオプションを指定できます。

 * `'font-scale'` - フォント サイズのスケール ファクターを指定します。 指定されている場合、この値は、個々の文字列に対して `textOptions` の `size` プロパティをオーバーライドします。
 * `'text-font'` - この文字列に使用する 1 つまたは複数のフォント ファミリを指定します。 指定されている場合、この値は、個々の文字列に対して `textOptions` の `font` プロパティをオーバーライドします。

次の疑似コードでは、テキスト フィールドの書式指定式の構造が定義されます。 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**例**

次の例では、太字フォントを追加し、機能の `title` プロパティのフォント サイズをスケール アップすることで、テキスト フィールドを書式設定します。 また、この例では、フォント サイズをスケール ダウンして、改行で機能の `subtitle` プロパティを追加します。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

次の図に示すように、このレイヤーではポイント機能がレンダリングされます。
 
<center>

![書式設定されたテキスト フィールドを含むポイント機能の図](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>数値の書式指定式

`number-format` 式は、シンボル レイヤーの `textField` オプションでのみ使用できます。 この式では、指定された数値が書式設定された文字列に変換されます。 この式では、JavaScript の [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 関数がラップされ、次のオプションのセットがサポートされています。

 * `locale` - 指定された言語に適した方法で数値を文字列に変換するには、このオプションを指定します。 このオプションに [BCP 47 言語タグ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)を渡します。
 * `currency` - 数値を、通貨を表す文字列に変換します。 指定できる値は、米ドルを表す "USD"、ユーロを表す "EUR"、中国人民元を表す "CNY" などの [ISO 4217 通貨コード](https://en.wikipedia.org/wiki/ISO_4217)です。
 * `'min-fraction-digits'` - 数値の文字列バージョンに含める小数点以下桁数の最小数が指定されます。
 * `'max-fraction-digits'` - 数値の文字列バージョンに含める小数点以下桁数の最大数が指定されます。

次の疑似コードでは、テキスト フィールドの書式指定式の構造が定義されます。 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**例**

次の例では、`number-format` 式を使用して、シンボル レイヤーの `textField` オプションでポイント機能の `revenue` プロパティをレンダリングする方法を変更して、米ドル値として表示されるようにします。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

次の図に示すように、このレイヤーではポイント機能がレンダリングされます。

<center>

![数値形式の式の例](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>ズーム式

`zoom` 式は、レンダリング時にマップの現在のズーム レベルを取得するために使用され、`['zoom']` として定義されます。 この式では、マップの最小ズーム レベルと最大ズーム レベルの範囲内にある数値が返されます。 この式を使用すると、マップのズーム レベルの変更に応じて、スタイルを動的に変更できます。 `zoom` 式は、`interpolate` 式と `step` 式でのみ使用できます。

**例**

既定では、ヒート マップ レイヤーにレンダリングされるデータ ポイントの半径には、すべてのズーム レベルの固定ピクセル半径が含まれます。 マップがズームされると、データがまとめて集計され、ヒート マップ レイヤーの外観が変化します。 `zoom` 式を使用すると、各データ ポイントによってマップの同じ物理領域がカバーされるように、各ズーム レベルの半径をスケーリングできます。 これにより、ヒート マップ レイヤーの外観は、より静的で一貫性の高いものになります。 マップの各ズーム レベルは、垂直方向および水平方向のピクセル数がすぐ下のズーム レベルの 2 倍になっています。 ズーム レベルごとに半径が 2 倍になるようにスケーリングすると、すべてのズーム レベルで外観に一貫性のあるヒート マップが作成されます。 この操作は、次に示すように、`base 2 exponential interpolation` 式で `zoom` 式を使用して実現できます。 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[実際に操作できる例をご覧ください](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>変数バインド式

変数バインド式では、計算結果を変数に格納して、格納された値を再計算することなく、式内の別の場所で繰り返し参照できるようにします。 これは、多くの計算を含む式で便利な最適化です。

| 式 | 戻り値の型 | 説明 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'let',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 結果を返す子式内に、`var` 式で使用する変数として 1 つまたは複数の値を格納します。 |
| `['var', name: string]` | 任意 | `let` 式を使用して作成された変数を参照します。 |

**例**

この例では、温度の比率を基準として収益を計算する式を使用した後、`case` 式を使用して、この値に対してさまざまなブール演算子を評価します。 `let` 式を使用して、温度の比率を基準とした収益を格納します。これにより、収益を 1 回計算するだけで済み、`var` 式では再計算を行うことなく、必要な回数だけこの変数を参照できます。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>次の手順

式を実装するその他のコード サンプルについては、次の記事を参照してください。

> [!div class="nextstepaction"] 
> [シンボル レイヤーを追加する](map-add-pin.md)

> [!div class="nextstepaction"] 
> [バブル レイヤーを追加する](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](map-add-shape.md)

> [!div class="nextstepaction"] 
> [ヒート マップ レイヤーを追加する](map-add-heat-map-layer.md)

式をサポートする次のレイヤー オプションの詳細を参照してください。

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
