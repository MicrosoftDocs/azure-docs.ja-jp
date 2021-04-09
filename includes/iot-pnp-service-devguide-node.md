---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: dfeeb451912dd32770a6ae92d73de83851d9d8f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244540"
---
次のリソースも使用できます。

- [Node.js SDK のリファレンス ドキュメント](/javascript/api/azure-iothub)
- [サービス クライアントのサンプル](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Digital Twins のサンプル](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>IoT Hub サービス クライアントの例

このセクションでは、IoT Hub サービス クライアントと **Registry** および **Client** クラスを使用した JavaScript の例を示します。 **Registry** クラスを使用し、デバイス ツインを使用してデバイスの状態を操作します。 **Registry** クラスを使用して、IoT ハブ内の [デバイス登録のクエリ](../articles/iot-hub/iot-hub-devguide-query-language.md)を行うこともできます。 デバイス上でコマンドを呼び出すには、**Client** クラスを使用します。 デバイスの [DTDL](../articles/iot-pnp/concepts-digital-twin.md) モデルにより、デバイスによって実装されるプロパティとコマンドが定義されます。 コード スニペット内では、`deviceId` 変数によって、IoT ハブに登録されている IoT プラグ アンド プレイ デバイスのデバイス ID が保持されます。

### <a name="get-the-device-twin-and-model-id"></a>デバイス ツインとモデル ID を取得する

IoT ハブに接続されている IoT プラグ アンド プレイ デバイスのデバイス ツインとモデル ID を取得するには:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>デバイス ツインの更新

次のコード スニペットは、デバイス上で `targetTemperature` プロパティを更新する方法を示しています。 サンプルに、ツインを更新する前に取得する方法を示します。 プロパティは、デバイスの既定のコンポーネント内で定義されます。

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

次のスニペットは、コンポーネント上で `targetTemperature` プロパティを更新する方法を示しています。 サンプルに、ツインを更新する前に取得する方法を示します。 プロパティは、**thermostat1** コンポーネント内で定義されています。

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

コンポーネント内のプロパティの場合、プロパティ パッチは次の例のようになります。

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Call コマンド

次のスニペットは、既定のコンポーネント内で定義されている `getMaxMinReport` コマンドを呼び出す方法を示しています。

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

次のスニペットは、コンポーネント内で `getMaxMinReport` コマンドを呼び出す方法を示しています。 コマンドは、**thermostat1** コンポーネント内で定義されています。

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub デジタル ツインの例

**DigitalTwinClient** クラスを使用し、デジタル ツインを使用してデバイスの状態を操作します。 デバイスの [DTDL](../articles/iot-pnp/concepts-digital-twin.md) モデルにより、デバイスによって実装されるプロパティとコマンドが定義されます。

このセクションでは、Digital Twins API を使用した JavaScript の例を示します。

`digitalTwinId` 変数には、IoT ハブに登録されている IoT プラグ アンド プレイ デバイスのデバイス ID が保持されます。

### <a name="get-the-digital-twin-and-model-id"></a>デジタル ツインとモデル ID を取得する

IoT ハブに接続されている IoT プラグ アンド プレイ デバイスのデジタル ツインとモデル ID を取得するには:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>デジタル ツインを更新する

次のコード スニペットは、デバイス上で `targetTemperature` プロパティを更新する方法を示しています。 プロパティは、デバイスの既定のコンポーネント内で定義されます。

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

次のスニペットは、コンポーネント上で `targetTemperature` プロパティを更新する方法を示しています。 プロパティは、**thermostat1** コンポーネント内で定義されています。

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Call コマンド

次のスニペットは、既定のコンポーネント内で定義されている `getMaxMinReport` コマンドを呼び出す方法を示しています。

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

次のスニペットは、コンポーネント内で `getMaxMinReport` コマンドを呼び出す方法を示しています。 コマンドは、**thermostat1** コンポーネント内で定義されています。

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>デバイス テレメトリを読み取る

IoT プラグ アンド プレイ デバイスにより、DTDL モデル内で定義されているテレメトリが IoT Hub に送信されます。 既定では、IoT Hub により、テレメトリが Event Hubs エンドポイントにルーティングされ、そこで使用できます。 詳細については、「[IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)」を参照してください。

次のコード スニペットは、既定の Event Hubs エンドポイントからテレメトリを読み取る方法を示しています。 このスニペットのコードは、IoT Hub クイックスタート「[デバイスから IoT ハブに利用統計情報を送信してバックエンド アプリケーションで読み取る](../articles/iot-hub/quickstart-send-telemetry-node.md)」から取得したものです。

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

前のコードからの次の出力は、マルチコンポーネント **TemperatureController** IoT プラグ アンド プレイ デバイスによって送信された温度テレメトリを示しています。 `dt-subject` システム プロパティには、テレメトリを送信したコンポーネントの名前が表示されます。 この例では、DTDL モデルで定義されているように 2 つのコンポーネントは `thermostat1` と `thermostat2` です。 `dt-dataschema` システム プロパティには、モデル ID が表示されます。

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>デバイス ツインの変更通知の読み取り

サポートされているエンドポイントにルーティングするデバイス ツインの変更通知を生成するように IoT Hub を構成できます。 詳細については、「[IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する > 非テレメトリ イベント](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)」を参照してください。

IoT Hub によってコンポーネントなしのサーモスタット デバイスに対してデバイス ツインの変更通知が生成されるときに、前の JavaScript コード スニペットに示されているコードによって次の出力が生成されます。 アプリケーション プロパティ `iothub-message-schema` と `opType` によって、変更通知の種類に関する情報が提供されます。

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

IoT Hub によってコンポーネントがあるデバイスに対してデバイス ツインの変更通知が生成されるときに、前の JavaScript コード スニペットに示されているコードによって次の出力が生成されます。 この例は、サーモスタット コンポーネントを含む温度センサー デバイスによって通知が生成されたときの出力を示しています。 アプリケーション プロパティ `iothub-message-schema` と `opType` によって、変更通知の種類に関する情報が提供されます。

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>デジタル ツインの変更通知を読む

サポートされているエンドポイントにルーティングするデジタル ツインの変更通知を生成するように IoT Hub を構成できます。 詳細については、「[IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する > 非テレメトリ イベント](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)」を参照してください。

IoT Hub によってコンポーネントなしのサーモスタット デバイスに対してデジタル ツインの変更通知が生成されるときに、前の JavaScript コード スニペットに示されているコードによって次の出力が生成されます。 アプリケーション プロパティ `iothub-message-schema` と `opType` によって、変更通知の種類に関する情報が提供されます。

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

IoT Hub によってコンポーネントがあるデバイスに対してデジタル ツインの変更通知が生成されるときに、前の JavaScript コード スニペットに示されているコードによって次の出力が生成されます。 この例は、サーモスタット コンポーネントを含む温度センサー デバイスによって通知が生成されたときの出力を示しています。 アプリケーション プロパティ `iothub-message-schema` と `opType` によって、変更通知の種類に関する情報が提供されます。

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
