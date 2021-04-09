---
title: Visual Studio Code からモジュールをデプロイする - Azure IoT Edge
description: Visual Studio Code を Azure IoT Tools と共に使用して、デプロイ マニフェストによる構成のとおりに、IoT Edge モジュールを IoT Hub から IoT Edge デバイスにプッシュします。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b4840b36d5dadc14bbd664b844e3bcce6f6fec4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201686"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Visual Studio Code から Azure IoT Edge モジュールをデプロイする

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

ビジネス ロジックで IoT Edge モジュールを作成したら、それらをデバイスにデプロイしてエッジで動作させます。 連携してデータを収集および処理する複数のモジュールがある場合は、一度にそのすべてをデプロイし、それらを接続するルーティング規則を宣言できます。

この記事では、JSON 配置マニフェストを作成し、そのファイルを使用して IoT Edge デバイスにデプロイをプッシュする方法を示します。 共有タグに基づいて複数のデバイスをターゲットとするデプロイの作成については、[Visual Studio Code を使用して IoT Edge モジュールを大規模にデプロイする](how-to-deploy-vscode-at-scale.md)方法に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。
* IoT Edge デバイス

  IoT Edge デバイスがセットアップされていない場合は、Azure 仮想マシンで作成できます。 クイックスタートの記事のいずれかの手順に従って、[仮想 Linux デバイスを作成](quickstart-linux.md)するか、[仮想 Windows デバイスを作成](quickstart.md)します。

* [Visual Studio Code](https://code.visualstudio.com/)。
* Visual Studio Code 用の [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 配置マニフェストのしくみとその作成方法について詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

Visual Studio Code を使用してモジュールをデプロイするには、配置マニフェストを .JSON ファイルとしてローカルに保存します。 コマンドを実行して構成をデバイスに適用するときには、次のセクションのファイル パスを使用します。

例として、1 つのモジュールでの基本的な配置マニフェストを次に示します。

>[!NOTE]
>このサンプルの配置マニフェストでは、IoT Edge エージェントとハブにスキーマ バージョン 1.1 を使用します。 スキーマ バージョン1.1 は IoT Edge バージョン 1.0.10 と共にリリースされており、モジュールの起動順序やルートの優先順位付けなどの機能を使用できます。

   ```json
   {
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
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
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
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT ハブで操作を実行できます。 これらの操作を動作させるには、Azure アカウントにサインインし、作業している IoT ハブを選択する必要があります。

1. Visual Studio Code で **エクスプローラー** ビューを開きます。

1. エクスプローラーの下部で、 **[Azure IoT Hub]** セクションを展開します。

   ![Azure IoT Hub セクションを展開する](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをポイントします。

1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。

1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。

1. Azure サブスクリプションを選択します。

1. IoT ハブを選択します。

## <a name="deploy-to-your-device"></a>デバイスにデプロイする

モジュールをデバイスにデプロイするには、モジュール情報で構成されている配置マニフェストを適用します。

1. Visual Studio Code エクスプローラー ビューで **[Azure IoT Hub]** セクションを展開し、 **[デバイス]** ノードを展開します。

1. デプロイ マニフェストで構成する IoT Edge デバイスを右クリックします。

    > [!TIP]
    > 選択したデバイスが IoT Edge デバイスであることを確認するには、そのデバイスを選択してモジュールの一覧を展開し、 **[$edgeHub]** と **[$edgeAgent]** が存在することを確認します。 すべての IoT Edge デバイスには、これらの 2 つのモジュールが含まれています。

1. **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

1. 使用する配置マニフェスト JSON ファイルに移動し、 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。

   ![Select Edge Deployment Manifest (Edge 配置マニフェストの選択)](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

デプロイの結果は、VS Code の出力に出力されます。 ターゲット デバイスが実行され、インターネットに接続されている場合、成功したデプロイは数分以内に適用されます。

## <a name="view-modules-on-your-device"></a>デバイス上のモジュールを表示する

モジュールをデバイスにデプロイした後で、そのすべてを **[Azure IoT Hub]** セクションで表示できます。 IoT Edge デバイスの横の矢印を選択して展開します。 現在実行中のすべてのモジュールが表示されます。

デバイスに新しいモジュールを最近デプロイした場合は、 **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション ヘッダーをポイントし、更新アイコンをクリックしてビューを更新します。

モジュールの名前を右クリックして、モジュール ツインを表示および編集します。

## <a name="next-steps"></a>次のステップ

[Visual Studio Code を使用して IoT Edge を大規模にデプロイおよび監視する](how-to-deploy-at-scale.md)方法を確認します。
