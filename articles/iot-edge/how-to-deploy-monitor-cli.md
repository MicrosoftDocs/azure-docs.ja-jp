---
title: コマンド ラインから自動展開を作成する - Azure IoT Edge | Microsoft Docs
description: Azure CLI 向け IoT 拡張機能を使用して、一連の IoT Edge デバイスの自動展開を作成します
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 61a3c1cdccf01b266581a13fe3c660bd57f59b2c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796198"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視

Azure コマンドライン インターフェイスを使用して **IoT Edge の自動展開**を作成して、多数のデバイスの進行中のデプロイを一度に管理します。 IoT Edge の自動展開は、IoT Hub の[自動デバイス管理](/azure/iot-hub/iot-hub-automatic-device-management)機能の一部です。 デプロイは、複数のモジュールを複数のデバイスにデプロイし、モジュールの状態と正常性を追跡し、必要に応じて変更できる動的プロセスです。 

詳細については、「[1 台のデバイスまたは多数のデバイスを対象とした IoT Edge 自動展開について](module-deployment-monitoring.md)」を参照してください。

この記事では、Azure CLI と IoT 拡張機能をセットアップします。 次に、使用可能な CLI コマンドを使用して一連の IoT Edge デバイスにモジュールをデプロイして、進行状況を監視する方法を説明します。

## <a name="cli-prerequisites"></a>CLI の前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。 
* IoT Edge ランタイムがインストールされた [IoT Edge デバイス](how-to-register-device-cli.md)。
* ご使用の環境内の [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.24 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。 
* [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 詳細については、[モジュールのデプロイと IoT Edge へのルートの確立の方法の学習](module-composition.md)に関する記事をご覧ください。

Azure CLI を使用してモジュールをデプロイするには、配置マニフェストを .txt ファイルとしてローカルに保存します。 コマンドを実行して構成をデバイスに適用するときには、次のセクションのファイル パスを使用します。 

例として、1 つのモジュールでの基本的な配置マニフェストを次に示します。

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
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
              }
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{}"
              }
            }
          },
          "modules": {
            "tempSensor": {
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
      "tempSensor": {
        "properties.desired": {}
      }
    }
  }
}
```

## <a name="identify-devices-using-tags"></a>タグを使用したデバイスの識別

デプロイを作成する前に、影響を与えるデバイスを指定できる必要があります。 Azure IoT Edge では、デバイス ツイン内の**タグ**を使用してデバイスを識別します。 各デバイスには、対象のソリューションにとって意味のある方法で定義した複数のタグを設定することができます。 たとえば、スマート ビルのキャンパスを管理している場合は、デバイスに次のタグを追加できます。

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

デプロイを作成するには、[az iot edge deployment create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) コマンドを使用します。

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

deployment create コマンドは、次のパラメーターを受け取ります。 

* **--deployment-id** - IoT ハブに作成されるデプロイの名前です。 デプロイに一意の名前を付けます。名前は最大 128 文字の英小文字で指定します。 スペースや、無効な文字は使用しないでください。`& ^ [ ] { } \ | " < > /`
* **--hub-name** - デプロイが作成される IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 `az account set -s [subscription name]` コマンドを使用して、現在のサブスクリプションを変更します。
* **--content** - デプロイ マニフェスト JSON へのファイルパスです。 
* **--labels** - デプロイを追跡するためのラベルを追加します。 ラベルは、デプロイを説明する、[名前] と [値] で一組になっています。 ラベルの名前と値には、JSON 形式を使用します。 たとえば、`{"HostPlatform":"Linux", "Version:"3.0.1"}` のように指定します。
* **--target-condition** - どのデバイスがこのデプロイの対象となるかを指定する対象の条件を入力します。 条件は、デバイス ツイン タグか、デバイス ツインから報告されるプロパティに基づいて指定し、式の形式に一致させる必要があります。 たとえば、「 `tags.environment='test' and properties.reported.devicemodel='4000x'` 」のように入力します。 
* **--priority** - 正の整数にする必要があります。 同じデバイスで複数のデプロイがターゲットとなっている場合は、優先度の数値が最も大きいデプロイが適用されます。

## <a name="monitor-a-deployment"></a>デプロイの監視

単一のデプロイの詳細を表示するには、[az iot edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) コマンドを使用します。

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

deployment show コマンドは、次のパラメーターを受け取ります。
* **--deployment-id** - IoT ハブに存在するデプロイの名前です。
* **--hub-name** - デプロイが存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

コマンド ウィンドウで、デプロイを検査します。  **メトリック** プロパティは、各ハブによって評価される各メトリックの数を表示します。

* **targetedCount** - ターゲット条件と一致する IoT Hub 内のデバイス ツインの数を指定するシステム メトリックです。
* **appliedCount** - IoT Hub 内でモジュール ツインにデプロイ コンテンツが適用されているデバイスの数を指定するシステム メトリックです。
* **reportedSuccessfulCount** - IoT Edge クライアント ランタイムから成功をレポートしているデプロイ内の IoT Edge デバイスの数を指定するデバイス メトリックです。
* **reportedFailedCount** - IoT Edge クライアント ランタイムから失敗をレポートしているデプロイ内の IoT Edge デバイスの数を指定するデバイス メトリックです。

[az iot edge deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) コマンドを使用して、各メトリックのデバイス ID またはオブジェクトの一覧を表示できます。

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

deployment show-metric コマンドは、次のパラメーターを受け取ります。 
* **--deployment-id** - IoT ハブに存在するデプロイの名前です。
* **--metric-id** - デバイス ID (`reportedFailedCount` など) の一覧を表示するメトリックの名前です。
* **--hub-name** - デプロイが存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

## <a name="modify-a-deployment"></a>デプロイの変更

デプロイを変更すると、変更はすべての対象デバイスにただちにレプリケートされます。 

対象の条件を更新すると、次の更新が実行されます。

* デバイスが古い対象条件を満たさないが、新しい対象条件を満たしており、このデプロイのそのデバイスに対する優先度が最も高い場合は、このデプロイは、このデバイスに適用されます。 
* このデプロイを現在実行しているデバイスが対象条件を満たさなくなった場合、このデプロイはアンインストールされ、次に優先度の高いデプロイが適用されます。 
* このデプロイを現在実行しているデバイスが対象条件を満たさなくなり、他のデプロイの対象条件を満たさない場合は、デバイスではなにも変更されません。 デバイスは現在の状態で現在のモジュールを実行し続けますが、このデプロイの一部としては管理されなくなります。 デバイスが他のデプロイの対象の条件を満たすと、このデプロイはアンインストールされ、新しいデプロイが適用されます。 

デプロイを更新するには、[az iot edge deployment update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) コマンドを使用します。

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

deployment update コマンドは、次のパラメーターを受け取ります。
* **--deployment-id** - IoT ハブに存在するデプロイの名前です。
* **--hub-name** - デプロイが存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。
* **--set** - デプロイのプロパティを更新します。 次のプロパティを更新することができます。
  * targetCondition - `targetCondition=tags.location.state='Oregon'` など
  * labels 
  * priority


## <a name="delete-a-deployment"></a>デプロイの削除

デプロイを削除すると、デバイスには、次に高い優先順位のデプロイが適用されます。 デバイスが他のいずれのデプロイの対象条件も満たさない場合、デプロイが削除されても、モジュールは削除されません。 

デプロイを削除するには、[az iot edge deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) コマンドを使用します。

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

deployment delete コマンドは、次のパラメーターを受け取ります。 
* **--deployment-id** - IoT ハブに存在するデプロイの名前です。
* **--hub-name** - デプロイが存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

## <a name="next-steps"></a>次の手順

[IoT Edge デバイスへのモジュールのデプロイ](module-deployment-monitoring.md)の詳細について学習します。
