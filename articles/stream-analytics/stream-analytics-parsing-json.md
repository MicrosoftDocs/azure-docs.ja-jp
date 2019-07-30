---
title: Azure Stream Analytics で JSON と AVRO を解析する
description: この記事では、配列、JSON、CSV 形式のデータなどの複雑なデータ型を操作する方法について説明します。
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: daf5b97e4ac586f89e5964ee16ee73c86f59b01d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329351"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Azure Stream Analytics で JSON データと Avro データを解析する

Azure Stream Analytics では、CSV、JSON、および Avro データ形式のイベントの処理をサポートしています。 JSON データと Avro データのどちらも、入れ子になったオブジェクト (レコード) や配列などの複合型を含む構造にすることができます。 




## <a name="record-data-types"></a>レコード データ型
レコード データ型は、対応する形式が入力データ ストリームで使用される場合に、JSON 配列と Avro の配列を表すために使用されます。 これらの例は、JSON 形式の入力イベントを読み取るサンプル センサーを示しています。 1 つのイベントの例を以下に示します。

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>既知のスキーマの入れ子のフィールドにアクセスする
クエリから直接、入れ子になったフィールドに簡単にアクセスするには、ドット表記 (.) を使用します。 たとえば、このクエリでは、上記の JSON データの Location プロパティの緯度と経度の座標が選択されます。 次に示したように、ドット表記を使用してさまざまなレベルに移動できます。

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>すべてのプロパティを選択する
'*' ワイルドカードを使用すると、入れ子になったレコードのすべてのプロパティを選択できます。 次の例を考えてみます。

```SQL
SELECT input.Location.*
FROM input
```

結果は次のとおりです。

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>プロパティ名が変数であるときに入れ子のフィールドにアクセスする
プロパティ名が変数の場合は、[GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) 関数を使用します。 

たとえば、サンプル データ ストリームを、各デバイス センサーのしきい値を含む参照データと結合する必要があるとします。 そのような参照データのスニペットを次に示します。

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>レコード フィールドを個々のイベントに変換する
レコード フィールドを個々のイベントに変換するには、[APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 演算子を [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) 関数と組み合わせて使用します。 たとえば前の例で、SensorReading のレコードが複数あった場合、次のクエリを使用して、それらを別々のイベントに抽出することができます。

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>配列データ型

配列データ型は、順序が付けられた値のコレクションです。 配列値の一般的な操作の詳細を以下に示します。 これらの例では、入力イベントに、配列データ型である "arrayField" という名前のプロパティが含まれることが想定されています。

これらの例では、関数 [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics)、[GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics)、[GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)、および [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 演算子を使用しています。

### <a name="working-with-a-specific-array-element"></a>特定の配列要素を操作する
指定したインデックス位置にある配列の要素を選択します (配列の最初の要素を選択します)。

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>配列の長さを選択する

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>配列要素を個々のイベントに変換する
配列のすべての要素を個々のイベントとして選択します。 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 演算子が [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) 組み込み関数と組み合わされて、配列のすべての要素を個々のイベントとして抽出します。

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>関連項目
[Azure Stream Analytics でのデータ型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
