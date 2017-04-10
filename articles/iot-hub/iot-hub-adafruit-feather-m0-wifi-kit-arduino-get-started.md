---
title: "Azure IoT への Arduino (C) の接続 - 作業開始 | Microsoft Docs"
description: "Adafruit Feather M0 WiFi の概要、Azure IoT Hub の作成、Adafruit Feather M0 WiFi の IoT Hub への接続"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot hub, IoT の概要, IoT チュートリアル, adafruit IoT, arduino の概要"
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 6789e6f1c094f8809163e29349c8ea54e1e97683
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-your-arduino-board-adafruit-feather-m0-wifi"></a>Arduino board: Adafruit Feather M0 WiFi の概要

このチュートリアルでは、Arduino ボードの操作の基本を学習することから始めます。 次に、[Azure IoT Hub](iot-hub-what-is-iot-hub.md) を使って、デバイスをクラウドにシームレスに接続する方法について説明します。

## <a name="lesson-1-configure-your-device"></a>レッスン 1: デバイスの構成
![レッスン 1 のエンド ツー エンドのダイアグラム][Lesson-1-end-to-end-diagram]

このレッスンでは、Arduino ボードにオペレーティング システムをインストールして構成し、開発環境をセットアップして、Arduino ボードにアプリケーションをデプロイします。

### <a name="configure-your-device"></a>デバイスを構成する
Arduino ボードを取り付け、電源をオンにして、ボードの初回使用時の構成を行ないます。

*推定所要時間: 5 分*

「[デバイスを構成する][configure-your-device]」に移動してください。

### <a name="get-the-tools"></a>ツールを入手する
Arduino ボードの最初のアプリケーションをビルドしてデプロイするためのツールとソフトウェアをダウンロードします。

*推定所要時間: 20 分*

「[ツールの入手][get-the-tools]」に移動してください

### <a name="create-and-deploy-the-blink-application"></a>点滅アプリケーションを作成してデプロイする
GitHub のサンプル Arduino 点滅アプリケーションを複製し、gulp を使ってこのアプリケーションを Arduino ボードにデプロイします。 このサンプル アプリケーションでは、GPIO #13 ボード搭載 LED を 2 秒間隔で点滅させます。

*推定所要時間: 5 分*

「[点滅アプリケーションを作成してデプロイする][create-and-deploy-the-blink-application]」に移動してください。

## <a name="lesson-2-create-your-iot-hub"></a>レッスン 2: IoT ハブの作成
![レッスン 2 のエンド ツー エンドのダイアグラム][lesson-2-end-to-end-diagram]

このレッスンでは、無料の Azure アカウントを作成し、Azure IoT ハブをプロビジョニングして、その IoT ハブに最初のデバイスを作成します。

このレッスンを開始する前に、レッスン 1 を完了してください。

### <a name="get-the-azure-tools"></a>Azure ツールの入手
Azure コマンド ライン インターフェイス (Azure CLI) をインストールします。

*推定所要時間: 10 分*

「[Azure ツールの入手][get-azure-tools]」に移動してください。

### <a name="create-your-iot-hub-and-register-your-arduino-board"></a>IoT Hub を作成して Arduino ボードを登録する
リソース グループを作成し、最初の Azure IoT Hub をプロビジョニングして、Azure CLI を使って IoT Hub に最初のデバイスを追加します。

*推定所要時間: 10 分*

「[IoT Hub を作成して Arduino ボードを登録する][create-your-iot-hub-and-register-your-arduino-board]」に移動してください。

## <a name="lesson-3-send-device-to-cloud-messages"></a>レッスン 3: デバイスからクラウドへのメッセージの送信
![レッスン 3 のエンド ツー エンドのダイアグラム][lesson-3-end-to-end-diagram]

このレッスンでは、Arduino ボードから IoT Hub にメッセージを送信します。 また、IoT ハブから受信メッセージを取得して、Azure Table Storage に書き込む Azure Function App を作成します。

このレッスンを開始する前に、レッスン 1 と 2 を完了してください。

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Azure Function App と Azure ストレージ アカウントの作成
Azure Resource Manager テンプレートを使用して、Azure Function App と Azure ストレージ アカウントを作成します。

*推定所要時間: 10 分*

「[Azure Function App と Azure ストレージ アカウントの作成][create-an-azure-function-app-and-azure-storage-account]」に移動してください。

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージを送信するサンプル アプリケーションの実行
IoT Hub にメッセージを送信するサンプル アプリケーションを Arduino ボードにデプロイして実行します。

*推定所要時間: 10 分*

「[デバイスからクラウドへのメッセージを送信するサンプル アプリケーションの実行][send-device-to-cloud-messages]」に移動してください。

### <a name="read-messages-persisted-in-azure-storage"></a>Azure Storage に保持されたメッセージの読み取り
Azure Storage に書き込まれた、デバイスからクラウドへのメッセージを監視します。

*推定所要時間: 5 分*

「[Azure Storage に保持されたメッセージの読み取り][read-messages-persisted-in-azure-storage]」に移動してください。

## <a name="lesson-4-send-cloud-to-device-messages"></a>レッスン 4: クラウドからデバイスへのメッセージの送信
![レッスン 4 のエンド ツー エンドのダイアグラム][lesson-4-end-to-end-diagram]

このレッスンでは、Azure IoT Hub から Arduino ボードにメッセージを送信する方法を紹介します。 メッセージにより、GPIO #13 ボード搭載 LED のオンとオフの動作を制御します。 サンプル アプリケーションは、このタスクを実行するための準備が整っています。

このレッスンを開始する前に、レッスン 1、2、3 を完了してください。

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>クラウドからデバイスへのメッセージを受信するサンプル アプリケーションの実行
レッスン 4 のサンプル アプリケーションは Arduino ボード上で動作し、IoT Hub からの受信メッセージを監視します。 新しい gulp タスクを使って IoT Hub から Arduino ボードにメッセージを送信して、LED を点滅させます。

*推定所要時間: 10 分*

「[クラウドからデバイスへのメッセージを受信するサンプル アプリケーションの実行][receive-cloud-to-device-messages]」に移動してください。

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>省略可能なセクション: LED のオンとオフの動作の変更
LED のオンとオフの動作を変更するメッセージをカスタマイズします。

*推定所要時間: 10 分*

省略可能なセクション「[LED のオンとオフの動作の変更][change-the-on-and-off-behavior-of-the-led]」に移動してください。

## <a name="troubleshooting"></a>トラブルシューティング
レッスン中に問題が発生した場合は、「[トラブルシューティング][troubleshooting]」で解決策を探してください。

<!-- Images and links -->

[Lesson-1-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson1.png
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[lesson-2-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson2.png
[get-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[lesson-3-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson3.png
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
[lesson-4-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson4.png
[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
