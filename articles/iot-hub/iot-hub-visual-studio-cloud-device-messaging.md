---
title: VS Cloud Explorer を使用して Azure IoT Hub デバイス メッセージングを管理する
description: Cloud Explorer for Visual Studio を使用して、Azure IoT Hub でデバイスからクラウドへのメッセージを監視し、クラウドからデバイスへのメッセージを送信する方法について説明します。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74079492"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Cloud Explorer for Visual Studio を使用してデバイスと IoT Hub 間でメッセージを送受信する

![エンド ツー エンド ダイアグラム](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) は、Azure リソースを表示し、そのプロパティを調べ、Visual Studio 内から開発者が重要な操作を実行できる、Visual Studio の便利な拡張機能です。 この記事では、Cloud Explorer を使用して、デバイスとハブ間でメッセージを送受信する方法を中心に説明します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>学習内容

この記事では、Cloud Explorer for Visual Studio を使用して、device-to-cloud メッセージの監視と cloud-to-device メッセージの送信を行う方法について説明します。 D2C メッセージは、デバイスが収集し、IoT Hub に送信するセンサー データである可能性があります。 C2D メッセージは、IoT Hub がデバイスに送信するコマンドである可能性があります。 たとえば、デバイスに接続されている LED を点滅させます。

## <a name="what-you-do"></a>作業内容

この記事では、次のタスクを実行します。

- Cloud Explorer for Visual Studio を使用して、デバイスからクラウドへのメッセージを監視します。

- Cloud Explorer for Visual Studio を使用して、クラウドからデバイスへのメッセージを送信します。

## <a name="what-you-need"></a>必要なもの

次の前提条件を満たす必要があります。

- 有効な Azure サブスクリプション

- サブスクリプションの Azure IoT Hub。

- Microsoft Visual Studio 2017 Update 9 以降。 この記事では、[Visual Studio 2019](https://www.visualstudio.com/vs/) を使用します。

- Visual Studio インストーラーの Cloud Explorer コンポーネント (Azure ワークロードによって既定で選択されます)。

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer を最新バージョンに更新する

Visual Studio 2017 用の Visual Studio インストーラーの Cloud Explorer コンポーネントでは、device-to-cloud メッセージと cloud-to-device メッセージの監視のみがサポートされます。 Visual Studio 2017 を使用するには、最新の [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) をダウンロードしてインストールします。

## <a name="sign-in-to-access-your-hub"></a>サインインしてハブにアクセスする

ハブにアクセスするには、次の手順に従います。

1. Visual Studio で、 **[表示]**  >  **[Cloud Explorer]** の順に選択して Cloud Explorer を開きます。

1. [アカウント管理] アイコンを選択して、サブスクリプションを表示します。

    ![[アカウント管理] アイコン](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Azure にサインインしている場合は、お使いのアカウントが表示されます。 初めて Azure にサインインするには、**[アカウントの追加]** を選択します。

1. 使用する Azure サブスクリプションを選択し、**[適用]** を選択します。

1. サブスクリプションを展開した後、**[IoT ハブ]** を展開します。  各ハブで、そのハブのデバイスを確認できます。

    ![デバイス一覧](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>D2C メッセージの監視

デバイスから IoT Hub に送信されたメッセージを監視するには、次の手順に従います。

1. IoT Hub またはデバイスを右クリックして、**[Start Monitoring D2C Message]\(D2C メッセージの監視を開始する\)** を選択します。

    ![D2C メッセージの監視を開始する](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. **[出力]** の下に、監視されたメッセージが表示されます。

    ![D2C メッセージ結果の監視](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. 監視を停止するには、いずれかの IoT Hub またはデバイスを右クリックして、**[Stop Monitoring D2C Message]\(D2C メッセージの監視を停止する\)** を選択します。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

IoT Hub からデバイスにメッセージを送信するには、次の手順に従います。

1. デバイスを右クリックして、**[Send C2D Message]\(C2D メッセージを送信する\)** を選択します。

1. 入力ボックスにメッセージを入力します。

    ![C2D メッセージを送信する](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    **[出力]** の下に結果が表示されます。

    ![C2D メッセージ結果を送信する](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>次のステップ

使用している IoT デバイスと Azure IoT Hub の間で D2C メッセージを監視し、C2D メッセージを送信する方法については学習しました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]