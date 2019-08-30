---
title: Azure IoT Central でデバイスを監視する | Microsoft Docs
description: オペレーターとして Azure IoT Central アプリケーションを使用して、デバイスを監視します。
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878783"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>チュートリアル:Azure IoT Central を使用してデバイスを監視する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

このチュートリアルでは、オペレーター向けに Microsoft Azure IoT Central アプリケーションを使用して、デバイスを監視し、設定を変更する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 通知の受信
> * 問題の調査
> * 問題の修復

## <a name="prerequisites"></a>前提条件

始める前に、作成者が Azure IoT Central アプリケーションを作成する 3 つの作成者向けチュートリアルを完了する必要があります。

* [新しいデバイスの種類を定義する](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [デバイスを追加する](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [デバイスのルールとアクションを構成する](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>通知の受信

Azure IoT Central では、デバイスに関する通知を電子メール メッセージとして送信します。 作成者は、接続された環境センサー デバイスの温度がしきい値を超えたときに通知を送信するルールを追加しました。 作成者が通知の受信用に選択したアカウントに送信された電子メールを確認してください。

「[デバイスのルールとアクションを構成する](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)」チュートリアルの最後で受信した電子メール メッセージを開きます。 メール内のデバイスへのリンクを選択します。

![アラート通知電子メール](media/tutorial-monitor-devices-pnp/email.png)

前のチュートリアルで作成した "環境センサー" シミュレートされたデバイスの **[ダッシュボード]** ページがブラウザーで開きます。

![通知電子メール メッセージをトリガーしたデバイス](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>問題の調査

オペレーターは、 **[ダッシュボード]** 、 **[Environmental Sensor のプロパティ]** 、および **[コマンド]** の各ページでデバイスに関する情報を確認できます。 作成者は、接続された環境センサー デバイスに関する重要な情報が表示されるよう、 **[ダッシュボード]** および **[Environmental Sensor のプロパティ]** ページをカスタマイズしました。

**[ダッシュボード]** ビューを選択すると、デバイスに関する情報が表示されます。

ダッシュボードのグラフには、デバイスの温度のプロットが表示されます。 **[デバイスのプロパティ]** タイルでは、デバイスの現在の目標温度を確認することもできます。 目標温度が高すぎると判断したとしましょう。

## <a name="remediate-an-issue"></a>問題の修復

デバイスに変更を加えるには、 **[Environmental Sensor のプロパティ]** ページを使用します。

1. **[Environmental Sensor のプロパティ]** を選択します。 **[Brightness Level]\(輝度\)** を 10 に変更します。 **[保存]** を選択してデバイスを更新します。 デバイスで設定の変更が確認されると、プロパティの状態が **[同期済み]** に変わります。

    ![設定の更新](media/tutorial-monitor-devices-pnp/change-settings.png)

2. **[ダッシュボード]** を選択し、新しいプロパティ値を確認します。

    ![更新されたデバイスのダッシュボード](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

* 通知の受信
* 問題の調査
* 問題の修復

ここでは、デバイスを監視する方法について説明しました。推奨される次の手順は次のとおりです。

> [!div class="nextstepaction"]
> [デバイスのルールとアクションを構成する](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。