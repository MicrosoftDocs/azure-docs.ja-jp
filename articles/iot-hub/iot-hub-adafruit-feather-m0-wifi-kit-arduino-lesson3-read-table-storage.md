---
title: "Azure IoT への Arduino (C) の接続 - レッスン 3: Table Storage | Microsoft Docs"
description: "Azure Table Storage に書き込まれた、デバイスからクラウドへのメッセージを監視します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "クラウドのデータ, クラウドのデータの収集, IoT クラウド サービス, IoT データ"
ms.assetid: 386083e0-0dbb-48c0-9ac2-4f8fb4590772
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 7a49a15b00bf25c9416235f41da8cc86e54d0062
ms.lasthandoff: 01/24/2017


---
# <a name="read-messages-persisted-in-azure-storage"></a>Azure Storage に保持されたメッセージの読み取り
## <a name="what-you-will-do"></a>学習内容
Azure Table Storage に書き込まれた、Adafruit Feather M0 WiFi Arduino ボードから IoT ハブに送信されたデバイスからクラウドへのメッセージを監視します。

問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
この記事では、gulp のメッセージ読み取りタスクを使用して、Azure Table Storage に保持されたメッセージを読み取る方法について説明します。

## <a name="what-you-need"></a>必要なもの
このプロセスを開始する前に、[Arduino ボードで Azure 点滅サンプル アプリケーションを実行する][run-blink-application]作業を正常に完了している必要があります。

## <a name="read-new-messages-from-your-storage-account"></a>ストレージ アカウントからの新しいメッセージの読み取り
前の記事では、Arduino ボードでサンプル アプリケーションを実行しました。 このサンプル アプリケーションによって、メッセージが Azure IoT ハブに送信されました。 IoT ハブに送信されたメッセージは、Azure Function App を介して Azure Table Storage に格納されます。 Azure Table Storage からメッセージを読み取るには、Azure Storage の接続文字列が必要になります。

Azure Table Storage に格納されたメッセージを読み取るには、次の手順を実行します。

1. 次のコマンドを実行して、接続文字列を取得します。

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   最初のコマンドで `storage name` を取得し、それを&2; 番目のコマンドで使用して接続文字列を取得します。 値を変更していない場合、`{resource group name}` の値として `iot-sample` を使用します。
2. 次のコマンドを実行して、Visual Studio Code で構成ファイル `config-arduino.json` を開きます。

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```
3. `[Azure storage connection string]` を、手順 1. で取得した接続文字列で置き換えます。
4. `config-arduino.json` ファイルを保存します。
5. もう一度メッセージを送信し、次のコマンドを実行して Azure Table Storage から読み取ります。

   ```bash
   gulp run --read-storage

   # You can monitor the serial port by running listen task:
   gulp listen

   # Or you can combine above two gulp tasks into one:
   gulp run --read-storage --listen
   ```

   Azure Table Storage からの読み取りのロジックは、`azure-table.js` ファイルにあります。

   ![gulp 実行 - ストレージの読み取り][gulp-run]

## <a name="summary"></a>概要
クラウドの IoT ハブに Arduino ボードを接続し、点滅を行うサンプル アプリケーションを使用してデバイスからクラウドへのメッセージを送信することに成功しました。 さらに、Azure Function App を使用して、IoT ハブの受信メッセージを Azure Table Storage に格納しました。 これで、IoT ハブから Arduino ボードに、クラウドからデバイスへのメッセージを送信する準備が整いました。

## <a name="next-steps"></a>次のステップ
[クラウドからデバイスへのメッセージを送信する][send-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[run-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[gulp-run]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_read_message_arduino.png
[send-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
