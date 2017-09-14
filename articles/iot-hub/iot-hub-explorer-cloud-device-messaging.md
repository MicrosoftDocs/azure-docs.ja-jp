---
title: "iothub-explorer を使用した Azure IoT Hub クラウド デバイス メッセージングの管理 | Microsoft Docs"
description: "iothub-explorer CLI ツールを使用して、Azure IoT Hub で D2C (デバイスからクラウド) メッセージを監視し、C2D (クラウドからデバイス) メッセージを送信する方法について説明します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iothub-explorer, クラウド デバイス メッセージング, IoT Hub C2D, C2D メッセージング"
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: a7abb8fb279e134b7f23df779f1c4548a9feb82d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/08/2017

---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>iothub-explorer を使用してデバイスと IoT Hub 間でメッセージを送受信する

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) には、IoT Hub 管理を容易にするためのコマンドがいくつか用意されています。 このチュートリアルでは、iothub-explorer を使用して、デバイスと IoT Hub 間でメッセージを送受信する方法を中心に説明します。

## <a name="what-you-will-learn"></a>学習内容

iothub-explorer を使用して、D2C メッセージを監視し、C2D メッセージを送信する方法について学習します。 D2C メッセージは、デバイスが収集し、IoT Hub に送信するセンサー データである可能性があります。 C2D メッセージは、IoT Hub がデバイスに送信するコマンドである可能性があります。このコマンドによって、そのデバイスに接続されている LED が点滅します。

## <a name="what-you-will-do"></a>学習内容

- iothub-explorer を使用して、D2C メッセージを監視します。
- iothub-explorer を使用して、C2D メッセージを送信します。

## <a name="what-you-need"></a>必要なもの

- 次の要件に対応するために、[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md)に関するチュートリアルを完了しておきます。
  - 有効な Azure サブスクリプション
  - サブスクリプションの Azure IoT Hub。
  - Azure IoT Hub にメッセージを送信するクライアント アプリケーション。
- iothub-explorer  ([iothub-explorer のインストール](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>D2C メッセージの監視

デバイスから IoT Hub に送信されたメッセージを監視するには、次の手順に従います。

1. コンソール ウィンドウを開きます。
1. 次のコマンドを実行します。

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > IoT Hub から `<device-id>` と `<IoTHubConnectionString>` を取得します。 前のチュートリアルが完了していることを確認してください。 または `HostName`、`SharedAccessKeyName`、および `SharedAccessKey` がある場合は、`iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` を使用してみることができます。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

IoT Hub からデバイスにメッセージを送信するには、次の手順に従います。

1. コンソール ウィンドウを開きます。
1. 次のコマンドを実行して、IoT Hub でセッションを開始します。

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
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

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

