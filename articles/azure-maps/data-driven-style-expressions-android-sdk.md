---
title: Android マップ でのデータ ドリブンのスタイルの式 | Microsoft Azure Maps
description: データドリブンのスタイルの式について説明します。 Azure Maps Android SDK でこれらの式を使用し、マップ内のスタイルを調整する方法を確認してください。
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 1babf1feb550109486089c45469ab4ce32f72cb3
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097416"
---
# <a name="data-driven-style-expressions-android-sdk"></a>データ ドリブンのスタイルの式 (Android SDK)

式を使用すると、データ ソース内の各図形で定義されたプロパティを監視するスタイル設定オプションにビジネス ロジックを適用できます。 式は、データ ソースまたはレイヤーのデータをフィルター処理できます。 式は、if ステートメントなどの条件付きロジックで構成されている場合があります。 また、文字列演算子、論理演算子、および算術演算子を使用して、データを操作するために使用することもできます。

データ ドリブンのスタイルを使用すると、スタイル設定関連のビジネス ロジックを実装するために必要なコードの量が削減されます。 レイヤーで使用する場合、式は別のスレッドでレンダリング時に評価されます。 この機能により、ビジネス ロジックを UI スレッドで評価する場合に比べてパフォーマンスが向上します。

Azure Maps Android SDK では、Azure Maps Web SDK とほぼすべて同じスタイルの式がすべてサポートされているため、「[データ ドリブンのスタイルの式 (Web SDK)](data-driven-style-expressions-web-sdk.md)」で概説されているすべての同じ概念を Android アプリに引き継ぐことができます。 Azure Maps Android SDK 内のすべてのスタイル式は、`com.microsoft.azure.maps.mapcontrol.options.Expression` 名前空間で使用できます。 スタイルの式にはさまざまな種類があります。

| 式の種類 | 説明 |
|---------------------|-------------|
| [ブール式](#boolean-expressions) | ブール式により、ブール値の比較を評価するためにブール演算子式のセットが提供されます。 |
| [色の式](#color-expressions) | 色の式を使用すると、色の値の作成と操作が容易になります。 |
| [条件式](#conditional-expressions) | 条件式では、if ステートメントのようなロジック操作が提供されます。 |
| [データ式](#data-expressions) | 機能内のプロパティ データへのアクセスを提供します。 |
| [補間式とステップ式](#interpolate-and-step-expressions) | 補間式とステップ式は、補間曲線またはステップ関数に沿って値を計算するために使用できます。 |
| [JSON ベースの式](#json-based-expressions) | Web SDK 用に作成されたスタイルの生の JSON ベースの式を、Android SDK で簡単に再利用できます。 |  
| [レイヤー固有の式](#layer-specific-expressions) | 1 つのレイヤーにのみ適用できる特殊な式。 |
| [数式](#math-expressions) | 式のフレームワーク内でデータ ドリブンの計算を実行する数学演算子を提供します。 |
| [文字列演算子式](#string-operator-expressions) | 文字列演算子式では、連結や大文字と小文字の変換など、文字列の変換操作を実行します。 |
| [型式](#type-expressions) | 型式では、文字列、数値、ブール値などのさまざまなデータ型をテストおよび変換するためのツールを提供します。 |
| [変数バインド式](#variable-binding-expressions) | 変数バインド式では、計算結果を変数に格納して、格納された値を再計算することなく、式内の別の場所で繰り返し参照することができます。 |
| [ズーム式](#zoom-expression) | レンダリング時に、マップの現在のズーム レベルを取得します。 |

> [!NOTE]
> 式の構文は、Java と Kotlin ではほぼ同じです。 ドキュメントを Kotlin に設定しているが、Java 用のコード ブロックを参照している場合、コードは両方の言語で同じになります。

このドキュメントのこのセクションのすべての例では、次の機能を使用して、これらの式を使用できるさまざまな方法を示します。

```json
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
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

次のコードは、アプリでこの GeoJSON 機能を手動で作成する方法を示しています。

::: zone pivot="programming-language-java-android"

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add properties to the feature.
feature.addNumberProperty("id", 123)
feature.addStringProperty("entityType", "restaurant")
feature.addNumberProperty("revenue", 12345)
feature.addStringProperty("subTitle", "Building 40")
feature.addNumberProperty("temperature", 64)
feature.addStringProperty("title", "Cafeteria")
feature.addStringProperty("zoneColor", "purple")

val abcArray = JsonArray()
abcArray.add("a")
abcArray.add("b")
abcArray.add("c")

feature.addProperty("abcArray", abcArray)

val array1 = JsonArray()
array1.add("a")
array1.add("b")

val array2 = JsonArray()
array1.add("x")
array1.add("y")

val array2d = JsonArray()
array2d.add(array1)
array2d.add(array2)

feature.addProperty("array2d", array2d)

val style = JsonObject()
style.addProperty("fillColor", "red")

feature.addProperty("_style", style)
```

::: zone-end

次のコードは、文字列化されたバージョンの JSON オブジェクトをアプリの GeoJSON 機能に逆シリアル化する方法を示しています。

::: zone pivot="programming-language-java-android"

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

val feature = Feature.fromJson(featureString)
```

::: zone-end

## <a name="json-based-expressions"></a>JSON ベースの式

Azure Maps Web SDK では、JSON 配列を使用して表されるデータ ドリブンのスタイルの式もサポートされています。 これらの同じ式は、Android SDK のネイティブ `Expression` クラスを使用して再作成できます。 また、これらの JSON ベースの式は、`JSON.stringify` などの Web 関数を使用して文字列に変換し、`Expression.raw(String rawExpression)` メソッドに渡すことができます。 たとえば、次の JSON 式を使用します。

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

上記の式の文字列化されたバージョンは `"['get','title']"` であり、次のように Android SDK に読み取ることができます。

::: zone pivot="programming-language-java-android"

```java
Expression exp = Expression.raw("['get','title']")
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val exp = Expression.raw("['get','title']")
```

::: zone-end

このアプローチを使用すると、Azure Maps を使用するモバイルおよび Web アプリ間でスタイル式を簡単に再利用できるようになります。

このビデオでは、Azure Maps でのデータ ドリブンのスタイル処理の概要を示します。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>データ式

データ式では、機能内のプロパティ データへのアクセスを提供します。

| 式 | の戻り値の型 : | 説明 |
|------------|-------------|-------------|
| `accumulated()` | number | これまでに累積したクラスター プロパティの値を取得します。 |
| `at(number | Expression, Expression)` | value | 配列から項目を取得します。 |
| `geometryType()` | string | 機能の geometry 型: Point、MultiPoint、LineString、MultiLineString、Polygon、MultiPolygon を取得します。 |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | value | 指定されたオブジェクトのプロパティからプロパティ値を取得します。 要求されたプロパティがない場合は、null が返されます。 |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | 機能のプロパティに、指定されたプロパティがあるかどうかを判断します。 |
| `id()` | value | 機能の ID がある場合は取得します。 |
| `in(string | number | Expression, Expression)` | boolean | 項目が配列に存在するかどうかを判断します |
| `length(string | Expression)` | number | 文字列または配列の長さを取得します。 |
| `properties()`| value | 機能のプロパティのオブジェクトを取得します。 |

次の Web SDK スタイル式は、Android SDK ではサポートされていません。

- index-of
- slice

**使用例**

機能のプロパティには、`get` 式を使用して式内で直接アクセスできます。 この例では、機能の `zoneColor` 値を使用して、バブル レイヤーの色のプロパティを指定します。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
)
```

::: zone-end

上の例は、すべてのポイント機能に `zoneColor` プロパティがある場合、問題なく動作します。 そうでない場合は、色が "黒色" にフォールバックする可能性があります。 フォールバックの色を変更するには、`switchCase` 式と `has` 式を組み合わせて使用して、このプロパティが存在するかどうかをチェックします。 このプロパティが存在しない場合は、フォールバックの色が返されます。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
)
```

::: zone-end

バブルとシンボルのレイヤーでは、既定でデータ ソース内のすべての図形の座標がレンダリングされます。 この動作により、多角形または線の頂点を強調表示できます。 レイヤーの `filter` オプションを使用すると、ブール式内で `geometryType` 式を使用して、レンダリングする機能の geometry 型を制限できます。 次の例では、`Point` 機能のみがレンダリングされるようにバブル レイヤーを制限できます。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
)
```

::: zone-end

次の例では、`Point` 機能と `MultiPoint` 機能の両方をレンダリングできます。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
)
```

::: zone-end

同様に、線レイヤーでは多角形のアウトラインがレンダリングされます。 線レイヤーでこの動作を無効にするには、`LineString` 機能と `MultiLineString` 機能のみを許可するフィルターを追加します。  

データ式を使用する方法のその他の例は次のとおりです。

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>数式

数式では、式のフレームワーク内でデータ ドリブンの計算を実行する数学演算子を提供します。

| 式 | の戻り値の型 : | 説明 |
|------------|-------------|-------------|
| `abs(number | Expression)` | number | 指定された数値の絶対値が計算されます。 |
| `acos(number | Expression)` | number | 指定された数値のアークコサインが計算されます。 |
| `asin(number | Expression)` | number | 指定された数値のアークサインが計算されます。 |
| `atan(number | Expression)` | number | 指定された数値のアークタンジェントが計算されます。 |
| `ceil(number | Expression)` | number | 数値が次の整数に切り上げられます。 |
| `cos(number | Expression)` | number | 指定された数値のコサインが計算されます。 |
| `division(number, number)` \| `division(Expression, Expression)` | number | 1 番目の数値が 2 番目の数値で除算されます。 Web SDK の同等の式: `/` |
| `e()` | number | 数理定数 `e` が返されます。 |
| `floor(number | Expression)` | number | この数値が前の整数に切り下げられます。 |
| `log10(number | Expression)` | number | 指定された数値の 10 を底とする対数が計算されます。 |
| `log2(number | Expression)` | number | 指定された数値の 2 を底とする対数が計算されます。 |
| `ln(number | Expression)` | number | 指定された数値の自然対数が計算されます。 |
| `ln2()` | number | 数理定数 `ln(2)` が返されます。 |
| `max(numbers... | expressions...)` | number | 指定された数値セット内の最大数が計算されます。 |
| `min(numbers... | expressions...)` | number | 指定された数値セット内の最小数が計算されます。 |
| `mod(number, number)` \| `mod(Expression, Expression)` | number | 1 番目の数値を 2 番目の数値で除算した際の剰余が計算されます。 Web SDK の同等の式: `%` |
| `pi()` | number | 数理定数 `PI` が返されます。 |
| `pow(number, number)` \| `pow(Expression, Expression)` | number | 1 番目の値を 2 番目の数値で累乗した値が計算されます。 |
| `product(numbers... | expressions...)` | number | 指定された数値が乗算されます。 Web SDK の同等の式: `*` |
| `round(number | Expression)` | number | 数値が最も近い整数に丸められます。 端数を含む値は、ゼロから離れる方向に丸められます。 たとえば、`round(-1.5)` は `-2` に評価されます。 |
| `sin(number | Expression)` | number | 指定された数値のサインが計算されます。 |
| `sqrt(number | Expression)` | number | 指定された数値の平方根が計算されます。 |
| `subtract(number | Expression` | number | 0 から、指定された数値が減算されます。 |
| `subtract(number | Expression, number | Expression)` | number | 1 番目の数値から 2 番目の数値が減算されます。 |
| `sum(numbers... | expressions...)` | number | 指定された数値の合計が計算されます。 |
| `tan(number | Expression)` | number | 指定された数値のタンジェントが計算されます。 |

## <a name="boolean-expressions"></a>ブール式

ブール式により、ブール値の比較を評価するためにブール演算子式のセットが提供されます。

値を比較する際に、比較は厳密に型指定されます。 異なる型の値は、常に等しくないと見なされます。 解析時に型が異なるとがわかった場合は、無効と見なされ、解析エラーが生成されます。

| 式 | の戻り値の型 : | 説明 |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | すべての入力が `true` の場合は `true` が返され、それ以外の場合は `false` が返されます。 |
| `any(Expression...)` | boolean | いずれかの入力が `true` の場合は `true` が返され、それ以外の場合は `false` が返されます。 |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 入力値が等しい場合は `true` が返され、それ以外の場合は `false` が返されます。 引数は、両方とも文字列、または両方とも数値である必要があります。 |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 最初の入力が厳密に 2 番目の入力より大きい場合は `true` が返され、それ以外の場合は `false` が返されます。 引数は、両方とも文字列、または両方とも数値である必要があります。 |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 最初の入力が 2 番目の入力以上の場合は `true` が返され、それ以外の場合は `false` が返されます。 引数は、両方とも文字列、または両方とも数値である必要があります。 |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 最初の入力が厳密に 2 番目の入力未満の場合は `true` が返され、それ以外の場合は `false` が返されます。 引数は、両方とも文字列、または両方とも数値である必要があります。 |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 最初の入力が 2 番目の入力以下の場合は `true` が返され、それ以外の場合は `false` が返されます。 引数は、両方とも文字列、または両方とも数値である必要があります。 |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | 入力値が等しくない場合は `true` が返され、それ以外の場合は `false` が返されます。 |
| `not(Expression | boolean)` | boolean | 論理否定。 入力が `false` の場合は `true` が返され、入力が `true` の場合は `false` が返されます。 |

## <a name="conditional-expressions"></a>条件式

条件式では、if ステートメントのようなロジック操作が提供されます。

次の式では、入力データに対して条件付きロジック操作を実行します。 たとえば、`switchCase` 式では "if/then/else" ロジックを提供する一方、`match` 式は "switch ステートメント" に似ています。

### <a name="switch-case-expression"></a>Switch case 式

`switchCase` 式は、"if/then/else" ロジックを提供する一種の条件式です。 この種類の式は、ブール条件のリストを通過します。 True に評価される最初のブール条件の出力値を返します。

次の疑似コードでは、`switchCase` 式の構造が定義されます。

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**例**

次の例では、さまざまなブール条件を通過し、`true` に評価されるものが見つかったら、その関連値が返されます。 `true` に評価されるブール条件がない場合は、フォールバック値が返されます。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
)
```

::: zone-end

### <a name="match-expression"></a>match 式

`match` 式は、ロジックなどの switch ステートメントを提供する一種の条件式です。 入力には、文字列または数値を返す `get( "entityType")` などの任意の式を使用できます。 各分岐点には、1 つのリテラル値、または値がすべて文字列またはすべて数値であるリテラル値の配列が必要です。 配列内のいずれかの値が一致する場合、入力は一致します。 各分岐点ラベルは一意である必要があります。 入力の型がラベルの型と一致しない場合、結果は既定のフォールバック値になります。

次の疑似コードでは、`match` 式の構造が定義されます。

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**使用例**

次の例では、バブル レイヤー内のポイント機能の `entityType` プロパティを確認し、一致を検索します。 一致が見つかると、その指定値が返されるか、フォールバック値が返されます。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

次の例では、配列を使用して、すべて同じ値を返すラベルのセットを一覧表示します。 この方法は、各ラベルを個々に一覧表示するよりもはるかに効率的です。 この場合、`entityType` プロパティが "restaurant" または "grocery_store" であれば、色 "red" が返されます。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(arrayOf("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

### <a name="coalesce-expression"></a>coalesce 式

`coalesce` 式では、一連の式を通過し、最初の null 以外の値を取得したら、その値が返されます。

次の疑似コードでは、`coalesce` 式の構造が定義されます。

```java
coalesce(Expression... input)
```

**例**

次の例では、`coalesce` 式を使用して、シンボル レイヤーの `textField` オプションを設定します。 `title` プロパティが機能内にないか、`null` に設定されている場合は、式で `subTitle` プロパティの検索が試行されます。このプロパティがないか `null` である場合は、空の文字列にフォールバックされます。

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
)
```

::: zone-end

## <a name="type-expressions"></a>型式

型式では、文字列、数値、ブール値などのさまざまなデータ型をテストおよび変換するためのツールを提供します。

| 式 | の戻り値の型 : | 説明 |
|------------|-------------|-------------|
| `array(Expression)` | Object[] | 入力が配列であることをアサートします。 |
| `bool(Expression)` | boolean | 入力値がブール値であることをアサートします。 |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | collator | ロケールに依存する比較操作で使用する collator が返されます。 大文字と小文字の区別と分音記号の区別のオプションは、既定で false に設定されています。 locale 引数は、使用するロケールの IETF 言語タグを指定します。 値が指定されない場合は、既定のロケールが使用されます。 要求されたロケールが使用できない場合、collator はシステム定義のフォールバック ロケールを使用します。 解決済みのロケールを使用して、ロケール フォールバック動作の結果をテストします。  |
| `literal(boolean \| number \| string \| Object \| Object[])` | boolean \| number \| string \| Object \| Object[] | リテラル配列またはオブジェクト値が返されます。 配列またはオブジェクトが式として評価されないようにするには、この式を使用します。 この操作は、式で配列またはオブジェクトを返さなければならない場合に必要となります。 |
| `number(Expression)` | number | 入力値が数値であることをアサートします。 |
| `object(Expression)` | Object | 入力値がオブジェクトであることをアサートします。 |
| `string(Expression)` | string | 入力値が文字列であることをアサートします。 |
| `toArray(Expression)` | Object[] | 式を JSON オブジェクト配列に変換します。 |
| `toBool(Expression)` | boolean | 入力値をブール値に変換します。 |
| `toNumber(Expression)` | number | 可能な場合は、入力値を数値に変換します。 |
| `toString(Expression)` | string | 入力値が文字列に変換されます。 |
| `typeoOf(Expression)` | string | 指定された値の型を示す文字列が返されます。 |

## <a name="color-expressions"></a>色の式

色の式を使用すると、色の値の作成と操作が容易になります。

| 式 | の戻り値の型 : | 説明 |
|------------|-------------|-------------|
| `color(int)` | color | 色の整数値を色の式に変換します。 |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | `0` から `255` の範囲でなければならない *red*、*green*、*blue* コンポーネント、およびアルファ コンポーネント `1` から、色の値を作成します。 いずれかのコンポーネントが範囲外である場合、式はエラーとなります。 |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | `0` から `255` の範囲でなければならない *red*、*green*、*blue* コンポーネント、および `0` から `1` の範囲内のアルファ コンポーネントから、色の値を作成します。 いずれかのコンポーネントが範囲外である場合、式はエラーとなります。 |
| `toColor(Expression)` | color  | 入力値が色に変換されます。 |
| `toRgba(Expression)` | color | 入力色の red、green、blue、アルファ コンポーネントを含む 4 つの要素の配列が、この順序で返されます。 |

**例**

次の例では、*red* 値 `255`、および `2.5` を `temperature` プロパティの値で乗算して計算された *green* 値と *blue* 値を含む、色の RGB 値が作成されます。 温度の変化に応じて、色はさまざまな色調の *red* に変化します。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
)
```

::: zone-end

すべての色パラメーターが数値の場合、`literal` 式を使用してラップする必要はありません。 例:

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
)
```

::: zone-end

> [!TIP]
> 文字列の色の値は、`android.graphics.Color.parseColor` メソッドを使用して色に変換できます。 次の例では、16 進数の色の文字列を、レイヤーで使用できる色の式に変換します。
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>文字列演算子式

文字列演算子式では、連結や大文字と小文字の変換など、文字列の変換操作を実行します。

| 式 | の戻り値の型 : | 説明 |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | string | 複数の文字列を連結します。 各値は文字列である必要があります。 必要に応じて、`toString` 型式を使用して、他の型の値を文字列に変換します。 |
| `downcase(string)` \| `downcase(Expression)` | string | 指定された文字列を小文字に変換します。 |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | 入力文字列で現在のフォント スタックでサポートされている文字セットを使用するかどうかを判断します。 例: `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | string | 指定された collator によって使用されているロケールの IETF 言語タグを返します。 これを使用して、既定のシステム ロケールを確認したり、要求されたロケールが正常に読み込まれたかどうかを確認したりできます。 |
| `upcase(string)` \| `upcase(Expression)` | string | 指定された文字列を大文字に変換します。 |

**例**

次の例では、ポイント機能の `temperature` プロパティが文字列に変換され、その末尾に "°F" が連結されます。

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
)
```

::: zone-end

上記の式では、次の図に示すように、テキスト "64°F" が上にオーバーレイされたピンをマップ上にレンダリングします。

![文字列演算子式の例](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>補間式とステップ式

補間式とステップ式は、補間曲線またはステップ関数に沿って値を計算するために使用できます。 これらの式では、入力として数値を返す式が包含されします (たとえば、`get("temperature")`)。 入力値は、入力値と出力値のペアに対して評価され、補間曲線またはステップ関数に最適な値が判断されます。 出力値は "分岐点" と呼ばれます。 各分岐点の入力値は数値で、昇順である必要があります。 出力値は、数値、数値の配列、または色である必要があります。

### <a name="interpolate-expression"></a>補間式

`interpolate` 式を使用すると、分岐点値間を補間することで、継続的で滑らかな値のセットを計算できます。 色の値を返す `interpolate` 式により、結果値の選択元となる色のグラデーションが生成されます。 `interpolate` 式の書式は次のとおりです。

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

`interpolate` 式で使用できる補間メソッドには、次の 3 種類があります。

| 名前 | [説明] |
|------|-------------|
| `linear()` | 分岐点のペア間を直線的に補間します。  |
| `exponential(number)` \| `exponential(Expression)` | 分岐点間を指数関数的に補間します。 「基数」が指定されており、出力の増加率を制御します。 この値が大きくなるほど、出力は範囲の上限値に偏るように増加します。 「基数」の値が 1 に近づくと、より直線的に増加する出力が生成されます。|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | 指定された制御点により定義された [3 次ベジエ曲線](https://developer.mozilla.org/docs/Web/CSS/timing-function)を使用して補間します。 |

`stop` 式の書式は `stop(stop, value)` です。

これらの異なる種類の補間の外観の例を次に示します。

| Linear  | 指数 | 3 次ベジエ |
|---------|-------------|--------------|
| ![線形補間グラフ](media/how-to-expressions/linear-interpolation.png) | ![指数補間グラフ](media/how-to-expressions/exponential-interpolation.png) | ![3 次ベジエ補間グラフ](media/how-to-expressions/bezier-curve-interpolation.png) |

**例**

次の例では、`linear interpolate` 式を使用して、ポイント機能の `temperature` プロパティに基づきバブル レイヤーの `bubbleColor` プロパティが設定されます。 `temperature` 値が 60 未満の場合、"blue" が返されます。 60 以上 70 未満の場合は、yellow が返されます。 70 以上 80 未満の場合は、"orange" (`#FFA500`) が返されます。 80 以上の場合は、"red" が返されます。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

次の図では、上記の式に対して色を選択する方法を示します。

![補間式の例](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>ステップ式

`step` 式を使用すると、分岐点により定義された[区分定数関数](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)を評価することで、非連続的な階段状の結果値を計算できます。

`interpolate` 式の書式は次のとおりです。

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

ステップ式では、入力値の直前の分岐点の出力値が返されます。または、入力値が最初の分岐点より小さい場合、最初の入力値が返されます。

**例**

次の例では、`step` 式を使用して、ポイント機能の `temperature` プロパティに基づきバブル レイヤーの `bubbleColor` プロパティが設定されます。 `temperature` 値が 60 未満の場合、"blue" が返されます。 60 以上 70 未満の場合は、"yellow" が返されます。 70 以上 80 未満の場合は、"orange" が返されます。 80 以上の場合は、"red" が返されます。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

次の図では、上記の式に対して色を選択する方法を示します。

![ステップ式の例](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>レイヤー固有の式

特定のレイヤーにのみ適用される特殊な式。

### <a name="heat-map-density-expression"></a>ヒート マップ密度式

ヒート マップ密度式では、ヒート マップ レイヤー内の各ピクセルに対してヒート マップ密度値を取得します。この式は `heatmapDensity` として定義されます。 この値は `0` から `1` までの数値です。 これは `interpolation` 式または `step` 式と組み合わせて使用して、ヒート マップの色分けに使用される色のグラデーションが定義されます。 この式は、ヒート マップ レイヤーの `heatmapColor` オプションでのみ使用できます。

> [!TIP]
> 補間式内のインデックス 0 の色、または段階色の既定の色では、データのない領域の色が定義されます。 インデックス 0 の色は背景色を定義するために使用できます。 多くの場合、この値は透明または半透明の黒に設定することが好まれます。

**例**

この例では、線形補間式を使用して、ヒート マップをレンダリングするための滑らかな色のグラデーションを作成します。

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
)
```

::: zone-end

滑らかなグラデーションを使用してヒート マップを色分けするのに加えて、`step` 式を使用して、範囲のセット内で色を指定することができます。 ヒート マップの色分けに `step` 式を使用すると、密度は、等高線またはレーダー スタイル マップのような範囲に視覚的に分割されます。

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

詳細については、[ヒート マップ レイヤーの追加](map-add-heat-map-layer-android.md)に関するドキュメントを参照してください。

### <a name="line-progress-expression"></a>線形進行状況の式

線形進行状況の式では、線レイヤー内のグラデーション線に沿って進行状況を取得します。この式は `lineProgress()` として定義されます。 この値は 0 から 1 までの数値です。 これは `interpolation` 式または `step` 式と組み合わせて使用されます。 この式は、線レイヤーの `strokeGradient` オプションでのみ使用できます。

> [!NOTE]
> 線レイヤーの `strokeGradient` オプションでは、データ ソースの `lineMetrics` オプションが `true` に設定される必要があります。

**例**

この例では、`lineProgress()` 式を使用して、線のストロークに色のグラデーションを適用します。

::: zone pivot="programming-language-java-android"

```java
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

[実際に操作できる例をご覧ください](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>テキスト フィールドの書式指定式

`format` 式は、シンボル レイヤーの `textField` オプションで、混合のテキスト書式設定を提供するために使用できます。 この式には、テキスト フィールドに追加する文字列と `formatOptions` のセットを指定する 1 つまたは複数の `formatEntry` 式があります。

| 式 | 説明 |
|------------|-------------|
| `format(Expression...)` | 混合形式のテキスト フィールドのエントリで使用する注釈を含む、書式設定されたテキストを返します。 |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | `format` 式で使用するための書式設定された文字列エントリを返します。 |

使用できる書式設定オプションは次のとおりです。

| 式 | 説明 |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | フォント サイズのスケール ファクターを指定します。 指定されている場合、この値は、個々の文字列の `textSize` プロパティをオーバーライドします。 |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | レンダリング時にテキストに適用する色を指定します。 |

**例**

次の例では、太字フォントを追加し、機能の `title` プロパティのフォント サイズをスケール アップすることで、テキスト フィールドを書式設定します。 また、この例では、フォント サイズをスケール ダウンして、改行で機能の `subTitle` プロパティを追加します。

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(arrayOf("StandardFont-Bold")),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
)
```

::: zone-end

次の図に示すように、このレイヤーではポイント機能がレンダリングされます。

![書式設定されたテキスト フィールドを含むポイント機能の図](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>ズーム式

`zoom` 式は、レンダリング時にマップの現在のズーム レベルを取得するために使用され、`zoom()` として定義されます。 この式では、マップの最小ズーム レベルと最大ズーム レベルの範囲内にある数値が返されます。 Web および Android 用の Azure Maps 対話型コントロールでは、25 のズーム レベル (0 から 24 までの番号が付けられている) がサポートされます。 `zoom` 式を使用すると、マップのズーム レベルの変更に応じて、スタイルを動的に変更できます。 `zoom` 式は、`interpolate` 式と `step` 式でのみ使用できます。

**例**

既定では、ヒート マップ レイヤーにレンダリングされるデータ ポイントの半径には、すべてのズーム レベルの固定ピクセル半径が含まれます。 マップがズームされると、データがまとめて集計され、ヒート マップ レイヤーの外観が変化します。 `zoom` 式を使用すると、各データ ポイントによってマップの同じ物理領域がカバーされるように、各ズーム レベルの半径をスケーリングできます。 これにより、ヒート マップ レイヤーの外観は、より静的で一貫性の高いものになります。 マップの各ズーム レベルは、垂直方向および水平方向のピクセル数がすぐ下のズーム レベルの 2 倍になっています。 ズーム レベルごとに半径が 2 倍になるようにスケーリングすると、すべてのズーム レベルで外観に一貫性のあるヒート マップが作成されます。 これを実現するには、次のように、`zoom` 式と `base 2 exponential interpolation` 式を使用し、最小ズーム レベルに設定されたピクセル半径と最大ズーム レベルにスケーリングされた半径を `2 * Math.pow(2, minZoom - maxZoom)` として計算します。

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
)
```

::: zone-end

## <a name="variable-binding-expressions"></a>変数バインド式

変数バインド式は、計算結果を変数に格納します。 そのため、計算結果は式内の別の場所で繰り返し参照することができます。 これは、多くの計算を含む式で便利な最適化です。

| 式 | の戻り値の型 : | 説明 |
|--------------|---------------|--------------|
| `let(Expression... input)` | | 結果を返す子式内に、`var` 式で使用する変数として 1 つまたは複数の値を格納します。 |
| `var(Expression expression)` \| `var(string variableName)` | Object | `let` 式を使用して作成された変数を参照します。 |

**例**

この例では、温度の比率を基準として収益を計算する式を使用した後、`case` 式を使用して、この値に対してさまざまなブール演算子を評価します。 `let` 式は、温度を基準とした収益の比率を格納するために使用され、その結果、1 回だけ計算すればよくなります。 `var` 式は、必要に応じてこの変数を再計算することなく何度も参照できます。

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
)
```

::: zone-end

## <a name="next-steps"></a>次のステップ

式をサポートする次のレイヤーの詳細を参照してください。

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [ヒート マップを追加する](map-add-heat-map-layer-android.md)
