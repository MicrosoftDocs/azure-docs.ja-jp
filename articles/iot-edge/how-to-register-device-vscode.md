---
title: Visual Studio Code から新しいデバイスを登録する - Azure IoT Edge | Microsoft Docs
description: Visual Studio Code を使用して Azure IoT Hub に新しい IoT Edge デバイスを作成し、接続文字列を取得します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 2b851e7f2ebdbff08fa09002765fbd7d7927deba
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051171"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Visual Studio Code から新しい Azure IoT Edge デバイスを登録する

Azure IoT Edge で IoT デバイスを使用する前に、それらを IoT ハブに登録する必要があります。 デバイスを登録すると、Edge ワークロード用にデバイスを設定するために使用できる接続文字列を受け取ります。

この記事では、Visual Studio Code (VS Code) を使用して新しい IoT Edge デバイスを登録する方法を示します。 VS Code では、ほとんどの操作を複数の方法で実行できます。 この記事ではエクスプローラーを使用していますが、コマンド パレットを使用してほとんどの手順を実行することもできます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT ハブで操作を実行できます。 これらの操作を動作させるには、Azure アカウントにサインインし、作業している IoT ハブを選択する必要があります。

1. Visual Studio Code で**エクスプローラー** ビューを開きます。

1. エクスプローラーの下部で、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。

   ![[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\) セクションを展開する](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

1. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。

1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。

1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。

1. Azure サブスクリプションを選択します。

1. IoT ハブを選択します。

## <a name="create-a-device"></a>デバイスを作成する

1. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。

1. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。

1. **[Create IoT Edge Device]\(IoT Edge デバイスの作成\)** を選択します。

1. 表示されたテキスト ボックスにデバイス ID を指定します。

コマンドの結果が出力画面に表示されます。 デバイスの情報が出力されます。この情報には、指定した **deviceId** および IoT ハブに物理デバイスを接続するために使用できる **connectionString** が含まれます。

## <a name="view-all-devices"></a>すべてのデバイスを表示する

IoT ハブに接続するすべてのデバイスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションに表示されます。 IoT Edge デバイスとそれ以外のデバイスはアイコンで区別できます。IoT Edge デバイスを展開すると、各デバイスにデプロイされたモジュールが表示されます。

   ![IoT ハブ内のすべての IoT Edge デバイスを表示する](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>接続文字列の取得

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

1. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションでデバイスの ID を右クリックします。

1. **[Copy Device Connection String]\(デバイス接続文字列のコピー\)** を選択します。

   接続文字列がクリップボードにコピーされます。

右クリック メニューから **[Get Device Info]\(デバイス情報の取得\)** を選択して、接続文字列を含むすべてのデバイス情報を出力ウィンドウに表示することもできます。

## <a name="next-steps"></a>次の手順

[Visual Studio Code でモジュールをデバイスにデプロイ](how-to-deploy-modules-vscode.md)する方法を学習します。
