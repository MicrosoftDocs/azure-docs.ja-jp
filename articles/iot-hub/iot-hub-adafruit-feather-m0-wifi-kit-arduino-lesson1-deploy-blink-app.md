---
title: "Azure IoT への Arduino の接続 - レッスン 1: アプリのデプロイ | Microsoft Docs"
description: "GitHub のサンプル Arduino アプリケーションを複製し、gulp を実行してこのアプリケーションを Adafruit Feather M0 WiFi にデプロイします。 このサンプル アプリケーションでは、GPIO を点滅させます"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Arduino LED プロジェクト, Arduino LED 点滅, Arduino LED 点滅コード, Arduino 点滅プログラム, Arduino 点滅の例"
ms.assetid: b0a7d076-d580-4686-9f7d-c0712750b615
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a2058611cecb33eab04de9249ad73b1c4ec6c2a5
ms.lasthandoff: 01/24/2017


---
# <a name="create-and-deploy-the-blink-application"></a>点滅アプリケーションを作成してデプロイする
## <a name="what-you-will-do"></a>学習内容
GitHub のサンプル Arduino アプリケーションを複製し、gulp ツールを使ってサンプル アプリケーションを Adafruit Feather M0 WiFi Arduino ボードにデプロイします。 このサンプル アプリケーションでは、GPIO #13 ボード搭載 LED を 2 秒間隔で点滅させます。

問題が発生した場合は、[トラブルシューティングのページ][troubleshooting-page]で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
* Arduino ボードにサンプル アプリケーションをデプロイして実行する方法。

## <a name="what-you-need"></a>必要なもの
次の作業を正常に完了している必要があります。

* [デバイスの構成][configure-your-device]
* [ツールの入手][get-the-tools]

## <a name="open-the-sample-application"></a>サンプル アプリケーションを開く
サンプル アプリケーションを開くには、次の手順を実行します。

1. 次のコマンドを実行して、GitHub からサンプル リポジトリを複製します。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-feather-m0-getting-started.git
   ```
2. 以下のコマンドを実行して、Visual Studio Code でサンプル アプリケーションを開きます。

   ```bash
   cd iot-hub-c-feather-m0-getting-started
   cd Lesson1
   code .
   ```

   ![リポジトリの構造][repo-structure]

`app` サブフォルダーの `app.ino` ファイルは、LED を制御するコードを含むキー ソース ファイルです。

### <a name="install-application-dependencies"></a>アプリケーションの依存関係のインストール
次のコマンドを実行して、サンプル アプリケーションに必要なライブラリやその他のモジュールをインストールします。

```bash
npm install
```

## <a name="configure-the-device-connection"></a>デバイス接続の構成
デバイス接続を構成するには、次の手順を実行します。

1. デバイス検出 CLI を使用してデバイスのシリアル ポートを取得します。

   ```bash
   devdisco list --usb
   ```

   次のような出力が表示され、Arduino ボード用の USB COM ポートが見つかります。![デバイスの検出][device-discovery]

2. レッスン フォルダーの `config.json` ファイルを開き、見つかった COM ポート番号の値を追加します。

   ```json
   {
       "device_port" : "COM1"
   }
   ```
   ![config.json][config-json]
   > [!NOTE]
   > Windows プラットフォームの COM ポートの場合は、`COM1, COM2, ...` という形式です。 MacOS や Ubuntu の場合は、`/dev/` で始まります。

## <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
### <a name="install-the-required-tools-for-your-arduino-board"></a>Arduino ボードに必要なツールのインストール

次のコマンドを実行して、Arduino ボード用の Azure IoT Hub SDK をインストールします。

```bash
gulp install-tools
```

ネットワーク接続に応じて、このタスクが完了するまでに時間がかかる場合があります。

> [!NOTE]
> gulp タスク `install-tools` と `run` を実行する際は、実行中の Arduino IDE インスタンスを終了してください。

### <a name="deploy-and-run-the-sample-app"></a>サンプル アプリのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションをデプロイして実行します。

```bash
gulp run

# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

### <a name="verify-the-app-works"></a>アプリの動作確認
LED が点滅していない場合、一般的な問題の解決策については、[トラブルシューティング ガイド][troubleshooting-page]をご覧ください。

![LED の点滅][led-blinking]

## <a name="summary"></a>概要
Arduino ボードを操作するために必要なツールをインストールし、Arduino ボードにサンプル アプリケーションをデプロイして、LED を点滅させました。 これで、Arduino ボードを Azure IoT Hub に接続してメッセージを送受信する別のサンプル アプリケーションを作成、デプロイ、実行する準備が整いました。

## <a name="next-steps"></a>次のステップ
[Azure ツールを入手する][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting-page]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-blink-arduino-mac.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[led-blinking]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
