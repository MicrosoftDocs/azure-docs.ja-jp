---
title: ゲートウェイを使用して modbus プロトコルを変換する - Azure IoT Edge | Microsoft Docs
description: IoT Edge ゲートウェイ デバイスを作成することで、Modbus TCP を使用したデバイスと Azure IoT Hub との間で通信を実現します。
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 0388520903e208b3225375d5cee81e8321740a1b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103487736"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>IoT Edge デバイス ゲートウェイ経由で Modbus TCP デバイスに接続する

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Modbus TCP または RTU プロトコルを使用する IoT デバイスを Azure IoT Hub に接続する場合は、ゲートウェイとして IoT Edge デバイスを使用できます。 ゲートウェイ デバイスが Modbus デバイスからデータを読み取り、サポートされているプロトコルを使用して、そのデータをクラウドに伝送します。

![Modbus デバイスを IoT Edge ゲートウェイ経由で IoT Hub に接続](./media/deploy-modbus-gateway/diagram.png)

この記事では、Modbus モジュール用に独自のコンテナー イメージを作成 (あらかじめ用意されているサンプルを使用してもかまいません) し、ゲートウェイとして機能する IoT Edge デバイスにそのイメージをデプロイする方法について取り上げます。

この記事は、Modbus TCP プロトコルの使用を前提として執筆されています。 Modbus RTU をサポートするようにモジュールを構成する方法について詳しくは、GitHub の [Azure IoT Edge Modbus モジュール](https://github.com/Azure/iot-edge-modbus) プロジェクトを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure IoT Edge デバイス。 そのセットアップ方法を紹介したチュートリアルについては、「[Windows に Azure IoT Edge をデプロイする](quickstart.md)」(またはその [Linux](quickstart-linux.md) 版) を参照してください。
* IoT Edge デバイスの主キー接続文字列。
* Modbus TCP をサポートする物理 (またはシミュレーション) Modbus デバイス。 その IPv4 アドレスがわかっている必要があります。

## <a name="prepare-a-modbus-container"></a>Modbus コンテナーの準備

Modbus ゲートウェイの機能をテストしたい方のために、Microsoft からサンプル モジュールが提供されています。 このモジュールには Azure Marketplace ([Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)) から、またはイメージの URI (`mcr.microsoft.com/azureiotedge/modbus:1.0`) を使用してアクセスできます。

独自のモジュールを作成し、実際の環境に合わせてカスタマイズする場合は、GitHub で公開されているオープン ソースの [Azure IoT Edge Modbus モジュール](https://github.com/Azure/iot-edge-modbus) プロジェクトをご利用ください。 プロジェクトのガイダンスに従って独自のコンテナー イメージを作成できます。 コンテナー イメージを作成するには、[Visual Studio での C# モジュールの開発](./how-to-visual-studio-develop-module.md)に関するページ、または [Visual Studio Code でのモジュールの開発](how-to-vs-code-develop-module.md)に関するページをご覧ください。 これらの記事では、新規のモジュール作成や、コンテナー イメージのレジストリへの発行に関する手順について説明します。

## <a name="try-the-solution"></a>ソリューションをお試しください

このセクションでは、IoT Edge デバイスに Microsoft のサンプル Modbus モジュールをデプロイする手順について説明します。

1. [Azure Portal](https://portal.azure.com/) で IoT Hub に移動します。

2. **[IoT Edge]** に移動し、使用する IoT Edge デバイスをクリックします。

3. **[Set modules]\(モジュールの設定\)** を選びます。

4. **[IoT Edge モジュール]** セクションで、Modbus モジュールを追加します。

   1. **[追加]** ドロップダウンをクリックし、**[Marketplace モジュール]** を選択します。
   2. `Modbus` を検索し、Microsoft の **[Modbus TCP Module] (Modbus TCP モジュール)** を選択します。
   3. このモジュールが IoT Hub に対して自動的に構成され、IoT Edge モジュールの一覧に表示されます。 また、ルートも自動的に構成されます。 **[Review + create]\(レビュー + 作成\)** を選択します。
   4. デプロイ マニフェストを確認し、**[作成]** を選択します。

5. 一覧内の Modbus モジュール (`ModbusTCPModule`) を選択し、 **[モジュール ツインの設定]** タブを選択します。モジュール ツインの目的のプロパティに必要な JSON にはデータが自動的に入力されます。

6. JSON の **SlaveConnection** プロパティを探し、その値を Modbus デバイスの IPv4 アドレスに設定します。

7. **[更新]** を選択します。

8. **[確認と作成]** を選択し、デプロイを確認して **[作成]** を選択します。

9. デバイスの詳細ページに戻り、**[更新]** を選びます。 新しい `ModbusTCPModule` モジュールが IoT Edge ランタイムと共に実行されているようすが表示されます。

## <a name="view-data"></a>データの表示

Modbus モジュールから来ているデータを表示します。

```cmd/sh
iotedge logs modbus
```

[Visual Studio Code 用の Azure IoT Hub 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (旧称 Azure IoT Toolkit 拡張機能) を使用して、デバイスから送信されているテレメトリを表示することもできます。

## <a name="next-steps"></a>次の手順

* IoT Edge デバイスのゲートウェイとしての振る舞いについて詳しくは、「[透過的なゲートウェイとして動作する IoT Edge デバイスを作成する](./how-to-create-transparent-gateway.md)」を参照してください。
* IoT Edge モジュールの動作について詳しくは、「[Azure IoT Edge モジュールについて](iot-edge-modules.md)」を参照してください。