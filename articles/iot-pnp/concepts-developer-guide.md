---
title: 開発者ガイド - IoT プラグ アンド プレイ プレビュー | Microsoft Docs
description: IoT プラグ アンド プレイ開発者向けのデバイス モデリングの説明
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6d5247454fe65e5539a2401330192f1db9a65114
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879235"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT プラグ アンド プレイ プレビュー モデリング開発者ガイド

IoT プラグ アンド プレイ プレビューでは、Azure IoT アプリケーションに機能を宣伝するデバイスをビルドできます。 IoT プラグ アンド プレイ デバイスでは、ユーザーが IoT プラグ アンド プレイ対応アプリケーションに接続するときに、手動で構成する必要がありません。 IoT プラグ アンド プレイ対応アプリケーションの例として、IoT Central が挙げられます。

IoT プラグ アンド プレイ デバイスをビルドするには、デバイス記述を作成する必要があります。 この記述は、Digital Twins Definition Language (DTDL) と呼ばれる単純な定義言語を使用して行われます。

## <a name="device-capability-model"></a>デバイス機能モデル

DTDL では、デバイスの各パーツを記述する "_デバイス機能モデル_" を作成します。 一般的な IoT デバイスは次のように構成されています。

- カスタム パーツ。デバイスを一意にするパーツです。
- 標準パーツ。すべてのデバイスに共通するパーツです。

これらのパーツは、デバイス機能モデルの "_インターフェイス_" と呼ばれます。 インターフェイスは、デバイスが実装する各パーツの詳細を定義します。

次の例は、あるサーモスタット デバイスのデバイス機能モデルを示します。

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

機能モデルには、いくつかの必須フィールドがあります。

- `@id`: 単純な Uniform Resource Name 形式の一意の ID。
- `@type`: このオブジェクトが機能モデルであることを宣言します。
- `@context`: 機能モデルに使用される DTDL バージョンを指定します。
- `implements`: デバイスが実装するインターフェイスが一覧表示されます。

implements セクションのインターフェイス一覧の各エントリには、次のフィールドがあります。

- `name`: インターフェイスのプログラミング名。
- `schema`: 機能モデルが実装するインターフェイス。

他にも表示名や説明など、機能モデルに詳細を追加するために使用できるオプションのフィールドが用意されています。 機能モデル内で宣言されたインターフェイスは、デバイスのコンポーネントと考えることができます。 パブリック プレビューの場合、インターフェイスの一覧にはスキーマごとに 1 つのエントリのみ存在できます。

## <a name="interface"></a>インターフェイス

DTDL では、インターフェイスを使用してデバイスの機能を記述します。 インターフェイスは、"_プロパティ_"、"_テレメトリ_"、および "_コマンド_" をデバイスの実装の一部として記述します。

- `Properties` プロパティは、デバイスの状態を表すデータ フィールドです。 プロパティは、冷却ポンプのオンオフ状態など、デバイスの持続的な状態を表すために使用します。 また、プロパティはデバイスのファームウェア バージョンなど、デバイスの基本的なプロパティも表すことができます。 プロパティは、読み取り専用または書き込み可能として宣言できます。
- `Telemetry` テレメトリ フィールドは、センサーの測定値を表します。 デバイスがセンサーの測定値を取得するたびに、センサー データを含むテレメトリ イベントを送信する必要があります。
- `Commands` コマンドは、デバイスのユーザーがデバイスで実行できるメソッドを表します。 たとえば、リセット コマンドや、ファンのオンとオフを切り替えるコマンドなどです。

次の例は、あるサーモスタット デバイスのインターフェイスを示します。

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/Interface.json"
}
```

インターフェイスには、いくつかの必須フィールドがあります。

- `@id`: 単純な Uniform Resource Name 形式の一意の ID。
- `@type`: このオブジェクトがインターフェイスであることを宣言します。
- `@context`: インターフェイスに使用される DTDL バージョンを指定します。
- `contents`: デバイスを構成するプロパティ、テレメトリ、およびコマンドが一覧表示されます。

この簡単な例では、テレメトリ フィールドが 1 つだけ存在します。 最小限記述するフィールドは、次の通りです。

- `@type`: 機能の種類 (`Telemetry`、`Property`、または`Command`) を指定します。
- `name`: テレメトリ値の名前を提供します。
- `schema`: テレメトリのデータ型を指定します。 この値は、double、integer、boolean、string などのプリミティブ型にすることができます。 複合オブジェクト型、配列、およびマップもサポートされています。

表示名や説明などの他のオプション フィールドを使用すると、インターフェイスや機能に詳細を追加できます。

### <a name="properties"></a>properties

既定では、プロパティは読み取り専用です。 読み取り専用のプロパティは、デバイスがプロパティ値の更新を IoT ハブに報告することを意味します。 IoT ハブで読み取り専用のプロパティの値を設定することはできません。

インターフェイスでは、プロパティを書き込み可能としてマークすることもできます。 デバイスは、IoT ハブから書き込み可能なプロパティに対する更新を受信できるだけでなく、ハブに対するプロパティ値の更新を報告することもできます。

プロパティ値を設定するためにデバイスに接続する必要はありません。 更新された値は、デバイスが次にハブに接続したときに転送されます。 この動作は、読み取り専用プロパティと書き込み可能なプロパティの両方に適用されます。

デバイスからテレメトリを送信するために、プロパティを使用しないでください。 たとえば、`temperatureSetting=80` などの読み取り専用プロパティは、デバイスの温度が 80 度に設定されており、デバイスがこの温度に達しようとしている、またはこの温度を維持しようとしていることを意味します。

書き込み可能なプロパティの場合、デバイス アプリケーションは、必要な状態の状態コード、バージョン、および説明を返して、プロパティ値を受け取って適用したかどうかを示します。

### <a name="telemetry"></a>テレメトリ

既定では、IoT Hub は、デバイスからのすべてのテレメトリ メッセージを、[Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/) と互換性のある[組み込みのサービス向けエンドポイント (**messages/events**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) にルーティングします。

[IoT Hub のカスタム エンドポイントとルーティング規則](../iot-hub/iot-hub-devguide-messages-d2c.md)を使用して、BLOB ストレージや他のイベント ハブなどの他の送信先にテレメトリを送信できます。 ルーティング規則では、メッセージ プロパティを使用してメッセージを選択します。

### <a name="commands"></a>command

コマンドは、同期または非同期のいずれかです。 同期コマンドは、既定で 30 秒以内に実行する必要があり、コマンドが到着したときにデバイスを接続する必要があります。 デバイスが時間内に応答しない場合、またはデバイスが接続されていない場合、コマンドは失敗します。

実行時間の長い操作には、非同期コマンドを使用してください。 デバイスは、テレメトリ メッセージを使用して進行状況情報を送信します。 それらの進行状況情報には次のヘッダー プロパティがあります。

- `iothub-command-name`: コマンド名 (`UpdateFirmware` など)。
- `iothub-command-request-id`: サーバー側で生成され、最初の呼び出しでデバイスに送信される要求 ID。
- `iothub-interface-id`:このコマンドが定義されているインターフェイスの ID (`urn:example:AssetTracker:1` など)。
 `iothub-interface-name`: このインターフェイスのインスタンス名 (`myAssetTracker` など)。
- `iothub-command-statuscode`: デバイスから返された状態コード (`202` など)。

## <a name="register-a-device"></a>デバイスの登録

IoT プラグ アンド プレイを使用すると、デバイスの機能を簡単に宣伝できます。 IoT プラグ アンド プレイでは、デバイスを IoT Hub に接続した後、デバイス機能モデルを登録する必要があります。 ユーザーは登録することでデバイスの IoT プラグ アンド プレイ機能を使用できるようになります。

このガイドでは、C 用 Azure IoT Device SDK を使用してデバイスを登録する方法について説明します。

デバイスが実装するインターフェイスごとに、インターフェイスを作成してその実装に接続する必要があります。

前に示したサーモスタット インターフェイスでは、C SDK を使用してサーモスタット インターフェイスを作成し、その実装に接続します。

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

デバイスが実装するインターフェイスごとに、このコードを繰り返します。

インターフェイスを作成したら、デバイス機能モデルとインターフェイスを IoT ハブに登録します。

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaceHandleList, 2,
    null, null);
```

## <a name="use-a-device"></a>デバイスの使用

IoT プラグ アンド プレイでは、IoT ハブに機能を登録したデバイスを使用できます。 たとえば、デバイスのプロパティとコマンドに直接アクセスできます。

IoT ハブに接続されている IoT プラグ アンド プレイ デバイスを使用するには、IoT Hub REST API またはいずれかの IoT 言語 SDK を使用します。 次の例では、IoT Hub REST API を使用します。

サーモスタットの `DeviceInformation` インターフェイスのファームウェア バージョン (`fwVersion`) などのデバイス プロパティの値を取得するには、Digital Twins REST API を使用します。

サーモスタット デバイスが `t-123` の場合、REST API GET 呼び出しにより、デバイスによって実装されているすべてのプロパティを取得します。

```REST
GET /digitalTwins/t-123/interfaces
```

より一般的に、すべてのプロパティにはこの REST API テンプレートを使用してアクセスできます。ここで、`{device-id}` はデバイスの識別子です。

```REST
GET /digitalTwins/{device-id}/interfaces
```

インターフェイスの名前がわかっていて、その特定のインターフェイスのプロパティを取得する必要がある場合は、名前を指定して要求の範囲を特定のインターフェイスに設定します。

```REST
GET /digitalTwins/t-123/interfaces/info
```

より一般的に、特定のインターフェイスのプロパティは、この REST API テンプレートを使用してアクセスできます。ここで、`device-id` はデバイスの識別子で、`{interface-name}` はインターフェイスの名前です。

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

IoT プラグ アンド プレイ デバイス コマンドを直接呼び出すことができます。 `t-123` デバイスの `Thermostat` インターフェイスに `restart` コマンドがある場合は、REST API POST 呼び出しを使用して呼び出すことができます。

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

より一般的に、コマンドはこの REST API テンプレートを使用して呼び出すことができます。

- `device-id`: デバイスの識別子。
- `interface-name`: デバイス機能モデルの implements セクションのインターフェイスの名前。
- `command-name`: コマンドの名前。

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>次の手順

ここまでで、デバイスのモデリングについて学習しました。その他のリソースを次に示します。

- [Digital Twin Definition Language (DTDL)](https://aka.ms/DTDL)
- [C デバイス SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
