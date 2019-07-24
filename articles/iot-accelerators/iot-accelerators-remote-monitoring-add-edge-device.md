---
title: リモート監視ソリューションによる Edge デバイスの追加 - Azure | Microsoft Docs
description: この記事では、IoT Edge デバイスをリモート監視ソリューション アクセラレータに追加する方法について説明します
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: d34ac159a216c5c77214b4c8b799a233c3671235
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749547"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>IoT Edge デバイスをリモート監視ソリューション アクセラレータに追加する

[IoT Edge](../iot-edge/about-iot-edge.md) デバイスをソリューション アクセラレータに追加するには、次の 2 つの手順を実行します。

1. リモート監視ソリューション アクセラレータ Web UI の **[デバイス エクスプローラー]** ページで Edge デバイスを追加します。
1. Edge デバイスに IoT Edge ランタイムをインストールします。

## <a name="add-the-iot-edge-device"></a>IoT Edge デバイスを追加する

リモート監視ソリューション アクセラレータに IoT Edge デバイスを追加するには、Web UI の **[Device Explorer]** ページに移動し、**[+ 新規デバイス]** をクリックします。

**[新規デバイス]** パネルで、**[IoT Edge デバイス]** を選択します。 その他の設定は既定値のままでかまいません。 次に、**[適用]** をクリックします。

![IoT Edge デバイスを追加する](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>IoT Edge デバイスを追加するその他の方法

IoT Edge デバイスをソリューション アクセラレータの IoT Hub インスタンスに直接登録することもできます。 次のような攻略ガイドに従う前に、ソリューション アクセラレータで IoT ハブの名前を知る必要があります。

- [Azure portal から新しい Azure IoT Edge デバイスを登録する](../iot-edge/how-to-register-device-portal.md)
- [Azure CLI を使用して新しい Azure IoT Edge デバイスを登録する](../iot-edge/how-to-register-device-cli.md)
- [Visual Studio Code から新しい Azure IoT Edge デバイスを登録する](../iot-edge/how-to-register-device-vscode.md)

リモート監視ソリューション アクセラレータの IoT ハブにデバイスを登録すると、それが Web UI の **[デバイス エクスプローラー]** ページに表示されます。

## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

Edge デバイスにモジュールをデプロイする前に、IoT Edge ランタイムを実際のデバイスにインストールする必要があります。 次の攻略ガイドでは、一般的なデバイス プラットフォームにランタイムをインストールする方法を説明しています。

- [Linux に Azure IoT Edge ランタイムをインストールする (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Linux に Azure IoT Edge ランタイムをインストールする (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Windows に Azure IoT Edge をインストールして Windows コンテナーと共に使用する](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Windows に Azure IoT Edge をインストールして Linux コンテナーと共に使用する](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [IoT Edge ランタイムを Windows IoT Core にインストールする](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>次の手順

IoT Edge デバイスの準備が完了したら、次はモジュールを IoT Edge デバイスに配置します。 「[リモート監視ソリューション アクセラレータに IoT Edge パッケージをインポートする](iot-accelerators-remote-monitoring-import-edge-package.md)」を参照してください。
