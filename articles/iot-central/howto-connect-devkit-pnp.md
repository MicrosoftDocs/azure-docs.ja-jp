---
title: DevKit デバイスを Azure IoT Central アプリケーションに接続する | Microsoft Docs
description: デバイス開発者向けに、IoT プラグ アンド プレイを使用して MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する方法について説明します。
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: f3cfb892fc116640df5dd30abf857bde6e196224
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879095"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する

この記事では、MXChip IoT DevKit (DevKit) デバイスを Azure IoT Central アプリケーションに接続する方法を説明します。 このデバイスは、DevKit デバイスの認定された IoT プラグ アンド プレイ モデルを使用して、IoT Central への接続を構成します。

このハウツー記事では、次のことを行います。

- IoT Central アプリケーションから接続の詳細を取得します。
- デバイスを準備し、IoT Central アプリケーションに接続します。
- IoT Central で、デバイスのテレメトリとプロパティを表示します。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のリソースが必要です。

1. [DevKit デバイス](https://aka.ms/iot-devkit-purchase)。
1. **プレビュー アプリケーション** テンプレートから作成された IoT Central アプリケーション。 [IoT プラグ アンド プレイ アプリケーションの作成](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)に関する記事の手順に従うことができます。

## <a name="get-device-connection-details"></a>デバイス接続の詳細を取得する

IoT Central アプリケーションで、 **[Administration]\(管理\)** タブを選択し、 **[Device Connection]\(デバイス接続\)** を選択します。 **スコープ ID** と**主キー**の値をメモしておきます。

![デバイス グループ接続の詳細](media/howto-connect-devkit-pnp/device-group-connection-details.png)

## <a name="prepare-the-device"></a>デバイスの準備

1. GitHub から DevKit デバイス用の最新の[事前構築済み Azure IoT Central プラグ アンド プレイ ファームウェア](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin)をダウンロードします。

1. USB ケーブルを使用して、DevKit デバイスを開発用コンピューターに接続します。 Windows では、DevKit デバイス上のストレージにマッピングされたドライブでファイル エクスプローラー ウィンドウが開きます。 たとえば、このドライブは **AZ3166 (D:)** と呼ばれることがあります。

1. **iotc_devkit.bin** ファイルをドライブ ウィンドウにドラッグします。 コピーが完了すると、デバイスが新しいファームウェアで再起動します。

    > [!NOTE]
    > **Wi-Fi なし**などのエラーが画面に表示される場合は、DevKit がまだ WiFi に接続していないことが原因です。

1. DevKit で **B ボタン**を押しながら、**リセット** ボタンを押して離した後、**B ボタン** を離します。これで、デバイスがアクセス ポイント モードになりました。 確認するために、画面に "IoT DevKit-AP" と構成ポータルの IP アドレスが表示されます。

1. コンピューターまたはタブレット上で、デバイスの画面に表示されている WiFi ネットワーク名に接続します。 WiFi ネットワークは、**AZ-** で始まり、その後に MAC アドレスが続きます。 このネットワークに接続するとき、インターネット アクセスは存在しません。 この状態は予期されたものであり、このネットワークにはデバイスを構成する間の短時間しか接続されません。

1. Web ブラウザーを開き、[http://192.168.0.1/](http://192.168.0.1/) に移動します。 次の Web ページが表示されます。

    ![構成の UI](media/howto-connect-devkit-pnp/config-ui.png)

    Web ページで、以下を入力します。

    - WiFi ネットワークの名前 (SSID)。
    - WiFi ネットワークのパスワード。
    - 接続の詳細: 自分で選択できる**デバイス ID**、および前にメモした**スコープ ID** と **グループ SAS の主キー**。

    > [!NOTE]
    > 現時点では、IoT DevKit は 2.4 GHz の Wi-Fi にのみ接続できます。ハードウェアの制限のため、5 GHz はサポートされていません。

1. **[デバイスの構成]** を選択します。DevKit デバイスが再起動してアプリケーションを実行します。

    ![再起動の UI](media/howto-connect-devkit-pnp/reboot-ui.png)

    DevKit の画面に、アプリケーションが実行されていることを確認するメッセージが表示されます。

    ![DevKit が実行中](media/howto-connect-devkit-pnp/devkit-running.png)

DevKit では、最初に IoT Central アプリケーションに新しいデバイスが登録されてから、データの送信が開始されます。

## <a name="view-the-telemetry"></a>テレメトリを表示する

この手順では、Azure IoT Central アプリケーションでテレメトリを表示します。

IoT Central アプリケーションで、 **[Devices]\(デバイス\)** タブを選択し、追加したデバイスを選択します。 **[Overview]\(概要\)** タブで、DevKit からのテレメトリを確認できます。

   ![IoT Central デバイスの概要](media/howto-connect-devkit-pnp/mxchip-overview-page.png)

## <a name="review-the-code"></a>コードの確認

コードを確認したり、コードを変更してコンパイルしたりするには、[MXChip IoT DevKit サンプル コード GitHub リポジトリ](https://github.com/MXCHIP/IoTDevKit/tree/master/pnp)にアクセスします。

## <a name="next-steps"></a>次の手順

ここでは、DevKit デバイスを Azure IoT Central アプリケーションに接続する方法について説明しました。次の手順として、独自の IoT デバイス用に[カスタム デバイス テンプレートを設定する](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)方法を学習することをお勧めします。
