---
title: Azure CLI コマンド ラインからモジュールをデプロイする - Azure IoT Edge
description: Azure CLI を Azure IoT Extension と共に使用して、デプロイ マニフェストによる構成のとおりに、IoT Edge モジュールを IoT Hub から IoT Edge デバイスにプッシュします。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: cc4308cf69ecb99fccb09a6668825397675983cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201144"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Azure CLI を使用して Azure IoT Edge モジュールをデプロイする

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

ビジネス ロジックで IoT Edge モジュールを作成したら、それらをデバイスにデプロイしてエッジで動作させます。 連携してデータを収集および処理する複数のモジュールがある場合は、一度にそのすべてをデプロイし、それらを接続するルーティング規則を宣言できます。

[Azure CLI](/cli/azure) は、IoT Edge などの Azure リソースを管理するための、オープン ソースのクロス プラットフォーム コマンド ライン ツールです。 これを使用すると、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを簡単に管理することができます。 新しい IoT 拡張機能によって、Azure CLI には、デバイス管理、完全な IoT Edge 対応などの機能が追加されました。

この記事では、JSON 配置マニフェストを作成し、そのファイルを使用して IoT Edge デバイスにデプロイをプッシュする方法を示します。 共有タグに基づいて複数のデバイスをターゲットとするデプロイの作成については、「[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。
* IoT Edge デバイス

  IoT Edge デバイスがセットアップされていない場合は、Azure 仮想マシンで作成できます。 クイックスタートの記事のいずれかの手順に従って、[仮想 Linux デバイスを作成](quickstart-linux.md)するか、[仮想 Windows デバイスを作成](quickstart.md)します。

* ご使用の環境内の [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.70 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。
* [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 配置マニフェストのしくみとその作成方法について詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

Azure CLI を使用してモジュールをデプロイするには、配置マニフェストを .json ファイルとしてローカルに保存します。 コマンドを実行して構成をデバイスに適用するときには、次のセクションのファイル パスを使用します。

例として、1 つのモジュールでの基本的な配置マニフェストを次に示します。

>[!NOTE]
>このサンプルの配置マニフェストでは、IoT Edge エージェントとハブにスキーマ バージョン 1.1 を使用します。 スキーマ バージョン 1.1 は IoT Edge バージョン 1.0.10 と共にリリースされており、モジュールの起動順序やルートの優先順位付けなどの機能を使用できます。

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
              "version": "1.0",
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
          "schemaVersion": "1.1",
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

## <a name="deploy-to-your-device"></a>デバイスにデプロイする

モジュールをデバイスにデプロイするには、モジュール情報で構成されている配置マニフェストを適用します。

デプロイ マニフェストが保存されているフォルダーにディレクトリを変更します。 VS Code IoT Edge テンプレートのいずれかを使用している場合は、ソリューション ディレクトリの **config** フォルダー内の `deployment.json` ファイルを使用し、`deployment.template.json` ファイルは使用しません。

次のコマンドを使用して、IoT Edge デバイスに構成を適用します。

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

device ID パラメーターでは大文字と小文字が区別されます。 content パラメーターは、保存した配置マニフェスト ファイルを指します。

   ![az iot edge set-modules の出力](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>デバイス上のモジュールを表示する

モジュールをデバイスにデプロイした後で、そのすべてを次のコマンドで表示できます。

IoT Edge デバイス上のモジュールを参照します。

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

device ID パラメーターでは大文字と小文字が区別されます。

   ![az iot hub module-identity list の出力](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>次のステップ

[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)の方法を学習します