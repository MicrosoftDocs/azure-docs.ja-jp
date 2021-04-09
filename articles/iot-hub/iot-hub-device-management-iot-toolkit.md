---
title: Azure IoT Tools for VSCode を使用した Azure IoT デバイスの管理
description: Azure IoT Hub デバイスの管理に、ダイレクト メソッドやツインの必要なプロパティの管理オプションなどの機能がある Visual Studio Code 用 Azure IoT ツールを使用します。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: d85e0e967dd802a77ccbc11b884d7a9f2891524d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "81688092"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Azure IoT Hub デバイスの管理に Visual Studio Code 用 Azure IoT Tools を使用する

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) は、IoT Hub の管理と IoT アプリケーションの開発を容易にする便利な Visual Studio Code 拡張機能です。 さまざまなタスクを実行するために使用できる管理オプションが付属しています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 管理オプション          | タスク                    |
|----------------------------|--------------------------------|
| ダイレクト メソッド             | メッセージの送信開始や停止、デバイスの再起動などの機能をデバイスに実行させます。                                        |
| デバイス ツインの読み取り           | デバイスの報告される状態を取得します。 たとえば、デバイスは、現在 LED が点滅していることを報告します。                                    |
| デバイス ツインの更新         | デバイスを特定の状態にします (LED を緑に設定したり、テレメトリの送信間隔を 30 分に設定したりします)。         |
| クラウドからデバイスへのメッセージ   | デバイスに通知を送信します。 例: "今日はほぼ雨が降ります。 必ず傘を持っていってください。"              |

これらのオプションの相違点の詳細な説明と使用するためのガイダンスについては、「[device-to-cloud 通信に関するガイダンス](iot-hub-devguide-d2c-guidance.md)」と「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」を参照してください。

デバイス ツインは、デバイスの状態に関する情報 (メタデータ、構成、状態) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 デバイス ツインの詳細については、「[デバイス ツインの使用](iot-hub-node-node-twin-getstarted.md)」を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>学習内容

さまざまな管理オプションを指定して、開発マシンに対して Visual Studio Code 用 Azure IoT Tools を使用する方法について学びます。

## <a name="what-you-do"></a>作業内容

さまざまな管理オプションを使用して、Visual Studio Code 用 Azure IoT Tools を実行します。

## <a name="what-you-need"></a>必要なもの

* 有効な Azure サブスクリプション
* サブスクリプションの Azure IoT Hub。
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code 用の Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。または URL (`vscode:extension/vsciot-vscode.azure-iot-tools`) をコピーして、ブラウザー ウィンドウに貼り付けてください。

## <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

1. VS Code の **エクスプローラー** ビューで、左下隅の **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。

2. コンテキスト メニューで **[Select IoT Hub]\(IoT Hub の選択\)** をクリックします。

3. 右下隅にポップアップが表示され、Azure への初めてのサインインを行うことができます。

4. サインインすると、Azure サブスクリプションの一覧が表示されるので、Azure サブスクリプションと IoT Hub を選択します。

5. 数秒で **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** タブにデバイスの一覧が表示されます。

   > [!Note]
   > **[Set IoT Hub Connection String]\(IoT Hub 接続文字列の設定\)** を選択して設定することもできます。 ポップアップ ウィンドウに、IoT デバイスの接続先 IoT ハブの **iothubowner** ポリシーの接続文字列を入力します。

## <a name="direct-methods"></a>ダイレクト メソッド

1. デバイスを右クリックし、 **[ダイレクト メソッドの呼び出し]** を選択します。 

2. 入力ボックスにメソッド名とペイロードを入力します。

3. 結果が、 **[OUTPUT]\(出力\)**  >  **[Azure IoT Hub]** ビューに表示されます。

## <a name="read-device-twin"></a>デバイス ツインの読み取り

1. デバイスを右クリックし、 **[デバイス ツインの編集]** を選択します。 

2. **azure-iot-device-twin.json** ファイルが開き、デバイス ツインの内容が表示されます。

## <a name="update-device-twin"></a>デバイス ツインの更新

1. **tags** フィールドまたは **properties.desired** フィールドを編集します。

2. **azure-iot-device-twin.json** ファイルを右クリックします。

3. **[デバイス ツインの更新]** を選択してデバイス ツインを更新します。

## <a name="send-cloud-to-device-messages"></a>C2D メッセージの送信

IoT Hub からデバイスにメッセージを送信するには、次の手順に従います。
 
1. デバイスを右クリックして、 **[Send C2D Message to Device]\(C2D メッセージをデバイスに送信する\)** を選択します。 

2. 入力ボックスにメッセージを入力します。

3. 結果が、 **[OUTPUT]\(出力\)**  >  **[Azure IoT Hub]** ビューに表示されます。

## <a name="next-steps"></a>次のステップ

ここでは、さまざまな管理オプションを使って、Visual Studio Code 用 Azure IoT Tools 拡張機能を使用する方法について学びました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
