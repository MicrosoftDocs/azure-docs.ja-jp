---
title: ゲートウェイを使用して modbus プロトコルを変換する - Azure IoT Edge | Microsoft Docs
description: IoT Edge ゲートウェイ デバイスを作成することで、Modbus TCP を使用したデバイスと Azure IoT Hub との間で通信を実現します。
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 325b69eb7b9b069db0ba49b4578541ee801c3444
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476171"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>IoT Edge デバイス ゲートウェイ経由で Modbus TCP デバイスに接続する

Modbus TCP (または RTU) プロトコルを使用した IoT デバイスを Azure IoT Hub に接続するには、ゲートウェイとして IoT Edge デバイスを使用します。 ゲートウェイ デバイスが Modbus デバイスからデータを読み取り、サポートされているプロトコルを使用して、そのデータをクラウドに伝送します。

![Modbus デバイスを IoT Edge ゲートウェイ経由で IoT Hub に接続](./media/deploy-modbus-gateway/diagram.png)

この記事では、Modbus モジュール用に独自のコンテナー イメージを作成 (あらかじめ用意されているサンプルを使用してもかまいません) し、ゲートウェイとして機能する IoT Edge デバイスにそのイメージをデプロイする方法について取り上げます。

この記事は、Modbus TCP プロトコルの使用を前提として執筆されています。 Modbus RTU をサポートするようにモジュールを構成する方法について詳しくは、GitHub の [Azure IoT Edge Modbus モジュール](https://github.com/Azure/iot-edge-modbus) プロジェクトを参照してください。

## <a name="prerequisites"></a>前提条件
* Azure IoT Edge デバイス。 そのセットアップ方法を紹介したチュートリアルについては、「[Windows に Azure IoT Edge をデプロイする](quickstart.md)」(またはその [Linux](quickstart-linux.md) 版) を参照してください。
* IoT Edge デバイスの主キー接続文字列。
* Modbus TCP をサポートする物理 (またはシミュレーション) Modbus デバイス。

## <a name="prepare-a-modbus-container"></a>Modbus コンテナーの準備

Modbus ゲートウェイの機能をテストしたい方のために、Microsoft からサンプル モジュールが提供されています。 Azure Marketplace や [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview) から、またはイメージ URI (**mcr.microsoft.com/azureiotedge/modbus:1.0**) を使用してモジュールにアクセスできます。

独自のモジュールを作成し、実際の環境に合わせてカスタマイズする場合は、GitHub で公開されているオープン ソースの [Azure IoT Edge Modbus モジュール](https://github.com/Azure/iot-edge-modbus) プロジェクトをご利用ください。 プロジェクトのガイダンスに従って独自のコンテナー イメージを作成できます。 コンテナー イメージを作成するには、[Visual Studio での C# モジュールの開発](how-to-visual-studio-develop-csharp-module.md)に関するページ、または [Visual Studio Code でのモジュールの開発](how-to-vs-code-develop-module.md)に関するページをご覧ください。 これらの記事では、新規のモジュール作成や、コンテナー イメージのレジストリへの発行に関する手順について説明します。

## <a name="try-the-solution"></a>ソリューションをお試しください

このセクションでは、IoT Edge デバイスに Microsoft のサンプル Modbus モジュールをデプロイする手順について説明します。

1. [Azure Portal](https://portal.azure.com/) で IoT Hub に移動します。

2. **[IoT Edge]** に移動し、使用する IoT Edge デバイスをクリックします。

3. **[Set modules]\(モジュールの設定\)** を選びます。

4. Modbus モジュールを追加します。

   1. **[追加]** をクリックし、 **[IoT Edge モジュール]** を選択します。

   2. **[名前]** フィールドに「modbus」と入力します。

   3. **[イメージ]** フィールドに、サンプル コンテナーのイメージの URI として「`mcr.microsoft.com/azureiotedge/modbus:1.0`」を入力します。

   4. 必要なモジュール ツインのプロパティを更新するには、 **[Enable]\(有効\)** チェック ボックスをオンにします。

   5. 次の JSON をテキスト ボックスにコピーします。 **SlaveConnection** の値を実際の Modbus デバイスの IPv4 アドレスに変更します。

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"40001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. **[保存]** を選択します。

5. **[モジュールの追加]** 手順に戻り、 **[次へ]** を選択します。

7. **[Specify Routes]\(ルートの指定\)** の手順で、以下の JSON をテキスト ボックスにコピーします。 Modbus モジュールによって収集されたすべてのメッセージが、このルートによって IoT Hub に送信されます。 このルートの **modbusOutput** は、Modbus モジュールがデータを出力するために使用するエンドポイントです。また、 **$upstream** は特別な転送先で、IoT Edge ハブに対して、メッセージを IoT Hub に送信するように指示します。

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. **[次へ]** を選択します。

9. **[Review Deployment]\(展開のレビュー\)** ステップで、 **[送信]** を選びます。

10. デバイスの詳細ページに戻り、 **[更新]** を選びます。 新しい **modbus** モジュールが IoT Edge ランタイムと一緒に実行されていることが表示されます。

## <a name="view-data"></a>データの表示
Modbus モジュールを介して送信されているデータを確認します。
```cmd/sh
iotedge logs modbus
```

[Visual Studio Code 用の Azure IoT Hub Toolkit 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (以前は Azure IoT Toolkit 拡張機能) を使用して、デバイスによって送信されているテレメトリを表示することもできます。

## <a name="next-steps"></a>次の手順

- IoT Edge デバイスのゲートウェイとしての振る舞いについて詳しくは、「[透過的なゲートウェイとして動作する IoT Edge デバイスを作成する](./how-to-create-transparent-gateway.md)」を参照してください。
- IoT Edge モジュールの動作について詳しくは、「[Azure IoT Edge モジュールについて](iot-edge-modules.md)」を参照してください。
