---
title: Raspberry Pi を Azure IoT Central アプリケーションに接続する (Python) | Microsoft Docs
description: デバイス開発者として、Python を使用して Raspberry Pi を Azure IoT Central アプリケーションに接続する方法。
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: eccc4100c89c971e264b9b915cd17b9f5ce4477b
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617447"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Raspberry Pi を Azure IoT Central アプリケーションに接続する (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

この記事では、デバイス開発者として、Python プログラミング言語を使用して Raspberry Pi を Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のコンポーネントが必要です。

* **サンプル Devkit** アプリケーション テンプレートから作成された Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](quick-deploy-iot-central.md)に関するページをご覧ください。
* Raspbian オペレーティング システムを実行している Raspberry Pi デバイス。 Raspberry Pi からインターネットに接続できる必要があります。 詳細については、[Raspberry Pi の設定](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)に関するページを参照してください。

## <a name="sample-devkits-application"></a>**サンプル Devkit** アプリケーション

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **Raspberry Pi** デバイス テンプレートが含まれています。

- デバイスが収集する次の測定値を含むテレメトリ:
  - 湿度
  - 気温
  - 圧力
  - 磁力計 (X、Y、Z)
  - 加速度計 (X、Y、Z)
  - ジャイロスコープ (X、Y、Z)
- 設定
  - 電圧
  - Current
  - ファン速度
  - IR 切り替え。
- Properties
  - Die number デバイス プロパティ
  - Location クラウド プロパティ

デバイス テンプレートの構成の詳細については、「[Raspberry PI デバイス テンプレートの詳細](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)」を参照してください。

## <a name="add-a-real-device"></a>実デバイスの追加

Azure IoT Central アプリケーションで、**Raspberry Pi** デバイス テンプレートから実際のデバイスを追加します。 デバイス接続の詳細 (**スコープ ID**、**デバイス ID**、および**主キー**) をメモします。 詳細については、「[Azure IoT Central アプリケーションに実デバイスを追加する](tutorial-add-device.md)」を参照してください。

### <a name="configure-the-raspberry-pi"></a>Raspberry Pi を構成する

次の手順では、GitHub からサンプル Python アプリケーションをダウンロードして構成する方法について説明します。 このサンプル アプリケーションには次の機能があります。

* テレメトリおよびプロパティの値を Azure IoT Central に送信する。
* Azure IoT Central で行われた設定変更に応答する。

デバイスを構成するには、[GitHub の詳しい手順に従ってください](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md)。

1. デバイスが構成されたら、デバイスはすぐに Azure IoT Central へのテレメトリ測定の送信を開始します。
1. Azure IoT Central アプリケーションでは、Raspberry Pi 上で実行されているコードがアプリケーションとどのように対話するかを確認できます。

    * 実デバイスの **[Measurements] (測定)** ページで、Raspberry Pi から送信されたテレメトリを表示できます。
    * **[設定]** ページで、電圧やファン速度などの Raspberry Pi 上の設定を変更できます。 Raspberry Pi で変更が承認されると、設定には**同期済み**と表示されます。

## <a name="raspberry-pi-device-template-details"></a>Raspberry PI デバイス テンプレートの詳細

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ **Raspberry Pi** デバイス テンプレートが含まれています。

### <a name="telemetry-measurements"></a>テレメトリ測定

| フィールド名     | Units  | 最小値 | 最大値 | 小数点以下の桁数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1,000    | 0              |
| magnetometerY  | mgauss | -1000   | 1,000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1,000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>設定

数値設定

| Display name | フィールド名 | Units | 小数点以下の桁数 | 最小値 | 最大値 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 電圧      | setVoltage | ボルト | 0              | 0       | 240     | 0       |
| Current      | setCurrent | アンペア  | 0              | 0       | 100     | 0       |
| ファン速度    | fanSpeed   | RPM   | 0              | 0       | 1,000    | 0       |

トグル設定

| Display name | フィールド名 | オンテキスト | オフテキスト | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | オフ     |

### <a name="properties"></a>Properties

| Type            | Display name | フィールド名 | データ型 |
| --------------- | ------------ | ---------- | --------- |
| デバイス プロパティ | サイコロの数字   | dieNumber  | number    |
| Text            | Location     | location   | 該当なし       |

## <a name="next-steps"></a>次の手順

ここでは、Raspberry Pi をご利用の Azure IoT Central アプリケーションに接続する方法について説明しました。推奨される次の手順は、独自の IoT デバイス用に[カスタム デバイス テンプレートを設定する](howto-set-up-template.md)方法を学習することです。
