---
title: Azure IoT Hub の MQTT サポートについて | Microsoft Docs
description: MQTT プロトコルを使用して IoT Hub デバイスに接続されているエンドポイントに接続するデバイスのサポート。 Azure IoT デバイス SDK での組み込み MQTT サポートについての情報も含まれます。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
- contperf-fy21q1
- fasttrack-edit
- iot
ms.openlocfilehash: cbc4bbf73c65d4d7eddad556f3776bc0bbd653ba
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431263"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>MQTT プロトコルを使用した IoT Hub との通信

IoT Hub により、デバイスは、以下を使用して IoT Hub デバイス エンドポイントと通信できます。

* ポート 8883 上の [MQTT v3.1.1](https://mqtt.org/)
* ポート 443 で WebSocket を介して MQTT v3.1.1

IoT Hub はフル機能の MQTT ブローカーではないため、MQTT v3.1.1 標準で指定されているすべての動作をサポートしているわけではありません。 この記事では、サポートされている MQTT 動作を使用して、デバイスと IoT Hub の通信を行う方法について説明します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub でのすべてのデバイス通信は、TLS/SSL を使用してセキュリティで保護する必要があります。 そのため、IoT Hub では、ポート 1883 経由のセキュリティで保護されていない接続はサポートしません。

## <a name="connecting-to-iot-hub"></a>IoT Hub への接続

デバイスでは、MQTT プロトコルを使用し、次のいずれかのオプションを利用して IoT ハブに接続できます。

* [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) にあるライブラリ。
* 直接的な MQTT プロトコル。

MQTT ポート (8883) は、多くの企業や教育用のネットワーク環境ではブロックされています。 ファイアウォールでポート 8883 を開けない場合は、MQTT over WebSocket を使用することをお勧めします。 MQTT over WebSocket は、ほとんどの場合はネットワーク環境で開放されているポート 443 を介して通信します。 Azure IoT SDK を使用しているときに MQTT プロトコルと MQTT over WebSocket プロトコルを指定する方法については、「[デバイス SDK の使用](#using-the-device-sdks)」を参照してください。

## <a name="using-the-device-sdks"></a>デバイス SDK の使用

MQTT プロトコルをサポートする[デバイス SDK](https://github.com/Azure/azure-iot-sdks) は、Java、Node.js、C、C#、および Python に対応しています。 デバイス SDK では、標準的な IoT Hub 接続文字列を使用して、IoT ハブへの接続を確立します。 MQTT プロトコルを使用するには、クライアント プロトコル パラメーターを **MQTT** に設定する必要があります。 また、クライアント プロトコル パラメーターで MQTT over Web Sockets を指定することもできます。 デバイス SDK は既定では、**CleanSession** フラグを **0** に設定して IoT ハブに接続し、IoT ハブとのメッセージ交換には **QoS 1** を使用します。 メッセージ交換を高速にすために **QoS 0** を構成することはできますが、配信が保証されず、確認応答がないことに注意する必要があります。 このため、**QoS 0** は "ファイア アンド フォーゲット" と呼ばれることがよくあります。

デバイスは、IoT ハブに接続されると、デバイス SDK によって、IoT ハブとのメッセージの交換を可能にするメソッドが提供されます。

次の表では、サポートされている各言語のコード サンプルへのリンクを提供すると共に、MQTT プロトコルまたは MQTT over WebSocket プロトコルを使用して IoT Hub への接続を確立するために使用するパラメーターを示します。

| Language | MQTT プロトコルのパラメーター | MQTT over WebSocket プロトコルのパラメーター
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt.Mqtt | azure-iot-device-mqtt.MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol).MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](/dotnet/api/microsoft.azure.devices.client.transporttype).Mqtt | MQTT が失敗した場合、TransportType.Mqtt は MQTT over WebSocket にフォールバックします。 MQTT over WebSocket のみを指定するには、TransportType.Mqtt_WebSocket_Only を使用します |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | 既定で MQTT をサポートします | 呼び出しに `websockets=True` を追加してクライアントを作成します |

次のフラグメントでは、Azure IoT Node.js SDK を使用しているときに MQTT over WebSocket プロトコルを指定する方法を示しています。

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

次のフラグメントでは、Azure IoT Python.js SDK を使用しているときに MQTT over WebSocket プロトコルを指定する方法を示しています。

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>既定のキープアライブ タイムアウト

クライアント/IoT Hub 接続を確実に存続させるために、サービスとクライアントは、どちらも *キープアライブ* ping を定期的に相手に送信します。 IoT SDK を使用しているクライアントは、次の表に定義されている間隔でキープアライブを送信します。

|Language  |既定のキープアライブ間隔  |構成可能  |
|---------|---------|---------|
|Node.js     |   180 秒      |     いいえ    |
|Java     |    230 秒     |     いいえ    |
|C     | 240 秒 |  [はい](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 秒 |  [はい](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 秒 |  いいえ   |

[MQTT 仕様](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)に従って、IoT Hub のキープアライブ ping の間隔は、クライアントのキープアライブ値の 1.5 倍です。 ただし、IoT Hub では、すべての Azure サービスは Azure ロードバランサーの TCP アイドル タイムアウト (29.45 分) にバインドされているため、サーバー側のタイムアウトの最大値は 29.45 分 (1,767 秒) に制限されます。 

たとえば、Java SDK を使用しているデバイスでは、キープアライブ ping が送信された後、ネットワーク接続が失われます。 230 秒後、デバイスはオフラインになっているため、キープアライブ ping は失敗します。 ただし、IoT Hub では、接続はすぐに閉じられるわけではありません。さらに `(230 * 1.5) - 230 = 115` 秒間待機した後、エラー [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md) でデバイスが切断されます。 

設定できるクライアントのキープアライブの最大値は、`1767 / 1.5 = 1177` 秒です。 すべてのトラフィックで、キープアライブがリセットされます。 たとえば、SAS トークンの正常な更新によって、キープアライブがリセットされます。

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>デバイス アプリの AMQP から MQTT への移行

[デバイス SDK](https://github.com/Azure/azure-iot-sdks) を使用している場合、AMQP から MQTT に使用を切り替えるには、前述のようにクライアントの初期化時にプロトコル パラメーターを変更する必要があります。

これを行う際は、次の項目をご確認ください。

* AMQP では、MQTT 接続を終了するときに、多くの条件のエラーが返されます。 そのため、例外処理のロジックを一部変更する必要が生じることがあります。

* MQTT は、[クラウドからデバイスへのメッセージ](iot-hub-devguide-messaging.md)を受信するときの "*拒否*" 操作をサポートしていません。 バックエンド アプリではデバイス アプリから応答を受信する必要がある場合、[ダイレクト メソッド](iot-hub-devguide-direct-methods.md)の使用を検討してください。

* Python SDK では AMQP は サポートされていません。

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Azure IoT SDK を使用しない MQTT を使用した C の例

この [IoT MQTT サンプル リポジトリ](https://github.com/Azure-Samples/IoTMQTTSample)では、Azure IoT C SDK を使用せずに、IoT ハブでテレメトリ メッセージを送信し、イベントを受信する方法を示す C/C++ のデモ プロジェクトをいくつか紹介します。 

これらのサンプルでは、Eclipse Mosquitto ライブラリを使用して、IoT ハブに実装されている MQTT ブローカーにメッセージを送信します。

[Azure IoT プラグ アンド プレイ](../iot-pnp/overview-iot-plug-and-play.md)の規則を使用するようにサンプルを調整する方法については、「[チュートリアル - MQTT を使用して IoT プラグ アンド プレイ デバイス クライアントを開発する](../iot-pnp/tutorial-use-mqtt.md)」を参照してください。

このリポジトリには、次のものが含まれます。

**Windows の場合:**

* TelemetryMQTTWin32: Windows マシンでビルドされ実行されている Azure IoT ハブにテレメトリ メッセージを送信するためのコードが含まれています。

* SubscribeMQTTWin32: Windows マシン上の特定の IoT ハブのイベントをサブスクライブするためのコードが含まれています。

* DeviceTwinMQTTWin32: Windows マシン上の Azure IoT ハブにあるデバイスのデバイス ツイン イベントに対してクエリを実行し、サブスクライブするためのコードが含まれています。

* PnPMQTTWin32: IoT プラグ アンド プレイのデバイス機能を使用して、Windows マシンでビルドされ実行されている Azure IoT ハブにテレメトリ メッセージを送信するためのコードが含まれています。 詳細については、[IoT プラグ アンド プレイ](../iot-pnp/overview-iot-plug-and-play.md)に関するページをご覧ください

**Linux の場合:**

* MQTTLinux: Linux で実行されるコードとビルド スクリプトが含まれています (現時点までで WSL、Ubuntu、Raspbian でテスト済み)。

* LinuxConsoleVS2019: WSL (Windows Linux サブ システム) をターゲットとする VS2019 プロジェクトの同じコードが含まれています。 このプロジェクトを使用すると、Linux で実行されているコードを Visual Studio からステップ バイ ステップでデバッグできます。

**mosquitto_pub の場合:**

このフォルダーには、Mosquitto.org によって提供される mosquitto_pub ユーティリティ ツールで使用される 2 つのサンプル コマンドが含まれています。

* Mosquitto_sendmessage: デバイスとして機能する Azure IoT ハブに単純なテキスト メッセージを送信します。

* Mosquitto_subscribe: Azure IoT ハブで発生しているイベントを表示します。

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>MQTT プロトコルの直接使用 (デバイスとして)

デバイスでデバイス SDK を使用できない場合でも、MQTT プロトコルをポート 8883 で使用して、デバイスをパブリックのデバイス エンドポイントに接続できます。 **CONNECT** パケットの場合、デバイスでは次の値を使用する必要があります。

* **ClientId** フィールドには、**deviceId** を使用します。

* **[Usename]** フィールドには、`{iothubhostname}/{device_id}/?api-version=2018-06-30` を使用します。`{iothubhostname}` は IoT Hub の完全な CName です。

    たとえば、IoT Hub の名前が **contoso.azure-devices.net** であり、デバイスの名前が **MyDevice01** であるとすると、**Username** フィールドの内容は 次のようになります。

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* **[Password]** フィールドには、SAS トークンを使用します。 SAS トークンの形式は、HTTPS プロトコルや AMQP プロトコルの場合と同じです。

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > X.509 証明書の認証を使用する場合は、SAS トークン パスワードは不要です。 詳細については、「[Azure IoT Hub での X.509 セキュリティの設定](iot-hub-security-x509-get-started.md)」を参照し、[TLS/SSL 構成セクション](#tlsssl-configuration)のコードの説明に従ってください。

  SAS トークンの生成方法について詳しくは、[IoT Hub のセキュリティ トークンの使用](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)に関するページにあるデバイスのセクションをご覧ください。

  テスト時には、クロスプラットフォームの [Visual Studio Code 用 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) または CLI 拡張機能コマンド [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token) を使用して SAS トークンを簡単に生成し、コピーして自分のコードに貼り付けることができます。

### <a name="for-azure-iot-tools"></a>Azure IoT Tools の場合

1. Visual Studio Code の左下隅にある **[Azure IoT ハブ デバイス]** タブを展開します。
  
2. デバイスを右クリックし、 **[Generate SAS Token for Device]\(デバイスの SAS トークンの生成\)** を選択します。
  
3. **[期限]** を設定し、Enter キーを押します。
  
4. SAS トークンが作成され、クリップボードにコピーされます。

   生成される SAS トークンは、次のような構成になります。

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   MQTT を使用して接続するための **[Password]** フィールドとして使用されるこのトークンの一部は次のようになります。

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT の接続パケットおよび切断パケットの場合、IoT Hub は **操作の監視** チャネルでイベントを発行します。 このイベントには、接続の問題をトラブルシューティングするために役立つ追加情報があります。

デバイス アプリは、**CONNECT** パケットに **Will** メッセージを指定できます。 また、デバイス アプリは、`devices/{device_id}/messages/events/` または `devices/{device_id}/messages/events/{property_bag}` を **Will** トピック名として使用することで、テレメトリ メッセージとして転送される **Will** メッセージ を定義する必要があります。 この場合、ネットワーク接続が閉じているが、**DISCONNECT** パケットがデバイスからまだ受信されていなければ、IoT Hub は、**CONNECT** パケットで提供される **Will** メッセージをテレメトリ チャネルに送信します。 テレメトリ チャネルは、既定の **イベント** エンドポイントにすることも、IoT Hub ルーティングで定義されるカスタム エンドポイントにすることもできます。 メッセージには **iothub-MessageType** プロパティが含まれており、その値には **Will** が割り当てられています。

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>MQTT プロトコルの直接使用 (モジュールとして)

モジュール ID を使用して MQTT を介して IoT Hub に接続するのは、デバイス ([直接デバイスとして MQTT プロトコルを使用することに関するセクション](#using-the-mqtt-protocol-directly-as-a-device)で説明されています) と類似していますが、以下を使用する必要があります。

* クライアント ID を `{device_id}/{module_id}` に設定します。

* ユーザー名とパスワードを使用して認証する場合、ユーザー名を `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` に設定し、パスワードとしてモジュール ID に関連付けられている SAS トークンを使用します。

* 利用統計情報を公開するためのトピックとして `devices/{device_id}/modules/{module_id}/messages/events/` を使用します。

* WILL トピックとして `devices/{device_id}/modules/{module_id}/messages/events/` を使用します。

* ツインの GET トピックと PATCH トピックは、モジュールとデバイスと同じです。

* ツインの状態トピックは、モジュールとデバイスと同じです。

## <a name="tlsssl-configuration"></a>TLS または SSL の構成

MQTT プロトコルを直接使用するには、クライアントは TLS または SSL 経由で接続する "*必要があります*"。 この手順をスキップすると、接続エラーで失敗します。

TLS 接続を確立するには、DigiCert Baltimore Root 証明書 をダウンロードして参照する必要がある場合があります。 この証明書は、Azure が接続をセキュリティで保護するために使用するものの 1 つです。 この証明書は、[Azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) リポジトリ内で見つけることができます。 これらの証明書について詳しくは、[DigiCert の Web サイト](https://www.digicert.com/digicert-root-certificates.htm)をご覧ください。

Eclipse Foundation による Python バージョンの [Paho MQTT ライブラリ](https://pypi.python.org/pypi/paho-mqtt)を使用してこれを実装する方法の例は、次のようになります。

最初に、コマンドライン環境から Paho ライブラリをインストールします。

```cmd/sh
pip install paho-mqtt
```

次に、Python スクリプトでクライアントを実装します。 プレースホルダーを次のように置き換えます。

* `<local path to digicert.cer>` は、DigiCert Baltimore ルート証明書を含むローカル ファイルのパスです。 このファイルは、証明書情報を Azure IoT SDK for C の[certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) からコピーすることで作成できます。`-----BEGIN CERTIFICATE-----` 行と `-----END CERTIFICATE-----` 行を含め、すべての行の先頭と末尾の `"` 印を削除し、すべての行の末尾の `\r\n` 文字を削除します。

* `<device id from device registry>` は、IoT ハブに追加したデバイスの ID です。

* `<generated SAS token>` は、この記事で前述のように作成されたデバイスの SAS トークンです。

* `<iot hub name>` は、IoT ハブの名前です。

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

デバイス証明書を使用して認証するには、上記のコード スニペットを次の変更で更新します (証明書ベースの認証を準備する方法については、「[X.509 CA 証明書の入手方法](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate)」を参照してください)。

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージの送信

接続に成功したら、デバイスから IoT Hub に `devices/{device_id}/messages/events/` または `devices/{device_id}/messages/events/{property_bag}` を **トピック名** として使用してメッセージを送信できます。 `{property_bag}` 要素を使用すると、デバイスは追加のプロパティ付きのメッセージを URL エンコード形式で送信できるようになります。 次に例を示します。

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> この `{property_bag}` 要素では、HTTPS プロトコルでのクエリ文字列と同じエンコードを使用します。

IoT Hub 実装固有のビヘイビアーのリストを以下に示します。

* IoT Hub では、QoS 2 メッセージはサポートされません。 デバイス アプリから **QoS 2** を使用したメッセージが発行されると、IoT Hub はネットワーク接続を閉じます。

* IoT Hub では、Retain メッセージは保持されません。 デバイスで **RETAIN** フラグを 1 に設定してメッセージが送信されると、IoT Hub によってそのメッセージに **mqtt-retain** アプリケーション プロパティが追加されます。 この場合、IoT Hub は、Retain メッセージを保持するのではなく、バックエンド アプリにそのメッセージを渡します。

* IoT Hub は、デバイスごとにアクティブな MQTT 接続を 1 つだけサポートします。 同じデバイス ID の代わりに新しい MQTT 接続を実行すると、IoT Hub によって既存の接続が削除され、**400027 ConnectionForcefullyClosedOnNewConnection** が IoT Hub ログに記録されます


詳しくは、[メッセージング開発者のガイド](iot-hub-devguide-messaging.md)をご覧ください。

## <a name="receiving-cloud-to-device-messages"></a>クラウドからデバイスへのメッセージの受信

IoT Hub からメッセージを受信するには、デバイスで、`devices/{device_id}/messages/devicebound/#` を **トピック フィルター** として使用してサブスクライブする必要があります。 トピック フィルター内の複数レベルのワイルドカード `#` は、デバイスがトピック名の追加プロパティを受信できるようにするためにのみ使用されます。 IoT Hub では、`#` または `?` ワイルドカードを使用してサブトピックをフィルター処理することはできません。 IoT Hub はパブリッシャーとサブスクライバー間の汎用メッセージング ブローカーではないため、ドキュメント化されたトピック名とトピック フィルターのみをサポートします。

デバイスは、`devices/{device_id}/messages/devicebound/#` トピック フィルターで表されるデバイス固有のエンドポイントへのサブスクライブが成功するまで、IoT Hub からメッセージを受信することはありません。 サブスクリプションが確立した後、デバイスは、サブスクリプション後に送信された cloud-to-device メッセージのみを受信します。 デバイスが **CleanSession** フラグを **0** に設定した状態で接続している場合、サブスクリプションは複数のセッションで保持されます。 この場合、次回 **CleanSession 0** の状態で接続したときに、デバイスは、切断中にデバイスに対して送信された未処理メッセージを受信します。 ただし、デバイスが **CleanSession** フラグを **1** に設定して使用している場合は、デバイス エンドポイントにサブスクライブするまで、デバイスが IoT Hub からメッセージを受信することはありません。

IoT Hub では、メッセージは **トピック名** `devices/{device_id}/messages/devicebound/` で配信されます。メッセージの プロパティがある場合は、`devices/{device_id}/messages/devicebound/{property_bag}` で配信されます。 `{property_bag}` には、メッセージ プロパティの URL でエンコードされた値/キーのペアが含まれています。 プロパティ バッグに含められるのは、アプリケーション プロパティとユーザーが設定可能なシステム プロパティ (**messageId**、**correlationId** など) のみです。 システム プロパティの名前にはプレフィックス **$** が付きます。アプリケーション プロパティでは、プレフィックスのない元々のプロパティ名が使用されます。 プロパティ バッグの形式に関するその他の詳細については、「[デバイスからクラウドへのメッセージの送信](#sending-device-to-cloud-messages)」を参照してください。

クラウドからデバイスへのメッセージでは、プロパティ バッグの値は次の表に示されるように表記します。

| プロパティ値 | [表記] | 説明 |
|----|----|----|
| `null` | `key` | プロパティ バッグにキーのみが含まれる |
| 空の文字列 | `key=` | キーの後に等号が続き、値はない |
| null 以外の空でない値 | `key=value` | キーの後に等号と値が続く |

以下の例では、次の 3 つのアプリケーション プロパティを含むプロパティ バッグを示します。値が `null` の **prop1**、空の文字列 ("") の **prop2**、"a string" という値の **prop3**。

```mqtt
/?prop1&prop2=&prop3=a%20string
```

デバイス アプリが **QoS 2** を使用してトピックをサブスクライブしている場合、IoT Hub は **SUBACK** パケットに最大の QoS レベル 1 を許可します。 その後、IoT Hub は QoS 1 を使用してデバイスにメッセージを配信します。

## <a name="retrieving-a-device-twins-properties"></a>デバイス ツインのプロパティの取得

まずデバイスが、操作の応答を受信するために、`$iothub/twin/res/#` にサブスクライブします。 次に、デバイスは、空のメッセージをトピック `$iothub/twin/GET/?$rid={request id}` に送信します (**要求 ID** に値を指定します)。 その後サービスが、要求と同じ **要求 ID** を使用して、トピック `$iothub/twin/res/{status}/?$rid={request id}` のデバイス ツイン データを含む応答メッセージを送信します。

要求 ID には、[IoT Hub メッセージング開発者のガイド](iot-hub-devguide-messaging.md)に従って、メッセージ プロパティ値として有効な任意の値を指定できます。ステータスは、整数として検証されます。

次の応答の例で示されているように、応答本文にはデバイス ツインのプロパティ セクションが含まれます。

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

使用できる状態コードは次のとおりです。

|Status | 説明 |
| ----- | ----------- |
| 200 | Success |
| 429 | 要求が多すぎます (スロットル)。[IoT Hub スロットル](iot-hub-devguide-quotas-throttling.md)に関するページを参照してください。 |
| 5** | サーバー エラー |

詳細については、[デバイス ツイン開発者ガイド](iot-hub-devguide-device-twins.md)をご覧ください。

## <a name="update-device-twins-reported-properties"></a>デバイス ツインの報告されるプロパティの更新

報告されたプロパティを更新するために、デバイスでは、指定した MQTT トピック経由の公開を介して IoT Hub に要求が発行されます。 要求を処理した後、IoT Hub は、別のトピックへの公開を介して、更新操作の成功または失敗の状態を応答します。 デバイスでは、そのツイン更新要求の結果を通知するために、このトピックをサブスクライブできます。 この種類の要求/応答のやり取りを MQTT 上で実装するために、その更新要求内でデバイスによって最初に提供される要求 ID (`$rid`) の概念を利用します。 この要求 ID はIoT Hub からの応答にも含まれ、デバイスが以前の特定の要求に応答を関連付けることができるようにします。

IoT Hub のデバイス ツインで報告されるプロパティをデバイスが更新する手順を次に示します。

1. デバイスはまず、`$iothub/twin/res/#` トピックをサブスクライブして、IoT Hub から操作の応答を受信する必要があります。

2. デバイスは、デバイス ツインの更新を含むメッセージを、`$iothub/twin/PATCH/properties/reported/?$rid={request id}` トピックに送信します。 このメッセージには、**要求 ID** の値が含まれます。

3. サービスは、トピック `$iothub/twin/res/{status}/?$rid={request id}` で報告されたプロパティ コレクションの新しい ETag 値を含む応答メッセージを送信します。 この応答メッセージでは、要求と同じ **要求 ID** が使われます。

要求メッセージの本文には、報告されるプロパティの新しい値を含む JSON ドキュメントが含まれています。 JSON ドキュメントの各メンバーは、デバイス ツインのドキュメントの対応するメンバーを更新または追加します。 メンバーを `null` に設定した場合、メンバーは包含オブジェクトから削除されます。 次に例を示します。

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

使用できる状態コードは次のとおりです。

|Status | 説明 |
| ----- | ----------- |
| 204 | 成功 (コンテンツは返されません) |
| 400 | 正しくない要求。 無効な形式の JSON |
| 429 | 要求が多すぎます (スロットル)。[IoT Hub スロットル](iot-hub-devguide-quotas-throttling.md)に関するページを参照してください。 |
| 5** | サーバー エラー |

以下の python のコード スニペットは、(Paho MQTT クライアントを使用した) MQTT 経由でのツインの報告されたプロパティの更新プロセスを示しています。

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

上記のツインの報告されたプロパティの更新操作が成功すると、IoT Hub からの発行メッセージには次のトピックが含まれます: `$iothub/twin/res/204/?$rid=1&$version=6`。ここで、`204` は成功を示す状態コードで、`$rid=1` はコード内のデバイスによって提供された要求 ID に対応します。`$version` は、更新後のデバイス ツインの報告されたプロパティ セクションのバージョンに対応します。

詳細については、[デバイス ツイン開発者ガイド](iot-hub-devguide-device-twins.md)をご覧ください。

## <a name="receiving-desired-properties-update-notifications"></a>必要なプロパティの更新通知の受信

デバイスが接続されると、IoT Hub は、ソリューション バックエンドによって実行された更新の内容を含むトピック `$iothub/twin/PATCH/properties/desired/?$version={new version}` に通知を送信します。 次に例を示します。

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

プロパティの更新の場合、値 `null` は、JSON オブジェクト メンバーが削除されることを意味します。 また、`$version` がツインの望ましいプロパティ セクションの新しいバージョンを示していることに注意してください。

> [!IMPORTANT]
> IoT Hub は、デバイスが接続されている場合にのみ変更通知を生成します。 IoT Hub とデバイス アプリ間で目的のプロパティが同期された状態を保つには、[デバイス再接続フロー](iot-hub-devguide-device-twins.md#device-reconnection-flow)を必ず実装してください。

詳細については、[デバイス ツイン開発者ガイド](iot-hub-devguide-device-twins.md)をご覧ください。

## <a name="respond-to-a-direct-method"></a>ダイレクト メソッドへの応答

最初に、デバイスは `$iothub/methods/POST/#` にサブスクライブする必要があります。 IoT Hub は、有効な JSON または空の本文と共にメソッド要求をトピック `$iothub/methods/POST/{method name}/?$rid={request id}` に送信します。

応答するために、デバイスは、有効な JSON を含むメッセージまたは 空の本文をトピック `$iothub/methods/res/{status}/?$rid={request id}` に送信します。 このメッセージでは、**要求 ID** は、要求メッセージ内のものと一致する必要があり、**ステータス** は整数である必要があります。

詳細については、[ダイレクト メソッド開発者のガイド](iot-hub-devguide-direct-methods.md)をご覧ください。

## <a name="additional-considerations"></a>その他の注意点

最終的な考慮事項として、クラウド側で MQTT プロトコルの動作をカスタマイズする必要がある場合は、[Azure IoT プロトコル ゲートウェイ](iot-hub-protocol-gateway.md)を確認する必要があります。 このソフトウェアによって、IoT Hub と直接連携する高パフォーマンスのカスタム プロトコル ゲートウェイを展開できます。 Azure IoT プロトコル ゲートウェイでは、ブラウンフィールド MQTT デプロイメントまたは他のカスタム プロトコルに応じてデバイス プロトコルをカスタマイズすることができます。 ただし、このアプローチでは、カスタム プロトコル ゲートウェイを実行して運用する必要があります。

## <a name="next-steps"></a>次のステップ

MQTT プロトコルについて詳しくは、[MQTT のドキュメント](https://mqtt.org/)をご覧ください。

IoT Hub のデプロイの計画に関する詳細については、以下をご覧ください。

* [IoT デバイス カタログ向けの Azure 認定](https://catalog.azureiotsolutions.com/)
* [その他のプロトコルのサポート](iot-hub-protocol-gateway.md)
* [Event Hubs との比較](iot-hub-compare-event-hubs.md)
* [スケーリング、HA、および DR](iot-hub-scaling.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)
* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/quickstart-linux.md)
