---
title: 新しい Azure IoT Edge デバイスを登録する | Microsoft Docs
description: Azure CLI の IoT 拡張機能を使用して新しい IoT Edge デバイスを登録し、接続文字列を取得する
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 0f41dbb4e6231e804249c3fce3dfc8275dcc00aa
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489012"
---
# <a name="register-an-azure-iot-edge-device"></a>Azure IoT Edge デバイスを登録する

Azure IoT Edge で IoT デバイスを使用する前に、それらを IoT ハブに登録する必要があります。 デバイスが登録されたら、IoT Edge ワークロード用にデバイスを設定するための接続文字列を取得できます。

次のいずれかのツールを使用して、登録できます。

* Azure リソースを作成、表示、および管理するためにグラフィカル ユーザー インターフェイスを使用する場合は、[Azure portal でデバイスを登録します](#register-in-the-azure-portal)。
* IoT ソリューションの開発と同じ場所で Azure IoT リソースを管理する場合は、[Visual Studio Code にデバイスを登録します](#register-with-visual-studio-code)。
* Azure リソースを管理するためのコマンドライン ツールを使用する場合、またはタスクを自動化する場合は、[Azure CLI にデバイスを登録します](#register-with-the-azure-cli)。

## <a name="register-in-the-azure-portal"></a>Azure portal での登録

Azure portal では、すべての登録タスクを実行できます。

### <a name="prerequisites-for-the-azure-portal"></a>Azure portal の前提条件

Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Azure portal で IoT Edge デバイスを作成する

Azure portal の IoT Hub で、IoT Edge デバイスは、エッジ対応ではない IOT デバイスとは別に作成および管理されます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
2. 左側のウィンドウで、メニューの **[IoT Edge]** を選択します。
3. **[IoT Edge デバイスの追加]** を選択します。
4. わかりやすいデバイス ID を指定します。 認証キーを自動生成して、新しいデバイスをハブに接続するために、既定の設定を使用します。
5. **[保存]** を選択します。

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Azure portal で IoT Edge デバイスを表示する

IoT ハブに接続するすべてのエッジ対応デバイスが、**IoT Edge** ページに一覧表示されます。

![IoT ハブ内のすべての IoT Edge デバイスを表示する](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Azure portal で接続文字列を取得する

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

1. ポータルの **IoT Edge** ページで、IoT Edge デバイスの一覧からデバイス ID をクリックします。
2. **[プライマリ接続文字列]** または **[セカンダリ接続文字列]** のどちらかの値をコピーします。

## <a name="register-with-visual-studio-code"></a>Visual Studio Code を使用した登録

VS Code では、ほとんどの操作を複数の方法で実行できます。 この記事ではエクスプローラーを使用していますが、コマンド パレットを使用して手順を実行することもできます。

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code の前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT Hub で操作を実行できます。 これらの操作を行うには、Azure アカウントにサインインし、IoT Hub を選択する必要があります。

1. Visual Studio Code で**エクスプローラー** ビューを開きます。
1. エクスプローラーの下部で、 **[Azure IoT Hub]** セクションを展開します。

   ![[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\) セクションを展開する](./media/how-to-register-device/azure-iot-hub-devices.png)

1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。
1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。
1. Azure サブスクリプションを選択します。
1. IoT ハブを選択します。

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Visual Studio Code を使用して IoT Edge デバイスを作成する

1. VS Code エクスプローラーで、 **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。
1. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Create IoT Edge Device]\(IoT Edge デバイスの作成\)** を選択します。
1. 表示されたテキスト ボックスにデバイス ID を指定します。

コマンドの結果が出力画面に表示されます。 デバイスの情報が出力されます。この情報には、指定した **deviceId** および IoT ハブに物理デバイスを接続するために使用できる **connectionString** が含まれます。

コマンドの結果が出力画面に表示されます。 デバイスの情報が出力されます。この情報には、指定した **deviceId** および IoT ハブに物理デバイスを接続するために使用できる **connectionString** が含まれます。

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visual Studio Code を使用して IoT Edge デバイスを表示する

IoT ハブに接続するすべてのデバイスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションに表示されます。 IoT Edge デバイスとそれ以外のデバイスはアイコンで区別できるほか、各 IoT Edge デバイスには **$edgeAgent** モジュールと **$edgeHub** モジュールがデプロイされています。

![IoT ハブ内のすべての IoT Edge デバイスを表示する](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Visual Studio Code を使用して接続文字列を取得する

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

1. **[Azure IoT Hub]** セクションでデバイスの ID を右クリックします。
1. **[Copy Device Connection String]\(デバイス接続文字列のコピー\)** を選択します。

   接続文字列がクリップボードにコピーされます。

右クリック メニューから **[Get Device Info]\(デバイス情報の取得\)** を選択して、接続文字列を含むすべてのデバイス情報を出力ウィンドウに表示することもできます。

## <a name="register-with-the-azure-cli"></a>Azure CLI を使用して登録する

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) は、IoT Edge などの Azure リソースを管理するための、オープン ソースのクロス プラットフォーム コマンドライン ツールです。 これを使用すると、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを簡単に管理することができます。 IoT 拡張機能によって、Azure CLI には、デバイス管理、完全な IoT Edge 対応などの機能が追加されました。

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI の前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。
* ご使用の環境内の [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.70 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。
* [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Azure CLI を使用して IoT Edge デバイスを作成する

[az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-create) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 次に例を示します。

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

このコマンドには、3 つのパラメーターが含まれます。

* **device-id**: IoT ハブに対して一意のわかりやすい名前を指定します。
* **hub-name**: IoT ハブの名前を指定します。
* **edge-enabled**: このパラメーターは、デバイスが IoT Edge に使用されることを宣言します。

   ![az iot hub device-identity create output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Azure CLI を使用して IoT Edge デバイスを表示する

IoT ハブのすべてのデバイスを表示するには、[az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-list) コマンドを使用します。 次に例を示します。

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Azure CLI を使用して接続文字列を取得する

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。 [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) コマンドを使用すると、単一のデバイスの接続文字列が返されます。

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` パラメーターの値は、大文字と小文字が区別されます。 接続文字列を囲む引用符はコピーしないでください。

## <a name="next-steps"></a>次のステップ

これで IoT ハブにデバイス ID が登録されたので、デバイスに IoT Edge ランタイムをインストールする準備ができました。 デバイスのオペレーティング システムに応じて、ランタイムをインストールします。

* [Windows に Azure IoT Edge をインストールする](how-to-install-iot-edge-windows.md)
* [Linux に Azure IoT Edge ランタイムをインストールする](how-to-install-iot-edge-linux.md)
