---
title: Azure IoT Edge および Azure IoT Central | Microsoft Docs
description: IoT Central アプリケーションで Azure IoT Edge を使用する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 1a464b9e039f256fae52c32d828b1ec39a20a228
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123277"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Edge デバイスを Azure IoT Central アプリケーションに接続する

"*この記事は、ソリューション ビルダーおよびデバイス開発者を対象としています。* "

Azure IoT Edge により、クラウドの分析とカスタム ビジネス ロジックはデバイスに移動されるので、組織はデータの管理ではなくビジネスの分析情報に集中できます。 ビジネス ロジックを標準のコンテナーにパッケージ化することで IoT ソリューションをスケールアウトし、それらのコンテナーをデバイスにデプロイして、それらをクラウドから監視します。

この記事では、次の内容について説明します。

* IoT Edge デバイスが IoT Central アプリケーションに接続される方法。
* IoT Central を使用して IoT Edge デバイスを管理する方法。

IoT Edge の詳細については、「[Azure IoT Edge とは](../../iot-edge/about-iot-edge.md)」を参照してください

## <a name="iot-edge"></a>IoT Edge

IoT Edge は、次の 3 つのコンポーネントで構成されます。

* *IoT Edge モジュール*: Azure サービス、パートナー サービス、またはカスタム コードを実行するコンテナーです。 モジュールは、IoT Edge デバイスにデプロイされ、そのデバイスでローカルに実行されます。 詳細については、「[Azure IoT Edge モジュールについて](../../iot-edge/iot-edge-modules.md)」を参照してください。
* *IoT Edge ランタイム*: 個々の IoT Edge デバイス上で動作し、各デバイスにデプロイされたモジュールを管理します。 ランタイムは、"*IoT Edge エージェント*" と "*IoT Edge ハブ*" という 2 つの IoT Edge モジュールで構成されます。 詳細については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](../../iot-edge/iot-edge-runtime.md)」を参照してください。
* *クラウドベースのインターフェイス*: IoT Edge デバイスをリモートから監視して管理します。 IoT Central はクラウド インターフェイスの例です。

次のものを IoT Edge デバイスにすることができます。

* モジュールで構成されるスタンドアロン デバイス。
* ダウンストリーム デバイスが接続される "*ゲートウェイ デバイス*"。

## <a name="iot-edge-as-a-gateway"></a>ゲートウェイとしての IoT Edge

IoT Edge デバイスは、ネットワーク上の他のダウンストリーム デバイスと IoT Central アプリケーションの間の接続を提供するゲートウェイとして動作できます。

ゲートウェイには、次の 2 つのパターンがあります。

* "*透過的ゲートウェイ*" パターンでは、IoT Central のように動作する IoT Edge ハブ モジュールによって、IoT Central に登録されているデバイスからの接続が処理されます。 メッセージは、間にゲートウェイが存在しないかのように、ダウンストリーム デバイスから IoT Central に渡されます。

* "*変換ゲートウェイ*" パターンでは、それ自体では IoT Central に接続できないデバイスが、代わりにカスタム IoT Edge モジュールに接続されます。 IoT Edge デバイス内のモジュールにより、ダウンストリーム デバイスからの受信メッセージが処理されて、IoT Central に転送されます。

透過的と変換のゲートウェイ パターンは、相互に排他的ではありません。 1 つの IoT Edge デバイスが、透過的ゲートウェイと変換ゲートウェイの両方として機能できます。

IoT Edge のゲートウェイ パターンの詳細については、「[IoT Edge デバイスをゲートウェイとして使用する方法](../../iot-edge/iot-edge-as-gateway.md)」を参照してください。

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>ゲートウェイおよびモジュールとのダウンストリーム デバイスのリレーションシップ

ダウンストリーム デバイスからは、"*IoT Edge ハブ*" モジュールを通して IoT Edge ゲートウェイ デバイスに接続できます。 このシナリオでの IoT Edge デバイスは、透過的ゲートウェイです。

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="透過的なゲートウェイの図" border="false":::

ダウンストリーム デバイスは、カスタム モジュールを使用して IoT Edge ゲートウェイ デバイスに接続することもできます。 次のシナリオのダウンストリーム デバイスは、*Modbus* カスタム モジュールを通して接続されます。 このシナリオでの IoT Edge デバイスは、変換ゲートウェイです。

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="カスタム モジュール接続の図" border="false":::

次の図では、両方の種類のモジュールを使用した IoT Edge ゲートウェイ デバイスへの接続が示されています。 このシナリオでの IoT Edge デバイスは、透過的ゲートウェイと変換ゲートウェイの両方になります。

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="両方の接続モジュールを使用した接続の図" border="false":::

ダウンストリーム デバイスからは、複数のカスタム モジュールを通して IoT Edge ゲートウェイ デバイスに接続できます。 次の図は、Modbus カスタム モジュール、BLE カスタム モジュール、"*IoT Edge ハブ*" モジュールを通して接続しているダウンストリーム デバイスを示したものです。

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="複数のカスタム モジュールを使用した接続の図" border="false":::

<!-- To do: add link to how to configure gateway article? -->

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge デバイスと IoT Central

IoT Edge デバイスで IoT Central による認証を行うには、*Shared Access Signature* トークンまたは X.509 証明書を使用できます。 初めて接続する前に IoT Edge デバイスを IoT Central に手動で登録するか、Device Provisioning Service を使用して登録を処理することができます。 詳細については、「[Azure IoT Central に接続する](concepts-get-connected.md)」を参照してください。

IoT Central とデバイスの対話方法は、[デバイス テンプレート](concepts-device-templates.md)を使用して IoT Central で定義されています。 たとえば、次の項目がデバイス テンプレートで指定されています。

* IoT Central で解釈して視覚エフェクトを作成できるように、デバイスから送信されるテレメトリとプロパティの種類。
* IoT Central でオペレーターがコマンドの呼び出しに使用する UI を表示できるように、デバイスが応答するコマンド。

標準デバイスと同じように、IoT Edge デバイスで、テレメトリの送信、プロパティ値の同期、コマンドへの応答を行うことができます。 そのため、IoT Edge デバイスには IoT Central のデバイス テンプレートが必要です。

### <a name="iot-edge-device-templates"></a>IoT Edge デバイス テンプレート

IoT Central デバイス テンプレートでは、モデルを利用してデバイスの機能を表します。 次の図は、IoT Edge デバイスのモデル構造を示しています。

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="IoT Central に接続されている IoT Edge デバイスのモデル構造" border="false":::

IoT Central は、IoT Edge デバイスを次のようにモデル化します。

* すべての IoT Edge デバイス テンプレートに機能モデルがあります。
* 配置マニフェストに一覧表示されているすべてのカスタム モジュールに対して、モジュール機能モデルが生成されます。
* 各モジュール機能モデルとデバイス モデルの間にリレーションシップが確立されます。
* モジュール機能モデルは、1 つまたは複数のモジュール インターフェイスを実装します。
* 各モジュール インターフェイスには、テレメトリ、プロパティ、およびコマンドが含まれます。

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge の配置マニフェストと IoT Central のデバイス テンプレート

IoT Edge では、ビジネス ロジックをモジュールの形式でデプロイして管理します。 IoT Edge モジュールは、IoT Edge によって管理される最小の計算単位であり、Azure Stream Analytics などの Azure サービスや独自のソリューション固有のコードを含めることができます。

IoT Edge の "*配置マニフェスト*" には、デバイスにデプロイする IoT Edge モジュールとその構成方法が記述されています。 詳細については、「[IoT Edge にモジュールをデプロイしてルートを確立する方法について説明します](../../iot-edge/module-composition.md)」を参照してください。

Azure IoT Central では、配置マニフェストをインポートして、IoT Edge デバイス用のデバイス テンプレートを作成します。

次のコード スニペットは、IoT Edge の配置マニフェストの例を示したものです。

```json
{
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

このスニペットからは、次のことがわかります。

* 3 つのモジュールがあります。 すべての配置マニフェストに存在する "*IoT Edge エージェント*" と "*IoT Edge ハブ*" のシステム モジュール。 カスタム **SimulatedTemperatureSensor** モジュール。
* パブリック モジュール イメージは、接続するために資格情報が必要ない Azure Container Registry リポジトリからプルされます。 プライベート モジュール イメージの場合は、使用するコンテナー レジストリ資格情報を、"*IoT Edge エージェント*" モジュールの `registryCredentials` に設定します。
* カスタム **SimulatedTemperatureSensor** モジュールには、`"SendData": true` と `"SendInterval": 10` という 2 つのプロパティがあります。

この配置マニフェストを IoT Central アプリケーションにインポートすると、次のデバイス テンプレートが生成されます。

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="配置マニフェストから作成されたデバイス テンプレートを示すスクリーンショット。":::

このスクリーンショットには、次のものが表示されています。

* **SimulatedTemperatureSensor** という名前のモジュール。 "*IoT Edge エージェント*" と "*IoT Edge ハブ*" のシステム モジュールは、テンプレートには表示されていません。
* **SendData** と **SendInterval** という 2 つの書き込み可能なプロパティが含まれる、**management** という名前のインターフェイス。

配置マニフェストには、**SimulatedTemperatureSensor** モジュールによって送信されるテレメトリや応答されるコマンドについての情報は含まれていません。 デバイス テンプレートを発行する前に、これらの定義を手動でそれに追加します。

詳細については、「[チュートリアル: Azure IoT Edge デバイスを Azure IoT Central アプリケーションに追加する](tutorial-add-edge-as-leaf-device.md)」を参照してください。

### <a name="update-a-deployment-manifest"></a>配置マニフェストを更新する

デバイス テンプレートの新しい[バージョン](howto-version-device-template.md)を作成する場合は、配置マニフェストを新しいバージョンに置き換えることができます。

配置マニフェストを置き換えると、接続されているすべての IoT Edge デバイスで新しいマニフェストがダウンロードされて、モジュールが更新されます。 ただし、モジュールの構成を変更しても、IoT Central によってデバイス テンプレート内のインターフェイスが更新されることはありません。 たとえば、前のスニペットで示されているマニフェストを次のマニフェストに置き換えても、デバイス テンプレートの **management** インターフェイスに **SendUnits** プロパティが自動的に追加されることはありません。 IoT Central によってそれが認識されるようにするには、新しいプロパティを **management** インターフェイスに手動で追加します。

```json
{
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>IoT Edge ランタイムをデプロイする

IoT Edge ランタイムを実行できる場所については、「[Azure IoT Edge のサポートされるシステム](../../iot-edge/support.md)」を参照してください。

次の環境に IoT Edge ランタイムをインストールすることもできます。

* [Azure IoT Edge for Linux をインストールまたはアンインストールする](../../iot-edge/how-to-install-iot-edge.md)
* [Windows デバイスに Azure IoT Edge for Linux をインストールしてプロビジョニングする (プレビュー)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Azure の Ubuntu 仮想マシン上で Azure IoT Edge を実行する](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>IoT Edge ゲートウェイ デバイス

IoT Edge デバイスをゲートウェイ デバイスにするよう選択した場合は、ゲートウェイ デバイスに接続するデバイスのデバイス モデルにダウンストリーム リレーションシップを追加できます。

<!-- TODO - add link to Edge Gateway how-to -->

## <a name="next-steps"></a>次のステップ

デバイスの開発者には、次のステップとして[独自の IoT Edge モジュールを開発する](../../iot-edge/module-development.md)方法について学習することをお勧めします。
