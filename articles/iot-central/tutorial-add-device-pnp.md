---
title: Azure IoT Central アプリケーションに実デバイスを追加する | Microsoft Docs
description: オペレーターとして、Azure IoT Central アプリケーションに実デバイスを追加します。
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878863"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>チュートリアル:シミュレートされたデバイスを Azure IoT Central アプリケーションに追加する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

このチュートリアルでは、シミュレートされたデバイスを Microsoft Azure IoT Central アプリケーションに追加して構成する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しいシミュレートされたデバイスの追加
> * シミュレートされたデバイスを作成エクスペリエンスで使用する

## <a name="prerequisites"></a>前提条件

始める前に、作成者が Azure IoT Central アプリケーションを作成する最初の作成者向けチュートリアルを完了する必要があります。

* [新しいデバイスの種類を定義する](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (必須)

## <a name="add-a-simulated-device"></a>シミュレートされたデバイスの追加

実デバイスをアプリケーションに追加するには、[新しいデバイスの種類を定義する](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)チュートリアルで作成した **Environmental Sensor** デバイス テンプレートを使用します。

1. オペレーターが新しいデバイスを追加するには、左側のナビゲーション メニューで **[デバイス]** を選択します。 **[デバイス]** タブには、 **[すべてのデバイス]** および **[環境センサー]** デバイス テンプレートが表示されます。

1. シミュレートされた環境センサー デバイスを追加するには、 **[+ 新規]** を選択します。 推奨される**デバイス ID** を使用するか、独自の小文字の**デバイス ID** を入力します。 新しいデバイスの名前を入力することもできます。 **[シミュレート済み]** トグルを **[オン]** に切り替え、 **[作成]** を選択します。

    ![シミュレートされたデバイス](./media/tutorial-add-device-pnp/simulated-device.png)

これで、シミュレートされたデータを使用してデバイス テンプレート用に作成者によって作成されたビューを操作できるようになりました。

## <a name="use-a-simulated-device-to-improve-views"></a>シミュレートされたデバイスを使用してビューを改善する

新しいシミュレートされたデバイスを作成した後、作成者はこのデバイスを使用して、デバイス テンプレートのビューを引き続き改善し、構築することができます。

1. デバイスのビューで、作成したシミュレートされたデバイスの**デバイス ID** をコピーします。

1. 左側のナビゲーション メニューで **[デバイス テンプレート]** タブを選択し、 **[環境センサー]** テンプレートを選択します。

1. 編集するビューを選択するか、新しいビューを作成します。 **[Configure preview device]\(プレビュー デバイスの構成\)** をクリックします。 ここでは、プレビュー デバイスを使用しないか、テスト用に構成できる実際のデバイスを使用するか、IoT Central に追加した既存のデバイスを使用するかを選択できます。

1. **[Select from a running device]\(実行中のデバイスから選択する\)** を選択し、コピーしたシミュレートされたデバイスの**デバイス ID** を入力します。

1. **[適用]** を選択します。 これで、デバイス テンプレート ビューの作成エクスペリエンスで、同じシミュレートされたデバイスを確認できるようになりました。 このビューは、グラフやその他の視覚化に特に役立ちます。

    ![プレビュー デバイスの構成](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

* 新しいシミュレートされたデバイスの追加
* シミュレートされたデバイスを作成エクスペリエンスで使用する

ここでは、シミュレートされたデバイスを Azure IoT Central アプリケーションに接続しました。推奨される次の手順は以下のとおりです。

オペレーターは、以下の方法を学ぶことができます。

> [!div class="nextstepaction"]
> [ルールを構成する](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

デバイス開発者は、以下の方法を学ぶことができます。

> [!div class="nextstepaction"]
> [MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



