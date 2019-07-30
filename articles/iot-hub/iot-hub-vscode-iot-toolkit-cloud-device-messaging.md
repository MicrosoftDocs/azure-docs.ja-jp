---
title: Visual Studio Code 用 Azure IoT Tools を使用して Azure IoT Hub クラウド デバイス メッセージングを管理する | Microsoft Docs
description: Visual Studio Code 用 Azure IoT Tools を使用して、Azure IoT Hub でデバイスからクラウドへのメッセージを監視し、クラウドからデバイスへのメッセージを送信する方法について説明します。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: f8799fcb06235474bb33cdaf0406300065208024
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543815"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Visual Studio Code 用 Azure IoT Tools を使用してデバイスと IoT Hub の間のメッセージを送受信する

![エンド ツー エンド ダイアグラム](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) は、IoT Hub の管理と IoT アプリケーションの開発を容易にする便利な Visual Studio Code 拡張機能です。 この記事では、Visual Studio Code 用 Azure IoT Tools を使用してデバイスと IoT Hub の間のメッセージを送受信する方法を説明します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>学習内容

Visual Studio Code 用 Azure IoT Tools を使用して、デバイスからクラウドへのメッセージを監視し、クラウドからデバイスへのメッセージを送信する方法について説明します。 D2C メッセージは、デバイスが収集し、IoT Hub に送信するセンサー データである可能性があります。 C2D メッセージは、IoT Hub がデバイスに送信するコマンドである可能性があります。このコマンドによって、そのデバイスに接続されている LED が点滅します。

## <a name="what-you-will-do"></a>学習内容

* Visual Studio Code 用 Azure IoT Tools を使用して、デバイスからクラウドへのメッセージを監視します。

* Visual Studio Code 用 Azure IoT Tools を使用して、クラウドからデバイスへのメッセージを送信します。

## <a name="what-you-need"></a>必要なもの

* 有効な Azure サブスクリプション

* サブスクリプションの Azure IoT Hub。

* [Visual Studio Code](https://code.visualstudio.com/)

* [VS Code 用の Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) または [Visual Studio Code でこのリンクを開きます](vscode:extension/vsciot-vscode.azure-iot-tools)。

## <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

1. VS Code の**エクスプローラー** ビューで、左下隅の **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。

2. コンテキスト メニューで **[Select IoT Hub]\(IoT Hub の選択\)** をクリックします。

3. 右下隅にポップアップが表示され、Azure への初めてのサインインを行うことができます。

4. サインインすると、Azure サブスクリプションの一覧が表示されるので、Azure サブスクリプションと IoT Hub を選択します。

5. 数秒で **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** タブにデバイスの一覧が表示されます。

   > [!Note]
   > **[Set IoT Hub Connection String]\(IoT Hub 接続文字列の設定\)** を選択して設定することもできます。 ポップアップ ウィンドウに、IoT デバイスの接続先 IoT ハブの接続文字列を入力します。

## <a name="monitor-device-to-cloud-messages"></a>D2C メッセージの監視

デバイスから IoT Hub に送信されたメッセージを監視するには、次の手順に従います。

1. デバイスを右クリックして、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** を選択します。

2. 監視対象のメッセージが、 **[OUTPUT]\(出力\)**  >  **[Azure IoT Hub Toolkit]** ビューに表示されます。

3. 監視を停止するには、 **[OUTPUT]\(出力\)** ビューを右クリックして、 **[Stop Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を停止する\)** を選択します。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

IoT Hub からデバイスにメッセージを送信するには、次の手順に従います。

1. デバイスを右クリックして、 **[Send C2D Message to Device]\(C2D メッセージをデバイスに送信する\)** を選択します。

2. 入力ボックスにメッセージを入力します。

3. 結果が、 **[OUTPUT]\(出力\)**  >  **[Azure IoT Hub Toolkit]** ビューに表示されます。

## <a name="next-steps"></a>次の手順

使用している IoT デバイスと Azure IoT Hub の間で D2C メッセージを監視し、C2D メッセージを送信する方法については学習しました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]