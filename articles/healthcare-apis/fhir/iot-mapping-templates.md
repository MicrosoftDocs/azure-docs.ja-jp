---
title: '概念: Azure API for FHIR の Azure IoT Connector for FHIR (プレビュー) 機能のマッピング テンプレート'
description: Azure IoT Connector for FHIR (プレビュー) で 2 種類のマッピング テンプレートを作成する方法について説明します。 デバイス マッピング テンプレートでは、デバイス データを正規化スキーマに変換します。 FHIR マッピング テンプレートでは、正規化されたメッセージを FHIR ベースの Observation リソースに変換します。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 581afbb5cec166f0ef5048b6ecc89f8ff95fd794
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019421"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Azure IoT Connector for FHIR (プレビュー) のマッピング テンプレート
この記事では、マッピング テンプレートを使用して、Azure IoT Connector for FHIR&#174; (高速ヘルスケア相互運用性リソース)* を構成する方法について詳しく説明します。

Azure IoT Connector for FHIR には、JSON ベースのマッピング テンプレートが 2 種類必要です。 1 種類目の **デバイス マッピング** は、`devicedata` Azure Event Hub エンドポイントに送信されるデバイス ペイロードのマッピングを担います。 型、デバイス識別子、測定日時、測定値を抽出します。 2 種類目の **FHIR マッピング** は、FHIR リソースのマッピングを制御します。 これにより、観察期間の長さ、値の格納に使用される FHIR データ型、用語コードを構成できます。 

マッピング テンプレートは、その種類に基づいた JSON ドキュメント内に構成されます。 その後これらの JSON ドキュメントは、Azure portal を通じて Azure IoT Connector for FHIR に追加されます。 デバイス マッピング ドキュメントは **[Configure Device mapping]\(デバイス マッピングの構成\)** ページで、FHIR マッピング ドキュメントは **[Configure FHIR mapping]\(FHIR マッピングの構成\)** ページで追加されます。

> [!NOTE]
> マッピング テンプレートは、基になる BLOB ストレージに格納され、コンピューティングの実行ごとに BLOB から読み込まれます。 更新された場合は、すぐに有効になります。 

## <a name="device-mapping"></a>デバイス マッピング
デバイス マッピングには、さらなる評価のためにデバイス コンテンツを共通の形式に抽出するマッピング機能が用意されています。 受信した各メッセージが、すべてのテンプレートに対して評価されます。 この方法によって、1 つの受信メッセージを複数の送信メッセージに投影し、後で FHIR の異なる観察にマップすることができます。 結果は、テンプレートによって解析された 1 つまたは複数の値を表す、正規化されたデータ オブジェクトです。 正規化されたデータ モデルには、検出して抽出する必要がある、いくつかの必須プロパティがあります。

| プロパティ | 説明 |
| - | - |
|**Type**|測定を分類するための名前または種類。 この値は、必要な FHIR マッピング テンプレートにバインドするために使用されます。  複数のテンプレートを同じ種類に出力して、複数のデバイス間で異なる表現を 1 つの共通出力にマップできます。|
|**OccurenceTimeUtc**|測定が発生した時刻。|
|**DeviceId**|デバイスの識別子。 この値は、送信先の FHIR サーバーに存在するデバイス リソースの識別子と一致している必要があります。|
 |**Properties**|作成された Observation リソースに値を保存できるように、少なくとも 1 つのプロパティを抽出します。  プロパティは、正規化中に抽出されたキーと値のペアのコレクションです。|

次に、正規化時の動作の概念を表す例を示します。

![正規化の例](media/concepts-iot-mapping-templates/normalization-example.png)

コンテンツ ペイロード自体は、次の 3 つの部分で構成される Azure Event Hub メッセージです: Body、Properties、SystemProperties。 `Body` は、UTF-8 でエンコードされた文字列を表すバイト配列です。 テンプレートの評価中に、バイト配列は自動的に文字列値に変換されます。 `Properties` は、メッセージの作成者が使用するキー値のコレクションです。 `SystemProperties` も、Azure Event Hub フレームワークによって予約され、エントリが自動的に入力されるキー値コレクションです。

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>JSON パスを使用したマッピング
現在サポートされている 3 つのデバイス コンテンツ テンプレートの種類は、必要なテンプレートと抽出された値の両方に一致する JSON パスに依存しています。 JSON パスの詳細については、[こちら](https://goessner.net/articles/JsonPath/)を参照してください。 3 種類のテンプレートすべてで、JSON パス式を解決するために、[JSON .NET 実装](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm)が使用されます。

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate では、JSON パスを使用して、イベント ハブ メッセージの値の照合と抽出を行うことができます。

| プロパティ | 説明 |<div style="width:150px">例</div>
| --- | --- | --- 
|**TypeName**|テンプレートに一致する測定値に関連付ける型。|`heartrate`
|**TypeMatchExpression**|イベント ハブのペイロードに対して評価される JSON パス式。 一致する JToken が見つかった場合、テンプレートは一致と見なされます。 後続のすべての式は、ここで一致した抽出済みの JToken に対して評価されます。|`$..[?(@heartRate)]`
|**TimestampExpression**|測定の OccurenceTimeUtc のタイムスタンプ値を抽出する JSON パス式。|`$.endDate`
|**DeviceIdExpression**|デバイス識別子を抽出する JSON パス式。|`$.deviceId`
|**PatientIdExpression**|*省略可能*:患者識別子を抽出する JSON パス式。|`$.patientId`
|**EncounterIdExpression**|*省略可能*:面接識別子を抽出する JSON パス式。|`$.encounterId`
|**Values[].ValueName**|後続の式によって抽出された値に関連付ける名前。 FHIR マッピング テンプレートで必要な値またはコンポーネントをバインドするために使用します。 |`hr`
|**Values[].ValueExpression**|必要な値を抽出する JSON パス式。|`$.heartRate`
|**Values[].Required**|ペイロード内に値が存在する必要があります。  見つからない場合は、測定値が生成されず、InvalidOperationException がスローされます。|`true`

##### <a name="examples"></a>例
---
**心拍数**

*メッセージ*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*テンプレート*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**血圧**

*メッセージ*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*テンプレート*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceid",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**1 つのメッセージから複数の測定値を投影する**

*メッセージ*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*テンプレート 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*テンプレート 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**メッセージ内の配列から複数の測定値を投影する**

*メッセージ*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*テンプレート*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate は JsonPathContentTemplate に似ていますが、DeviceIdExpression と TimestampExpression は必要ありません。

このテンプレートを使用する場合、評価対象メッセージが [Azure IoT Hub デバイス SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) または [Azure IoT Central](../../iot-central/core/overview-iot-central.md) の[データのエクスポート (レガシ)](../../iot-central/core/howto-export-data-legacy.md) 機能を使用して送信されたことが前提になります。 これらの SDK を使用する場合、デバイス ID (Azure Iot Hub/Central からのデバイス識別子が宛先 FHIR サーバー上のデバイス リソースの識別子として登録されていることを前提としています) とメッセージのタイムスタンプは既知です。 Azure IoT Hub デバイス SDK を使用しているが、メッセージ本文でデバイス ID または測定タイムスタンプにカスタム プロパティを使用している場合でも、JsonPathContentTemplate を使用できます。

*注意事項: IotJsonPathContentTemplate を使用する場合は、TypeMatchExpression でメッセージ全体を JToken として解決する必要があります。以下の例を参照してください。* 
##### <a name="examples"></a>例
---
**心拍数**

*メッセージ*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*テンプレート*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**血圧**

*メッセージ*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*テンプレート*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate も DeviceIdExpression と TimestampExpression を必要とせず、これは、評価対象メッセージが [Azure IoT Central](../../iot-central/core/overview-iot-central.md) の[データのエクスポート](../../iot-central/core/howto-export-data.md)機能を使用して送信されるときに使用されます。 この機能を使用する場合、デバイス ID (Azure Iot Central からのデバイス識別子が宛先 FHIR サーバー上のデバイス リソースの識別子として登録されていることを前提としています) とメッセージのタイムスタンプは既知です。 Azure IoT Central のデータ エクスポート機能を使用しているが、メッセージ本文でデバイス ID または測定タイムスタンプにカスタム プロパティを使用している場合でも、JsonPathContentTemplate を使用できます。

*注: IotCentralJsonPathContentTemplate を使用する場合は、TypeMatchExpression でメッセージ全体を JToken として解決する必要があります。以下の例を参照してください。* 
##### <a name="examples"></a>例
---
**心拍数**

*メッセージ*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*テンプレート*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**血圧**

*メッセージ*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*テンプレート*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="fhir-mapping"></a>FHIR マッピング
デバイス コンテンツが正規化されたモデルに抽出されると、デバイス識別子、測定の種類、期間に従ってデータが収集され、グループ化されます。 このグループの出力は、FHIR リソース (現在は [Observation](https://www.hl7.org/fhir/observation.html)) に変換するために送信されます。 ここで、FHIR マッピング テンプレートによって、データを FHIR Observation にマップする方法を制御します。 特定の時点または 1 時間という期間にわたって観察を作成する必要がありますか。 観察にどのようなコードを追加する必要がありますか。 値を [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) として、または [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity) として表す必要がありますか。 これらのデータ型はすべて、FHIR マッピング構成コントロールのオプションです。

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate は、FHIR マッピングで現在サポートされている唯一のテンプレートです。  これを使用すると、コード、有効期間、観察の値を定義できます。 複数の値の種類がサポートされています: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)、[CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)、[Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)。 これらの構成可能な値と共に、Observation リソースの識別子と、適切な Device および Patient リソースへのリンクが自動的に処理されます。

| プロパティ | 説明 
| --- | ---
|**TypeName**| このテンプレートのバインド先となる測定の種類。 この種類を出力するデバイス マッピング テンプレートが少なくとも 1 つ存在する必要があります。
|**PeriodInterval**|作成された観察が表す期間。 サポートされている値は、0 (1 インスタンス)、60 (1 時間)、1440 (1 日) です。
|**カテゴリ**|作成される観察の種類を分類する任意の数の [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept)。
|**Codes**|作成された観察に適用する 1 つ以上の [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Codes[].Code**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) のコード。
|**Codes[].System**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) のシステム。
|**Codes[].Display**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) の表示。
|**Value**|観察で抽出して表す値。 詳細については、「[値の型テンプレート](#valuetypes)」を参照してください。
|**コンポーネント**|*省略可能:* 観察に対して作成する 1 つ以上のコンポーネント。
|**Components[].Codes**|コンポーネントに適用する 1 つ以上の [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Components[].Value**|コンポーネントで抽出して表す値。 詳細については、「[値の型テンプレート](#valuetypes)」を参照してください。

### <a name="value-type-templates"></a>値の型テンプレート <a name="valuetypes"></a>
現在サポートされている値の型テンプレートを以下に示します。 今後、さらにテンプレートが追加される可能性があります。
#### <a name="sampleddata"></a>SampledData
[SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR データ型を表します。観察の測定値は、特定の時点から開始し、定義された期間を使用して前方にインクリメントされる値ストリームに書き込まれます。 値が存在しない場合、`E` はデータ ストリームに書き込まれます。 この期間が、さらに 2 つの値がデータ ストリーム内の同じ位置を占めている場合は、最新の値が使用されます。 SampledData を使用した観察が更新されるときは、同じロジックが適用されます。

| プロパティ | 説明 
| --- | ---
|**DefaultPeriod**|使用する既定の時間 (ミリ秒単位)。 
|**単位**|SampledData の原点に設定する単位。 

#### <a name="quantity"></a>Quantity
FHIR データ型の [Quantity](http://hl7.org/fhir/datatypes.html#Quantity) を表します。 グループ内に複数の値が存在する場合は、最初の値のみが使用されます。 同じ観察にマップされる新しい値が到着すると、古い値が上書きされます。

| プロパティ | 説明 
| --- | --- 
|**単位**| 単位の表記。
|**コード**| 単位のコード化された形式。
|**システム**| コード化された単位の形式を定義するシステム。

### <a name="codeableconcept"></a>CodeableConcept
FHIR データ型 [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) を表します。 実際の値は使用されません。

| プロパティ | 説明 
| --- | --- 
|**[テキスト]**|プレーンテキスト表現。 
|**Codes**|作成された観察に適用する 1 つ以上の [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding)。
|**Codes[].Code**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) のコード。
|**Codes[].System**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) のシステム。
|**Codes[].Display**|[Coding](http://hl7.org/fhir/datatypes-definitions.html#coding) の表示。

### <a name="examples"></a>例
**心拍 - SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**歩数 - SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**血圧 - SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**血圧 - Quantity**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**削除されたデバイス - CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>次のステップ

Azure IoT Connector for FHIR (プレビュー) についてよく寄せられる質問を確認してください。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR についてよく寄せられる質問](fhir-faq.md)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。 FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。