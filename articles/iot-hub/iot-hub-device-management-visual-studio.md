---
title: Visual Studio Cloud Explorer を使用した Azure IoT デバイス管理
description: Azure IoT Hub デバイス管理用の Cloud Explorer for Visual Studio を使用します。このツールでは、ダイレクト メソッドとデバイス ツインの必要なプロパティを管理するためのオプションを使用できます。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "73953180"
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

デバイス ツインは、デバイスに関する情報 (メタデータ、構成、状態など) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 デバイス ツインの詳細については、「[デバイス ツインの使用](iot-hub-node-node-twin-getstarted.md)」を参照してください。

## <a name="what-you-learn"></a>学習内容

この記事では、開発コンピューター上で Cloud Explorer for Visual Studio をさまざまな管理オプションで使用する方法を説明します。

## <a name="what-you-do"></a>作業内容

この記事では、Cloud Explorer for Visual Studio をさまざまな管理オプションで実行します。

## <a name="what-you-need"></a>必要なもの

次の前提条件を満たす必要があります。

- 有効な Azure サブスクリプション

- サブスクリプションの Azure IoT Hub。

- Microsoft Visual Studio 2017 Update 9 以降。 この記事では、[Visual Studio 2017 または Visual Studio 2019](https://www.visualstudio.com/vs/) を使用します。

- Visual Studio インストーラーの Cloud Explorer コンポーネント (Azure ワークロードで既定で選択済み)。

## <a name="update-cloud-explorer-to-latest-version"></a>Cloud Explorer を最新バージョンに更新する

Visual Studio 2017 用の Visual Studio インストーラーの Cloud Explorer コンポーネントでは、device-to-cloud メッセージと cloud-to-device メッセージの監視のみがサポートされます。 Visual Studio 2017 を使用するには、最新の [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) をダウンロードしてインストールします。

## <a name="sign-in-to-access-your-hub"></a>サインインしてハブにアクセスする

1. Visual Studio で、 **[表示]**  >  **[Cloud Explorer]** の順に選択して Cloud Explorer を開きます。

1. [アカウント管理] アイコンを選択して、サブスクリプションを表示します。

    ![[アカウント管理] アイコン](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Azure にサインインしている場合は、お使いのアカウントが表示されます。 初めて Azure にサインインするには、**[アカウントの追加]** を選択します。

1. 使用する Azure サブスクリプションを選択し、**[適用]** を選択します。

1. サブスクリプションを展開した後、**[IoT ハブ]** を展開します。  各ハブで、そのハブのデバイスを確認できます。 管理オプションにアクセスする 1 つのデバイスを右クリックします。

    ![管理オプション](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>ダイレクト メソッド

ダイレクト メソッドを使用するには、次の手順を実行します。

1. デバイスを右クリックし、**[Invoke Device Direct Method]\(デバイス ダイレクト メソッドの呼び出し)** を選択します。

1. **[Invoke Direct Method]\(ダイレクト メソッドの呼び出し\)** にメソッド名とペイロードを入力し、**[OK]** を選択します。

    **[出力]** に結果が表示されます。

## <a name="update-device-twin"></a>デバイス ツインの更新

デバイス ツインを編集するには、次の手順を実行します。

1. デバイスを右クリックし、 **[デバイス ツインの編集]** を選択します。

   **azure-iot-device-twin.json** ファイルが開き、デバイス ツインの内容が表示されます。

1. **azure-iot-device-twin.json** ファイルの **tags** または **properties.desired** フィールドを一部編集します。

1. **Ctrl+S** キーを押してデバイス ツインを更新します。

   **[出力]** に結果が表示されます。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

IoT Hub からデバイスにメッセージを送信するには、次の手順に従います。

1. デバイスを右クリックして、**[Send C2D Message]\(C2D メッセージを送信する\)** を選択します。

1. **[Send C2D message]\(C2D メッセージを送信する\)** にメッセージを入力し、**[OK]** を選択します。

   **[出力]** に結果が表示されます。

## <a name="next-steps"></a>次のステップ

Cloud Explorer for Visual Studio をさまざまな管理オプションで使用する方法を学習しました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
