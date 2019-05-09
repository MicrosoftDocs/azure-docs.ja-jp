---
title: Cloud Explorer for Visual Studio を使用した Azure IoT Hub クラウド デバイス メッセージングの管理 | Microsoft Docs
description: Cloud Explorer for Visual Studio を使用して、Azure IoT Hub でデバイスからクラウドへのメッセージを監視し、クラウドからデバイスへのメッセージを送信する方法について説明します。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571313"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Cloud Explorer for Visual Studio を使用してデバイスと IoT Hub 間でメッセージを送受信する

![エンド ツー エンド ダイアグラム](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) は、Azure リソースを表示し、そのプロパティを調べ、Visual Studio 内から開発者が重要な操作を実行できる、Visual Studio の便利な拡張機能です。 この記事では、Cloud Explorer を使用して、デバイスと IoT Hub 間でメッセージを送受信する方法を中心に説明します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>学習内容

Cloud Explorer for Visual Studio を使用して、デバイスからクラウドへのメッセージを監視し、クラウドからデバイスへのメッセージを送信する方法について説明します。 D2C メッセージは、デバイスが収集し、IoT Hub に送信するセンサー データである可能性があります。 C2D メッセージは、IoT Hub がデバイスに送信するコマンドである可能性があります。 たとえば、デバイスに接続されている LED を点滅させます。

## <a name="what-you-will-do"></a>学習内容

- Cloud Explorer for Visual Studio を使用して、デバイスからクラウドへのメッセージを監視します。
- Cloud Explorer for Visual Studio を使用して、クラウドからデバイスへのメッセージを送信します。

## <a name="what-you-need"></a>必要なもの

- 有効な Azure サブスクリプション
- サブスクリプションの Azure IoT Hub。
- Microsoft Visual Studio 2017 Update 8 以降
- Visual Studio インストーラーの Cloud Explorer コンポーネント (Azure ワークロードで既定で選択済み)

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer を最新バージョンに更新する

Visual Studio インストーラーの Cloud Explorer コンポーネントは、デバイスからクラウドへのメッセージおよびクラウドからデバイスへのメッセージの監視のみサポートします。 デバイスまたはクラウドにメッセージを送信するには、最新バージョンの [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) をダウンロードしてインストールします。

## <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT Hub にアクセスする

1. Visual Studio の **Cloud Explorer** ウィンドウで、アカウント管理アイコンをクリックします。 Cloud Explorer ウィンドウは、**ビュー** > **[Cloud Explorer]** メニューから開くことができます。

    ![アカウント管理をクリックする](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)


2. Cloud Explorer の **アカウントの管理** をクリックします。

3. 新しいウィンドウで **アカウントの追加...** をクリックして Azure に初めてサインインします。

4. サインインした後、Azure サブスクリプションの一覧が表示されます。 表示する Azure サブスクリプションを選択し、**適用** をクリックします。

5. **自分のサブスクリプション** > **[IoT Hubs]** > **自分の IoT Hub** の順に展開すると、IoT Hub ノードの下にデバイスの一覧が表示されます。

    ![デバイス一覧](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>D2C メッセージの監視

デバイスから IoT Hub に送信されたメッセージを監視するには、次の手順に従います。

1. IoT Hub またはデバイスを右クリックして、**[Start Monitoring D2C Message]\(D2C メッセージの監視を開始する\)** を選択します。

    ![D2C メッセージの監視を開始する](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

2. 監視対象のメッセージが、**[IoT Hub]** 出力ペインに表示されます。

    ![D2C メッセージ結果の監視](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

3. 監視を停止するには、いずれかの IoT Hub またはデバイスを右クリックして、**[Stop Monitoring D2C Message]\(D2C メッセージの監視を停止する\)** を選択します。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

IoT Hub からデバイスにメッセージを送信するには、次の手順に従います。

1. デバイスを右クリックして、**[Send C2D Message]\(C2D メッセージを送信する\)** を選択します。

    ![C2D メッセージを送信する](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

2. 入力ボックスにメッセージを入力します。

3. 結果が **[IoT Hub]** 出力ペインに表示されます。

    ![C2D メッセージ結果を送信する](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>次の手順

使用している IoT デバイスと Azure IoT Hub の間で D2C メッセージを監視し、C2D メッセージを送信する方法については学習しました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]