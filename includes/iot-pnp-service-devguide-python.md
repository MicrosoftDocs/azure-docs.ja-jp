---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: a925c3a17988ef6f4b95a1e3cf4dd5fb8baa4829
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510615"
---
次のリソースも使用できます。

- [Python SDK のリファレンス ドキュメント](/python/api/azure-iot-hub/azure.iot.hub)
- [サービス クライアントのサンプル](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Digital Twins のサンプル](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>IoT Hub サービス クライアントの例

このセクションでは、IoT Hub サービス クライアント、および **IoTHubRegistryManager** クラスと **CloudToDeviceMethod** クラスを使用した Python の例を示します。 **IoTHubRegistryManager** クラスを使用し、デバイス ツインを使用してデバイスの状態を操作します。 また、**IoTHubRegistryManager** クラスを使用して、IoT Hub 内の [デバイス登録のクエリ](../articles/iot-hub/iot-hub-devguide-query-language.md)を実行することもできます。 デバイス上でコマンドを呼び出すには、**CloudToDeviceMethod** クラスを使用します。 デバイスの [DTDL](../articles/iot-pnp/concepts-digital-twin.md) モデルにより、デバイスによって実装されるプロパティとコマンドが定義されます。 コード スニペット内では、`device_id` 変数によって、IoT ハブに登録されている IoT プラグ アンド プレイ デバイスのデバイス ID が保持されます。

### <a name="get-the-device-twin-and-model-id"></a>デバイス ツインとモデル ID を取得する

IoT ハブに接続されている IoT プラグ アンド プレイ デバイスのデバイス ツインとモデル ID を取得するには:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>デバイス ツインの更新

次のコード スニペットは、デバイス上で `targetTemperature` プロパティを更新する方法を示しています。 サンプルでは、ツインの `etag` を更新する前に取得する方法を示します。 プロパティは、デバイスの既定のコンポーネント内で定義されます。

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

次のスニペットは、コンポーネント上で `targetTemperature` プロパティを更新する方法を示しています。 サンプルでは、ツインの `ETag` を更新する前に取得する方法を示します。 プロパティは、**thermostat1** コンポーネント内で定義されています。

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
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

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

次のスニペットは、コンポーネント内で `getMaxMinReport` コマンドを呼び出す方法を示しています。 コマンドは、**thermostat1** コンポーネント内で定義されています。

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub デジタル ツインの例

**DigitalTwinClient** クラスを使用し、デジタル ツインを使用してデバイスの状態を操作します。 デバイスの [DTDL](../articles/iot-pnp/concepts-digital-twin.md) モデルにより、デバイスによって実装されるプロパティとコマンドが定義されます。

`device_id` 変数には、IoT ハブに登録されている IoT プラグ アンド プレイ デバイスのデバイス ID が保持されます。

### <a name="get-the-digital-twin-and-model-id"></a>デジタル ツインとモデル ID を取得する

IoT ハブに接続されている IoT プラグ アンド プレイ デバイスのデジタル ツインとモデル ID を取得するには:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>デジタル ツインを更新する

次のコード スニペットは、デバイス上で `targetTemperature` プロパティを更新する方法を示しています。 プロパティは、デバイスの既定のコンポーネント内で定義されます。

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

次のスニペットは、コンポーネント上で `targetTemperature` プロパティを更新する方法を示しています。 プロパティは、**thermostat1** コンポーネント内で定義されています。

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Call コマンド

次のスニペットは、既定のコンポーネント内で定義されている `getMaxMinReport` コマンドを呼び出す方法を示しています。

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

次のスニペットは、コンポーネント内で `getMaxMinReport` コマンドを呼び出す方法を示しています。 コマンドは、**thermostat1** コンポーネント内で定義されています。

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>デバイス テレメトリを読み取る

IoT プラグ アンド プレイ デバイスにより、DTDL モデル内で定義されているテレメトリが IoT Hub に送信されます。 既定では、IoT Hub により、テレメトリが Event Hubs エンドポイントにルーティングされ、そこで使用できます。 詳細については、「[IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)」を参照してください。

次のコード スニペットは、既定の Event Hubs エンドポイントからテレメトリを読み取る方法を示しています。 このスニペットのコードは、IoT Hub クイックスタート「[デバイスから IoT ハブに利用統計情報を送信してバックエンド アプリケーションで読み取る](../articles/iot-hub/quickstart-send-telemetry-python.md)」から取得したものです。

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

前のコードからの次の出力は、既定のコンポーネントのみを持つ、コンポーネントのない **サーモスタット** IoT プラグ アンド プレイ デバイスによって送信された温度テレメトリを示しています。 `dt-dataschema` システム プロパティには、モデル ID が表示されます。

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

前のコードからの次の出力は、マルチコンポーネント **TemperatureController** IoT プラグ アンド プレイ デバイスによって送信された温度テレメトリを示しています。 `dt-subject` システム プロパティには、テレメトリを送信したコンポーネントの名前が表示されます。 この例では、DTDL モデルで定義されているように 2 つのコンポーネントは `thermostat1` と `thermostat2` です。 `dt-dataschema` システム プロパティには、モデル ID が表示されます。

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>デバイス ツインの変更通知の読み取り

サポートされているエンドポイントにルーティングするデバイス ツインの変更通知を生成するように IoT Hub を構成できます。 詳細については、「[IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する > 非テレメトリ イベント](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)」を参照してください。

IoT Hub によってコンポーネントなしのサーモスタット デバイスに対してデバイス ツインの変更通知が生成されるときに、前の Python コード スニペットに示されているコードによって次の出力が生成されます。 アプリケーション プロパティ `iothub-message-schema` と `opType` によって、変更通知の種類に関する情報が提供されます。

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

IoT Hub によってコンポーネントがあるデバイスに対してデバイス ツインの変更通知が生成されるときに、前の Python コード スニペットに示されているコードによって次の出力が生成されます。 この例は、サーモスタット コンポーネントを含む温度センサー デバイスによって通知が生成されたときの出力を示しています。 アプリケーション プロパティ `iothub-message-schema` と `opType` によって、変更通知の種類に関する情報が提供されます。

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>デジタル ツインの変更通知を読む

サポートされているエンドポイントにルーティングするデジタル ツインの変更通知を生成するように IoT Hub を構成できます。 詳細については、「[IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する > 非テレメトリ イベント](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)」を参照してください。

IoT Hub によってコンポーネントなしのサーモスタット デバイスに対してデジタル ツインの変更通知が生成されるときに、前の Python コード スニペットに示されているコードによって次の出力が生成されます。 アプリケーション プロパティ `iothub-message-schema` と `opType` によって、変更通知の種類に関する情報が提供されます。

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

IoT Hub によってコンポーネントがあるデバイスに対してデジタル ツインの変更通知が生成されるときに、前の Python コード スニペットに示されているコードによって次の出力が生成されます。 この例は、サーモスタット コンポーネントを含む温度センサー デバイスによって通知が生成されたときの出力を示しています。 アプリケーション プロパティ `iothub-message-schema` と `opType` によって、変更通知の種類に関する情報が提供されます。

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
