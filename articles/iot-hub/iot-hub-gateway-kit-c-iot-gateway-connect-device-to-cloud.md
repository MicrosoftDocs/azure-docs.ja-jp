---
title: "IoT ゲートウェイを使用して Azure IoT Hub にデバイスを接続する | Microsoft Docs"
description: "IoT のゲートウェイとして Intel NUC を使用して、TI SensorTag を接続し、クラウドで Azure IoT Hub にセンサー データを送信する方法について説明します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IoT ゲートウェイはデバイスをクラウドに接続します"
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 61cb8eb0ad23a7d4b333bf54342b872a3a8ae1da
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017

---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>IoT ゲートウェイを使用してモノをクラウドに接続する - SensorTag から Azure IoT Hub へ

> [!NOTE]
> このチュートリアルを開始する前に、[IoT ゲートウェイとしての Intel NUC のセットアップ](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)を完了しておいてください。 [IoT ゲートウェイとしての Intel NUC のセットアップ](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)で、Intel NUC デバイスを IoT ゲートウェイとして設定します。

## <a name="what-you-will-learn"></a>学習内容

IoT ゲートウェイを使用して、Texas Instruments SensorTag (CC2650STK) を Azure IoT Hub に接続する方法を学習します。 IoT ゲートウェイは、SensorTag から収集された温度と湿度のデータを Azure IoT Hub に送信します。

## <a name="what-you-will-do"></a>学習内容

- IoT Hub を作成します。
- SensorTag のために IoT Hub 内でデバイスを登録します。
- IoT ゲートウェイと SensorTag の間の接続を有効にします。
- BLE サンプル アプリケーションを実行し、SensorTag データを IoT Hub に送信します。

## <a name="what-you-need"></a>必要なもの

- Intel NUC を IoT ゲートウェイとしてセットアップする [IoT ゲートウェイとしての Intel NUC のセットアップ](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) が完了していること。
- * 有効な Azure サブスクリプション Azure アカウントがない場合は、[無料試用版の Azure アカウント](https://azure.microsoft.com/free/)を数分で作成できます。
- ホスト コンピューターで実行される SSH クライアント。 Windows では PuTTY をお勧めします。 Linux と macOS には既に SSH クライアントが付属しています。
- IP アドレスと、SSH クライアントからゲートウェイにアクセスするためのユーザー名とパスワード。
- インターネット接続。

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> ここで、この新しいデバイスを SensorTag 用に登録します

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>IoT ゲートウェイと SensorTag の間の接続を有効にします

このセクションでは、次のタスクを実行します。

- Bluetooth 接続のための SensorTag の MAC アドレスを取得する。
- IoT ゲートウェイから SensorTag への Bluetooth 接続を開始する。

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>Bluetooth 接続のための SensorTag の MAC アドレスの取得

1. ホスト コンピューターで SSH クライアントを実行して IoT ゲートウェイに接続します。
1. 次のコマンドを実行して Bluetooth のブロックを解除します。

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. 次のコマンドを実行して IoT ゲートウェイで Bluetooth サービスを開始し、Bluetooth を構成するための Bluetooth シェルを開始します。

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. Bluetooth シェルで次のコマンドを実行して、Bluetooth コント ローラーをパワーオンします。

   ```bash
   power on
   ```

   ![bluetoothctl を使用して IoT ゲートウェイ上の Bluetooth コント ローラーをパワーオンする](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. 次のコマンドを実行して、近くの Bluetooth デバイスのスキャンを開始します。

   ```bash
   scan on
   ```

   ![bluetoothctl を使用して近くの Bluetooth デバイスをスキャンする](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. SensorTag のペアリング ボタンを押します。 SensorTag の緑色の LED が点滅します。
1. Bluetooth シェルに SensorTag が見つかったことが表示されます。 SensorTag の MAC アドレスをメモしておきます。 この例では、SensorTag の MAC アドレスは `24:71:89:C0:7F:82` です。
1. 次のコマンドを実行してスキャンをオフにします。

   ```bash
   scan off
   ```

   ![bluetoothctl を使用した近くの Bluetooth デバイスのスキャンを停止する](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>IoT ゲートウェイから SensorTag への Bluetooth 接続の開始

1. 次のコマンドを実行して SensorTag に接続します。

   ```bash
   connect <MAC address>
   ```

   ![bluetoothctl を使用して SensorTag に接続する](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. 次のコマンドを実行して SensorTag から切断し、Bluetooth シェルを終了します。

   ```bash
   disconnect
   exit
   ```

   ![bluetoothctl を使用して SensorTag から切断する](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

SensorTag と IoT ゲートウェイの間の接続が正常に有効になりました。

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>BLE サンプル アプリケーションを実行して SensorTag データを IoT Hub に送信する

Bluetooth Low Energy (BLE) サンプル アプリケーションは、Azure IoT Edge によって提供されます。 サンプル アプリケーションでは、BLE 接続からデータを収集し、IoT hub にデータを送信します。 サンプル アプリケーションを実行するには、次のことを行う必要があります。

1. サンプル アプリケーションを構成する。
1. IoT ゲートウェイでサンプル アプリケーションを実行する。

### <a name="configure-the-sample-application"></a>サンプル アプリケーションの構成

1. 次のコマンドを実行して、サンプル アプリケーションのフォルダーに移動します。

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples/ble_gateway
   ```

1. 次のコマンドを実行して、構成ファイルを開きます。

   ```bash
   vi ble_gateway.json
   ```

1. 構成ファイルで、次の値を入力します。

   **IoTHubName**: IoT Hub の名前。

   **IoTHubSuffix**: メモしておいたデバイス接続文字列の主キーから IoTHubSuffix を取得します。 IoT Hub 接続文字列の主キーでなく、デバイス接続文字列の主キーを取得していることを確認します。 デバイス接続文字列の主キーの形式は `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY` です。

   **Transport**: 既定値は `amqp` です。 この値は転送中のプロトコルを示します。 これは `http`、`amqp`、または `mqtt` の場合もあります。

   **macAddress**: メモしておいた SensorTag の MAC アドレス。

   **deviceID**: IoT Hub で作成したデバイスの ID。

   **deviceKey**: デバイス接続文字列の主キー。

   ![BLE サンプル アプリケーションの構成ファイルの入力](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. `ESC` キーを押し、`:wq` と入力してファイルを保存します。

### <a name="run-the-sample-application"></a>サンプル アプリケーションの実行

1. SensorTag がパワーオンになっていることを確認します。
1. 次のコマンドを実行します。

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>次のステップ

[Azure IoT Edge でセンサー データを変換するための IoT ゲートウェイを使用する](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)

