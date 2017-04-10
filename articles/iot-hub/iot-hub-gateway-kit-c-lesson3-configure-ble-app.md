---
title: "SensorTag デバイスと Azure IoT Gateway - レッスン 3: サンプル アプリの実行 | Microsoft Docs"
description: "BLE SensorTag からデータを受信して IoT ハブに送信する BLE サンプル アプリケーションを実行します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "BLE アプリ, センサー モニター アプリ, センサー データの収集, センサーのデータ, クラウドのセンサー データ"
ms.assetid: b33e53a1-1df7-4412-ade1-45185aec5bef
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a9f689c0b231af3cdf9257e2179bf86ff7bc9a31
ms.lasthandoff: 01/25/2017


---
# <a name="configure-and-run-a-ble-sample-application"></a>BLE サンプル アプリケーションの構成と実行

## <a name="what-you-will-do"></a>学習内容

- リポジトリを複製します。 
- SensorTag と Intel NUC 間の接続をセットアップします。 
- Azure CLI を使用して、BLE (Bluetooth Low Energy) サンプル アプリケーション用の IoT ハブと SensorTag の情報を取得し、 BLE アンプル アプリケーションを構成して実行します。 

問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容

この記事では、次のことについて説明します。

- BLE アンプル アプリケーションを構成して実行する方法。

## <a name="what-you-need"></a>必要なもの

次の作業を完了している必要があります。

- [IoT ハブの作成と SensorTagの登録](iot-hub-gateway-kit-c-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>ホスト コンピューターへのサンプル リポジトリの複製

サンプル リポジトリを複製するには、ホスト コンピューターで次の手順を実行します。

1. Windows でコマンド プロンプト ウィンドウを開くか、macOS または Ubuntu でターミナルを開きます。
2. 次のコマンドを実行します。

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="set-up-the-connectivity-between-sensortag-and-intel-nuc"></a>SensorTag と Intel NUC 間の接続のセットアップ

接続をセットアップするには、ホスト コンピューターで次の手順を実行します。

1. 次のコマンドを実行して、構成ファイルを初期化します。

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

2. 次のコマンドを実行して、Visual Studio Code で `config-gateway.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

3. 次のコード行を探し、`[device hostname or IP address]` を、Intel NUC の IP アドレスまたはホスト名に置き換えます。
   ![config-gateway のスクリーンショット](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

4. 次のコマンドを実行して、Intel NUC にヘルパー ツールをインストールします。

   ```bash
   gulp install-tools
   ```

5. 次の画像のように電源ボタンを押して SensorTag をオンにします。緑色の LED が点滅します。

   ![センサー タグをオンにする](media/iot-hub-gateway-kit-lessons/lesson3/turn on_off sensortag.jpg)

6. 次のコマンドを実行して、SensorTag デバイスをスキャンします。

   ```bash
   gulp discover-sensortag
   ```

7. 次のコマンドを実行して、SensorTag と Intel NUC 間の接続をテストします。

   ```bash
   gulp test-connectivity --mac {mac address}
   ```

   `{mac address}`を、前の手順で取得した名前に置き換えます。

## <a name="get-the-connection-string-of-sensortag"></a>SensorTag の接続文字列の取得

SensorTag の Azure IoT ハブ接続文字列を取得するには、ホスト コンピューターで次のコマンドを実行します。

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` は、使用する IoT ハブの名前です。 レッスン 2 で値を変更していない場合は、`{resource group name}` の値として iot-gateway を、`{device id}` の値として mydevice を使用します。

## <a name="configure-the-ble-sample-application"></a>BLE サンプル アプリケーションの構成

BLE サンプル アプリケーションを構成して実行するには、ホスト コンピューターで次の手順を実行します。

1. 次のコマンドを実行して、Visual Studio Code で `config-sensortag.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![config-sensortag のスクリーンショット](media/iot-hub-gateway-kit-lessons/lesson3/config_sensortag.png)

2. コードを次のように置き換えます。
   - `[IoT hub name]` を使用する IoT ハブの名前に置き換えます。
   - `[IoT device connection string]` を、取得済みの SensorTag の接続文字列に置き換えます。
   - `[device_mac_address]` を、取得済みの SensorTag の MAC アドレスに置き換えます。

3. BLE サンプル アプリケーションを実行します。

   BLE サンプル アプリケーションを実行するには、ホスト コンピューターで次の手順を実行します。

   1. センサー タグをオンにします。

   2. 次のコマンドを実行して、Intel NUC にサンプル アプリケーションをデプロイして実行します。
   
      ```bash
      gulp run
      ```

## <a name="verify-that-the-ble-sample-application-works"></a>BLE サンプル アプリケーションの動作確認

出力は次のように表示されます。

![BLE サンプル アプリケーションの出力](media/iot-hub-gateway-kit-lessons/lesson3/BLE_running.png)

サンプル アプリケーションは、繰り返し温度データを収集して IoT ハブに送信します。 このサンプル アプリケーションは、40 秒間送信を実行した後で自動的に終了します。

## <a name="summary"></a>まとめ

SensorTag と Intel NUC 間の接続を適切にセットアップし、SensorTag からデータを収集して IoT ハブに送信する BLE サンプル アプリケーションを実行しました。 IoT ハブがデータを受信したことを確認する方法を学習する準備ができました。

## <a name="next-steps"></a>次のステップ
[IoT ハブからのメッセージの読み取り](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)
