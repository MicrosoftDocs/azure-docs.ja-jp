---
title: "Azure IoT への Arduino (C) の接続 - レッスン 1: デバイスの構成 | Microsoft Docs"
description: "Adafruit Feather M0 WiFi の初回使用時の構成を行います。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Arduino のセットアップ, PC への Arduino の接続, Arduino ボード"
ms.assetid: f5b334f0-a148-41aa-b374-ce7b9f5b305a
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3789c07cd6142ba5b6a58a67813c0ead6b87bd76
ms.lasthandoff: 01/24/2017


---
# <a name="configure-your-device"></a>デバイスを構成する
## <a name="what-you-will-do"></a>学習内容
Adafruit Feather M0 WiFi Arduino ボードを組み立て、電源をオンにして、ボードの初回使用時の構成を行います。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)で解決策を探してください。

## <a name="what-you-need"></a>必要なもの
この操作を完了するには、Adafruit Feather M0 WiFi スタート キットの次のパーツが必要です。

* Adafruit Feather M0 WiFi ボード
* マイクロ B - タイプ A のUSB ケーブル

![キット][kit]

以下も必要です。

* Windows、Mac、または Linux を実行しているコンピューター。
* Arduino ボードを接続するためのワイヤレス接続。
* インターネット接続 (構成ツールをダウンロードするため)。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。

* 後続のレッスンのために Arduino ボードを組み立てて電源をオンにする方法。
* Ubuntu でシリアル ポートのアクセス許可を追加する方法。

## <a name="connect-your-arduino-board-to-your-computer"></a>コンピューターへの Arduino ボードの接続

1. 上部のマイクロ USB ポートにマイクロ USB ケーブルを接続します。

   ![上部のマイクロ USB ポート][top-micro-usb-port]

2. USB ケーブルのもう一方の端をコンピューターに接続します。

   ![コンピューターの USB][computer-usb]

## <a name="add-serial-port-permissions-on-ubuntu"></a>Ubuntu におけるシリアル ポートのアクセス許可の追加

Windows または Mac OS を使用する場合は、このセクションを省略できます。 Ubuntu の場合は、次の手順に従って、通常の Linux ユーザーに、Arduino ボードの USB ポートを操作するアクセス許可があることを確認する必要があります。

1. 通常のユーザーとしてターミナルで以下を実行します。

   ```bash
   ls -l /dev/ttyUSB*
   # Or
   ls -l /dev/ttyACM*
   ```

   次のような結果が表示されます。

   ```bash
   crw-rw---- 1 root uucp 188, 0 5 apr 23.01 ttyUSB0
   # Or
   crw-rw---- 1 root dialout 188, 0 5 apr 23.01 ttyACM0
   ```

   "0" は別の数字である可能性があります。または、複数のエントリが返される可能性があります。 最初のケースで必要なデータは `uucp` で、2 番目のケースでは `dialout` です。これはファイルのグループ所有者です。

2. ユーザーをグループに追加します。

   ```bash
   sudo usermod -a -G group-name username
   ```

   `group-name` は最初の手順で検出されたデータで、`username` はお使いの Linux ユーザー名です。

3. この変更を反映し、セットアップを完了するには、ログアウトしてからログインし直す必要があります。

## <a name="summary"></a>概要
この記事では、Arduino ボードを構成する方法を学習しました。 次のタスクでは、Arduino ボードでサンプル アプリケーションを実行するための準備として、必要なツールとソフトウェアをインストールします。

![ハードウェアの準備完了][hardware-is-ready]

## <a name="next-steps"></a>次のステップ
[ツールの入手][get-the-tools]
<!-- Images and links -->

[kit]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/kit.png
[top-micro-usb-port]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/top_usbport.jpg
[computer-usb]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/computer_usb.jpg
[hardware-is-ready]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/hardware_ready.jpg
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
