---
title: Cloud Explorer for Visual Studio を使用した Azure IoT デバイス管理 | Microsoft Docs
description: Azure IoT Hub デバイス管理用の Cloud Explorer for Visual Studio を使用します。このツールでは、ダイレクト メソッドとデバイス ツインの必要なプロパティを管理するためのオプションを使用できます。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: 87a0847f5d42e014f3b2691c96446892176b481b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791931"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Azure IoT Hub デバイス管理に Cloud Explorer for Visual Studio を使用する

![エンド ツー エンド ダイアグラム](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) は、Azure リソースを表示し、そのプロパティを調べ、Visual Studio 内から開発者が重要な操作を実行できる、Visual Studio の便利な拡張機能です。 さまざまなタスクを実行するために使用できる管理オプションが付属しています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 管理オプション          | タスク                    |
|----------------------------|--------------------------------|
| ダイレクト メソッド             | メッセージの送信開始や停止、デバイスの再起動などの機能をデバイスに実行させます。                                        |
| デバイス ツインの読み取り           | デバイスの報告される状態を取得します。 たとえば、デバイスは、現在 LED が点滅していることを報告します。                                    |
| デバイス ツインの更新         | デバイスを特定の状態にします (LED を緑に設定したり、テレメトリの送信間隔を 30 分に設定したりします)。         |
| クラウドからデバイスへのメッセージ   | デバイスに通知を送信します。 例: "今日はほぼ雨が降ります。 必ず傘を持っていってください。"              |

これらのオプションの相違点の詳細な説明と使用するためのガイダンスについては、「[device-to-cloud 通信に関するガイダンス](iot-hub-devguide-d2c-guidance.md)」と「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」を参照してください。

デバイス ツインは、デバイスの状態に関する情報 (メタデータ、構成、状態) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 デバイス ツインの詳細については、「[デバイス ツインの使用](iot-hub-node-node-twin-getstarted.md)」を参照してください。

## <a name="what-you-learn"></a>学習内容

開発マシン上で Cloud Explorer for Visual Studio をさまざまな管理オプションで使用する方法を説明します。

## <a name="what-you-do"></a>作業内容

Cloud Explorer for Visual Studio をさまざまな管理オプションで実行します。

## <a name="what-you-need"></a>必要なもの

- 有効な Azure サブスクリプション
- サブスクリプションの Azure IoT Hub。
- Microsoft Visual Studio 2017 Update 8 以降
- Visual Studio インストーラーの Cloud Explorer コンポーネント (Azure ワークロードで既定で選択済み)

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer を最新バージョンに更新する

Visual Studio インストーラーの Cloud Explorer コンポーネントは、デバイスからクラウドへのメッセージおよびクラウドからデバイスへのメッセージの監視のみサポートします。 管理オプションにアクセスするには、最新バージョンの [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) をダウンロードしてインストールする必要があります。

## <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT Hub にアクセスする

1. Visual Studio の **Cloud Explorer** ウィンドウで、アカウント管理アイコンをクリックします。 Cloud Explorer ウィンドウは、**ビュー** > **[Cloud Explorer]** メニューから開くことができます。

    ![アカウント管理をクリックする](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Cloud Explorer の **アカウントの管理** をクリックします。
1. 新しいウィンドウで **アカウントの追加...** をクリックして Azure に初めてサインインします。
1. サインインした後、Azure サブスクリプションの一覧が表示されます。 表示する Azure サブスクリプションを選択し、**適用** をクリックします。
1. **自分のサブスクリプション** > **[IoT Hubs]** > **自分の IoT Hub** の順に展開すると、IoT Hub ノードの下にデバイスの一覧が表示されます。 管理オプションにアクセスする 1 つのデバイスを右クリックします。

    ![管理オプション](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>ダイレクト メソッド

1. デバイスを右クリックし、**[Invoke Device Direct Method]\(デバイス ダイレクト メソッドの呼び出し)** を選択します。
1. 入力ボックスにメソッド名とペイロードを入力します。
1. 結果が **[IoT Hub]** 出力ペインに表示されます。

## <a name="read-device-twin"></a>デバイス ツインの読み取り

1. デバイスを右クリックし、**[デバイス ツインの編集]** を選択します。
1. **azure-iot-device-twin.json** ファイルが開き、デバイス ツインの内容が表示されます。

## <a name="update-device-twin"></a>デバイス ツインの更新

1. **azure-iot-device-twin.json** ファイルの **tags** または **properties.desired** フィールドを一部編集します。
1. **Ctrl+S** キーを押してデバイス ツインを更新します。
1. 結果が **[IoT Hub]** 出力ペインに表示されます。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

IoT Hub からデバイスにメッセージを送信するには、次の手順に従います。

1. デバイスを右クリックして、**[Send C2D Message]\(C2D メッセージを送信する\)** を選択します。
1. 入力ボックスにメッセージを入力します。
1. 結果が **[IoT Hub]** 出力ペインに表示されます。

## <a name="next-steps"></a>次の手順

Cloud Explorer for Visual Studio をさまざまな管理オプションで使用する方法を学習しました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]