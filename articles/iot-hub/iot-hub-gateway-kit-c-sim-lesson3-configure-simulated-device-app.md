---
title: "シミュレートされたデバイスと Azure IoT Gateway - レッスン 3: サンプル アプリの実行 | Microsoft Docs"
description: "IoT ハブに温度データを送信するシミュレート デバイス サンプル アプリケーションを実行します"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "クラウドに送信されるデータ"
ms.assetid: 5d051d99-9749-4150-b3c8-573b0bda9c52
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 873f3cef8cb1d115f77f0d3fa2c4b50391f66d91
ms.lasthandoff: 01/25/2017


---
# <a name="configure-and-run-a-simulated-device-sample-app"></a>シミュレート デバイス サンプル アプリの構成と実行

## <a name="what-you-will-do"></a>学習内容

- リポジトリを複製します。
- Azure CLI を使用して、シミュレート デバイス サンプル アプリケーション用の IoT ハブと論理デバイスの情報を取得します。 シミュレート デバイス サンプル アプリケーションを構成して実行します。

問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-sim-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容

この記事では、次のことについて説明します。

- シミュレート デバイス サンプル アプリケーションを構成して実行する方法。

## <a name="what-you-need"></a>必要なもの

次の作業を完了している必要があります。

- [IoT ハブを作成してデバイスを登録する](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>ホスト コンピューターへのサンプル リポジトリの複製

サンプル リポジトリを複製するには、ホスト コンピューターで次の手順を実行します。

1. Windows でコマンド プロンプトを開くか、macOS または Ubuntu でターミナルを開きます。
2. 次のコマンドを実行します。

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="configure-the-simulated-device-and-your-nuc"></a>シミュレート デバイスと NUC の構成

1. 次のコマンドを実行して、Visual Studio Code で構成ファイル `config.json` を開きます。

   ```bash
   code config.json
   ```

2. `"has_sensortag": true` を `"has_sensortag": false` に置き換えます。

   ![TI SensorTag デバイスがない場合の構成](media/iot-hub-gateway-kit-lessons/lesson3/config_no_sensortag.png)

3. 次のコマンドを実行して、構成ファイルを初期化します。

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

4. 次のコマンドを実行して、Visual Studio Code で `config-gateway.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

5. 次のコード行を探し、`[device hostname or IP address]` を、Intel NUC の IP アドレスまたはホスト名に置き換えます。
   ![config-gateway のスクリーンショット](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

## <a name="get-the-connection-string-of-your-iot-hub-logical-device"></a>IoT ハブの論理デバイスへの接続文字列の取得

論理デバイスの Azure IoT ハブ接続文字列を取得するには、ホスト コンピューターで次のコマンドを実行します。

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` は、使用する IoT ハブの名前です。 レッスン 2 で値を変更していない場合は、`{resource group name}` の値として iot-gateway を、`{device id}` の値として mydevice を使用します。

## <a name="configure-the-simulated-device-cloud-upload-sample-application"></a>シミュレート デバイスからクラウドへのアップロードを実行するサンプル アプリケーションの構成

シミュレート デバイスからクラウドへのアップロードを実行するサンプル アプリケーションを構成して実行するには、ホスト コンピューターで次の手順を実行します。

1. 次のコマンドを実行して、Visual Studio Code で `config-sensortag.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![config-sensortag のスクリーンショット](media/iot-hub-gateway-kit-lessons/lesson3/config_simulated_device.png)

2. コードを次のように置き換えます。
   - `[IoT hub name]` を IoT ハブの名前に置き換えます。
   - `[IoT device connection string]`を IoT ハブの論理デバイスへの接続文字列に置き換えます。

3. アプリケーションを実行します。

   次のコマンドを実行して、アプリケーションをデプロイして実行します。

   ```bash
   gulp run
   ```

## <a name="verify-the-sample-application-works"></a>サンプル アプリケーションの動作確認

出力は次のように表示されます。

![シミュレート デバイス サンプル アプリケーションの出力](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_simudev.png)

アプリケーションは、温度データを IoT ハブに送信します。この送信は 40 秒間続きます。

## <a name="summary"></a>まとめ

シミュレート デバイスのクラウドへのアップロードを実行するサンプル アプリケーション (シミュレート デバイスを使用して IoT ハブにデータを送信します) を構成して実行しました。

## <a name="next-steps"></a>次のステップ
[IoT ハブからのメッセージの読み取り](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)
