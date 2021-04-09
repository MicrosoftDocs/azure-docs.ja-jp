---
title: Visual Studio Code - Azure IoT Edge を使用して大規模モジュールをデプロイする
description: Visual Studio Code 用の IoT 拡張機能を使用して、IoT Edge デバイスのグループの自動デプロイを作成します。
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a20992ef26b74bcc37a7403e4ee77cacc0f8f66e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200292"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Visual Studio Code を使用して大規模 IoT Edge モジュールをデプロイする

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Visual Studio Code を使用して **IoT Edge の自動デプロイ** を作成し、多数のデバイスの進行中のデプロイを一括管理できます。 IoT Edge の自動展開は、IoT Hub の[自動デバイス管理](../iot-hub/iot-hub-automatic-device-management.md)機能の一部です。 デプロイは動的なプロセスであり、複数のモジュールを複数デバイスにデプロイすることができます。 また、モジュールの状態と正常性をトラッキングし、必要に応じて変更を加えることもできます。

詳細については、「[1 台のデバイスまたは多数のデバイスを対象とした IoT Edge 自動展開について](module-deployment-monitoring.md)」を参照してください。

この記事では、Visual Studio Code と、その IoT 拡張機能の設定について説明します。 その上で、一連の IoT Edge デバイスにモジュールをデプロイする方法を学習します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。
* 1 つまたは複数の IoT Edge デバイス。

  IoT Edge デバイスがセットアップされていない場合は、Azure 仮想マシンで作成できます。 クイックスタートの記事のいずれかの手順に従って、[仮想 Linux デバイスを作成](quickstart-linux.md)するか、[仮想 Windows デバイスを作成](quickstart.md)します。

* [Visual Studio Code](https://code.visualstudio.com/)。
* Visual Studio Code 用の [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)

## <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、ハブでの操作を行うことができます。 これらの操作を機能させるには、Azure アカウントにサインインし、あなたが作業している IoT ハブを選択する必要があります。

1. Visual Studio Code で **エクスプローラー** ビューを開きます。

1. エクスプローラーの下部で、 **[Azure IoT Hub]** セクションを展開します。

1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをポイントします。

1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。

1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。

1. Azure サブスクリプションを選択します。

1. IoT ハブを選択します。

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

デプロイ マニフェストとは、デプロイするモジュールを記述した JSON ドキュメントです。 これはまた、モジュール間のデータ フローと、モジュール ツインの望ましいプロパティも記述します。 詳細については、[モジュールのデプロイと IoT Edge へのルートの確立の方法の学習](module-composition.md)に関する記事をご覧ください。

Visual Studio Code を使用してモジュールをデプロイするには、配置マニフェストを .JSON ファイルとしてローカルに保存します。 デバイスに構成を適用するには、コマンド実行時にその場所を入力する必要があります。

例として、1 つのモジュールでの基本的な配置マニフェストを次に示します。

>[!NOTE]
>このサンプルの配置マニフェストでは、IoT Edge エージェントとハブにスキーマ バージョン 1.1 を使用します。 スキーマ バージョン1.1 は IoT Edge バージョン 1.0.10 と共にリリースされ、モジュールの起動順序やルートの優先順位付けなどの機能を使用できます。

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

現在どの IoT Edge デバイスを構成できるかを判断する必要がある場合には、**IoT Edge の [デバイス情報取得]** コマンドを実行します。

## <a name="identify-devices-with-target-conditions"></a>ターゲット条件に合致したデバイスを特定する

デプロイを受ける IoT Edge デバイスを特定するには、ターゲット条件を指定する必要があります。 指定した条件がデバイス ID、タグ値、プロパティの報告値と一致する場合、ターゲット条件を満たすと判断されます。

デバイス ツインのタグを構成してください。 タグの付いたデバイス ツインの例を以下に示します。

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

デプロイのターゲット条件に、`tag.location.building = '20'`などのタグの値のいずれかと一致する式が含まれている場合、そのデバイスはデプロイを受け取ります。

タグまたはその他の値に関わりなく、特定のデバイスを対象にしたい場合には、ターゲット条件に `deviceId` を指定します。

以下に、その他の例をいくつか紹介します。

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' OR deviceId = 'linuxprod2' OR deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus2'
* tags.environment = 'prod' OR tags.location = 'westus2'
* tags.operator = 'John' AND tags.environment = 'prod' AND NOT deviceId = 'linuxprod1'

詳細については、[ターゲット条件](module-deployment-monitoring.md#target-condition)を参照してください。 デバイス ツインとタグの詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。

### <a name="edit-the-device-twin"></a>デバイス ツインの編集

Visual Studio Code でデバイス ツインを編集して、タグを構成することができます。 **[表示]** メニューで **[コマンドパレット]** を選択し、**IoT Edge:  デバイス ツインの編集** コマンドを実行します。 IoT Edge デバイスを選択すると、デバイス ツインが表示されます。

この例では、タグは定義されていません。 現時点で空のセクション `"tags": {}` を、あなた独自のタグ定義に置換します。

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

ローカルファイルを保存した後、**IoT Edge:デバイス ツインの更新** コマンドを実行します。

## <a name="create-deployment-at-scale"></a>大規模デプロイの作成

デバイスツインでデプロイ マニフェストと構成済みタグの構成を完了し、デプロイの準備が整いました。

1. **[表示]** メニューで **[コマンドパレット]** を選択し、**Azure IoT Edge:大規模デプロイの作成** コマンドを選択します。

1. 使用する配置マニフェスト JSON ファイルに移動し、 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。

1. プロンプトに従って値を入力します。最初は **デプロイ ID** です。

   ![デプロイ ID の指定](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   次の各パラメーターの値を指定します。

  | パラメーター | 説明 |
  | --- | --- |
  | デプロイ ID | IoT ハブに作成されるデプロイの名前です。 デプロイに一意の名前を付けます。名前は最大 128 文字の英小文字で指定します。 スペースや、無効な文字は使用しないでください。`& ^ [ ] { } \ | " < > /` |
  | ターゲット条件 | ターゲット条件を入力し、このデプロイのターゲットとなるデバイスを決定します。  条件は、デバイス ツイン タグか、デバイス ツインから報告されるプロパティに基づいて指定し、式の形式に一致させる必要があります。 たとえば、「 `tags.environment='test' and properties.reported.devicemodel='4000x'` 」のように入力します。 |
  | 優先度 |  正の整数。 複数のデプロイが同じデバイスをターゲットしている場合には、優先度の数値が最も大きいデプロイが適用されます。 |

  優先度を指定すると、ターミナルは次のような出力を表示します。

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>デプロイのモニタリングと変更

[Azure portal](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) または [Azure CLI](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) を使用して、デプロイのモニタリング、変更、および削除を行います。 どちらも、あなたのデプロイにメトリックを提供します。

## <a name="next-steps"></a>次のステップ

[IoT Edge デバイスへのモジュールのデプロイ](module-deployment-monitoring.md)の詳細について学習します。