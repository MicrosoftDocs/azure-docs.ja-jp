---
title: "Azure IoT への Intel Edison (C) の接続 - 作業開始 | Microsoft Docs"
description: "Intel Edison を使用し、Azure IoT ハブを作成して、その IoT ハブに Edison を接続します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Intel Edison 開発, Azure IoT Hub, モノのインターネットの概要, モノのインターネットのチュートリアル, Adafruit モノのインターネット, Intel Edison Arduino, Arduino の概要"
ms.assetid: 4885fa2c-c2ee-4253-b37f-ccd55f92b006
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: 248bc4c309ed61ae00ef144ebdb4c820f285783d
ms.lasthandoff: 03/02/2017


---
# <a name="connect-your-intel-edison-device-to-your-iot-hub-using-c"></a>C を使用した Intel Edison デバイスの IoT Hub への接続
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-intel-edison-kit-node-get-started.md)
> * [C](iot-hub-intel-edison-kit-c-get-started.md)

このチュートリアルでは、Intel Edison の操作の基本を学習することから始めます。 次に、[Azure IoT Hub](iot-hub-what-is-iot-hub.md) を使って、デバイスをクラウドにシームレスに接続する方法について説明します。

キットをお持ちでない場合は、 [ここ](https://azure.microsoft.com/develop/iot/starter-kits)から開始

## <a name="lesson-1-configure-your-device"></a>レッスン 1: デバイスの構成
![レッスン 1 のエンド ツー エンドのダイアグラム](media/iot-hub-intel-edison-lessons/e2e-lesson1.png)

このレッスンでは、Intel Edison デバイスにオペレーティング システムをインストールして構成し、開発環境をセットアップして、Edison にアプリケーションをデプロイします。

### <a name="configure-your-device"></a>デバイスを構成する
ボードを組み立て、電源をオンにして、デスクトップ OS に構成ツールをインストールすることで Intel Edison の初回使用時の構成を行い、Edison のファームウェアをフラッシュし、パスワードを設定して Wi-Fi に接続します。  

*推定所要時間: 30 分*

[デバイスの構成][configure-your-device]に関するページに移動してください。

### <a name="get-the-tools"></a>ツールを入手する
Intel Edison の最初のアプリケーションをビルドしてデプロイするためのツールとソフトウェアをダウンロードします。

*推定所要時間: 20 分*

「[ツールの入手][get-the-tools]」に移動してください。

### <a name="create-and-deploy-the-blink-application"></a>点滅アプリケーションを作成してデプロイする
GitHub のサンプル点滅アプリケーションを複製し、gulp を使ってこのアプリケーションを Intel Edison ボードにデプロイします。 このサンプル アプリケーションでは、ボードに接続された LED を&2; 秒間隔で点滅させます。

*推定所要時間: 5 分*

「[点滅アプリケーションを作成してデプロイする][create-and-deploy-the-blink-application]」に移動してください。

## <a name="lesson-2-create-your-iot-hub"></a>レッスン 2: IoT ハブの作成
![レッスン 2 のエンド ツー エンドのダイアグラム](media/iot-hub-intel-edison-lessons/e2e-lesson2.png)

このレッスンでは、無料の Azure アカウントを作成し、Azure IoT ハブをプロビジョニングして、その IoT ハブに最初のデバイスを作成します。

このレッスンを開始する前に、レッスン 1 を完了してください。

### <a name="get-the-azure-tools"></a>Azure ツールの入手
Azure コマンド ライン インターフェイス (Azure CLI) をインストールします。

*推定所要時間: 10 分*

「[Azure ツールの入手][get-azure-tools]」に移動してください。

### <a name="create-your-iot-hub-and-register-intel-edison"></a>IoT ハブの作成と Intel Edison の登録
リソース グループを作成し、最初の Azure IoT ハブをプロビジョニングして、Azure CLI を使って IoT ハブに最初のデバイスを追加します。

*推定所要時間: 10 分*

「[IoT ハブの作成と Intel Edison の登録](iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md)」に移動してください。

## <a name="lesson-3-send-device-to-cloud-messages"></a>レッスン 3: デバイスからクラウドへのメッセージの送信
![レッスン 3 のエンド ツー エンドのダイアグラム](media/iot-hub-intel-edison-lessons/e2e-lesson3.png)

このレッスンでは、Edison から IoT ハブにメッセージを送信します。 また、IoT ハブから受信メッセージを取得して、Azure Table Storage に書き込む Azure Function App を作成します。

このレッスンを開始する前に、レッスン 1 と 2 を完了してください。

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Azure Function App と Azure ストレージ アカウントの作成
Azure Resource Manager テンプレートを使用して、Azure Function App と Azure ストレージ アカウントを作成します。

*推定所要時間: 10 分*

「[Azure Function App と Azure ストレージ アカウントの作成][create-an-azure-function-app-and-azure-storage-account]」に移動してください。

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージを送信するサンプル アプリケーションの実行
IoT ハブにメッセージを送信するサンプル アプリケーションを Intel Edison デバイスにデプロイして実行します。

*推定所要時間: 10 分*

「[デバイスからクラウドへのメッセージを送信するサンプル アプリケーションの実行][send-device-to-cloud-messages]」に移動してください。

### <a name="read-messages-persisted-in-azure-storage"></a>Azure Storage に保持されたメッセージの読み取り
Azure Storage に書き込まれた、デバイスからクラウドへのメッセージを監視します。

*推定所要時間: 5 分*

「[Azure Storage に保持されたメッセージの読み取り][read-messages-persisted-in-azure-storage]」に移動してください。

## <a name="lesson-4-send-cloud-to-device-messages"></a>レッスン 4: クラウドからデバイスへのメッセージの送信
![レッスン 4 のエンド ツー エンドのダイアグラム](media/iot-hub-intel-edison-lessons/e2e-lesson4.png)

このレッスンでは、Azure IoT ハブから Intel Edison にメッセージを送信する方法を紹介します。 メッセージにより、Edison に接続されている LED のオンとオフの動作を制御します。 サンプル アプリケーションは、このタスクを実行するための準備が整っています。

このレッスンを開始する前に、レッスン 1、2、3 を完了してください。

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>クラウドからデバイスへのメッセージを受信するサンプル アプリケーションの実行
レッスン 4 のサンプル アプリケーションは Edison 上で動作し、IoT ハブからの受信メッセージを監視します。 新しい gulp タスクを使って IoT ハブから Edison にメッセージを送信して、LED を点滅させます。

*推定所要時間: 10 分*

「[クラウドからデバイスへのメッセージを受信するサンプル アプリケーションの実行][receive-cloud-to-device-messages]」に移動してください。

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>省略可能なセクション: LED のオンとオフの動作の変更
LED のオンとオフの動作を変更するメッセージをカスタマイズします。

*推定所要時間: 10 分*

省略可能なセクション「[LED のオンとオフの動作の変更][change-the-on-and-off-behavior-of-the-led]」に移動してください。

## <a name="troubleshooting"></a>トラブルシューティング
レッスン中に問題が発生した場合は、「[トラブルシューティング][troubleshooting]」で解決策を探してください。
<!-- Images and links -->

[configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-c-lesson1-deploy-blink-app.md
[get-azure-tools]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-c-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-c-lesson3-read-table-storage.md
[receive-cloud-to-device-messages]:iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-c-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
