---
title: "Azure IoT への Raspberry Pi (Node) の接続 - レッスン 4: アプリの変更 | Microsoft Docs"
description: "LED のオンとオフの動作を変更するメッセージをカスタマイズします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Raspberry Pi を使用した LED の制御, Raspberry Pi による LED の制御, Raspberry Pi で制御する LED"
ms.assetid: 3b42a4ad-0197-42f6-8ca9-04c883e879e8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 53c9408bedc6c61cdb0e755b46b3090e8723e271
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>LED のオンとオフの動作の変更
## <a name="what-you-will-do"></a>学習内容
LED のオンとオフの動作を変更するメッセージをカスタマイズします。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-raspberry-pi-kit-node-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
Node.js 関数を追加して、LED のオンとオフの動作を変更します。

## <a name="what-you-need"></a>必要なもの
[クラウドからデバイスへのメッセージを受信する Raspberry Pi のサンプル アプリケーションの実行](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)を正常に完了している必要があります。

## <a name="add-nodejs-functions"></a>Node.js 関数の追加
1. 以下のコマンドを実行して、Visual Studio Code でサンプル アプリケーションを開きます。
   
   ```bash
   cd Lesson4
   code .
   ```
2. `app.js` ファイルを開いて、末尾に以下の関数を追加します。
   
   ```javascript
   function turnOnLED() {
     wpi.digitalWrite(CONFIG_PIN, 1);
   }
   
   function turnOffLED() {
     wpi.digitalWrite(CONFIG_PIN, 0);
   }
   ```
   
   ![関数を追加した App.js ファイル](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)
3. `receiveMessageCallback` 関数の switch-case ブロックにある既定の条件の前に、以下の条件を追加します。
   
   ```javascript
   case 'on':
     turnOnLED();
     break;
   case 'off':
     turnOffLED();
     break;
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
   
   ![関数を追加した Gulpfile.js ファイル](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)
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

![サンプル アプリケーションの "オン/オフ" メッセージ](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

お疲れさまでした。 IoT ハブから Pi に送信されるメッセージを正常にカスタマイズしました。

### <a name="summary"></a>概要
このオプションのセクションでは、サンプル アプリケーションが LED のオンとオフの動作を通常とは異なる方法で制御できるようにメッセージをカスタマイズする方法を紹介しました。


