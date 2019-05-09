---
title: Visual Studio Code からモジュールをデプロイする - Azure IoT Edge | Microsoft Docs
description: Visual Studio Code を使用して IoT Edge デバイスにモジュールをデプロイする
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 91a074cf98291b105864a69730314efff3482254
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54155016"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Visual Studio Code から Azure IoT Edge モジュールをデプロイする

ビジネス ロジックで IoT Edge モジュールを作成したら、それらをデバイスにデプロイしてエッジで動作させます。 連携してデータを収集および処理する複数のモジュールがある場合は、一度にそのすべてをデプロイし、それらを接続するルーティング規則を宣言できます。

この記事では、JSON 配置マニフェストを作成し、そのファイルを使用して IoT Edge デバイスにデプロイをプッシュする方法を示します。 共有タグに基づいて複数のデバイスをターゲットとするデプロイの作成については、「[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。
* IoT Edge ランタイムがインストールされた [IoT Edge デバイス](how-to-register-device-portal.md)。
* [Visual Studio Code](https://code.visualstudio.com/)。
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)。

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 配置マニフェストのしくみとその作成方法について詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

Visual Studio Code を使用してモジュールをデプロイするには、配置マニフェストを .JSON ファイルとしてローカルに保存します。 コマンドを実行して構成をデバイスに適用するときには、次のセクションのファイル パスを使用します。

例として、1 つのモジュールでの基本的な配置マニフェストを次に示します。

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
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT ハブで操作を実行できます。 これらの操作を動作させるには、Azure アカウントにサインインし、作業している IoT ハブを選択する必要があります。

1. Visual Studio Code で**エクスプローラー** ビューを開きます。

1. エクスプローラーの下部で、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。

   ![[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\) セクションを展開する](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをポイントします。

1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。

1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。

1. Azure サブスクリプションを選択します。

1. IoT ハブを選択します。

## <a name="deploy-to-your-device"></a>デバイスにデプロイする

モジュールをデバイスにデプロイするには、モジュール情報で構成されている配置マニフェストを適用します。

1. Visual Studio Code エクスプローラー ビューで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。

1. デプロイ マニフェストで構成する IoT Edge デバイスを右クリックします。

    > [!TIP]
    > 選択したデバイスが IoT Edge デバイスであることを確認するには、モジュールの一覧を展開して **$edgeHub** と **$edgeAgent** が存在していることを確認します。 すべての IoT Edge デバイスには、これら 2 つのモジュールが含まれています。

1. **[Create Deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

1. 使用する配置マニフェスト JSON ファイルに移動し、**[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。

   ![Select Edge Deployment Manifest (Edge 配置マニフェストの選択)](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

デプロイの結果は、VS Code の出力に出力されます。 ターゲット デバイスが実行され、インターネットに接続されている場合、成功したデプロイは数分以内に適用されます。

## <a name="view-modules-on-your-device"></a>デバイス上のモジュールを表示する

モジュールをデバイスにデプロイした後で、そのすべてを **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションで表示できます。 IoT Edge デバイスの横の矢印を選択して展開します。 現在実行中のすべてのモジュールが表示されます。

デバイスに新しいモジュールを最近デプロイした場合は、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション ヘッダーをポイントし、更新アイコンをクリックしてビューを更新します。

モジュールの名前を右クリックして、モジュール ツインを表示および編集します。

## <a name="next-steps"></a>次の手順

[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)の方法を学習します
