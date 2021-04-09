---
title: Azure CLI を使用した大規模なモジュールの展開 - Azure IoT Edge
description: Azure CLI 向け IoT 拡張機能を使用して、一連の IoT Edge デバイスの自動展開を作成します
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 7b12b6c1cdc85eaba531f34b23aa74bee6b38f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201121"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure コマンドライン インターフェイスを使用して **IoT Edge の自動展開** を作成して、多数のデバイスの進行中のデプロイを一度に管理します。 IoT Edge の自動展開は、IoT Hub の[自動デバイス管理](../iot-hub/iot-hub-automatic-device-management.md)機能の一部です。 デプロイは、複数のモジュールを複数のデバイスにデプロイし、モジュールの状態と正常性を追跡し、必要に応じて変更できる動的プロセスです。

詳細については、「[1 台のデバイスまたは多数のデバイスを対象とした IoT Edge 自動展開について](module-deployment-monitoring.md)」を参照してください。

この記事では、Azure CLI と IoT 拡張機能をセットアップします。 次に、使用可能な CLI コマンドを使用して一連の IoT Edge デバイスにモジュールをデプロイして、進行状況を監視する方法を説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。
* 1 つまたは複数の IoT Edge デバイス。

  IoT Edge デバイスがセットアップされていない場合は、Azure 仮想マシンで作成できます。 クイックスタートの記事のいずれかの手順に従って、[仮想 Linux デバイスを作成](quickstart-linux.md)するか、[仮想 Windows デバイスを作成](quickstart.md)します。

* ご使用の環境内の [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.70 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。
* [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 詳細については、[モジュールのデプロイと IoT Edge へのルートの確立の方法の学習](module-composition.md)に関する記事をご覧ください。

Azure CLI を使用してモジュールをデプロイするには、配置マニフェストを .txt ファイルとしてローカルに保存します。 コマンドを実行して構成をデバイスに適用するときには、次のセクションのファイル パスを使用します。

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

## <a name="layered-deployment"></a>多層デプロイ

多層デプロイは、互いの上に積み重ねることができる自動デプロイの一種です。 多層デプロイの詳細については、「[1 台のデバイスまたは多数のデバイスを対象とした IoT Edge 自動展開について](module-deployment-monitoring.md)」を参照してください。

多層デプロイは、自動デプロイのように Azure CLI を使用して作成および管理できますが、いくつかの違いがあります。 多層デプロイが作成されたら、他のデプロイと同じように、同じ Azure CLI を使用して多層デプロイを操作できます。 多層デプロイを作成するには、create コマンドに `--layered` フラグを追加します。

2 番目の違いは、デプロイ マニフェストの構築です。 標準の自動デプロイには、ユーザー モジュールに加えてシステム ランタイム モジュールを含める必要がありますが、多層デプロイにはユーザー モジュールしか含めることができません。 その代わりに多層デプロイには、システム ランタイム モジュールなど、すべての IoT Edge デバイスに必要なコンポーネントを提供するために、標準の自動デプロイもデバイス上に必要です。

例として、1 つのモジュールでの基本的な多層デプロイ マニフェストを次に示します。

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
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

前の例では、モジュールの `properties.desired` を設定する多層デプロイを示しました。 この多層デプロイは、同じモジュールが既に適用されているデバイスをターゲットにしていた場合、既存の適切なプロパティをすべて上書きしていました。 適切なプロパティを上書きするのではなく更新するために、新しいサブセクションを定義できます。 次に例を示します。

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

多層デプロイでのモジュール ツインの構成の詳細については、[多層デプロイ](module-deployment-monitoring.md#layered-deployment)に関するページを参照してください。

## <a name="identify-devices-using-tags"></a>タグを使用したデバイスの識別

デプロイを作成する前に、影響を与えるデバイスを指定できる必要があります。 Azure IoT Edge では、デバイス ツイン内の **タグ** を使用してデバイスを識別します。 各デバイスには、対象のソリューションにとって意味のある方法で定義した複数のタグを設定することができます。 たとえば、スマート ビルのキャンパスを管理している場合は、デバイスに次のタグを追加できます。

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

デバイス ツインとタグの詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。

## <a name="create-a-deployment"></a>デプロイの作成

モジュールをターゲット デバイスに展開するには、配置マニフェストとその他のパラメーターで構成されるデプロイを作成します。

デプロイを作成するには、[az iot edge deployment create](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-create) コマンドを使用します。

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

同じコマンドと `--layered` フラグを使用して、多層デプロイを作成します。

deployment create コマンドは、次のパラメーターを受け取ります。

* **--layered** - デプロイを多層デプロイとして識別する省略可能なフラグ。
* **--deployment-id** - IoT ハブに作成されるデプロイの名前です。 デプロイに一意の名前を付けます。名前は最大 128 文字の英小文字で指定します。 スペースや、無効な文字は使用しないでください。`& ^ [ ] { } \ | " < > /` 必須のパラメーター。
* **--content** - デプロイ マニフェスト JSON へのファイルパスです。 必須のパラメーター。
* **--hub-name** - デプロイが作成される IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 `az account set -s [subscription name]` コマンドを使用して、現在のサブスクリプションを変更します。
* **--labels** - デプロイを追跡するためのラベルを追加します。 ラベルは、デプロイを説明する、[名前] と [値] で一組になっています。 ラベルの名前と値には、JSON 形式を使用します。 たとえば、`{"HostPlatform":"Linux", "Version:"3.0.1"}` のように指定します。
* **--target-condition** - どのデバイスがこのデプロイの対象となるかを指定する対象の条件を入力します。  条件は、デバイス ツイン タグか、デバイス ツインから報告されるプロパティに基づいて指定し、式の形式に一致させる必要があります。 たとえば、「 `tags.environment='test' and properties.reported.devicemodel='4000x'` 」のように入力します。
* **--priority** - 正の整数にする必要があります。 同じデバイスで複数のデプロイがターゲットとなっている場合は、優先度の数値が最も大きいデプロイが適用されます。
* **--metrics** - edgeHub に報告されたプロパティに対してクエリを実行し、デプロイのステータスを追跡するメトリックを作成します。 メトリックは JSON 入力またはファイル パスを取ります。 たとえば、「 `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'` 」のように入力します。

Azure CLI を使用してデプロイを監視するには、「[IoT Edge デプロイの監視](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli)」を参照してください。

## <a name="modify-a-deployment"></a>デプロイの変更

デプロイを変更すると、変更はすべての対象デバイスにただちにレプリケートされます。

対象の条件を更新すると、次の更新が実行されます。

* デバイスが古い対象条件を満たさないが、新しい対象条件を満たしており、このデプロイのそのデバイスに対する優先度が最も高い場合は、このデプロイは、このデバイスに適用されます。
* このデプロイを現在実行しているデバイスが対象条件を満たさなくなった場合、このデプロイはアンインストールされ、次に優先度の高いデプロイが適用されます。
* このデプロイを現在実行しているデバイスが対象条件を満たさなくなり、他のデプロイの対象条件を満たさない場合は、デバイスではなにも変更されません。 デバイスは現在の状態で現在のモジュールを実行し続けますが、このデプロイの一部としては管理されなくなります。 デバイスが他のデプロイの対象の条件を満たすと、このデプロイはアンインストールされ、新しいデプロイが適用されます。

デプロイ マニフェストで定義されたモジュールとルートを含むデプロイの内容は更新できません。 デプロイの内容を更新する場合は、同じデバイスをターゲットにした、より優先順位が高い新しいデプロイを作成することによって更新します。 ターゲット条件、ラベル、メトリック、優先順位など、既存のモジュールの特定のプロパティを変更できます。

デプロイを更新するには、[az iot edge deployment update](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-update) コマンドを使用します。

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

deployment update コマンドは、次のパラメーターを受け取ります。

* **--deployment-id** - IoT ハブに存在するデプロイの名前です。
* **--hub-name** - デプロイが存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。
* **--set** - デプロイのプロパティを更新します。 次のプロパティを更新することができます。
  * targetCondition - `targetCondition=tags.location.state='Oregon'` など
  * labels
  * priority
* **--add** - ターゲット条件やラベルなど、新しいプロパティをデプロイに追加します。
* **--remove** - ターゲット条件やラベルなど、既存のプロパティを削除します。

## <a name="delete-a-deployment"></a>デプロイの削除

デプロイを削除すると、デバイスには、次に高い優先順位のデプロイが適用されます。 デバイスが他のいずれのデプロイの対象条件も満たさない場合、デプロイが削除されても、モジュールは削除されません。

デプロイを削除するには、[az iot edge deployment delete](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-delete) コマンドを使用します。

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

deployment delete コマンドは、次のパラメーターを受け取ります。

* **--deployment-id** - IoT ハブに存在するデプロイの名前です。
* **--hub-name** - デプロイが存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

## <a name="next-steps"></a>次のステップ

[IoT Edge デバイスへのモジュールのデプロイ](module-deployment-monitoring.md)の詳細について学習します。