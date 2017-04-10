---
title: "Raspberry Pi 3 の使用 | Microsoft Docs"
description: "Raspberry Pi 3 の使用、Azure IoT ハブの作成、IoT ハブへの Pi の接続。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Azure IoT Hub, モノのインターネットの概要, IoT ツールキット"
experiment_id: xshi-happypathemu-20161202
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1a67616d42d233866d5d3eb86aafb1df911d5cdf
ms.lasthandoff: 12/08/2016


---
# <a name="get-started-with-raspberry-pi-3-nodejs"></a>Raspberry Pi 3 の使用 (Node.JS)
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-raspberry-pi-kit-node-get-started.md)
> * [C](iot-hub-raspberry-pi-kit-c-get-started.md)

このチュートリアルでは、まず Raspbian を実行する Raspberry Pi 3 の操作の基礎について説明します。 次に、[Azure IoT Hub](iot-hub-what-is-iot-hub.md) を使って、デバイスをクラウドにシームレスに接続する方法について説明します。 Windows 10 IoT Core サンプルについては、[Windows デベロッパー センター](http://www.windowsondevices.com/)を参照してください。

キットをお持ちでない場合は、 [Raspberry Pi 3 エミュレーター](https://blogs.msdn.microsoft.com/iliast/2016/11/10/how-to-emulate-raspberry-pi/)を試してください。 または、[こちら](https://azure.microsoft.com/develop/iot/starter-kits)で新しいキットを購入してください。

## <a name="lesson-1-configure-your-device"></a>レッスン 1: デバイスの構成
![レッスン 1 のエンド ツー エンドのダイアグラム](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

このレッスンでは、Raspberry Pi 3 デバイスにオペレーティング システムをインストールして構成し、開発環境をセットアップして、Pi にアプリケーションをデプロイします。

### <a name="configure-your-device"></a>デバイスを構成する
Raspberry Pi 3 の初回使用時の構成を行い、Raspbian をインストールします。 Raspbian は、Raspberry Pi ハードウェア用に最適化された無料のオペレーティング システムです。

*推定所要時間: 30 分*

「[デバイスを構成する](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)」に移動してください。

### <a name="get-the-tools"></a>ツールを入手する
Raspberry Pi 3 の最初のアプリケーションをビルドしてデプロイするためのツールとソフトウェアをダウンロードします。

*推定所要時間: 20 分*

「[ツールの入手](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)」に移動してください。

### <a name="create-and-deploy-the-blink-application"></a>点滅アプリケーションを作成してデプロイする
GitHub のサンプル Node.js 点滅アプリケーションを複製し、gulp を使ってこのアプリケーションを Raspberry Pi 3 ボードにデプロイします。 このサンプル アプリケーションでは、ボードに接続された LED を 2 秒間隔で点滅させます。

*推定所要時間: 5 分*

「[点滅アプリケーションを作成してデプロイする](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)」に移動してください。

## <a name="lesson-2-create-your-iot-hub"></a>レッスン 2: IoT ハブの作成
![レッスン 2 のエンド ツー エンドのダイアグラム](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

このレッスンでは、無料の Azure アカウントを作成し、Azure IoT ハブをプロビジョニングして、IoT ハブに最初のデバイスを作成します。

このレッスンを開始する前に、レッスン 1 を完了してください。

### <a name="get-the-azure-tools"></a>Azure ツールの入手
Azure コマンド ライン インターフェイス (Azure CLI) をインストールします。

*推定所要時間: 10 分*

「[Azure ツールの入手](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)」に移動してください。

### <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>IoT ハブの作成と Raspberry Pi 3 の登録
リソース グループを作成し、最初の Azure IoT ハブをプロビジョニングして、Azure CLI を使って IoT ハブに最初のデバイスを追加します。

*推定所要時間: 10 分*

「[IoT ハブの作成と Raspberry Pi 3 の登録](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)」に移動してください。

## <a name="lesson-3-send-device-to-cloud-messages"></a>レッスン 3: デバイスからクラウドへのメッセージの送信
![レッスン 3 のエンド ツー エンドのダイアグラム](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

このレッスンでは、Pi から IoT ハブにメッセージを送信します。 また、IoT ハブから受信メッセージを取得して、Azure Table Storage に書き込む Azure Function App を作成します。

このレッスンを開始する前に、レッスン 1 と 2 を完了してください。

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Azure Function App と Azure ストレージ アカウントの作成
Azure Resource Manager テンプレートを使用して、Azure Function App と Azure ストレージ アカウントを作成します。

*推定所要時間: 10 分*

「[Azure Function App と Azure Storage アカウントの作成](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)」に移動してください。

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージを送信するサンプル アプリケーションの実行
IoT ハブにメッセージを送信するサンプル アプリケーションを Raspberry Pi 3 デバイスにデプロイして実行します。

*推定所要時間: 10 分*

「[デバイスからクラウドへのメッセージを送信するサンプル アプリケーションの実行](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)」に移動してください。

### <a name="read-messages-persisted-in-azure-storage"></a>Azure Storage に保持されたメッセージの読み取り
Azure Storage に書き込まれた、デバイスからクラウドへのメッセージを監視します。

*推定所要時間: 5 分*

「[Azure Storage に保持されたメッセージの読み取り](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)」に移動してください。

## <a name="lesson-4-send-cloud-to-device-messages"></a>レッスン 4: クラウドからデバイスへのメッセージの送信
![レッスン 4 のエンド ツー エンドのダイアグラム](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

このレッスンでは、Azure IoT ハブから Raspberry Pi 3 にメッセージを送信する方法を紹介します。 メッセージにより、Pi に接続されている LED のオンとオフの動作を制御します。 サンプル アプリケーションは、このタスクを実行するための準備が整っています。

このレッスンを開始する前に、レッスン 1、2、および 3 を完了してください。

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>クラウドからデバイスへのメッセージを受信するサンプル アプリケーションの実行
レッスン 4 のサンプル アプリケーションは Pi 上で動作し、IoT ハブからの受信メッセージを監視します。 新しい gulp タスクを使って IoT ハブから Pi にメッセージを送信して、LED を点滅させます。

*推定所要時間: 10 分*

「[クラウドからデバイスへのメッセージを受信するサンプル アプリケーションの実行](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)」に移動してください。

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>省略可能なセクション: LED のオンとオフの動作の変更
LED のオンとオフの動作を変更するメッセージをカスタマイズします。

*推定所要時間: 10 分*

省略可能なセクション「[LED のオンとオフの動作の変更](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)」に移動してください。

## <a name="troubleshooting"></a>トラブルシューティング
レッスン中に問題が発生した場合は、「[トラブルシューティング](iot-hub-raspberry-pi-kit-node-troubleshooting.md)」で解決策を探してください。


