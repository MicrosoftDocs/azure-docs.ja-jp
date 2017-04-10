---
title: "Azure IoT への Arduino (C) の接続 - レッスン 4: クラウドからデバイス | Microsoft Docs"
description: "サンプル アプリケーションは、Adafruit Feather M0 WiFi 上で動作し、IoT Hub からの受信メッセージを監視します。 新しい gulp タスクによって、IoT ハブから Adafruit Feather M0 WiFi にメッセージを送信して、LED を点滅させます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Web からの Arduino による LED の制御, Web 経由の Arduino による LED の制御"
ms.assetid: a0bf53fb-29fb-485f-ba4a-6c715057b1a2
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 4f9feb665ac93028023df86889ca53a64ac2ec0e
ms.openlocfilehash: 63113841ca836681232e0aa43b15b444f8bb92e2
ms.lasthandoff: 01/28/2017


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>クラウドからデバイスへのメッセージを受信するサンプル アプリケーションの実行
この記事では、Adafruit Feather M0 WiFi Arduino ボードにサンプル アプリケーションをデプロイします。

このサンプル アプリケーションは、IoT Hub から受信するメッセージを監視するものです。 また、コンピューターで gulp タスクを実行して、IoT Hub から Arduino ボードにメッセージを送信します。 このサンプル アプリケーションは、メッセージを受信すると LED を点滅させます。 問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策を探してください。

## <a name="what-you-will-do"></a>学習内容
* サンプル アプリケーションを IoT Hub に接続する。
* サンプル アプリケーションをデプロイし、実行する。
* IoT Hub から Arduino ボードにメッセージを送信して LED を点滅させる。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。
* IoT Hub から受信したメッセージを監視する方法。
* IoT ハブから Arduino ボードに、クラウドからデバイスへのメッセージを送信する方法。

## <a name="what-you-need"></a>必要なもの
* セットアップ済みの Arduino ボード。 Arduino ボードのセットアップ方法については、「[Configure your device (デバイスを構成する)][configure-your-device]」をご覧ください。
* Azure サブスクリプションで作成した IoT Hub。 IoT ハブの作成方法については、[Azure IoT Hub の作成][create-your-azure-iot-hub]に関する記事をご覧ください。

## <a name="connect-the-sample-application-to-your-iot-hub"></a>IoT ハブへのサンプル アプリケーションの接続

1. 現在のリポジトリ フォルダーが `iot-hub-c-feather-m0-getting-started` であることを確認します。

   以下のコマンドを実行して、Visual Studio Code でサンプル アプリケーションを開きます。

   ```bash
   cd Lesson4
   code .
   ```

   `app` サブフォルダーに `app.ino` ファイルがあります。 この `app.ino` ファイルは、IoT ハブから受信したメッセージを監視するためのコードが入ったキー ソース ファイルです。 `blinkLED` 関数は、LED を点滅させるものです。

   ![サンプル アプリケーションのリポジトリの構造][repo-structure]

2. デバイス検出 CLI を使用してデバイスのシリアル ポートを取得します。

   ```bash
   devdisco list --usb
   ```

   次のような出力が表示され、Arduino ボード用の USB COM ポートが見つかります。

   ![デバイスの検出][device-discovery]

3. レッスン フォルダーの `config.json` ファイルを開き、見つかった COM ポート番号の値を入力します。

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Windows プラットフォームの COM ポートの場合は、`COM1, COM2, ...` という形式です。 MacOS または Ubuntu の場合は、`/dev/` で始まります。

4. 次のコマンドを実行して、構成ファイルを初期化します。

   ```bash
   # For Windows command prompt
   npm install
   gulp init
   gulp install-tools
   ```

5. `config-arduino.json` ファイルの値を次のように置き換えます。

   [Azure Function App とストレージ アカウントの作成][create-an-azure-function-app-and-storage-account]の手順をこのコンピューターで完了している場合はすべての構成が継承されるため、この手順を省略して、サンプル アプリケーションをデプロイして実行するタスクに進むことができます。 [Azure Function App とストレージ アカウントの作成][create-an-azure-function-app-and-storage-account]の手順を他のコンピューターで完了している場合は、`config-arduino.json` ファイル内のプレースホルダーを置き換える必要があります。 `config-arduino.json` ファイルは、ホーム フォルダーのサブフォルダーにあります。

   ![config-arduino.json ファイルの内容][config-arduino-json]

   * **[Wi-Fi SSID]** をインターネットに接続している Wi-Fi SSID に置き換えます。
   * **[Wi-Fi password]** をお使いの Wi-Fi パスワードに置き換えます。 Wi-Fi パスワードが不要な場合は、文字列を削除します。
   * **[IoT device connection string]** の部分を、デバイスの接続文字列で置き換えます。デバイスの接続文字列は、`az iot device show-connection-string --hub-name {my hub name} --device-id {device id}` コマンドで取得できます。
   * **[IoT hub connection string]** の部分を、IoT ハブの接続文字列で置き換えます。IoT ハブの接続文字列は、`az iot hub show-connection-string --name {my hub name}` コマンドで取得できます。

## <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションを Arduino ボードにデプロイして実行します。

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

この gulp コマンドにより、Arduino ボードにサンプル アプリケーションがデプロイされます。 次に、Arduino ボードでアプリケーションが実行されます。このとき、ホスト コンピューターでは別のタスクとして、Arduino ボードから IoT Hub に 20 件の blink メッセージが送信されます。

サンプル アプリケーションは、実行されると、IoT ハブからのメッセージのリッスンを開始します。 このとき、gulp タスクによって IoT Hub から Arduino ボードに "blink" メッセージが複数回送信されます。 ボードが blink メッセージを受信するたびに、サンプル アプリケーションは LED を点滅させる `blinkLED` 関数を呼び出します。

gulp タスクによって IoT ハブから Arduino ボードに 20 件のメッセージが送信され、LED が 2 秒間隔で点滅します。 最後のメッセージは、アプリケーションの実行を停止する "stop" メッセージです。

![サンプル アプリケーションの実行と gulp コマンドと blink メッセージ][sample-application]

## <a name="summary"></a>概要
IoT Hub から Arduino ボードにメッセージを送信して、LED を点滅させることができました。 次の、LED のオンとオフの動作の変更タスクは省略可能です。

## <a name="next-steps"></a>次のステップ
[LED のオン/オフの動作を変更する][change-the-on-and-off-led-behavior]


<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/repo_structure_arduino.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[create-an-azure-function-app-and-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/config-arduino.png
[sample-application]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_blink_arduino.png
[change-the-on-and-off-led-behavior]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
