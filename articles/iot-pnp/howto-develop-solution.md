---
title: Azure IoT ソリューションから IoT プラグ アンド プレイ プレビュー デバイスとやり取りする | Microsoft Docs
description: ソリューション開発者を対象にして、サービス SDK を使用して IoT プラグ アンド プレイ デバイスとやり取りする方法について説明します。
author: YasinMSFT
ms.author: yahajiza
ms.date: 07/24/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 5abfe0300bd61f5ccfbfccedf16659f055eb8ad4
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878613"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>IoT プラグ アンド プレイ プレビュー デバイスに接続してやり取りする

この攻略ガイドでは、IoT ソリューションが IoT プラグ アンド プレイ プレビュー デバイスとやり取りする方法を示す Node サービス SDK のサンプルの使用方法について説明します。

[ソリューションへの IoT プラグ アンド プレイ デバイスの接続](quickstart-connect-pnp-device-solution.md)に関するクイックスタートを完​​了していない場合は、ここで完了してください。 このクイックスタートでは、SDK をダウンロードしてインストールし、いくつかのサンプルを実行する方法について説明します。

サービス サンプルを実行する前に、新しいターミナルを開き、複製したリポジトリのルート フォルダーに移動します。**digitaltwins/quickstarts/service** フォルダーに移動してから、次のコマンドを実行して依存関係をインストールします。

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>サービス サンプルを実行する

Node.js Service SDK の機能を調べるには、次のサンプルを使用します。 使用するシェルで `IOTHUB_CONNECTION_STRING` 環境変数が設定されていることを確認します。

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>デジタル ツインを取得してインターフェイスを一覧表示する

**get_digital_twin.js** は、デバイスに関連付けられているデジタル ツインを取得し、そのコンポーネントをコマンド ラインに出力します。 実行中のデバイス サンプルが成功する必要はありません。

**get_digital_twin_interface_instance.js** は、デバイスに関連付けられているデジタル ツインの単一のインターフェイス インスタンスを取得し、それをコマンド ラインに出力します。 デバイス サンプルが実行されている必要はありません。

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Node Service SDK を使用してプロパティを取得および設定する

**update_digital_twin.js** は、完全パスを使用して、デバイス デジタル ツイン上の書き込み可能なプロパティを更新します。 必要に応じて、複数のインターフェイス上の複数のプロパティを更新できます。 これを成功させるには、デバイス サンプルが同時に実行されている必要があります。 成功すると、デバイス サンプルがプロパティの更新に関する何かを出力し、サービス サンプルが更新されたデジタル ツインをターミナルに出力しているように見えます。

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Node Service SDK を使用してコマンドを送信し、応答を取得する

**invoke_command.js** は、デバイス デジタル ツイン上の同期コマンドを呼び出します。 これを成功させるには、デバイス サンプルが同時に実行されている必要があります。 成功すると、デバイス サンプルがコマンドの受信確認に関する何かを出力し、サービス クライアントがコマンドの結果をターミナルに出力しているように見えます。

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Node Service SDK を使用してパブリック リポジトリに接続し、モデル定義を取得する

サービスおよびデバイスのサンプルと同じ手順を使用して、以下の環境変数を設定する必要があります。

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

この接続文字列は、[Azure Certified for IoT ポータル](https://preview.catalog.azureiotsolutions.com)の **[Company repository]\(会社リポジトリ\)** の **[接続文字列]** タブで確認できます。

接続文字列は、次の例のようになります。

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

これら 4 つの環境変数を設定したら、他のサンプルを実行したときと同じ方法でサンプルを実行します。

```cmd/sh
node model_repo.js
```

このサンプルは、**ModelDiscovery** インターフェイスをダウンロードし、このモデルをターミナルに出力します。

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>機能モデルとインターフェイスに基づいて IoT Hub でクエリを実行する

IoT Hub クエリ言語では、以下の例で示されているように、`HAS_INTERFACE` および `HAS_CAPABILITYMODEL` がサポートされています。

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>デジタル ツイン ルートを作成する

ソリューションは、デジタル ツインの変更イベントの通知を受け取ることができます。 これらの通知をサブスクライブするには、[IoT Hub ルーティング機能](../iot-hub/iot-hub-devguide-endpoints.md)を使用して、Blob Storage、Event Hubs、Service Bus キューなどのエンドポイントに通知を送信します。

デジタル ツイン ルートを作成するには:

1. Azure portal で IoT Hub リソースに移動します。
1. **[メッセージ ルーティング]** を選択します。
1. **[ルート]** タブで **[追加]** を選択します。
1. **[名前]** フィールドに値を入力し、 **[エンドポイント]** を選択します。 エンドポイントを構成していない場合は、 **[エンドポイントの追加]** を選択します。
1. **[データ ソース]** ドロップダウンで、 **[Digital Twin Change Events]\(デジタル ツインの変更イベント\)** を選択します。
1. **[保存]** を選択します。

次の JSON は、デジタル ツイン変更イベントの例を示しています。

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>次の手順

ここでは、IoT プラグ アンド プレイ デバイスとやり取りするサービス ソリューションについて学習しました。次のステップとして、[モデル検出](concepts-model-discovery.md)について学習することをお勧めします。
