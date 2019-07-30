---
title: Azure IoT Hub の AMQP サポートについて | Microsoft Docs
description: 開発者ガイド - AMQP プロトコルを使用して IoT Hub デバイスに接続されているエンドポイントとサービスに接続されているエンドポイントに接続するデバイスをサポートします。 Azure IoT device SDK での組み込み AMQP サポートについての情報が含まれます。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: b53bb0f04bf6a739b588b14febd622f6bf7a6a63
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68354897"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>AMQP プロトコルを使用した IoT Hub との通信

Azure IoT Hub では、デバイスに接続されているエンドポイントとサービスに接続されているエンドポイントからさまざまな機能を提供するために、 [OASIS Advanced Message Queuing Protocol (AMQP) バージョン 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) をサポートします。 このドキュメントでは、IoT Hub の機能を使用するために、IoT ハブに接続する AMQP クライアントの使用について説明します。

## <a name="service-client"></a>サービス クライアント

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>IoT ハブに接続して認証する (サービス クライアント)

AMQP を使用して IoT ハブに接続するには、クライアントは[クレーム ベース セキュリティ (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) または[簡易認証およびセキュリティ層 (SASL) の認証](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)を使用できます。

サービス クライアントでは、次の情報が必要になります。

| Information | 値 |
|-------------|--------------|
| IoT ハブ ホスト名 | `<iot-hub-name>.azure-devices.net` |
| キー名 | `service` |
| アクセス キー | サービスに関連付けられたプライマリ キーまたはセカンダリ キー |
| 共有アクセス署名 | 次の形式の、存続期間が短い共有アクセス署名: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`。 この署名を生成するためのコードを入手するには、「[IoT Hub へのアクセスの制御](./iot-hub-devguide-security.md#security-token-structure)」を参照してください。

次のコード スニペットでは、[Python で uAMQP ライブラリ](https://github.com/Azure/azure-uamqp-python)を使用して、送信者リンク経由で IoT ハブに接続します。

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>cloud-to-device メッセージを呼び出す (サービス クライアント)

サービスと IoT ハブ間およびデバイスと IoT ハブ間の cloud-to-device メッセージ交換の詳細については、「[cloud-to-device メッセージを IoT Hub から送信する](iot-hub-devguide-messages-c2d.md)」を参照してください。 次の表で説明しているように、サービス クライアントでは 2 つのリンクを使用して、メッセージを送信したり、以前にデバイスから送信されたメッセージに対するフィードバックを受信したりします。

| 作成者 | リンクの種類 | リンク パス | 説明 |
|------------|-----------|-----------|-------------|
| Service | 送信者リンク | `/messages/devicebound` | デバイスが宛先の cloud-to-device メッセージは、サービスによってこのリンクに送信されます。 このリンク経由で送信されたメッセージには、ターゲット デバイスの受信者リンク パスである `/devices/<deviceID>/messages/devicebound` に設定された `To` プロパティがあります。 |
| Service | 受信者リンク | `/messages/serviceBound/feedback` | デバイスから取得する完了、拒否、破棄のフィードバック メッセージは、サービスによってこのリンクで受信されます。 フィードバック メッセージの詳細については、「[cloud-to-device メッセージを IoT Hub から送信する](./iot-hub-devguide-messages-c2d.md#message-feedback)」を参照してください。 |

次のコード スニペットは、[Python の uAMQP ライブラリ](https://github.com/Azure/azure-uamqp-python)を使用して、cloud-to-device メッセージを作成し、デバイスに送信する方法を示しています。

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

フィードバックを受信するために、サービス クライアントによって受信者リンクが作成されます。 次のコード スニペットは、[Python の uAMQP ライブラリ](https://github.com/Azure/azure-uamqp-python)を使用してリンクを作成する方法を示しています。

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

前のコードで示しているように、cloud-to-device フィードバック メッセージのコンテンツ タイプは *application/vnd.microsoft.iothub.feedback.json* です。 メッセージの JSON 本文のプロパティを使用して、元のメッセージの配信状態を推測できます。

* フィードバック本文のキー `statusCode` は、次のいずれかの値になります: *Success* (成功)、*Expired* (有効期限切れ)、*DeliveryCountExceeded* (配信数超過)、*Rejected* (拒否)、または *Purged* (消去済み)。

* フィードバックの本文のキー `deviceId` には、ターゲット デバイスの ID が含まれます。

* フィードバックの本文のキー `originalMessageId` には、サービスによって送信された元の cloud-to-device メッセージの ID が含まれます。 この配信状態を使用して、フィードバックを cloud-to-device メッセージに関連付けることができます。

### <a name="receive-telemetry-messages-service-client"></a>テレメトリのメッセージの受信 (サービス クライアント)

既定では、IoT ハブは取り込まれたデバイス テレメトリ メッセージを組み込みのイベント ハブ内に格納します。 サービス クライアントは AMQP プロトコルを使用して、格納されたイベントを受信できます。

そのため、サービス クライアントは最初に IoT ハブ エンドポイントに接続し、組み込みのイベント ハブへのリダイレクト アドレスを受け取る必要があります。 その後、サービス クライアントは提供されたアドレスを使用して、組み込みのイベント ハブに接続します。

各手順で、クライアントは次の情報を提示する必要があります。

* 有効なサービス資格情報 (サービス共有アクセス署名トークン)。

* メッセージの取得元となるコンシューマー グループ パーティションへの適切な形式のパス。 指定のコンシューマー グループおよびパーティション ID について、パスは次の形式になります: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (既定のコンシューマー グループは `$Default`)。

* パーティション内の開始点を指定するための省略可能なフィルター処理述語。 この述語は、シーケンス番号、オフセット、またはキューに格納されたタイムスタンプの形式で指定できます。

次のコード スニペットでは、[Python の uAMQP ライブラリ](https://github.com/Azure/azure-uamqp-python)を使用して前述の手順を示しています。

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

指定のデバイス ID に対して、IoT ハブはデバイス ID のハッシュを使用して、そのメッセージを格納するパーティションを判断します。 上記のコード スニペットでは、そのような単一のパーティションからイベントを受信する方法を示しています。 ただし、一般的なアプリケーションは多くの場合、すべてのイベント ハブ パーティションに格納されているイベントを取得する必要があることに注意してください。

## <a name="device-client"></a>デバイス クライアント

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>IoT ハブに接続して認証する (デバイス クライアント)

AMQP を使用して IoT ハブに接続するには、デバイスで[クレーム ベース セキュリティ (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) または[簡易認証およびセキュリティ層 (SASL) の認証](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)を使用できます。

デバイス クライアントでは、次の情報が必要です。

| Information | 値 |
|-------------|--------------|
| IoT ハブ ホスト名 | `<iot-hub-name>.azure-devices.net` |
| アクセス キー | デバイスに関連付けられたプライマリ キーまたはセカンダリ キー |
| 共有アクセス署名 | 次の形式の、存続期間が短い共有アクセス署名: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`。 この署名を生成するためのコードを入手するには、「[IoT Hub へのアクセスの制御](./iot-hub-devguide-security.md#security-token-structure)」を参照してください。

次のコード スニペットでは、[Python で uAMQP ライブラリ](https://github.com/Azure/azure-uamqp-python)を使用して、送信者リンク経由で IoT ハブに接続します。

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

次のリンク パスは、デバイス操作としてサポートされます。

| 作成者 | リンクの種類 | リンク パス | 説明 |
|------------|-----------|-----------|-------------|
| デバイス | 受信者リンク | `/devices/<deviceID>/messages/devicebound` | デバイスが宛先の cloud-to-device メッセージは、各宛先デバイスによってこのリンクで受信されます。 |
| デバイス | 送信者リンク | `/devices/<deviceID>messages/events` | デバイスから送信された cloud-to-device メッセージは、このリンク経由で送信されます。 |
| デバイス | 送信者リンク | `/messages/serviceBound/feedback` | cloud-to-device メッセージのフィードバックは、デバイスによってこのリンク経由でサービスに送信されます。 |

### <a name="receive-cloud-to-device-commands-device-client"></a>cloud-to-device コマンドを受信する (デバイス クライアント)

デバイスに送信される cloud-to-device コマンドは、`/devices/<deviceID>/messages/devicebound` リンクで到着します。 デバイスではバッチでこれらのメッセージを受信することができ、必要に応じて、メッセージのデータ ペイロード、メッセージ プロパティ、注釈、またはメッセージのアプリケーション プロパティを使用できます。

次のコード スニペットでは、[Python の uAMQP ライブラリ](https://github.com/Azure/azure-uamqp-python)を使用して、デバイスによる cloud-to-device メッセージを受信します。

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>テレメトリのメッセージの送信 (デバイス クライアント)

AMQP を使用してデバイスからテレメトリ メッセージを送信することもできます。 デバイスでは必要に応じて、アプリケーション プロパティのディクショナリ、またはさまざまなメッセージ プロパティ (メッセージ ID など) を提供できます。

次のコード スニペットでは、[Python の uAMQP ライブラリ](https://github.com/Azure/azure-uamqp-python)を使用して、デバイスから cloud-to-device メッセージを送信します。

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>その他のメモ

* AMQP 接続は、ネットワーク障害や、(コード内で生成された) 認証トークンの有効期限切れのために、中断される可能性があります。 サービス クライアントでは、これらの状況に対処し、必要に応じて、接続とリンクを再確立する必要があります。 認証トークンの有効期限切れの場合、クライアントは、有効期限が切れる前に先を見越してトークンを更新することによって、接続の解除を回避できます。

* クライアントは時として、リンクのリダイレクトを正しく処理できる必要があります。 そのような操作を理解するには、AMQP クライアントのドキュメントを参照してください。

## <a name="next-steps"></a>次の手順

AMQP プロトコルの詳細については、[AMQP v1.0 の仕様](https://www.amqp.org/sites/amqp.org/files/amqp.pdf)を参照してください。

IoT Hub のメッセージングの詳細については、以下を参照してください。

* [cloud-to-device メッセージ](./iot-hub-devguide-messages-c2d.md)
* [Support for additional protocols](iot-hub-protocol-gateway.md) (その他のプロトコルのサポート)
* [Support for the Message Queuing Telemetry Transport (MQTT) Protocol](./iot-hub-mqtt-support.md) (Message Queuing Telemetry Transport (MQTT) プロトコルのサポート)