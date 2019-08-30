---
title: SensorTile.box デバイスを Azure IoT Central アプリケーションに接続する | Microsoft Docs
description: デバイス開発者として、SensorTile.box デバイスを Azure IoT Central アプリケーションに接続する方法を学習します。
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: ce0c5abe6e89094623c07afa2d1c85903e0e7ee7
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877440"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>SensorTile.box デバイスを Azure IoT Central アプリケーションに接続する

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

この記事では、デバイス開発者として、SensorTile.box デバイスを Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のリソースが必要です。

* SensorTile.box デバイス。 詳細については、[SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html) に関するページを参照してください。
* Android デバイスにインストールされている ST BLE Sensor アプリ。[こちらからダウンロードできます](https://play.google.com/store/apps/details?id=com.st.bluems)。 詳細については、次を参照してください:「[STBLESensor](https://www.st.com/stblesensor)」
* **DevKits** アプリケーション テンプレートから作成された Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](quick-deploy-iot-central.md)に関するページをご覧ください。
* **[デバイス テンプレート]** ページにアクセスし、 **[+ 新規]** をクリックし、 **[SensorTile.box]** テンプレートを選択することで、IoT Central アプリケーションに **SensorTile.box** デバイス テンプレートを追加します。

### <a name="get-your-device-connection-details"></a>デバイス接続の詳細の取得

Azure IoT Central アプリケーションでは、**SensorTile.box** デバイス テンプレートから実デバイスを追加し、デバイスの接続詳細を書きとめます。**スコープ ID**、**デバイス ID**、**主キー**。

1. Device Explorer からデバイスを追加します。 **[+ 新規]、[Real]\(実在\)** の順に選択し、実在のデバイスを追加します。

    * 小文字の **[デバイス ID]** を入力するか、推奨される**デバイス ID** を使用します。
    * **[デバイス名]** を入力するか、推奨名を使います

    ![デバイスの追加](media/howto-connect-sensortile/real-device.png)

1. デバイス接続の詳細である **スコープ ID**、**デバイス ID**、**主キー**を取得するには、デバイス ページで **[接続]** を選択します。

    ![接続の詳細](media/howto-connect-sensortile/connect-device.png)

1. 接続の詳細をメモします。 次の手順で DevKit デバイスを準備するときに、インターネットから一時的に切断されます。

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>モバイル アプリケーションで SensorTile.box を設定する

このセクションでは、デバイス上にアプリケーションのファームウェアをプッシュする方法を説明します。 その後さらに、Bluetooth Low Energy (BLE) 接続を利用して ST BLE Sensor モバイル アプリ経由で IoT Central にデバイス データを送信する方法を紹介します。

1. ST BLE Sensor アプリを起動し、 **[新しいアプリの作成]** ボタンを押します。

    ![アプリを作成する](media/howto-connect-sensortile/create-app.png)

1. **Barometer** アプリケーションを選択します。
1. アップロード ボタンを押します。

    ![バロメーター アップロード](media/howto-connect-sensortile/barometer-upload.png)

1. SensorTile.box に関連付けられている再生ボタンを押します。
1. このプロセスが完了すると、SensorTile.box によって BLE 経由で温度、気圧、湿度がストリーム配信されます。

## <a name="connect-the-sensortilebox-to-the-cloud"></a>SensorTile.box をクラウドに接続する

このセクションでは、SensorTile.box をモバイル アプリケーションに接続し、モバイル アプリケーションをクラウドに接続する方法を説明します。

1. 左側のメニューを使用し、 **[クラウドのログ記録]** ボタンを選択します。

    ![クラウドのログ記録](media/howto-connect-sensortile/cloud-logging.png)

1. クラウド プロバイダーとして **[Azure IoT Central]** を選択します。
1. 前にメモしたデバイス ID とスコープ ID を挿入します。

    ![資格情報](media/howto-connect-sensortile/credentials.png)

1. **[アプリケーション キー]** ラジオ ボタンを選択します。
1. **[接続]** をクリックし、アップロードする利用統計情報を選択します。
1. 数秒後、IoT Central アプリケーション ダッシュボードにデータが表示されます。

## <a name="sensortilebox-device-template-details"></a>SensorTile.box デバイス テンプレートの詳細

SensorTile.box デバイス テンプレートから作成された、次の特性を持つアプリケーション:

### <a name="telemetry"></a>テレメトリ

| フィールド名     | Units  | 最小値 | 最大値 | 小数点以下の桁数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | dps   | -3276   | 3276    | 1              |
| gyroscopeY     | dps   | -3276   | 3276    | 1              |
| gyroscopeZ     | dps   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>次の手順

ここでは、SensorTile.box をご利用の Azure IoT Central アプリケーションに接続する方法について説明しました。推奨される次の手順は、独自の IoT デバイス用に[カスタム デバイス テンプレートを設定する方法](howto-set-up-template.md)を学習することです。
