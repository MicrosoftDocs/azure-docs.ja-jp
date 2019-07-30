---
title: Azure IoT Hub の MQTT サポートについて | Microsoft Docs
description: 開発者ガイド - MQTT プロトコルを使用して IoT Hub デバイスに接続されているエンドポイントに接続するデバイスのサポート。 Azure IoT デバイス SDK での組み込み MQTT サポートについての情報も含まれます。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 238627f5cb57a614d5f82f0eeba1ea6b4e32f458
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445476"
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

## <a name="using-the-device-sdks"></a>デバイス SDK の使用

MQTT プロトコルをサポートする[デバイス SDK](https://github.com/Azure/azure-iot-sdks) は、Java、Node.js、C、C#、および Python に対応しています。 デバイス SDK では、標準的な IoT Hub 接続文字列を使用して、IoT ハブへの接続を確立します。 MQTT プロトコルを使用するには、クライアント プロトコル パラメーターを **MQTT**に設定する必要があります。 デバイス SDK は既定では、**CleanSession** フラグを **0** に設定して IoT ハブに接続し、IoT ハブとのメッセージ交換には **QoS 1** を使用します。

デバイスは、IoT ハブに接続されると、デバイス SDK によって、IoT ハブとのメッセージの交換を可能にするメソッドが提供されます。

次の表では、サポートされている各言語のコード サンプルへのリンクを提供すると共に、MQTT プロトコルを使用して IoT Hub への接続を確立するために使用するパラメーターを示します。

| 言語 | プロトコル パラメーター |
| --- | --- |
| [Node.JS](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |azure-iot-device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol.MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType.Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples) |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>デバイス アプリの AMQP から MQTT への移行

[デバイス SDK](https://github.com/Azure/azure-iot-sdks) を使用している場合、AMQP から MQTT に使用を切り替えるには、前述のようにクライアントの初期化時にプロトコル パラメーターを変更する必要があります。

これを行う際は、次の項目をご確認ください。

* AMQP では、MQTT 接続を終了するときに、多くの条件のエラーが返されます。 そのため、例外処理のロジックを一部変更する必要が生じることがあります。

* MQTT は、[クラウドからデバイスへのメッセージ](iot-hub-devguide-messaging.md)を受信するときの "*拒否*" 操作をサポートしていません。 バックエンド アプリではデバイス アプリから応答を受信する必要がある場合、[ダイレクト メソッド](iot-hub-devguide-direct-methods.md)の使用を検討してください。

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>MQTT プロトコルの直接使用 (デバイスとして)

デバイスでデバイス SDK を使用できない場合でも、MQTT プロトコルをポート 8883 で使用して、デバイスをパブリックのデバイス エンドポイントに接続できます。 **CONNECT** パケットの場合、デバイスでは次の値を使用する必要があります。

* **ClientId** フィールドには、**deviceId** を使用します。

* **[Usename]** フィールドには、`{iothubhostname}/{device_id}/?api-version=2018-06-30` を使用します。`{iothubhostname}` は IoT Hub の完全な CName です。

    たとえば、IoT Hub の名前が **contoso.azure-devices.net** であり、デバイスの名前が **MyDevice01** であるとすると、**Username** フィールドの内容は 次のようになります。

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* **[Password]** フィールドには、SAS トークンを使用します。 SAS トークンの形式は、HTTPS プロトコルや AMQP プロトコルの場合と同じです。

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > X.509 証明書の認証を使用する場合は、SAS トークン パスワードは不要です。 詳細については、「[Azure IoT Hub での X.509 セキュリティの設定](iot-hub-security-x509-get-started.md)」をご覧ください

  SAS トークンの生成方法について詳しくは、[IoT Hub のセキュリティ トークンの使用](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)に関するページにあるデバイスのセクションをご覧ください。

  テストするときは、クロスプラットフォームの [Visual Studio Code 対応の Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) か、[Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ツールを使用して、SAS トークンをすばやく生成し、それをコピーして独自のコードに貼り付けることもできます。

Azure IoT Tools の場合:

1. Visual Studio Code の左下隅にある **[Azure IoT ハブ デバイス]** タブを展開します。
  
2. デバイスを右クリックし、 **[Generate SAS Token for Device]\(デバイスの SAS トークンの生成\)** を選択します。
  
3. **[期限]** を設定し、Enter キーを押します。
  
4. SAS トークンが作成され、クリップボードにコピーされます。

デバイス エクスプローラーの場合:

1. **デバイス エクスプローラー**で **[管理]** タブに移動します。

2. **[SAS トークン]** (右上) をクリックします。

3. **[SASTokenForm]** の **[DeviceID]** ドロップダウンでデバイスを選択します。 **[TTL]** を設定します。

4. **[生成]** をクリックしてトークンを作成します。

   生成される SAS トークンは、次のような構成になります。

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   MQTT を使用して接続するための **[Password]** フィールドとして使用されるこのトークンの一部は次のようになります。

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT の接続パケットおよび切断パケットの場合、IoT Hub は **操作の監視** チャネルでイベントを発行します。 このイベントには、接続の問題をトラブルシューティングするために役立つ追加情報があります。

デバイス アプリは、**CONNECT** パケットに **Will** メッセージを指定できます。 また、デバイス アプリは、`devices/{device_id}/messages/events/` または `devices/{device_id}/messages/events/{property_bag}` を **Will** トピック名として使用することで、テレメトリ メッセージとして転送される **Will** メッセージ を定義する必要があります。 この場合、ネットワーク接続が閉じているが、**DISCONNECT** パケットがデバイスからまだ受信されていなければ、IoT Hub は、**CONNECT** パケットで提供される **Will** メッセージをテレメトリ チャネルに送信します。 テレメトリ チャネルは、既定の**イベント** エンドポイントにすることも、IoT Hub ルーティングで定義されるカスタム エンドポイントにすることもできます。 メッセージには **iothub-MessageType** プロパティが含まれており、その値には **Will** が割り当てられています。

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>MQTT プロトコルの直接使用 (モジュールとして)

モジュール ID を使用して MQTT を介して IoT Hub に接続するのは、デバイス ([上](#using-the-mqtt-protocol-directly-as-a-device)で説明されています) と類似していますが、以下を使用する必要があります。

* クライアント ID を `{device_id}/{module_id}` に設定します。

* ユーザー名とパスワードを使用して認証する場合、ユーザー名を `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` に設定し、パスワードとしてモジュール ID に関連付けられている SAS トークンを使用します。

* 利用統計情報を公開するためのトピックとして `devices/{device_id}/modules/{module_id}/messages/events/` を使用します。

* WILL トピックとして `devices/{device_id}/modules/{module_id}/messages/events/` を使用します。

* ツインの GET トピックと PATCH トピックは、モジュールとデバイスと同じです。

* ツインの状態トピックは、モジュールとデバイスと同じです。

### <a name="tlsssl-configuration"></a>TLS または SSL の構成

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

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージの送信

接続に成功したら、デバイスから IoT Hub に `devices/{device_id}/messages/events/` または `devices/{device_id}/messages/events/{property_bag}` を**トピック名**として使用してメッセージを送信できます。 `{property_bag}` 要素を使用すると、デバイスは追加のプロパティ付きのメッセージを URL エンコード形式で送信できるようになります。 例:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> この `{property_bag}` 要素では、HTTPS プロトコルでのクエリ文字列と同じエンコードを使用します。

IoT Hub 実装固有のビヘイビアーのリストを以下に示します。

* IoT Hub では、QoS 2 メッセージはサポートされません。 デバイス アプリから **QoS 2** を使用したメッセージが発行されると、IoT Hub はネットワーク接続を閉じます。

* IoT Hub では、Retain メッセージは保持されません。 デバイスが **RETAIN** フラグを 1 に設定してメッセージを送信すると、IoT Hub はそのメッセージに **x-opt-retain** アプリケーション プロパティを追加します。 この場合、IoT Hub は、Retain メッセージを保持するのではなく、バックエンド アプリにそのメッセージを渡します。

* IoT Hub は、デバイスごとにアクティブな MQTT 接続を 1 つだけサポートします。 同じデバイス ID で新しい MQTT 接続が行われると、IoT Hub は既存の接続を破棄します。

詳しくは、[メッセージング開発者のガイド](iot-hub-devguide-messaging.md)をご覧ください。

### <a name="receiving-cloud-to-device-messages"></a>クラウドからデバイスへのメッセージの受信

IoT Hub からメッセージを受信するには、デバイスで、`devices/{device_id}/messages/devicebound/#` を**トピック フィルター**として使用してサブスクライブする必要があります。 トピック フィルター内の複数レベルのワイルドカード `#` は、デバイスがトピック名の追加プロパティを受信できるようにするためにのみ使用されます。 IoT Hub では、`#` または `?` ワイルドカードを使用してサブトピックをフィルター処理することはできません。 IoT Hub はパブリッシャーとサブスクライバー間の汎用メッセージング ブローカーではないため、ドキュメント化されたトピック名とトピック フィルターのみをサポートします。

デバイスは、`devices/{device_id}/messages/devicebound/#` トピック フィルターで表されるデバイス固有のエンドポイントへのサブスクライブが成功するまで、IoT Hub からメッセージを受信することはありません。 サブスクリプションが確立した後、デバイスは、サブスクリプション後に送信された cloud-to-device メッセージのみを受信します。 デバイスが **CleanSession** フラグを **0** に設定した状態で接続している場合、サブスクリプションは複数のセッションで保持されます。 この場合、次回 **CleanSession 0** の状態で接続したときに、デバイスは、切断中にデバイスに対して送信された未処理メッセージを受信します。 ただし、デバイスが **CleanSession** フラグを **1** に設定して使用している場合は、デバイス エンドポイントにサブスクライブするまで、デバイスが IoT Hub からメッセージを受信することはありません。

IoT Hub は、**トピック名** `devices/{device_id}/messages/devicebound/` またはメッセージ プロパティがある場合は `devices/{device_id}/messages/devicebound/{property_bag}` のメッセージを配信します。 `{property_bag}` には、メッセージ プロパティの URL でエンコードされた値/キーのペアが含まれています。 プロパティ バッグに含められるのは、アプリケーション プロパティとユーザーが設定可能なシステム プロパティ (**messageId**、**correlationId** など) のみです。 システム プロパティの名前にはプレフィックス **$** が付きます。アプリケーション プロパティでは、プレフィックスのない元々のプロパティ名が使用されます。

デバイス アプリが **QoS 2** を使用してトピックをサブスクライブしている場合、IoT Hub は **SUBACK** パケットに最大の QoS レベル 1 を許可します。 その後、IoT Hub は QoS 1 を使用してデバイスにメッセージを配信します。

### <a name="retrieving-a-device-twins-properties"></a>デバイス ツインのプロパティの取得

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
| 204 | 成功 (コンテンツは返されません) |
| 429 | 要求が多すぎます (スロットル)。[IoT Hub スロットル](iot-hub-devguide-quotas-throttling.md)に関するページを参照してください。 |
| 5** | サーバー エラー |

詳細については、[デバイス ツイン開発者のガイド](iot-hub-devguide-device-twins.md)をご覧ください。

### <a name="update-device-twins-reported-properties"></a>デバイス ツインの報告されるプロパティの更新

報告されたプロパティを更新するために、デバイスでは、指定した MQTT トピック経由の公開を介して IoT Hub に要求が発行されます。 要求を処理した後、IoT Hub は、別のトピックへの公開を介して、更新操作の成功または失敗の状態を応答します。 デバイスでは、そのツイン更新要求の結果を通知するために、このトピックをサブスクライブできます。 この種類の要求/応答のやり取りを MQTT 上で実装するために、その更新要求内でデバイスによって最初に提供される要求 ID (`$rid`) の概念を利用します。 この要求 ID は、デバイスが以前の特定の要求に応答を関連付けられるようにするために IoT Hub からの応答にも含まれます。

IoT Hub のデバイス ツインで報告されるプロパティをデバイスが更新する手順を次に示します。

1. デバイスはまず、`$iothub/twin/res/#` トピックをサブスクライブして、IoT Hub から操作の応答を受信する必要があります。

2. デバイスは、デバイス ツインの更新を含むメッセージを、`$iothub/twin/PATCH/properties/reported/?$rid={request id}` トピックに送信します。 このメッセージには、**要求 ID** の値が含まれます。

3. サービスは、トピック `$iothub/twin/res/{status}/?$rid={request id}` で報告されたプロパティ コレクションの新しい ETag 値を含む応答メッセージを送信します。 この応答メッセージでは、要求と同じ**要求 ID** が使われます。

要求メッセージの本文には、報告されるプロパティの新しい値を含む JSON ドキュメントが含まれています。 JSON ドキュメントの各メンバーは、デバイス ツインのドキュメントの対応するメンバーを更新または追加します。 メンバーを `null` に設定した場合、メンバーは包含オブジェクトから削除されます。 例:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

使用できる状態コードは次のとおりです。

|Status | 説明 |
| ----- | ----------- |
| 200 | Success |
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
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" + rid, twin_reported_property_patch, qos=0)
```

上記のツインの報告されたプロパティの更新操作が成功すると、IoT Hub からの発行メッセージには次のトピックが含まれます: `$iothub/twin/res/204/?$rid=1&$version=6`。ここで、`204` は成功を示す状態コードで、`$rid=1` はコード内のデバイスによって提供された要求 ID に対応します。`$version` は、更新後のデバイス ツインの報告されたプロパティ セクションのバージョンに対応します。

詳細については、[デバイス ツイン開発者のガイド](iot-hub-devguide-device-twins.md)をご覧ください。

### <a name="receiving-desired-properties-update-notifications"></a>必要なプロパティの更新通知の受信

デバイスが接続されると、IoT Hub は、ソリューション バックエンドによって実行された更新の内容を含むトピック `$iothub/twin/PATCH/properties/desired/?$version={new version}` に通知を送信します。 例:

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

詳しくは、[デバイス ツイン開発者のガイド](iot-hub-devguide-device-twins.md)をご覧ください。

### <a name="respond-to-a-direct-method"></a>ダイレクト メソッドへの応答

最初に、デバイスは `$iothub/methods/POST/#` にサブスクライブする必要があります。 IoT Hub は、有効な JSON または空の本文と共にメソッド要求をトピック `$iothub/methods/POST/{method name}/?$rid={request id}` に送信します。

応答するために、デバイスは、有効な JSON を含むメッセージまたは 空の本文をトピック `$iothub/methods/res/{status}/?$rid={request id}` に送信します。 このメッセージでは、**要求 ID**は、要求メッセージ内のものと一致する必要があり、**ステータス**は整数である必要があります。

詳しくは、[ダイレクト メソッド開発者のガイド](iot-hub-devguide-direct-methods.md)をご覧ください。

### <a name="additional-considerations"></a>追加の考慮事項

最終的な考慮事項として、クラウド側で MQTT プロトコルの動作をカスタマイズする必要がある場合は、[Azure IoT プロトコル ゲートウェイ](iot-hub-protocol-gateway.md)を確認する必要があります。 このソフトウェアによって、IoT Hub と直接連携する高パフォーマンスのカスタム プロトコル ゲートウェイを展開できます。 Azure IoT プロトコル ゲートウェイでは、ブラウンフィールド MQTT デプロイメントまたは他のカスタム プロトコルに応じてデバイス プロトコルをカスタマイズすることができます。 ただし、このアプローチでは、カスタム プロトコル ゲートウェイを実行して運用する必要があります。

## <a name="next-steps"></a>次の手順

MQTT プロトコルについて詳しくは、[MQTT のドキュメント](https://mqtt.org/documentation)をご覧ください。

IoT Hub のデプロイの計画に関する詳細については、以下をご覧ください。

* [IoT デバイス カタログ向けの Azure 認定](https://catalog.azureiotsolutions.com/)
* [その他のプロトコルのサポート](iot-hub-protocol-gateway.md)
* [Event Hubs との比較](iot-hub-compare-event-hubs.md)
* [スケーリング、HA、および DR](iot-hub-scaling.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)
* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/tutorial-simulate-device-linux.md)
