---
title: "Azure IoT への Arduino (C) の接続 - レッスン 4: アプリの変更 | Microsoft Docs"
description: "LED のオンとオフの動作を変更するメッセージをカスタマイズします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Arduino による LED の制御"
ms.assetid: d7a25430-450e-43c4-a3ed-1eed995b8b7e
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3585dfbac8816140c0a62931920aff1a6bf7d540
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>LED のオンとオフの動作の変更
## <a name="what-you-will-do"></a>学習内容
LED のオンとオフの動作を変更するメッセージをカスタマイズします。 問題が発生した場合は、Adafruit Feather M0 WiFi Arduino ボードの[トラブルシューティングのページ](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
追加の Arduino 関数を使用して、LED のオンとオフの動作を変更します。

## <a name="what-you-need"></a>必要なもの
[クラウドからデバイスへのメッセージを受信するサンプル アプリケーションを Arduino ボードで実行する][receive-cloud-to-device-messages]作業を正常に完了している必要があります。

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>main.c と gulpfile.js への関数の追加
1. 以下のコマンドを実行して、Visual Studio Code でサンプル アプリケーションを開きます。

   ```bash
   cd Lesson4
   code .
   ```
2. `app.ino` ファイルを開き、blinkLED() 関数の後に次の関数を追加します。

   ```arduino
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![関数が追加された app.ino ファイル][app-ino-file]
3. `receiveMessageCallback` 関数の `else if` ブロックの前に、以下の条件を追加します。

   ```arduino
   else if (strcmp((const char*)value, "\"on\"") == 0)
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\"") == 0)
   {
       turnOffLED();
   }
   ```

   これで、サンプル アプリケーションが応答できるメッセージ経由の命令を増やす構成が完了しました。 "on" 命令では LED がオンになり、"off" 命令ではオフになります。
4. gulpfile.js ファイルを開いて、関数 `sendMessage` の前に新しい関数を追加します。

   ```javascript
   var buildCustomMessage = function (messageId) {
     if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
       return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
     } else if (messageId < MAX_MESSAGE_COUNT) {
       return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
     } else {
       return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
     }
   };
   ```

   ![関数を追加した Gulpfile.js ファイル][gulp-file-js]
5. `sendMessage` 関数の `var message = buildMessage(sentMessageCount);` の行を、以下のスニペットに示した新しい行で置き換えます。

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. 変更をすべて保存します。

### <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションを Arduino ボードにデプロイして実行します。

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

LED が&2; 秒間点灯した後、2 秒間消灯します。 最後の "stop" メッセージは、サンプル アプリケーションの実行を停止するためのものです。

![オンとオフ][on-and-off]

ご利用ありがとうございます。 IoT ハブから Arduino ボードに送信されるメッセージを正常にカスタマイズしました。

### <a name="summary"></a>概要
このオプションのセクションでは、サンプル アプリケーションが LED のオンとオフの動作を通常とは異なる方法で制御できるようにメッセージをカスタマイズする方法を紹介しました。

<!-- Images and links -->

[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[app-ino-file]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_app_ino.png
[gulp-file-js]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_gulpfile_js.png
[on-and-off]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_on_and_off_arduino.png
