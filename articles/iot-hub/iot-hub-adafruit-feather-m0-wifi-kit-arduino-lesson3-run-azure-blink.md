---
title: "Azure IoT への Arduino (C) の接続 - レッスン 3: サンプルの実行 | Microsoft Docs"
description: "IoT ハブにメッセージを送信して LED を点滅させるサンプル アプリケーションを Adafruit Feather M0 WiFi にデプロイして実行します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT クラウド サービス, クラウドにデータを送信する Arduino"
ms.assetid: 92cce319-2b17-4c9b-889d-deac959e3e7c
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 631a6677e4801b1c3475b9e8f2726219dd101132
ms.lasthandoff: 01/24/2017


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージを送信するサンプル アプリケーションの実行
## <a name="what-you-will-do"></a>学習内容
この記事では、IoT ハブにメッセージを送信する Adafruit Feather M0 WiFi Arduino ボードにサンプル アプリケーションをデプロイして実行する方法を説明します。

問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策をお探しください。

## <a name="what-you-will-learn"></a>学習内容
gulp ツールを使用し、サンプル Arduino アプリケーションを Arduino ボードにデプロイして実行する方法について説明します。

## <a name="what-you-need"></a>必要なもの
* このタスクを開始する前に、[IoT ハブのメッセージを処理して格納するための Azure Function App とストレージ アカウントの作成][process-and-store-iot-hub-messages]を正常に完了している必要があります。

## <a name="get-your-iot-hub-and-device-connection-strings"></a>IoT ハブとデバイスの接続文字列の取得
デバイスの接続文字列は、Arduino ボードを IoT ハブに接続するために使用されます。 IoT ハブの接続文字列は、IoT ハブの Arduino ボードを表すデバイス ID に IoT ハブを接続するために使用されます。

* 次の Azure CLI コマンドを実行して、リソース グループ内のすべての IoT ハブを一覧表示します。

```bash
az iot hub list -g iot-sample --query [].name
```

値を変更していない場合、`{resource group name}` の値として `iot-sample` を使用します。

* 次の Azure CLI コマンドを実行して、IoT ハブの接続文字列を取得します。

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` は、IoT ハブを作成し、Arduino ボードを登録するときに指定した名前です。

* 次のコマンドを実行して、デバイスの接続文字列を取得します。

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id mym0wifi
```

値を変更していない場合、`{device id}` の値として `mym0wifi` を使用します。
## <a name="configure-the-device-connection"></a>デバイス接続の構成
デバイス接続を構成するには、次の手順を実行します。

1. デバイス検出 CLI を使用してデバイスのシリアル ポートを取得します。

   ```bash
   devdisco list --usb
   ```

   次のような出力が表示され、Arduino ボード用の USB COM ポートが見つかります。

   ![デバイスの検出][device-discovery]

2. レッスン フォルダーの `config.json` ファイルを開き、見つかった COM ポート番号の値を追加します。

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Windows プラットフォームの COM ポートの場合は、`COM1, COM2, ...` という形式です。 MacOS や Ubuntu の場合は、`/dev/` で始まります。

3. 次のコマンドを実行して、構成ファイルを初期化します。

   ```bash
   npm install
   gulp init
   gulp install-tools
   ```
4. 次のコマンドを実行して、Visual Studio Code でデバイス構成ファイル `config-arduino.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```

   ![config arduino.json][config-arduino-json]

5. `config-arduino.json` ファイルの値を次のように置き換えます。

   * **[Wi-fi SSID]** をインターネットに接続している Wi-fi SSID で置き換えます。
   * **[Wi-Fi password]** をお使いの Wi-Fi パスワードに置き換えます。 Wi-Fi パスワードが必要ない場合は、文字列を削除します。
   * **[IoT device connection string]** を、取得した `device connection string` で置き換えます。
   * **[IoT hub connection string]** を、取得した `iot hub connection string` で置き換えます。

   > [!NOTE]
   > この記事では、`azure_storage_connection_string` は必要ありません。 そのままにしておいてください。

## <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションを Arduino ボードにデプロイして実行します。

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

> [!NOTE]
> 既定の gulp タスクでは、`install-tools`、`run` のタスクが順番に実行されます。 [点滅アプリケーションをデプロイ][deployed-the-blink-app]した場合は、これらのタスクを個別に実行します。

## <a name="verify-that-the-sample-application-works"></a>サンプル アプリケーションの動作確認
GPIO #0 のオンボード LED が 2 秒間隔で点滅していることを確認します。 LED が点滅するたびに、サンプル アプリケーションは IoT ハブにメッセージを送信し、メッセージが IoT ハブに正常に送信されていることを検証します。 さらにコンソール ウィンドウには、IoT ハブが受信した各メッセージが出力されます。 サンプル アプリケーションは、メッセージを 20 回送信した後に自動的に終了します。

![サンプル アプリケーションの実行とメッセージの送受信の出力][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>概要
点滅を行う新しいサンプル アプリケーションを Arduino ボードにデプロイして実行し、デバイスからクラウドへのメッセージを IoT ハブに送信しました。 これで、ストレージ アカウントに書き込まれたメッセージを監視する準備が整いました。

## <a name="next-steps"></a>次のステップ
[Azure Storage に保持されたメッセージを読み取る][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/config-arduino.png
[deployed-the-blink-app]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_run_arduino.png
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
