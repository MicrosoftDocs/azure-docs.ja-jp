---
title: Azure Stream Analytics で JSON と AVRO を解析する
description: この記事では、配列、JSON、CSV 形式のデータなどの複雑なデータ型を操作する方法について説明します。
services: stream-analytics
ms.service: stream-analytics
author: jasonwhowell
ms.author: mamccrea
manager: kfile
ms.topic: conceptual
ms.date: 08/03/2018
ms.openlocfilehash: 3d4c1bfa8b376ec50efc7b3cddc8d22a40e70892
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341420"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Azure Stream Analytics で JSON データと Avro データを解析する

Azure Stream Analytics では、CSV、JSON、および Avro データ形式のイベントの処理をサポートしています。 JSON データと Avro データのどちらも、入れ子になったオブジェクト (レコード) や配列などの複合型を含めることができます。

## <a name="array-data-types"></a>配列データ型
配列データ型は、順序が付けられた値のコレクションです。 配列値の一般的な操作の詳細を以下に示します。 これらの例では、入力イベントに、配列データ型である "arrayField" という名前のプロパティが含まれることが想定されています。

これらの例では、関数 [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics)、[GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics)、[GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics)、および [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) 演算子を使用しています。

## <a name="examples"></a>例
指定したインデックス位置にある配列の要素を選択します (配列の最初の要素を選択します)。

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

配列の長さを選択します。

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

配列のすべての要素を個々のイベントとして選択します。 [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) 演算子が [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics) 組み込み関数と組み合わされて、配列のすべての要素を個々のイベントとして抽出します。

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```

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
        "CustomSensor02" : 99
    }
}
```

## <a name="examples"></a>例
入れ子になったフィールドにアクセスするには、ドット表記 (.) を使用します。 たとえば、このクエリは、上記の JSON データの Location プロパティの Lat および Long 座標を選択します。

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long
FROM input
```

プロパティ名が不明の場合は、[GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics) 関数を使用します。 たとえば、サンプル データ ストリームを、各デバイス センサーのしきい値を含む参照データと結合する必要があるとします。

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
FROM input
JOIN thresholds
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

レコード フィールドを個々のイベントに変換するには、[APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) 演算子を [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics) 関数と組み合わせて使用します。 たとえば、サンプル ストリームを、個々のセンサー読み取り値を含むイベントのストリームに変換するには、次のクエリを使用できます。

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

'*' ワイルドカードを使用すると、入れ子になったレコードのすべてのプロパティを選択できます。 次の例を考えてみます。

```SQL
SELECT input.SensorReadings.*
FROM input
```

結果は次のとおりです。

```json
{
    "Temperature" : 80,
    "Humidity" : 70,
    "CustomSensor01" : 5,
    "CustomSensor022" : 99
}
```

## <a name="see-also"></a>関連項目
[Azure Stream Analytics でのデータ型](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)
