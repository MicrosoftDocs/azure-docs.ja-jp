---
title: "Azure IoT への Raspberry Pi (C) の接続 - レッスン 4: アプリの変更 | Microsoft Docs"
description: "LED のオンとオフの動作を変更するメッセージをカスタマイズします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Raspberry Pi を使用した LED の制御, Raspberry Pi による LED の制御, Raspberry Pi で制御する LED"
ms.assetid: 0201b8ed-d5e6-4445-9a4d-1305003d1eff
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: c1c999e40ecc92a1018067822be66f14b61a8c36
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>LED のオンとオフの動作の変更
## <a name="what-you-will-do"></a>学習内容
LED のオンとオフの動作を変更するメッセージをカスタマイズします。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-raspberry-pi-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
Node.js 関数を追加して、LED のオンとオフの動作を変更します。

## <a name="what-you-need"></a>必要なもの
[クラウドからデバイスへのメッセージを受信する Raspberry Pi のサンプル アプリケーションの実行](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md)を正常に完了している必要があります。

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>main.c および gulpfile.js に関数を追加する
1. 以下のコマンドを実行して、Visual Studio Code でサンプル アプリケーションを開きます。

   ```bash
   cd Lesson4
   code .
   ```
2. `main.c` ファイルを開き、blinkLED() 関数の後に次の関数を追加します。

   ```c
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![関数を追加した main.c ファイル](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_c.png)
3. `receiveMessageCallback` 関数の `if` ブロックにある既定の条件の前に、以下の条件を追加します。

   ```c
   else if (0 == strcmp((const char*)value, "\"on\""))
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\""))
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
   }
   ```

   ![関数を追加した Gulpfile.js ファイル](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile_c.png)
5. `sendMessage` 関数の `var message = buildMessage(sentMessageCount);` の行を、以下のスニペットに示した新しい行で置き換えます。

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. 変更をすべて保存します。

### <a name="deploy-and-run-the-sample-application"></a>サンプル アプリケーションのデプロイと実行
次のコマンドを実行して、サンプル アプリケーションを Pi にデプロイして実行します。

```bash
gulp deploy && gulp run
```

LED が&2; 秒間点灯した後、2 秒間消灯します。 最後の "stop" メッセージは、サンプル アプリケーションの実行を停止するためのものです。

![サンプル アプリケーションの "オン/オフ" メッセージ](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off_c.png)

お疲れさまでした。 IoT ハブから Pi に送信されるメッセージを正常にカスタマイズしました。

### <a name="summary"></a>概要
このオプションのセクションでは、サンプル アプリケーションが LED のオンとオフの動作を通常とは異なる方法で制御できるようにメッセージをカスタマイズする方法を紹介しました。

