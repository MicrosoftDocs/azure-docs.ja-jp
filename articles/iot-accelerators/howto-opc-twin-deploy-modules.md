---
title: Azure 用 OPC Twin モジュールをゼロからデプロイする方法 |Microsoft Docs
description: OPC Twin をゼロからデプロイする方法
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 798f087c260b6b0a1efc366b864fe2bb7bce732e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603703"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>OPC Twin モジュールおよび依存関係をゼロからデプロイする

OPC Twin モジュールは IoT Edge 上で動作し、OPC デバイス ツインおよびレジストリ サービスにいくつかのエッジ サービスを提供します。 

モジュールを [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) ゲートウェイにデプロイする方法は、次の方法など、いくつかあります。

- [Azure portal の IoT Edge ブレードからデプロイする](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [AZ CLI を使用してデプロイする](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> デプロイの詳細と手順については、GitHub の[リポジトリ](https://github.com/Azure/azure-iiot-components)を参照してください。

## <a name="deployment-manifest"></a>配置マニフェスト

すべてのモジュールは配置マニフェストを使用してデプロイされます。  [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) と [OPC Twin](https://github.com/Azure/azure-iiot-opc-twin-module) の両方をデプロイするマニフェストの例を次に示します。

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
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
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Azure portal からデプロイする

モジュールを Azure IoT Edge ゲートウェイ デバイスにデプロイする最も簡単な方法は、Azure portal を使用することです。  

### <a name="prerequisites"></a>前提条件

1. OPC Twin の[依存関係](howto-opc-twin-deploy-dependencies.md)をデプロイし、結果の `.env` ファイルを取得した。 結果の `.env` ファイル内の `PCS_IOTHUBREACT_HUB_NAME` 変数のデプロイ済み `hub name` をメモします。

2. [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) または [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge ゲートウェイを登録して起動し、その `device id` をメモします。

### <a name="deploy-to-an-edge-device"></a>Edge デバイスへのデプロイ

1. [Azure Portal](https://portal.azure.com/) にサインインし、IoT Hub に移動します。

2. 左側のメニューから **[IoT Edge]** を選択します。

3. デバイスの一覧でターゲット デバイスの ID をクリックします。

4. **[Set Modules] \(モジュールの設定)** を選択します。

5. このページの **[Deployment modules]\(デプロイ モジュール\)** セクションで、 **[追加]** と **[IoT Edge モジュール]** を選択します。

6. **[IoT Edge のカスタム モジュール]** ダイアログで、モジュールの名前として `opctwin` を使用し、次にコンテナーの*画像 URI* を次のように指定します

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   *作成オプション*として、次の JSON を使用します。

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   必要な場合は、省略可能なフィールドに入力します。 コンテナー作成オプション、再起動ポリシー、および必要な状態について詳しくは、「[edgeAgent の必要なプロパティ](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties)」をご覧ください。 モジュール ツインについて詳しくは、「[必要なプロパティの定義または更新](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)」をご覧ください。

7. **[保存]** を選択し、手順 **5** を繰り返します。  

8. [IoT Edge のカスタム モジュール] ダイアログで、モジュールの名前として `opcpublisher` を使用し、コンテナーの*画像 URI* を次のように指定します 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   *作成オプション*として、次の JSON を使用します。

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. **[保存]** を選択し、 **[次へ]** を選択して、ルートのセクションに進みます。

10. ルート タブに以下を貼り付けます 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    そして **[次へ]** を選択します

11. デプロイ情報とマニフェストを確認します。  上の配置マニフェストのようになります。  **[Submit]\(送信\)** をクリックします。

12. モジュールをデバイスにデプロイした後で、そのすべてを Portal の **[デバイスの詳細]** ページで表示できます。 このページには、デプロイした各モジュールの名前、およびデプロイ状態や終了コードなどの役立つ情報が表示されます。

## <a name="deploying-using-azure-cli"></a>Azure CLI を使用したデプロイ

### <a name="prerequisites"></a>前提条件

1. [こちら](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)から [Azure コマンド ライン インターフェイス (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) の最新バージョンをインストールします。

### <a name="quickstart"></a>クイック スタート

1. 上の配置マニフェストを `deployment.json` ファイルに保存します。  

2. 次のコマンドを使用して、IoT Edge デバイスに構成を適用します。

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   `device id` パラメーターは、大文字と小文字が区別されます。 content パラメーターは、保存した配置マニフェスト ファイルを指します。 
    ![az IoT Edge set-modules の出力](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. モジュールをデバイスにデプロイした後で、そのすべてを次のコマンドで表示できます。

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   device ID パラメーターでは大文字と小文字が区別されます。 ![az iot hub module-identity list の出力](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>次の手順

ここでは、OPC Twin をゼロからデプロイする方法を学習しました。次に以下の記事を読むことをお勧めします。

> [!div class="nextstepaction"]
> [既存のプロジェクトに OPC Twin をデプロイする](howto-opc-twin-deploy-existing.md)
