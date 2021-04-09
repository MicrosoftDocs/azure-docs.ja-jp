---
title: Azure Event Grid でのイベントのフィルター処理
description: Azure Event Grid サブスクリプションを作成するときにイベントをフィルター処理する方法について説明します。
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 94445341891149d5d02c7f33caef20bf45123e9b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197777"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Event Grid サブスクリプションでのイベントのフィルター処理を理解します

この記事では、ご利用のエンドポイントに送信されるイベントをフィルター処理するさまざまな方法について説明します。 イベント サブスクリプションを作成するとき、フィルター処理を行うためのオプションとして次の 3 つがあります。

* イベントの種類
* 指定の値で開始または終了する件名
* 高度なフィールドおよび演算子

## <a name="event-type-filtering"></a>イベントの種類のフィルター処理

既定では、イベント ソースに関するすべての[イベントの種類](event-schema.md)がエンドポイントに送信されます。 特定のイベントの種類のみをご利用のエンドポイントに送信するように決めることができます。 たとえば、ご利用のリソースに対する更新は通知されても、削除のような他の操作は通知されないようにすることができます。 その場合は、イベントの種類 `Microsoft.Resources.ResourceWriteSuccess` でフィルター処理します。 イベントの種類を含む配列を指定するか、またはイベント ソースに関するすべてのイベントの種類を取得する `All` を指定します。

イベントの種類でフィルター処理を行うための JSON 構文を次に示します。

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>件名のフィルター処理

件名によるシンプルなフィルター処理の場合は、件名の開始値または終了値を指定します。 たとえば、`.txt` で終わる件名を指定することで、ストレージ アカウントへのテキスト ファイルのアップロードに関連するイベントのみを取得できます。 または、`/blobServices/default/containers/testcontainer` で終わる件名をフィルター処理することで、ストレージ アカウント内の他のコンテナーではなく、そのコンテナーのすべてのイベントを取得できます。

イベントをカスタム トピックに発行する場合は、サブスクライバーがそのイベントに関心があるかどうかを簡単に知ることができるイベントの件名を作成してください。 サブスクライバーは、件名のプロパティを使用してイベントのフィルター処理またはルーティングを行います。 そのイベントが発生したパスを追加することにより、サブスクライバーがそのパスのセグメントでフィルター処理できるように考慮してください。 このパスにより、サブスクライバーはイベントを狭く、または幅広くフィルター処理できます。 `/A/B/C` のように件名に 3 つのセグメント パスを示した場合、サブスクライバーは最初のセグメント `/A` でフィルター処理して幅広い一連のイベントを取得できます。 これらのサブスクライバーは、`/A/B/C` や `/A/D/E` などの件名を持つイベントを取得します。 他のサブスクライバーは、`/A/B` でフィルター処理して、より狭い一連のイベントを取得できます。

件名でフィルター処理を行うための JSON 構文を次に示します。

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>高度なフィルター処理

データ フィールド内の値でフィルター処理して、比較演算子を指定するには、高度なフィルター処理オプションを使用します。 高度なフィルター処理では、次を指定します。

* 演算子の種類 - 比較の種類。
* キー - フィルター処理のために使用するイベント データ内のフィールド。 数値、ブール値、文字列、または配列を指定できます。
* 値 - キーと比較する 1 つ以上の値。

## <a name="key"></a>Key
キーは、フィルター処理のために使用するイベント データ内のフィールドです。 これは、次のいずれかの型になります。

- Number
- Boolean
- String
- Array 型。 この機能を使用するには、`enableAdvancedFilteringOnArrays` プロパティを true に設定する必要があります。 現時点では、Azure portal でこの機能を有効にすることはサポートされていません。 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

**Event Grid スキーマ** 内のイベントの場合、キーには `ID`、`Topic`、`Subject`、`EventType`、`DataVersion`、またはイベント データ (例: `data.key1`) の値を使用します。

**Cloud Events スキーマ** 内のイベントの場合、キーには `eventid`、`source`、`eventtype`、`eventtypeversion`、またはイベント データ (例: `data.key1`) の値を使用します。

**カスタム入力スキーマ** の場合は、イベント データ フィールドを使用します (例: `data.key1`)。 data セクション内のフィールドにアクセスするには、`.` (ドット) 表記を使用します。 たとえば、次のサンプル イベントの `sitename` または `action` にアクセスするには、それぞれ `data.sitename`、`data.appEventTypeDetail.action` を使用します。

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>値
値には、数値、文字列、ブール値、または配列を指定できます。

## <a name="operators"></a>オペレーター

**数値** に対して使用できる演算子は次のとおりです。

## <a name="numberin"></a>NumberIn
**キー** 値が、指定した **フィルター** 値のいずれかである場合、NumberIn 演算子は true に評価されます。 次の例では、これによって `data` セクションの `counter` 属性の値が 5 または 1 であるかどうかが確認されます。 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに `[a, b, c]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
**キー** 値が、指定した **フィルター値の** いずれでも **ない** 場合、NumberNotIn 演算子は true に評価されます。 次の例では、これによって `data` セクションの `counter` 属性の値が 41 と 0 のどちらでもないかどうかが確認されます。 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに `[a, b, c]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
**キー** 値が、指定された **フィルター** 値 **より小さい** 場合、NumberLessThan 演算子は true に評価されます。 次の例では、これによって `data` セクションの `counter` 属性の値が 100 より小さいかどうかが確認されます。 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値に対して確認されます。 キーに `[v1, v2, v3]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
**キー** 値が、指定された **フィルター** 値 **より大きい** 場合、NumberGreaterThan 演算子は true に評価されます。 次の例では、これによって `data` セクションの `counter` 属性の値が 20 より大きいかどうかが確認されます。 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値に対して確認されます。 キーに `[v1, v2, v3]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
**キー** 値が、指定された **フィルター** 値 **以下** の場合、NumberLessThanOrEquals 演算子は true に評価されます。 次の例では、これによって `data` セクションの `counter` 属性の値が 100 以下かどうかが確認されます。 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値に対して確認されます。 キーに `[v1, v2, v3]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
**キー** 値が、指定された **フィルター** 値 **以上** の場合、NumberGreaterThanOrEquals 演算子は true に評価されます。 次の例では、これによって `data` セクションの `counter` 属性の値が 30 以上かどうかが確認されます。 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値に対して確認されます。 キーに `[v1, v2, v3]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NumberInRange
**キー** 値が、指定された **フィルター範囲** のいずれかである場合、NumberInRange 演算子は true に評価されます。 次の例では、これによって `data` セクションの `key1` 属性の値が、3.14159 から 999.95、3000 から 4000 の 2 つの範囲のいずれかであるかどうかが確認されます。 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

`values` プロパティは範囲の配列です。 前の例では、2 つの範囲の配列です。 確認対象の 1 つの範囲がある配列の例を次に示します。 

**1 つの範囲の配列:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに範囲の配列を使用した擬似コードを次に示します。 この擬似コードでは、`a` と `b` は配列内の各範囲の最小値と最大値です。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
**キー** 値が、指定された **フィルター範囲** のいずれでも **ない** 場合、NumberNotInRange 演算子は true に評価されます。 次の例では、これによって `data` セクションの `key1` 属性の値が、3.14159 から 999.95、3000 から 4000 の 2 つの範囲のいずれかであるかどうかが確認されます。 そうである場合、演算子は false を返します。 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
`values` プロパティは範囲の配列です。 前の例では、2 つの範囲の配列です。 確認対象の 1 つの範囲がある配列の例を次に示します。

**1 つの範囲の配列:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに範囲の配列を使用した擬似コードを次に示します。 この擬似コードでは、`a` と `b` は配列内の各範囲の最小値と最大値です。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


**ブール値** に対して使用できる演算子は次のとおりです。 

## <a name="boolequals"></a>BoolEquals です。
**キー** 値が、指定されたブール値 **フィルター** である場合、BoolEquals 演算子は true に評価されます。 次の例では、これによって `data` セクションの `isEnabled` 属性の値が `true` かどうかが確認されます。 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

キーが配列の場合は、配列内のすべての値がフィルター ブール値に対して確認されます。 キーに `[v1, v2, v3]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

**文字列** に対して使用できる演算子は次のとおりです。

## <a name="stringcontains"></a>StringContains
指定された任意の **フィルター** 値 (部分文字列) が **キー** 値に **含まれ** ている場合、**StringContains** は true に評価されます。 次の例では、それによって `data` セクションの `key1` 属性の値に、指定された部分文字列 `microsoft` または `azure` のいずれかが含まれているかどうかが確認されます。 たとえば、`azure data factory` には `azure` が含まれています。 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに `[a,b,c]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
指定された **フィルター** 値が部分文字列として **キー** に **含まれていない** 場合、**StringNotContains** 演算子は true に評価されます。 指定された値のいずれかが部分文字列としてキーに含まれている場合、この演算子は false と評価されます。 次の例では、`data` セクションの `key1` 属性の値に部分文字列として `contoso` と `fabrikam` が含まれていない場合にのみ、この演算子は true を返します。 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに `[a,b,c]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```

## <a name="stringbeginswith"></a>StringBeginsWith
**キー** 値が、指定されたいずれかの **フィルター** 値で **始まる** 場合、**StringBeginsWith** 演算子は true に評価されます。 次の例では、これによって `data` セクションの `key1` 属性の値が `event` または `grid` で始まるかどうかが確認されます。 たとえば、`event hubs` は `event` で始まります。  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに `[a,b,c]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
**キー** 値が、指定されたどの **フィルター** 値でも **始まらない** 場合、**StringNotBeginsWith** 演算子は true に評価されます。 次の例では、これによって `data` セクションの `key1` 属性の値が `event` または `message` で始まらないかどうかが確認されます。

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに `[a,b,c]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
**キー** 値が、指定されたいずれかの **フィルター** 値で **終わる** 場合、**StringEndsWith** 演算子は true に評価されます。 次の例では、これによって `data` セクションの `key1` 属性の値が `jpg`、`jpeg`、または `png` で終わるかどうかが確認されます。 たとえば、`eventgrid.png` は `png` で終わります。


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに `[a,b,c]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
**キー** 値が、指定されたどの **フィルター** 値でも **終わらない** 場合、**StringNotEndsWith** 演算子は true に評価されます。 次の例では、これによって `data` セクションの `key1` 属性の値が `jpg`、`jpeg`、または `png` で終わらないかどうかが確認されます。 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに `[a,b,c]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>StringIn
**StringIn** 演算子を使用すると、**キー** 値が、指定された **フィルター** 値のいずれかと **完全に一致** するかどうかが確認されます。 次の例では、これによって `data` セクションの `key1` 属性の値が `exact`、`string`、または `matches` であるかどうかが確認されます。 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに `[a,b,c]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
**StringNotIn** 演算子を使用すると、**キー** 値が、指定されたどの **フィルター** 値とも **一致しない** かどうかが確認されます。 次の例では、これによって `data` セクションの `key1` 属性の値が `aws` と `bridge` のどちらでもないかどうかが確認されます。 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

キーが配列の場合は、配列内のすべての値がフィルター値の配列に対して確認されます。 キーに `[v1, v2, v3]`、およびフィルターに `[a,b,c]` を使用した擬似コードを次に示します。 データ型がフィルターのデータ型と一致しないキー値はすべて無視されます。

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


すべての文字列の比較では、大文字と小文字は区別されません。

> [!NOTE]
> イベント JSON に高度なフィルター キーが含まれていない場合、フィルターは次の演算子に対して **一致なし** と評価されます: NumberGreaterThan、NumberGreaterThanOrEquals、NumberLessThan、NumberLessThanOrEquals、NumberIn、BoolEquals、StringContains、StringNotContains、StringBeginsWith、StringNotBeginsWith、StringEndsWith、StringNotEndsWith、StringIn。
> 
>フィルターは、次の演算子に対して **一致あり** と評価されます: NumberNotIn、StringNotIn。


## <a name="isnullorundefined"></a>IsNullOrUndefined
IsNullOrUndefined 演算子は、キーの値が NULL または未定義の場合に true に評価されます。 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

次の例では、key1 が指定されていないため、この演算子は true に評価されます。 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

次の例では、key1 に NULL が設定されているため、この演算子は true に評価されます。

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

これらの例で key1 に他の値が含まれている場合、この演算子は false に評価されます。 

## <a name="isnotnull"></a>IsNotNull
IsNotNull 演算子は、キーの値が NULL でも未定義でもない場合に true に評価されます。 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>OR と AND
複数の値を使用した単一のフィルターを指定する場合、**OR** 操作が実行されます。そのため、キー フィールドの値はそれらの値のいずれかである必要があります。 たとえば次のようになります。

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

複数の異なるフィルターを指定する場合、**AND** 操作が実行されます。そのため、各フィルターの条件が満たされる必要があります。 次に例を示します。 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
**CloudEvents スキーマ** 内のイベントの場合、キーには `eventid`、`source`、`eventtype`、`eventtypeversion`、またはイベント データ (例: `data.key1`) の値を使用します。 

[CloudEvents 1.0 の拡張コンテキスト属性](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes)を使用することもできます。 次の例で、`comexampleextension1` と `comexampleothervalue` は拡張コンテキスト属性です。 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

フィルターで拡張コンテキスト属性を使用する例を次に示します。

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>制限事項

高度なフィルター処理には次の制限事項があります。

* イベント グリッド サブスクリプションあたり、フィルター全体で高度なフィルターが 5、フィルター値が 25
* 文字列値あたり 512 文字
* **in** 演算子および **not in** 演算子の値は 5 つ
* **`.` (ドット)** 文字を含むキー。 たとえば、`http://schemas.microsoft.com/claims/authnclassreference` や `john.doe@contoso.com` などです。 現時点では、エスケープ文字を含むキーはサポートされていません。 

複数のフィルターで同じキーを使用できます。





## <a name="next-steps"></a>次のステップ

* PowerShell および Azure CLI を使用したイベント フィルター処理については、[Event Grid でのイベントのフィルター処理](how-to-filter-events.md)に関するページを参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
