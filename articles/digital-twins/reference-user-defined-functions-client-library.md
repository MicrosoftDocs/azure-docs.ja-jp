---
title: Azure Digital Twins ユーザー定義関数クライアント ライブラリ リファレンス | Microsoft Docs
description: Azure Digital Twins ユーザー定義関数クライアント ライブラリ リファレンス。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 12/27/2018
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: d309765e6c4b0b0cc8a7e92977134d4135a69a13
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995470"
---
# <a name="user-defined-functions-client-library-reference"></a>ユーザー定義関数クライアント ライブラリ リファレンス

このドキュメントでは、Azure Digital Twins ユーザー定義関数クライアント ライブラリの参照情報を示します。

## <a name="helper-methods"></a>ヘルパー メソッド

クライアント ライブラリでは、よく使用される操作のヘルパー メソッドが定義されています。

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

空間識別子が指定されると、この関数はグラフから空間を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 空間識別子 |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

センサーの識別子が指定されると、この関数はグラフからセンサーを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | センサーの識別子 |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

デバイスの識別子が指定されると、この関数はグラフからデバイスを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | デバイスの識別子 |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

センサーの識別子とそのデータ型が指定されると、この関数はそのセンサーの現在の値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | センサーの識別子 |
| *dataType*  | `string` | センサーのデータ型 |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

空間識別子と値名が指定されると、この関数はその空間プロパティの現在の値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | 空間識別子 |
| *valueName* | `string` | 空間プロパティの名前 |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

センサーの識別子とそのデータ型が指定されると、この関数はそのセンサーの履歴の値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | センサーの識別子 |
| *dataType* | `string` | センサーのデータ型 |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

空間識別子と値名が指定されると、この関数は空間のそのプロパティの履歴の値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |
| *valueName* | `string` | 空間プロパティの名前 |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

空間識別子が指定されると、この関数はその親空間の子空間を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

空間識別子が指定されると、この関数はその親空間の子センサーを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

空間識別子が指定されると、この関数はその親空間の子デバイスを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

デバイスの識別子が指定されると、この関数はその親デバイスの子センサーを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | デバイスの識別子 |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

空間識別子が指定されると、この関数はその親空間を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | 空間識別子 |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

センサーの識別子が指定されると、この関数はその親空間を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | センサーの識別子 |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

デバイスの識別子が指定されると、この関数はその親空間を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | デバイスの識別子 |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

センサーの識別子が指定されると、この関数はその親デバイスを取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | センサーの識別子 |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

空間識別子が指定されると、この関数は空間からプロパティとその値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |
| *propertyName* | `string` | 空間プロパティの名前 |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

センサーの識別子が指定されると、この関数はセンサーからプロパティとその値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | センサーの識別子 |
| *propertyName* | `string` | センサー プロパティの名前 |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

デバイスの識別子が指定されると、この関数はデバイスからプロパティとその値を取得します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | デバイスの識別子 |
| *propertyName* | `string` | デバイス プロパティの名前 |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

この関数は、指定されたデータ型のセンサー オブジェクトの値を設定します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | センサーの識別子 |
| *dataType*  | `string` | センサーのデータ型 |
| *value*  | `string` | 値 |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

この関数は、指定されたデータ型の空間オブジェクトの値を設定します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | 空間識別子 |
| *dataType* | `string` | データ型 |
| *value* | `string` | 値 |

### <a name="logmessage"></a>log(message)

この関数は、ユーザー定義関数内での次のメッセージをログに記録します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *message* | `string` | 記録されるメッセージ |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

この関数は、ディスパッチされるカスタム通知を送信します。

**種類**: グローバル関数

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | グラフのオブジェクト識別子。 例: 空間、センサー、デバイスの ID。|
| *topologyObjectType*  | `string` | 例: センサー、デバイス。|
| *payload*  | `string` | 通知と共に送信される JSON ペイロード。 |

## <a name="return-types"></a>戻り値の型

以下に、クライアント リファレンス ヘルパー メソッドから返される応答モデルを示します。

### <a name="space"></a>スペース

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
}
```

### <a name="space-methods"></a>空間メソッド

#### <a name="parent--space"></a>Parent() ⇒ `space`

この関数は、現在の空間の親空間を返します。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

この関数は、現在の空間の子センサーを返します。

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

この関数は、現在の空間の子デバイスを返します。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

この関数は、現在の空間の拡張プロパティとその値を返します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 拡張プロパティの名前 |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

この関数は、現在の空間の値を返します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 値の名前 |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

この関数は、現在の空間の履歴の値を返します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | 値の名前 |

#### <a name="notifypayload"></a>Notify(payload)

この関数は、指定されたペイロードを含む通知を送信します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 通知に含める JSON ペイロード |

### <a name="device"></a>Device

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>デバイスのメソッド

#### <a name="parent--space"></a>Parent() ⇒ `space`

この関数は、現在のデバイスの親空間を返します。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

この関数は、現在のデバイスの子センサーを返します。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

この関数は、現在のデバイスの拡張プロパティとその値を返します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 拡張プロパティの名前 |

#### <a name="notifypayload"></a>Notify(payload)

この関数は、指定されたペイロードを含む通知を送信します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 通知に含める JSON ペイロード |

### <a name="sensor"></a>センサー

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>センサーのメソッド

#### <a name="space--space"></a>Space() ⇒ `space`

この関数は、現在のセンサーの親空間を返します。

#### <a name="device--device"></a>Device() ⇒ `device`

この関数は、現在のセンサーの親デバイスを返します。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

この関数は、現在のセンサーの拡張プロパティとその値を返します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | 拡張プロパティの名前 |

#### <a name="value--value"></a>Value() ⇒ `value`

この関数は、現在のセンサーの値を返します。

#### <a name="history--value"></a>History() ⇒ `value[]`

この関数は、現在のセンサーの履歴の値を返します。

#### <a name="notifypayload"></a>Notify(payload)

この関数は、指定されたペイロードを含む通知を送信します。

| パラメーター  | type                | 説明  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | 通知に含める JSON ペイロード |

### <a name="value"></a>値

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>拡張プロパティ

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>次の手順

- [Azure Digital Twins ユーザー定義関数](./concepts-user-defined-functions.md)について学習します。

- [ユーザー定義関数を作成する方法](./how-to-user-defined-functions.md)について学習します。

- [ユーザー定義関数をデバッグする方法](./how-to-diagnose-user-defined-functions.md)について学習します。
