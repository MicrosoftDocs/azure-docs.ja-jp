---
title: Azure Stream Analytics で JSON と AVRO を解析する
description: この記事では、配列、JSON、CSV 形式のデータなどの複雑なデータ型を操作する方法について説明します。
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: conceptual
ms.date: 01/29/2020
ms.custom: devx-track-js
ms.openlocfilehash: f6cb131fb3ff3cab4122aac5e1c6960dee4f8421
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012242"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Azure Stream Analytics で JSON データと Avro データを解析する

Azure Stream Analytics では、CSV、JSON、および Avro データ形式のイベントの処理をサポートしています。 JSON データと Avro データのどちらも、入れ子になったオブジェクト (レコード) や配列などの複合型を含む構造にすることができます。 

>[!NOTE]
>Event Hub Capture によって作成される AVRO ファイルは、*カスタム逆シリアライザー* 機能を使用する必要がある特定の形式を使用します。 詳細については、[.NET カスタム逆シリアライザーを使用して任意の形式の入力を読み取る](./custom-deserializer-examples.md)を参照してください。
>
>Stream Analytics AVRO 逆シリアル化では、マップの種類はサポートされません。 EventHub キャプチャでマップが使用されるため、Stream Analytics は EventHub キャプチャ BLOB を読み取ることができません。


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
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

結果は次のとおりです。

|DeviceID|Lat|Long|気温|Version|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>すべてのプロパティを選択する
'*' ワイルドカードを使用すると、入れ子になったレコードのすべてのプロパティを選択できます。 次の例を確認してください。

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

結果は次のとおりです。

|DeviceID|Lat|Long|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>プロパティ名が変数であるときに入れ子のフィールドにアクセスする

プロパティ名が変数の場合は、[GetRecordPropertyValue](/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) 関数を使用します。 これにより、プロパティ名をハードコーディングすることなく、動的なクエリを作成できます。

たとえば、サンプル データ ストリームを、各デバイス センサーのしきい値を含む **参照データと結合する** 必要があるとします。 そのような参照データのスニペットを次に示します。

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

ここでの目的は、記事の先頭にあるサンプルデータセットをその参照データに結合し、各センサーメジャーに対してしきい値を上回る 1 つのイベントを出力することです。 これは、結合により、複数のセンサーがそれぞれのしきい値を超えた場合に、上記の 1 つのイベントによって複数の出力イベントが生成される可能性があることを意味します。 結合せずに同様の結果を得るには、以下のセクションを参照してください。

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** により、 *SensorReadings* のプロパティを選択します。この名前は参照データから取得したプロパティ名と一致します。 次に、*SensorReadings* の関連する値が抽出されます。

結果は次のとおりです。

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|湿度|注意：しきい値を超えたセンサー|

### <a name="convert-record-fields-into-separate-events"></a>レコード フィールドを個々のイベントに変換する

レコード フィールドを個々のイベントに変換するには、[APPLY](/stream-analytics-query/apply-azure-stream-analytics) 演算子を [GetRecordProperties](/stream-analytics-query/getrecordproperties-azure-stream-analytics) 関数と組み合わせて使用します。

元のサンプルデータでは、次のクエリを使用して、さまざまなイベントにプロパティを抽出できます。

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

結果は次のとおりです。

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|気温|80|
|12345|湿度|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[object Object]|

[WITH](/stream-analytics-query/with-azure-stream-analytics)を使用すると、これらのイベントを異なる宛先にルーティングできます。

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>SQL 参照データの JSON レコードを解析する
ジョブで参照データとして Azure SQL Database を使用する場合、JSON 形式のデータを含む列を持つことができます。 次に例を示します。

|DeviceID|Data|
|-|-|
|12345|{"key" : "value1"}|
|54321|{"key" : "value2"}|

単純な JavaScript ユーザー定義関数を記述することで、*Data* 列の JSON レコードを解析できます。

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

次に、以下に示すように Stream Analytics クエリのステップを作成して、JSON レコードのフィールドにアクセスできます。

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>配列データ型

配列データ型は、順序が付けられた値のコレクションです。 配列値の一般的な操作の詳細を以下に示します。 これらの例では、関数 [GetArrayElement](/stream-analytics-query/getarrayelement-azure-stream-analytics)、[GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics)、[GetArrayLength](/stream-analytics-query/getarraylength-azure-stream-analytics)、および [APPLY](/stream-analytics-query/apply-azure-stream-analytics) 演算子を使用しています。

1 つのイベントの例を以下に示します。 `CustomSensor03` と `SensorMetadata` のどちらも **配列** 型です。

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>特定の配列要素を操作する

指定したインデックス位置にある配列の要素を選択します (配列の最初の要素を選択します)。

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

結果は次のとおりです。

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>配列の長さを選択する

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

結果は次のとおりです。

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>配列要素を個々のイベントに変換する

配列のすべての要素を個々のイベントとして選択します。 [APPLY](/stream-analytics-query/apply-azure-stream-analytics) 演算子が [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics) 組み込み関数と組み合わされて、配列のすべての要素を個々のイベントとして抽出します。

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

結果は次のとおりです。

|deviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
結果は次のとおりです。

|deviceId|smKey|smValue|
|-|-|-|
|12345|Manufacturer|ABC|
|12345|Version|1.2.45|

抽出されたフィールドを列に表示する必要がある場合は、[JOIN](/stream-analytics-query/join-azure-stream-analytics) 操作に加えて、[WITH](/stream-analytics-query/with-azure-stream-analytics)構文を使用してデータセットをピボットすることができます。 この結合では、重複を防ぐ[時間境界](/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) 条件が必要になります。

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

結果は次のとおりです。

|deviceId|Lat|Long|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>参照
[Azure Stream Analytics でのデータ型](/stream-analytics-query/data-types-azure-stream-analytics)