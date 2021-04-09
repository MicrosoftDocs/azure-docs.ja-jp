---
title: Azure IoT Edge を使用した発行とサブスクライブ | Microsoft Docs
description: IoT Edge MQTT ブローカーを使用したメッセージの発行とサブスクライブ
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 730680b0cb6e8a728ed3072419674346de649368
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200685"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Aure IoT Edge を使用した発行とサブスクライブ

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Azure IoT Edge MQTT ブローカーを使用して、メッセージの発行とサブスクライブを行うことができます。 この記事では、このブローカーに接続し、ユーザー定義のトピックでメッセージを発行およびサブスクライブし、IoT Hub メッセージング プリミティブを使用する方法について説明します。 IoT Edge MQTT ブローカーは、IoT Edge ハブに組み込まれています。 詳細については、[IoT Edge ハブのブローカー機能](iot-edge-runtime.md)に関するページを参照してください。

> [!NOTE]
> IoT Edge MQTT ブローカーは、現在、パブリック プレビュー段階です。

## <a name="pre-requisites"></a>前提条件

- 有効なサブスクリプションがある Azure アカウント
- `azure-iot` CLI 拡張機能がインストールされている [Azure CLI](/cli/azure/)。 詳細については、[Azure CLI 用の Azure IoT 拡張機能のインストール手順](/cli/azure/azure-cli-reference-for-iot)に関するページを参照してください。
- SKU (F1、S1、S2、S3 のいずれか) の **IoT Hub**。
- **バージョン 1.2 以上の IoT Edge デバイス** があること。 IoT Edge MQTT ブローカーは現在パブリック プレビュー段階であるため、edgeHub コンテナーで次の環境変数を true に設定して、MQTT ブローカーを有効にします。

   | 名前 | 値 |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- IoT Edge デバイスにインストールされている **Mosquitto クライアント**。 この記事では、一般的な Mosquitto クライアント [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) と [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html) を使用します。 代わりに他の MQTT クライアントを使用することもできます。 Ubuntu デバイスに Mosquitto クライアントをインストールするには、次のコマンドを実行します。

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Mosquitto サーバーは、MQTT ポート (8883 と 1883) がブロックされ、IoT Edge ハブと競合する可能性があるため、インストールしないでください。

## <a name="connect-to-iot-edge-hub"></a>IoT Edge ハブに接続する

IoT Edge ハブに接続するには、[汎用の MQTT クライアントを使用した IoT Hub への接続に関する記事](../iot-hub/iot-hub-mqtt-support.md)または [IoT Edge ハブの概念説明に関する記事](iot-edge-runtime.md)に記載されているものと同じ手順に従ってください。 手順は次のとおりです。

1. 必要に応じて、MQTT クライアントが、トランスポート層セキュリティ (TLS) を使用して、IoT Edge ハブとの *セキュリティで保護された接続* を確立します
2. MQTT クライアントが IoT Edge ハブに対する自動 *認証* を行います
3. IoT Edge ハブが、承認ポリシーに従って、MQTT クライアントを *承認* します

### <a name="secure-connection-tls"></a>セキュリティで保護された接続 (TLS)

トランスポート層セキュリティ (TLS) は、クライアントと IoT Edge ハブ間の暗号化された通信を確立するために使用されます。

TLS を無効にするには、ポート 1883 (MQTT) を使用し、edgeHub コンテナーをポート 1883 にバインドします。

TLS を有効にするため、クライアントがポート 8883 (MQTTS) で MQTT ブローカーに接続すると、TLS チャネルが開始されます。 ブローカーは、クライアントが検証する必要がある証明書チェーンを送信します。 証明書チェーンを検証するためには、MQTT ブローカーのルート証明書が、信頼された証明書としてクライアントにインストールされている必要があります。 ルート証明書が信頼されていない場合、クライアント ライブラリは MQTT ブローカーによって拒否され、証明書の検証エラーが発生します。 クライアントにこのブローカーのルート証明書をインストールする手順は、[等価的なゲートウェイ](how-to-create-transparent-gateway.md)の場合と同じで、「[ダウンストリーム デバイスを準備する](how-to-connect-downstream-device.md#prepare-a-downstream-device)」のドキュメントで説明されています。

### <a name="authentication"></a>認証

MQTT クライアントが自動認証を行うには、まず、CONNECT パケットを MQTT ブローカーに送信して、その名前で接続を開始する必要があります。 このパケットは、`client identifier`、`username`、`password` の 3 つの認証情報を提供します。

- `client identifier` フィールドは、IoT Hub のデバイスまたはモジュールの名前です。 このツールでは、次の構文が使用されます。

  - デバイスの場合: `<device_name>`

  - モジュールの場合: `<device_name>/<module_name>`

   MQTT ブローカーに接続するには、デバイスまたはモジュールを IoT Hub に登録する必要があります。

   ブローカーでは、同じ資格情報を使用して複数のクライアントを接続することはできません。 2 つ目のクライアントが同じ資格情報を使用して接続すると、ブローカーは、既に接続されているクライアントを切断します。

- `username` フィールドは、デバイスまたはモジュールの名前と、デバイスが属している IoT Hub の名前から、次の構文を使用して取得されます。

  - デバイスの場合: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - モジュールの場合: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- CONNECT パケットの `password` フィールドは、認証モードによって異なります。

  - [対称キー認証](how-to-authenticate-downstream-device.md#symmetric-key-authentication)を使用する場合、`password` フィールドは SAS トークンです。
  - [x.509 自己署名認証](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)を使用する場合、`password` フィールドは存在しません。 この認証モードでは、TLS チャネルが必要です。 クライアントは、TLS 接続を確立するためにポート 8883 に接続する必要があります。 TLS ハンドシェイク中、MQTT ブローカーはクライアント証明書を要求します。 この証明書は、クライアントの ID を確認するために使用されます。そのため、後で CONNECT パケットが送信されるときに `password` フィールドが必要になることはありません。 クライアント証明書とパスワード フィールドの両方を送信すると、エラーが発生し、接続が閉じられます。 MQTT ライブラリと TLS クライアント ライブラリは、通常、接続を開始するときにクライアント証明書を送信する方法を備えています。 詳細な例については、[クライアント認証での X509 証明書の使用](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)に関するセクションを参照してください。

IoT Edge によってデプロイされたモジュールは[対称キー認証](how-to-authenticate-downstream-device.md#symmetric-key-authentication)を使用し、ローカルの [IoT Edge ワークロード API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) を呼び出して、オフラインのときでもプログラムによって SAS トークンを取得できます。

### <a name="authorization"></a>承認

MQTT クライアントが IoT Edge ハブに対して認証された後、接続を認可する必要があります。 接続された後は、特定のトピックを発行またはサブスクライブすることを承認する必要があります。 これらの承認は、承認ポリシーに基づいて、IoT Edge ハブによって付与されます。 承認ポリシーは、ツインを介して IoT Edge ハブに送信される JSON 構造として表現される一連のステートメントです。 IoT Edge ハブのツインを編集して、その承認ポリシーを構成します。

> [!NOTE]
> パブリック プレビューの場合、MQTT ブローカーの認可ポリシーの編集は、Visual Studio、Visual Studio Code または Azure CLI でのみ可能です。 Azure portal は、現在、IoT Edge ハブのツインとその承認ポリシーの編集をサポートしていません。

各認可ポリシー ステートメントは、`identities`、`allow` または `deny` の結果、`operations` および `resources` の組み合わせで構成されています。

- `identities` はポリシーの件名を記述します。 これは CONNECT パケット内のクライアントから送信された `client identifier` にマップする必要があります。
- `allow` または `deny` の結果は、操作を許可するか拒否するかを定義します。
- `operations` は承認するためのアクションを定義します。 `mqtt:connect`、`mqtt:publish`、`mqtt:subscribe` は、現在サポートされている 3 つのアクションです。
- `resources` はポリシーのオブジェクトを定義します。 これは [MQTT ワイルドカード](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)で定義されたトピックまたはトピック パターンにすることができます。

次に示すのは、"rogue_client" クライアントの接続を明示的に許可せず、任意の Azure IoT クライアントの接続を許可し、"sensor_1" がトピック `events/alerts` に発行するのを許可する承認ポリシーの例です。

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

承認ポリシーを作成するときは、留意すべき点がいくつかあります。

- `$edgeHub` ツイン スキーマ バージョン 1.2 が必要です
- 既定では、すべての操作が拒否されます。
- 認可ステートメントは、JSON 定義に出現する順序で評価されます。 まず、`identities` を確認してから、要求に一致する最初の allow または deny ステートメントを選択します。 allow と deny のステートメントの間で競合が発生した場合は、deny ステートメントが優先されます。
- 認可ポリシーでは、いくつかの変数 (置換など) を使用できます。
    - `{{iot:identity}}` は、現在接続されているクライアントの ID を表します。 たとえば、`myDevice` のようなデバイス ID や、`myEdgeDevice/SampleModule` のようなモジュール ID です。
    - `{{iot:device_id}}` は、現在接続されているデバイスの ID を表します。 たとえば、`myDevice` のようなデバイス ID や、`myEdgeDevice` のようなモジュールが実行されているデバイスの ID です。
    - `{{iot:module_id}}` は、現在接続されているモジュールの ID を表します。 この変数は、接続されているデバイスの場合は空白、または `SampleModule` のようなモジュール ID になります。
    - `{{iot:this_device_id}}` は、承認ポリシーを実行している IoT Edge デバイスの ID を表します。 たとえば、「 `myIoTEdgeDevice` 」のように入力します。

IoT ハブのトピックの認可は、ユーザー定義のトピックとは少し異なる方法で処理されます。 注意すべき重要な点は次のとおりです。

- IoT Edge ハブの MQTT ブローカーに接続するには、Azure IoT デバイスまたはモジュールに明示的な承認規則が必要です。 既定の接続承認ポリシーは次のとおりです。
- Azure IoT デバイスまたはモジュールは、既定では明示的な承認規則なしで独自の IoT ハブのトピックにアクセスできます。 ただし、この場合、承認は親/子関係から生じ、これらの関係を設定する必要があります。 IoT Edge モジュールは自動的にその IoT Edge デバイスの子として設定されますが、デバイスはその IoT Edge ゲートウェイの子として明示的に設定する必要があります。

次に示す既定の承認ポリシーは、すべての Azure IoT のデバイスまたはモジュールがブローカーに **接続** できるようにするために使用できます。

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

ここでは、IoT Edge MQTT ブローカーに接続する方法について説明しました。次は、最初にユーザー定義のトピックで、次に IoT ハブのトピックで、最後に別の MQTT ブローカーにメッセージを発行およびサブスクライブする方法について説明します。

## <a name="publish-and-subscribe-on-user-defined-topics"></a>ユーザー定義のトピックでの発行とサブスクライブ

この記事では、トピックをサブスクライブする **sub_client** という名前の 1 つのクライアントと、トピックに発行する **pub_client** というもう 1 つのクライアントを使用します。 [対称キー認証](how-to-authenticate-downstream-device.md#symmetric-key-authentication)を使用しますが、[x.509 自己署名認証](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)または [x.509 CA 署名認証](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)を使用して行うこともできます。

### <a name="create-publisher-and-subscriber-clients"></a>パブリッシャーとサブスクライバーのクライアントを作成する

IoT Hub に 2 つの IoT デバイスを作成し、パスワードを取得します。 ターミナルから Azure CLI を使用して次のことを行います。

1. IoT Hub に 2 つの IoT デバイスを作成し、IoT Edge デバイスの親にします。

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. SAS トークンを生成してパスワードを取得します。

    - デバイスの場合:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       ここで、3600 は SAS トークンの継続期間 (例: 3600 = 1 時間) です。
    
    - モジュールの場合:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       ここで、3600 は SAS トークンの継続期間 (例: 3600 = 1 時間) です。

3. 出力から "sas" キーに対応する値である SAS トークンをコピーします。 上記の Azure CLI コマンドからの出力例を次に示します。

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>パブリッシャーとサブスクライバーのクライアントを承認する

パブリッシャーとサブスクライバーを承認するには、Azure CLI、Visual Studio、または Visual Studio Code のいずれかで IoT Edge デプロイを作成することで IoT Edge ハブのツインを編集し、次の承認ポリシーを含めます。

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>TLS を使用しない対称キー認証

#### <a name="subscribe"></a>購読

IoT Edge デバイスで次のコマンドを実行して、**sub_client** MQTT クライアントを MQTT ブローカーに接続し、`test_topic` をサブスクライブします。

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

クライアントが IoT Edge と同じデバイスで実行されているため、この例では `<edge_device_address>` = `localhost` です。

この最初の例では、TLS を使用せずに、ポート 1883 (MQTT) を使用することに注意してください。 次のセクションでは、ポート 8883 (MQTTS) を使用したもう 1 つの例 (TLS が有効になっている) について説明します。

これで **sub_client** MQTT クライアントが開始され、`test_topic` で受信メッセージを待機しています。

#### <a name="publish"></a>発行

**pub_client** MQTT クライアントを MQTT ブローカーに接続し、別のターミナルから IoT Edge デバイスで次のコマンドを実行して、上記と同じ `test_topic` でメッセージを発行します。

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

クライアントが IoT Edge と同じデバイスで実行されているため、この例では `<edge_device_address>` = `localhost` です。

コマンドを実行すると、**sub_client** MQTT クライアントが "hello" メッセージを受信します。

### <a name="symmetric-keys-authentication-with-tls"></a>TLS を使用した対称キー認証

TLS を有効にするには、ポートを 1883 (MQTT) から 8883 (MQTTS) に変更する必要があり、クライアントが MQTT ブローカーによって送信された証明書チェーンを検証できるように、MQTT ブローカーのルート証明書を持っている必要があります。 これは、「[セキュリティで保護された接続 (TLS)](#secure-connection-tls)」のセクションの手順に従うことで実行できます。

クライアントは上記の例の MQTT ブローカーと同じデバイス上で実行されているため、同じ手順を適用して、ポート番号を 1883 (MQTT) から 8883 (MQTTS) に変更するだけで TLS を有効にすることができます。

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>IoT Hub トピックでの発行とサブスクライブ

[Azure IoT Device SDK](https://github.com/Azure/azure-iot-sdks) では、クライアントは既に IoT Hub の操作を実行できますが、ユーザー定義のトピックでの発行/サブスクライブは許可されていません。 IoT Hub の操作は、IoT ハブのプリミティブ プロトコルが遵守されている限り、発行とサブスクライブのセマンティクスを使用して MQTT クライアントを使用して実行できます。 これらのプロトコルがどのように機能するかを理解するために、この特異性について説明します。

### <a name="send-telemetry-data-to-iot-hub"></a>テレメトリ データを IoT Hub に送信する

テレメトリ データを IoT Hub に送信することは、ユーザー定義のトピックでの発行に似ていますが、特定の IoT Hub トピックを使用します。

- デバイスの場合、テレメトリは次のトピックで送信されます: `devices/<device_name>/messages/events/`
- モジュールの場合、テレメトリは次のトピックで送信されます: `devices/<device_name>/<module_name>/messages/events/`

さらに、IoT Edge MQTT ブローカーから IoT ハブにテレメトリを送信するための `FROM /messages/* INTO $upstream` などのルートを作成します。 ルーティングについては､「[ルートの宣言](module-composition.md#declare-routes)」を参照してください｡

### <a name="get-twin"></a>ツインの取得

デバイス/モジュールのツインの取得は、一般的な MQTT パターンではありません。 クライアントは、IoT Hub が提供するツインの要求を発行する必要があります。

ツインを受信するためには、クライアントは IoT Hub 固有のトピック `$iothub/twin/res/#` をサブスクライブする必要があります。 このトピック名は IoT Hub から継承され、すべてのクライアントが同じトピックをサブスクライブする必要があります。 これはデバイスまたはモジュールが相互にツインを受け取るという意味ではありません。 IoT Hub と IoT Edge ハブは、すべてのデバイスが同じトピック名をリッスンしている場合でも、どのツインが配信されるべきかを認識しています。 

サブスクリプションが行われたら、クライアントでは、IoT Hub 固有のトピック `$iothub/twin/GET/?rid=<request_id>/#` にメッセージを発行することによって、ツインを要求する必要があります。ここで、`<request_id>` は任意の識別子です。 その後、IoT ハブは、クライアントがサブスクライブするトピック `$iothub/twin/res/200/?rid=<request_id>` に関する要求されたデータとともに応答を送信します。 これは、クライアントが要求を応答とペアにできる方法です。

### <a name="receive-twin-patches"></a>ツイン パッチを受信する

ツイン パッチを受信するには、クライアントは特殊な IoT Hub トピック `$iothub/twin/PATCH/properties/desired/#` をサブスクライブする必要があります。 サブスクリプションが行われると、クライアントは、このトピックで IoT Hub によって送信されたツイン パッチを受信します。 

### <a name="receive-direct-methods"></a>ダイレクト メソッドを受信する

ダイレクト メソッドを受信することは、完全なツインを受け取ることと似ていますが、加えてクライアントが呼び出しを受信したことを確認する必要があります。 まず、クライアントは、IoT ハブの特殊なトピック `$iothub/methods/POST/#` をサブスクライブします。 次に、このトピックでダイレクト メソッドを受信した後、クライアントはダイレクト メソッドを受信したサブトピックから要求識別子 `rid` を抽出し、最後に IoT ハブの特別なトピック `$iothub/methods/res/200/<request_id>` に確認メッセージを発行する必要があります。

### <a name="send-direct-methods"></a>ダイレクト メソッドを送信する

ダイレクト メソッドの送信は HTTP 呼び出しであるため、MQTT ブローカーを通過しません。 ダイレクト メソッドを IoT ハブに送信する方法については、[ダイレクト メソッドの概要と呼び出し](../iot-hub/iot-hub-devguide-direct-methods.md)に関するページを参照してください。 ダイレクト メソッドを別のモジュールにローカルで送信する方法については、こちらの [Azure IoT C# SDK ダイレクト メソッドの呼び出しの例](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597)を参照してください。

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>MQTT ブローカー間での発行とサブスクライブ

2 つの MQTT ブローカーを接続するために、IoT Edge ハブには MQTT ブリッジが含まれています。 MQTT ブリッジは、通常、実行中の MQTT ブローカーを別の MQTT ブローカーに接続するために使用されます。 通常、ローカル トラフィックのサブセットのみが別のブローカーにプッシュされます。

> [!NOTE]
> 現在、IoT Edge ハブのブリッジは、入れ子になった IoT Edge デバイス間でのみ使用できます。 IoT ハブは、完全な機能を備えた MQTT ブローカーではないため、IoT ハブにデータを送信するためには使用できません。 IoT ハブの MQTT ブローカー機能のサポートの詳細については、「[MQTT プロトコルを使用した IoT Hub との通信](../iot-hub/iot-hub-mqtt-support.md)」を参照してください。 IoT Edge デバイスの詳細については、[Azure IoT Edge ゲートウェイへのダウンストリーム IoT Edge デバイスの接続](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)に関するページを参照してください 

入れ子になった構成では、IoT Edge ハブの MQTT ブリッジは親の MQTT ブローカーのクライアントとして機能するため、親の EdgeHub に対して承認規則を設定して、ブリッジが構成されている特定のユーザー定義トピックを子の EdgeHub が発行およびサブスクライブできるようにする必要があります。

IoT Edge MQTT ブリッジは、ツインを介して IoT Edge ハブに送信される JSON 構造を使用して構成されます。 IoT Edge ハブのツインを編集して、その MQTT ブリッジを構成します。

> [!NOTE]
> パブリック プレビューの場合、MQTT ブリッジの構成は、Visual Studio、Visual Studio Code、または Azure CLI 経由でのみ使用できます。 Azure portal は、現在、IoT Edge ハブの ツインとその MQTT ブリッジ構成の編集をサポートしていません。

MQTT ブリッジは、IoT Edge ハブの MQTT ブローカーを複数の外部ブローカーに接続するように構成できます。 外部ブローカーごとに、以下の設定が必要です。

- `endpoint` は、接続先のリモート MQTT ブローカーのアドレスです。 現在のところ、親の IoT Edge デバイスのみがサポートされており、変数 `$upstream` で定義されます。
- `settings` は、エンドポイントに対してブリッジするトピックを定義します。 エンドポイントごとに複数の設定を設定でき、次の値を使用して構成します。
    - `direction`: リモート ブローカーのトピックをサブスクライブするための `in`、またはリモート ブローカーのトピックに発行するための `out` のいずれか
    - `topic`: 照合するコア トピック パターン。 [MQTT ワイルドカード](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)は、このパターンを定義するために使用できます。 ローカル ブローカーとリモート ブローカーでは、このトピック パターンに異なるプレフィックスを適用できます。
    - `outPrefix`:リモート ブローカーの `topic` パターンに適用されるプレフィックス。
    - `inPrefix`:ローカル ブローカーの `topic` パターンに適用されるプレフィックス。

次に示すのは、親の IoT Edge デバイスのトピック `alerts/#` で受信したすべてのメッセージを同じトピックの子の IoT Edge デバイスに再発行し、子の IoT Edge デバイスのトピック `/local/telemetry/#` で送信されたすべてのメッセージをトピック `/remote/messages/#` の親の IoT Edge デバイスに再発行する IoT Edge MQTT ブリッジ構成の例です。

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
IoT Edge ハブ MQTT ブリッジに関するその他の注意事項:
- MQTT ブローカーが使用され、その IoT Edge が入れ子になった構成 (たとえば、`parent_hostname` が指定されている) で 使用されている場合は、MQTT プロトコルが自動的にアップストリーム プロトコルとして使用されます。 アップストリーム プロトコルの詳細については、[クラウドの通信](iot-edge-runtime.md#cloud-communication)に関するページを参照してください。 入れ子になった構成の詳細については、[Azure IoT Edge ゲートウェイへのダウンストリーム IoT Edge デバイスの接続](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[IoT Edge ハブについて](iot-edge-runtime.md#iot-edge-hub)
