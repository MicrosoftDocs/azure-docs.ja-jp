---
title: IoT Hub から Azure IoT Central にデバイスを移動する方法
description: IoT Hub から Azure IoT Central にデバイスを移動する方法
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 543c3f1c72857098540cc2a77e8a0093b907b799
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210837"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>IoT Hub から Azure IoT Central にデバイスを移動する方法

"*この記事は、オペレーターとデバイス開発者を対象としています。* "  

この記事では、IoT Hub から Azure IoT Central アプリケーションにデバイスを移動する方法について説明します。 

デバイスは、アプリケーションに接続するうえで必要な情報を取得するために、まず DPS エンドポイントに接続します。 IoT Central アプリケーションは、内部的に IoT ハブを使用してデバイスの接続を処理します。  

デバイスは、接続文字列を使用するか、DPS を使用して、IoT ハブに直接接続できます。 [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) は、IoT Central のルートです。

## <a name="to-move-the-device-to-azure-iot-central"></a>デバイスを Azure IoT Central に移動するには

デバイスを IoT Hub から IoT Central に接続するには、次の方法でデバイスを更新する必要があります。

* IoT Central アプリケーションの [スコープ ID](../../iot-dps/concepts-service.md)。
* [グループの SAS](concepts-get-connected.md) キー、または [x.509 証明書](../../iot-hub/iot-hub-x509ca-overview.md)から派生したキー

IoT Central と対話するには、デバイスが実装するプロパティ/テレメトリ/コマンドをモデル化するデバイス テンプレートが必要です。 詳細については、「[Azure IoT Central に接続する](concepts-get-connected.md)」および「[デバイス テンプレートとは](concepts-device-templates.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

デバイス開発者にお勧めする次のステップは次のとおりです。

- 「[チュートリアル:クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)」で、SAS トークンの使い方を示すサンプル コードを確認する
- [IoT Central アプリケーション用の Node.js デバイス SDK を使用して、x.509 証明書を使用するデバイスを接続する](how-to-connect-devices-x509.md)方法を確認する
- [Azure CLI を使用してデバイスの接続性を監視する](./howto-monitor-devices-azure-cli.md)方法を確認する
- [Azure IoT Central アプリケーションで新しい IoT デバイスの種類を定義する](./howto-set-up-template.md)方法を確認する
- [Azure IoT Edge デバイスと Azure IoT Central](./concepts-iot-edge.md) について確認する