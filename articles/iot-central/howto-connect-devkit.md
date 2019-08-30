---
title: DevKit デバイスを Azure IoT Central アプリケーションに接続する | Microsoft Docs
description: デバイス開発者として、MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する方法を学習します。
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 81a355cc7c0d1190ee86fac6ed155380e6e5c0d1
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877528"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

この記事では、デバイス開発者として、MXChip IoT DevKit (DevKit) デバイスを Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のリソースが必要です。

1. **サンプル Devkit** アプリケーション テンプレートから作成された Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](quick-deploy-iot-central.md)に関するページをご覧ください。
1. DevKit デバイス。 DevKit デバイスを購入するには、「[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)」を参照してください。

## <a name="sample-devkits-application"></a>サンプル Devkit アプリケーション

**サンプル Devkit** アプリケーション テンプレートから作成されたアプリケーションには、次のデバイス特性を定義する **MXChip** デバイス テンプレートが含まれています。

- **湿度**、**温度**、**圧力**、**磁力計** (X、Y、Z 軸に沿って測定)、**加速度計** (X、Y、Z 軸に沿って測定)、**ジャイロスコープ** (X、Y、Z 軸に沿って測定) のテレメトリ測定値。
- **デバイス状態**の状態測定。
- **ボタン B 押下**でのイベント測定。
- **電圧**、**電流**、**ファン速度**、**IR** のトグルの設定。
- デバイス プロパティである **[Die Number] (サイコロの数字)** と、場所プロパティである **[デバイスの場所]** 。
- クラウド プロパティである **[メーカー]** 。
- コマンド **Echo** および **Countdown**。 実際のデバイス上で **Echo** コマンドが受信されると、送信された値がデバイスの画面に表示されます。 実際のデバイスで **Countdown** コマンドが受信されると、LED がパターンを順番に繰り返し、デバイスによってカウントダウン値が IoT Central に送信されます。

構成について詳しくは、「[MXChip デバイス テンプレートの詳細](#mxchip-device-template-details)」をご覧ください

## <a name="add-a-real-device"></a>実デバイスの追加

### <a name="get-your-device-connection-details"></a>デバイス接続の詳細の取得

Azure IoT Central アプリケーションでは、**MXChip** デバイス テンプレートから実デバイスを追加し、デバイスの接続詳細を書きとめます。**スコープ ID、デバイス ID、主キー**:

1. Device Explorer から**実際のデバイス**を追加し、 **[+ 新規] > [Real]\(リアル\)** を選択して実際のデバイスを追加します。

    * 小文字の **[デバイス ID]** を入力するか、推奨される**デバイス ID** を使用します。
    * **[デバイス名]** を入力するか、推奨名を使います

    ![デバイスの追加](media/howto-connect-devkit/add-device.png)

1. デバイス接続の詳細である **スコープ ID**、**デバイス ID**、**主キー**を取得するには、デバイス ページで **[接続]** を選択します。

    ![接続の詳細](media/howto-connect-devkit/device-connect.png)

1. 接続の詳細をメモします。 次の手順で DevKit デバイスを準備するときに、インターネットから一時的に切断されます。

### <a name="prepare-the-devkit-device"></a>DevKit デバイスを準備する

以前にデバイスを使用しており、別の WiFi ネットワーク、接続文字列、またはテレメトリ測定を使用するようにそのデバイスを再構成するには、**A** ボタンと **B** ボタンの両方を同時に押します。 それで機能しない場合は、 **[リセット]** ボタンを押して再試行します。

#### <a name="to-prepare-the-devkit-device"></a>DevKit デバイスを準備するには

1. GitHub 上の[リリース](https://aka.ms/iotcentral-docs-MXChip-releases) ページから、MXChip 用の事前に構築された最新の Azure IoT Central ファームウェアをダウンロードします。
1. USB ケーブルを使用して、DevKit デバイスを開発用コンピューターに接続します。 Windows では、DevKit デバイス上のストレージにマッピングされたドライブでファイル エクスプローラー ウィンドウが開きます。 たとえば、このドライブは **AZ3166 (D:)** と呼ばれることがあります。
1. **iotCentral.bin** ファイルをドライブ ウィンドウにドラッグします。 コピーが完了すると、デバイスが新しいファームウェアで再起動します。

1. DevKit デバイスが再起動すると、次の画面が表示されます。

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > 画面に何か表示されている場合は、デバイスをリセットし、デバイスの **A** ボタンと **B** ボタンを同時に押して、デバイスを再起動します。

1. これで、デバイスがアクセス ポイント (AP) モードになりました。 コンピューターまたはモバイル デバイスからこの WiFi アクセス ポイントに接続できます。

1. コンピューター、電話、またはタブレット上で、デバイスの画面に表示されている WiFi ネットワーク名に接続します。 このネットワークに接続するとき、インターネット アクセスは存在しません。 この状態は予測されたものであり、このネットワークにはデバイスを構成する間の短時間しか接続されません。

1. Web ブラウザーを開き、[http://192.168.0.1/start](http://192.168.0.1/start) に移動します。 次の Web ページが表示されます。

    ![デバイス構成ページ](media/howto-connect-devkit/configpage.png)

    Web ページで、以下を入力します。
    - WiFi ネットワークの名前
    - WiFi ネットワークのパスワード
    - デバイスの画面に表示されている PIN コード
    - デバイスの接続の詳細である**スコープ ID**、**デバイス ID**、**主キー** (手順に従っている場合、この情報は書き留めてあるはずです)
    - すべての使用可能なテレメトリ測定を選択する

1. **[デバイスの構成]** を選択すると、次のページが表示されます。

    ![構成されたデバイス](media/howto-connect-devkit/deviceconfigured.png)

1. デバイス上の **Reset** ボタンを押します。

## <a name="view-the-telemetry"></a>テレメトリを表示する

DevKit デバイスが再起動すると、デバイスの画面に次のものが表示されます。

* 送信されたテレメトリ メッセージの数。
* エラーの数。
* 受信された必要なプロパティの数、および送信された報告されるプロパティの数。

> [!NOTE]
> 接続しようとしたときにデバイスがループしているように見える場合は、IoT Central でデバイスが **[ブロック済み]** かどうかを確認し、アプリに接続できるようにデバイスを **[ブロック解除]** します。

デバイスをシェイクして、報告されるプロパティを送信します。 デバイスは、 **[Die Number] (サイコロの数字)** デバイス プロパティとして乱数を送信します。

Azure IoT Central でテレメトリ測定や報告されるプロパティの値を表示したり、設定を構成したりできます。

1. **Device Explorer** を使用して、追加した MXChip 実デバイスの **[Measurements] (測定)** ページに移動します。

    ![実デバイスに移動する](media/howto-connect-devkit/realdevicenew.png)

1. **[Measurements] (測定)** ページで、MXChip デバイスから来たテレメトリを表示できます。

    ![実デバイスからのテレメトリを表示する](media/howto-connect-devkit/devicetelemetrynew.png)

1. **[プロパティ]** ページで、デバイスによって報告された最後のサイコロの数字とデバイスの場所を表示できます。

    ![デバイスのプロパティを表示する](media/howto-connect-devkit/devicepropertynew.png)

1. **[設定]** ページで、MXChip デバイス上の設定を更新できます。

    ![デバイス設定を表示する](media/howto-connect-devkit/devicesettingsnew.png)

1. **[コマンド]** ページで、**Echo** および **Countdown** コマンドを呼び出すことができます。

    ![コマンドの呼び出し](media/howto-connect-devkit/devicecommands.png)

1. **[ダッシュボード]** ページで、場所のマップを確認できます

    ![デバイス ダッシュボードの表示](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>ソース コードをダウンロードする

デバイス コードを調査したり変更したりする場合は、それを GitHub からダウンロードできます。 コードを変更する予定がある場合は、この手順に従って、使用しているデスクトップ オペレーティング システムの[開発環境を準備する](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment)必要があります。

ソース コードをダウンロードするには、デスクトップ コンピューター上で次のコマンドを実行します。

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

前のコマンドは、ソース コードを `iot-central-firmware` という名前のフォルダーにダウンロードします。

> [!NOTE]
> **git** が開発環境にインストールされていない場合は、それを [https://git-scm.com/download](https://git-scm.com/download) からダウンロードできます。

## <a name="review-the-code"></a>コードの確認

Visual Studio Code を使用して、`iot-central-firmware` フォルダー内の `MXCHIP/mxchip_advanced` フォルダーを開きます。

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

テレメトリが Azure IoT Central アプリケーションにどのように送信されたかを確認するには、`src` フォルダー内の **telemetry.cpp** ファイルを開きます。

- 関数 `TelemetryController::buildTelemetryPayload` は、デバイス上のセンサからのデータを使用して JSON テレメトリ ペイロードを作成します。

- 関数 `TelemetryController::sendTelemetryPayload` は、その JSON ペイロードを Azure IoT Central アプリケーションで使用される IoT ハブに送信するために **AzureIOTClient.cpp** 内の `sendTelemetry` を呼び出します。

プロパティ値が Azure IoT Central アプリケーションにどのように報告されたかを確認するには、`src` フォルダー内の **telemetry.cpp** ファイルを開きます。

- 関数 `TelemetryController::loop` では、報告される **location** プロパティが約 30 秒ごとに送信されます。 これには、**AzureIOTClient.cpp** ソース ファイル内の `sendReportedProperty` 関数が使用されます。

- 関数 `TelemetryController::loop` では、デバイス加速度計がダブル タップを検出したときに報告される **dieNumber** プロパティが送信されます。 これには、**AzureIOTClient.cpp** ソース ファイル内の `sendReportedProperty` 関数が使用されます。

IoT Central アプリケーションから呼び出されたコマンドにデバイスがどのように応答するかを確認するには、`src` フォルダー内の **registeredMethodHandlers.cpp** ファイルを開きます。

- **dmEcho** 関数は、**echo** コマンドのハンドラーです。 これは、デバイスの画面上のペイロード内の **displayedValue** フィールドに表示されます。

- **dmCountdown** 関数は、**countdown** コマンドのハンドラーです。 これによりデバイスの LED の色が変更され、報告されるプロパティを使用してカウントダウン値が IoT Central アプリケーションに送信されます。 報告されるプロパティの名前はコマンドと同じです。 関数では、**AzureIOTClient.cpp** ソース ファイル内の `sendReportedProperty` 関数が使用されます。

**AzureIOTClient.cpp** ソース ファイル内のコードは、[C 用の Microsoft Azure IoT SDK およびライブラリ](https://github.com/Azure/azure-iot-sdk-c)からの関数を使用して IoT Hub と対話します。

サンプル コードを変更、構築、およびデバイスにアップロードする方法については、`MXCHIP/mxchip_advanced` フォルダー内の **readme.md** ファイルを参照してください。

## <a name="mxchip-device-template-details"></a>MXChip デバイス テンプレートの詳細

サンプル Devkit アプリケーション テンプレートから作成されたアプリケーションには、次の特性を持つ MXChip デバイス テンプレートが含まれています。

### <a name="measurements"></a>測定

#### <a name="telemetry"></a>テレメトリ

| フィールド名     | Units  | 最小値 | 最大値 | 小数点以下の桁数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>States 
| Name          | Display name   | 正常 | 注意 | 危険 | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | デバイス状態   | 緑  | オレンジ  | 赤    | 

#### <a name="events"></a>events 
| Name             | Display name      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | ボタン B の押下  | 

### <a name="settings"></a>設定

数値設定

| Display name | フィールド名 | Units | 小数点以下の桁数 | 最小値 | 最大値 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 電圧      | setVoltage | ボルト | 0              | 0       | 240     | 0       |
| Current      | setCurrent | アンペア  | 0              | 0       | 100     | 0       |
| ファン速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

トグル設定

| Display name | フィールド名 | オンテキスト | オフテキスト | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | オフ     |

### <a name="properties"></a>properties

| Type            | Display name | フィールド名 | データ型 |
| --------------- | ------------ | ---------- | --------- |
| デバイス プロパティ | サイコロの数字   | dieNumber  | number    |
| デバイス プロパティ | デバイスの場所   | location  | location    |
| Text            | メーカー     | manufacturedIn   | 該当なし       |

### <a name="commands"></a>command

| Display name | フィールド名 | 戻り値の型 | 入力フィールドの表示名 | 入力フィールド名 | 入力フィールドの種類 |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| エコー         | echo       | text        | 表示する値         | displayedValue   | text             |
| カウントダウン    | countdown  | number      | カウント開始               | countFrom        | number           |

## <a name="next-steps"></a>次の手順

これで、MXChip IoT DevKit を Azure IoT Central アプリケーションに接続する方法を学習したので、推奨される次の手順は、独自の IoT デバイス用に[カスタム デバイス テンプレートを設定する](howto-set-up-template.md)方法を学習することです。
