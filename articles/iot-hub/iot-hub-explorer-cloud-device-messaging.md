---
title: "iothub-explorer を使用した Azure IoT Hub クラウド デバイス メッセージングの管理 | Microsoft Docs"
description: "iothub-explorer CLI ツールを使用して、Azure IoT Hub で D2C (デバイスからクラウド) メッセージを監視し、C2D (クラウドからデバイス) メッセージを送信する方法について説明します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iothub-explorer, クラウド デバイス メッセージング, IoT Hub C2D, C2D メッセージング"
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 8c25b8f558aae638a95d6e7186e19e77d02edbb2
ms.lasthandoff: 03/30/2017


---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>iothub-explorer を使用してデバイスと IoT Hub 間でメッセージを送受信する

> [!NOTE]
> このチュートリアルを開始する前に、[ESP8266 と Azure IoT Hub の接続](/iot-hub-arduino-huzzah-esp8266-get-started.md)に関するページで説明する操作を必ず完了してください。 [ESP8266 と Azure IoT Hub の接続](/iot-hub-arduino-huzzah-esp8266-get-started.md)に関するページでは、IoT デバイスと IoT Hub を設定し、デバイスで実行するサンプル アプリケーションをデプロイします。 そのアプリケーションは、収集されたセンサー データを IoT Hub に送信します。

[iothub-explorer](https://github.com/azure/iothub-explorer) には、IoT Hub 管理を容易にするためのコマンドがいくつか用意されています。 このチュートリアルでは、iothub-explorer を使用して、デバイスと IoT Hub 間でメッセージを送受信する方法を中心に説明します。

## <a name="what-you-will-learn"></a>学習内容

iothub-explorer を使用して、D2C メッセージを監視し、C2D メッセージを送信する方法について学習します。 D2C メッセージは、デバイスが収集し、IoT Hub に送信するセンサー データである可能性があります。 C2D メッセージは、IoT Hub がデバイスに送信するコマンドである可能性があります。このコマンドによって、そのデバイスに接続されている LED が点滅します。

## <a name="what-you-will-do"></a>学習内容

- iothub-explorer を使用して、D2C メッセージを監視します。
- iothub-explorer を使用して、C2D メッセージを送信します。

## <a name="what-you-need"></a>必要なもの

- 次の要件が含まれる [ESP8266 を Azure IoT Hub に接続](/iot-hub-arduino-huzzah-esp8266-get-started.md)するためのチュートリアルを完了します。
  - 有効な Azure サブスクリプション
  - サブスクリプションの Azure IoT Hub。
  - Azure IoT Hub にメッセージを送信するクライアント アプリケーション。
- iothub-explorer  ([iothub-explorer のインストール](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>D2C メッセージの監視

デバイスから IoT Hub に送信されたメッセージを監視するには、次の手順に従います。

1. コンソール ウィンドウを開きます。
1. 次のコマンドを実行します。

   ```bash
   iothub-explorer monitor-events <device-id> --login <IoTHubConnectionString>
   ```

   > [!Note]
   > IoT Hub から `<device-id>` と `<IoTHubConnectionString>` を取得します。 前のチュートリアルが完了していることを確認してください。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

IoT Hub からデバイスにメッセージを送信するには、次の手順に従います。

1. コンソール ウィンドウを開きます。
1. 次のコマンドを実行して、IoT Hub でセッションを開始します。

   ```bash
   iothub-explorer login <IoTHubConnectionString>
   ```

1. 次のコマンドを実行して、メッセージをデバイスに送信します。

   ```bash
   iothub-explorer send <device-id> <message>
   ```

コマンドによって、デバイスに接続されている LED が点滅し、メッセージがデバイスに送信されます。

> [!Note]
> デバイスは、メッセージの受信時に、IoT Hub に対して個別に確認コマンドを送信する必要はありません。

## <a name="next-steps"></a>次のステップ

使用している IoT デバイスと Azure IoT Hub の間で D2C メッセージを監視し、C2D メッセージを送信する方法については学習しました。

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

- [Azure データ ストレージへの IoT Hub メッセージの保存](iot-hub-store-data-in-azure-table-storage.md)
