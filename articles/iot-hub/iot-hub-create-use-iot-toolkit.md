---
title: VS Code 用の Azure IoT Tools を使用した Azure IoT ハブの作成 | Microsoft Docs
description: Visual Studio Code 用の Azure IoT Tools を使用して、リソース グループに Azure IoT ハブを作成する方法について説明します。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75912239"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Visual Studio Code 用の Azure IoT Tools を使用して IoT ハブを作成する

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

この記事では、[Visual Studio Code 用の Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して Azure IoT ハブを作成する方法について説明します。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事を完了するには、以下が必要です。

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- [Visual Studio Code](https://code.visualstudio.com/)

- Visual Studio Code 用の [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="create-an-iot-hub"></a>IoT Hub の作成

1. Visual Studio Code で **エクスプローラー** ビューを開きます。

2. エクスプローラーの下部で、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

   ![Azure IoT Hub デバイスを展開する](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをポイントします。 

4. **[Create IoT Hub]\(IoT ハブを作成する\)** を選択します。

5. 右下隅にポップアップが表示され、Azure への初めてのサインインを行うことができます。

6. Azure サブスクリプションを選択します。 

7. リソース グループを選択します。

8. 場所を選択します。

9. 価格レベルを選択します。

10. IoT ハブのグローバルに一意の名前を入力します。

11. IoT ハブが作成されるまで数分待ちます。

## <a name="next-steps"></a>次のステップ

ここでは、Visual Studio Code 用の Azure IoT Tools を使用して IoT ハブをデプロイしました。 次の記事に進んでください。

* [Visual Studio Codes 用の Azure IoT Tools を使用してデバイスと IoT ハブの間のメッセージを送受信する](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

* [Azure IoT Hub デバイスの管理に Visual Studio Code 用の Azure IoT Tools を使用する](iot-hub-device-management-iot-toolkit.md)

* [VS Code 用 Azure IoT Hub の Wiki ページを参照する](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki)。
