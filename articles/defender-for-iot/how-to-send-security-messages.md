---
title: Defender for IoT デバイスのセキュリティ メッセージを送信する
description: Defender for IoT を使用してセキュリティ メッセージを送信する方法について説明します。
ms.topic: how-to
ms.date: 2/8/2021
ms.custom: devx-track-js
ms.openlocfilehash: dcda7f4c1d078fa32a5ab8c7b7ce690ab2134afe
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781638"
---
# <a name="send-security-messages-sdk"></a>セキュリティ メッセージの送信 SDK

この攻略ガイドでは、Defender for IoT エージェントを使用せずにデバイス セキュリティ メッセージを収集して送信する場合の Defender for IoT サービスの機能と、それを行う方法について説明します。

このガイドでは、以下の方法について説明します。

> [!div class="checklist"]
> * Azure IoT C SDK を使用してセキュリティ メッセージを送信する
> * Azure IoT C# SDK を使用してセキュリティ メッセージを送信する
> * Azure IoT Python SDK を使用してセキュリティ メッセージを送信する
> * Azure IoT Node.js SDK を使用してセキュリティ メッセージを送信する
> * Azure IoT Java SDK を使用してセキュリティ メッセージを送信する

## <a name="defender-for-iot-capabilities"></a>Defender for IoT の機能

Defender for IoT では、送信されるデータが [Defender for IoT スキーマ](https://aka.ms/iot-security-schemas)に準拠していて、メッセージがセキュリティ メッセージとして設定されていれば、すべての種類のセキュリティ メッセージ データを処理および分析できます。

## <a name="security-message"></a>セキュリティ メッセージ

Defender for IoT では、次の条件を使用してセキュリティ メッセージが定義されています。

- メッセージが Azure IoT SDK で送信された場合
- メッセージが[セキュリティ メッセージ スキーマ](https://aka.ms/iot-security-schemas)に準拠している場合
- メッセージが送信前にセキュリティ メッセージとして設定されている場合

各セキュリティ メッセージには、`AgentId`、`AgentVersion`、`MessageSchemaVersion`、セキュリティ イベントのリストなど、送信者のメタデータが含まれています。
スキーマでは、イベントの種類など、セキュリティ メッセージの有効で必要なプロパティが定義されています。

> [!NOTE]
> スキーマに準拠していない、送信されたメッセージは、無視されます。 現在、無視されたメッセージは保存されないため、データの送信を開始する前にスキーマを確認してください。

> [!NOTE]
> Azure IoT SDK を使用してセキュリティ メッセージとして設定されずに送信されたメッセージは、Defender for IoT パイプラインにルーティングされません

## <a name="valid-message-example"></a>有効なメッセージの例

次の例では、有効なセキュリティ メッセージ オブジェクトを示します。 この例には、メッセージ メタデータと 1 つの `ProcessCreate` セキュリティ イベントが含まれています。

セキュリティ メッセージとして設定されて送信されると、このメッセージは Defender for IoT によって処理されます。

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>セキュリティ メッセージの送信

Defender for IoT エージェント *なしで* セキュリティ メッセージを送信するか、[Azure IoT C デバイス SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)、[Azure IoT C# デバイス SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)、[Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node)、[Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)、または [Azure IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java) を使用して送信します。

Defender for IoT で処理するためにデバイスからデバイス データを送信するには、以下のいずれかの API を使用して、Defender for IoT 処理パイプラインへの正しいルーティングが行われるようにメッセージをマークします。

送信されるすべてのデータは、正しいヘッダーでマークされている場合でも、[Defender for IoT メッセージ スキーマ](https://aka.ms/iot-security-schemas)にも準拠する必要があります。

### <a name="send-security-message-api"></a>セキュリティ メッセージの送信 API

**セキュリティ メッセージの送信** API は、現在 C、C#、Python、Node.js、Java で使用できます。

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Node.js API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python API

Python API を使用するには、パッケージ [azure-iot-device](https://pypi.org/project/azure-iot-device/) をインストールする必要があります。

Python API を使用する場合、一意のデバイスまたはモジュール接続文字列を使用して、モジュールまたはデバイスを通じてセキュリティ メッセージを送信できます。 次の Python スクリプトの例を使用した場合、デバイスでは **IoTHubDeviceClient** を使用し、モジュールでは **IoTHubModuleClient** を使用します。

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>次のステップ

- Defender for IoT サービスの[概要](overview.md)を確認する
- Defender for IoT の[アーキテクチャ](architecture.md)について詳細を確認する
- [サービス](quickstart-onboard-iot-hub.md)を有効にします
- [FAQ](resources-frequently-asked-questions.md) を読みます
- [未加工のセキュリティ データ](how-to-security-data-access.md)にアクセスする方法を学習します
- [レコメンデーション](concept-recommendations.md)について理解します
- [アラート](concept-security-alerts.md)について理解します
