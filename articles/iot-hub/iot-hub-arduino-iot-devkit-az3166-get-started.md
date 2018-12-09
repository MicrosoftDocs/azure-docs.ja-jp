---
title: 'IoT DevKit をクラウドへ: IoT DevKit AZ3166 を Azure IoT Hub に接続する | Microsoft Docs'
description: このチュートリアルでは、IoT DevKit AZ3166 を設定して Azure IoT Hub に接続し、Azure クラウド プラットフォームにデータを送信する方法について説明します。
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 08/27/2018
ms.author: rangv
ms.openlocfilehash: 2ece10c43f25ac637a29324f46a88e50d9655431
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620440"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 を Azure IoT Hub に接続する

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を使用すると、Microsoft Azure サービスを活用したモノのインターネット (IoT) ソリューションを開発し、プロトタイプを作成できます。 これには、豊富な周辺機器やセンサーを備えた Arduino 互換ボード、オープン ソース ボード パッケージ、および増え続ける[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が含まれます。

## <a name="what-you-do"></a>作業内容

作成する Azure IoT ハブに [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を接続し、センサーから温度と湿度のデータを収集して、そのデータを IoT ハブに送信します。

DevKit をお持ちでない場合は、 [DevKit シミュレーター](https://azure-samples.github.io/iot-devkit-web-simulator/)を使用するか、[DevKit を購入](https://aka.ms/iot-devkit-purchase)してください。

## <a name="what-you-learn"></a>学習内容

* IoT DevKit をワイヤレス アクセス ポイントに接続し、開発環境を準備する方法。
* IoT ハブを作成し、MXChip IoT DevKit のデバイスを登録する方法。
* MXChip IoT DevKit のサンプル アプリケーションを実行して、センサー データを収集する方法。
* センサー データを IoT ハブに送信する方法。

## <a name="what-you-need"></a>必要なもの

* MXChip IoT DevKit ボードとマイクロ USB ケーブル。 [こちら](https://aka.ms/iot-devkit-purchase)から今すぐ入手できます。
* Windows 10 または macOS 10.10 以降を実行しているコンピューター。
* 有効な Azure サブスクリプション [30 日間の無料試用版 Microsoft Azure アカウント](https://azureinfo.microsoft.com/us-freetrial.html)をアクティブにします。
  
## <a name="prepare-your-hardware"></a>ハードウェアの準備

次のハードウェアをコンピューターに接続します。

* DevKit ボード
* マイクロ USB ケーブル

![必要なハードウェア](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit をコンピューターに接続するには、次の手順を実行します。

1. USB ケーブルの端部をコンピューターに接続します。

2. マイクロ USB の端部を DevKit に接続します。

3. 電源の緑色の LED で、接続されていることを確認できます。

   ![ハードウェアの接続](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Wi-Fi を構成する

IoT プロジェクトではインターネット接続を使用します。 次の手順を使用して、DevKit が Wi-Fi に接続できるように構成します。

### <a name="enter-ap-mode"></a>AP モードの開始

B ボタンを押しながら、リセット ボタンを押して離し、その後、B ボタンを離します。DevKit が AP モードに切り替わり、Wi-Fi を構成できるようになります。 DevKit のサービス セット識別子 (SSID) と、構成ポータルの IP アドレスが画面に表示されます。

![リセット ボタン、B ボタン、および SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>DevKit AP への接続

ここで、別の Wi-Fi 対応デバイス (コンピューターまたは携帯電話) を使用して、DevKit SSID (前の図で強調表示されています) に接続します。 パスワードは空のままにしてください。

![ネットワーク情報と [接続] ボタン](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>DevKit 用に Wi-Fi を構成する

コンピューターまたは携帯電話のブラウザーで、DevKit の画面に表示されている IP アドレスを開いて、DevKit を接続する Wi-Fi ネットワークを選択し、パスワードを入力します。 **[接続]** を選択します。

![[パスワード] ボックスと [接続] ボタン](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

接続に成功すると、DevKit は数秒後に再起動されます。 Wi-Fi 名と IP アドレスが画面に表示されます。

![Wi-Fi 名と IP アドレス](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> 写真に表示されている IP アドレスは、DevKit 画面に表示されている、実際に割り当てられた IP アドレスと一致しない場合があります。 Wi-Fi は DHCP を使用して IP を動的に割り当てるため、これは正常な動作です。

Wi-Fi の構成後は、デバイスを取り外しても、その接続の資格情報がデバイス上で保持されます。 たとえば、自宅で Wi-Fi を構成した DevKit をオフィスに持っていった場合は、(「AP モードの開始」セクション以降の手順を実行して) AP モードを再構成し、オフィスの Wi-Fi に DevKit を接続する必要があります。 

## <a name="start-using-the-devkit"></a>DevKit の使用を開始する

DevKit で実行されている既定のアプリによって、ファームウェアが最新バージョンであるかどうかが確認され、センサー診断データの一部が表示されます。

### <a name="upgrade-to-the-latest-firmware"></a>最新ファームウェアへのアップグレード

> [!NOTE]
> バージョン 1.1 以降、DevKit ではブートローダーで ST-SAFE が有効になっています。 v1.1 より前のバージョンを実行している場合は、ファームウェアをアップグレードする必要があります。

ファームウェアのアップグレードが必要な場合は、現在のファームウェア バージョンと最新のファームウェア バージョンが画面に表示されます。 アップグレードするには、[ファームウェアのアップグレード](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) ガイドに従ってください。

![現在および最新のファームウェア バージョンの表示](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> この操作を行う必要があるのは 1 回です。 DevKit での開発を開始して、アプリをアップロードすると、そのアプリに対して最新のファームウェアが適用されます。

### <a name="test-various-sensors"></a>さまざまなセンサーのテスト

B ボタンを押してセンサーをテストします。 B ボタンを押して離す操作を繰り返して、センサーごとのテストを実行します。

![B ボタンとセンサーの表示](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>開発環境の準備

### <a name="install-azure-iot-workbench"></a>Azure IoT Workbench のインストール

DevKit での開発には、Visual Studio Code 用の [Azure IoT Workbench](https://aka.ms/iot-workbench) 拡張機能を使用することをお勧めします。

Azure IoT Workbench には、IoT ソリューションを開発するための統合されたエクスペリエンスが用意されています。 Azure IoT をはじめとする各種サービスを使用したデバイス開発とクラウド開発の両方に活用できます。 その機能の概要については、この [Channel 9 ビデオ](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code)をご覧ください。

次の手順に従って、DevKit の開発環境を準備します。

1. [Arduino IDE](https://www.arduino.cc/en/Main/Software) をダウンロードしてインストールします。 Arduino コードをコンパイルしたりアップロードしたりするために必要なツールチェーンが備わっています。
    * **Windows**: Windows インストーラー バージョンを使用してください。 App Store からインストールしないでください。
    * **macOS**: 抽出した **Arduino.app** を `/Applications` フォルダーにドラッグ アンド ドロップします。
    * **Ubuntu**: `$HOME/Downloads/arduino-1.8.5` などのフォルダーにファイルを解凍します。

2. IntelliSense コード補完やデバッグ機能など強力な開発者ツールを備えたクロス プラットフォームのソース コード エディターである [Visual Studio Code](https://code.visualstudio.com/) をインストールします。

3. 拡張機能マーケットプレースから **Azure IoT Workbench** を探してインストールします。
    ![Azure IoT Workbench のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-workbench.png) IoT Workbench と共に、依存する他の拡張機能がインストールされます。

4. Arduino を構成する
    * **Windows**: **Windows** で **[ファイル] > [優先設定] > [設定]** を開いて、**...** をクリックし、settings.json を開いてから、Arduino の構成を行う次の行を追加します。 
      
    ```json
    "arduino.path": "C:\\Program Files (x86)\\Arduino",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

    * **macOS**: **macOS** で **[コード] > [環境設定] > [設定]** を開いて、**...** をクリックし、settings.json を開いてから、Arduino の構成を行う次の行を追加します

    ```json
    "arduino.path": "/Applications",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

    * **Ubuntu**:

    ```json
    "arduino.path": "/home/{username}/Downloads/arduino-1.8.5",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

5. `F1` をクリックしてコマンド パレットを開き、**[Arduino: Board Manager]\(Arduino: ボード マネージャー\)** を入力して選択します。 **AZ3166** を検索し、最新バージョンをインストールします。
    ![DevKit SDK のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link のドライバーのインストール

[ST-Link/V2](http://www.st.com/en/development-tools/st-link-v2.html) は、IoT DevKit が開発マシンとの通信に使用する USB インターフェイスです。 OS ごとの手順に従って、対象デバイスに対するマシンのアクセスを許可してください。

* **Windows**: [STMicroelectronics Web サイト](http://www.st.com/en/development-tools/stsw-link009.html)から USB ドライバーをダウンロードしてインストールします。
* **macOS**: macOS の場合、ドライバーは必要ありません。
* **Ubuntu**: ターミナルから次のコマンドを実行し、グループの変更を反映するために一度ログアウトしてからログインします。
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

これで必要な開発環境の準備と構成がすべて完了しました。 IoT の "Hello World" サンプルを作成しましょう。温度のテレメトリを Azure IoT Hub に送信するものです。

## <a name="build-your-first-project"></a>初めてのプロジェクトを作成する

1. IoT DevKit がお使いのコンピューターに接続されて**いない**ことを確認します。 まず VS Code を起動し、DevKit をコンピューターに接続します。

1. 右下のステータス バーで、選択したボードとして **MXCHIP AZ3166** が表示されていること、またシリアル ポートと **STMicroelectronics** が使用されていることを確認します。
    ![ボードと COM を選択](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-board.png)

1. `F1` をクリックしてコマンド パレットを開き、**[IoT Workbench: Examples]\(IoT Workbench: 例)** を入力して選択します。 次に、**[IoT DevKit]** をボードとして選択します。

1. [IoT Workbench Examples]\(IoT Workbench の例\) ページで **[Get Started]\(はじめに\)** を探して **[Open Sample]\(サンプルを開く\)** をクリックします。 次に、サンプル コードをダウンロードするための既定のパスを選択します。
    ![サンプルを開く](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. VS Code に Arduino 拡張機能がインストールされていない場合は、通知ウィンドウで **[インストール]** をクリックします。
    ![Arduino 拡張機能のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino-ext.png)

1. 新しく開いたプロジェクト ウィンドウで、`F1` をクリックしてコマンド パレットを開き、**[IoT Workbench: Cloud]\(IoT Workbench: クラウド\)** を入力して選択し、**[Azure Provision]\(Azure プロビジョニング\)** を選択します。 ステップ バイ ステップ ガイドに従って、Azure IoT Hub のプロビジョニングとデバイスの作成を完了します。
    ![クラウド プロビジョニング](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. `F1` をクリックしてコマンド パレットを開き、**[IoT Workbench: Device]\(IoT Workbench: デバイス)** を入力して選択した後、**[Config Device Settings]\(デバイス設定の構成) > [Config Device Connection String]\(デバイス接続文字列の構成) > [Select IoT Hub Device Connection String]\(IoT Hub デバイス接続文字列の選択)** を選択します。

1. DevKit で**ボタン A** を押しながら、**リセット** ボタンを押して離した後、**ボタン A** を離します。DevKit が構成モードに移行し、接続文字列が保存されます。
    ![接続文字列](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. `F1` を再度クリックして **[IoT Workbench: Device]\(IoT Workbench: デバイス\)** を選択し、**[Device Upload]\(デバイスのアップロード\)** を選択します。
    ![Arduino のアップロード](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit が再起動され、コードの実行が開始されます。

> [!NOTE]
> エラーや中断が生じた場合は、再度コマンドを実行することにより、いつでも復旧できます。

## <a name="test-the-project"></a>プロジェクトのテスト

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Azure IoT Hub に送信されたテレメトリを確認する

ステータス バーの電源プラグ アイコンをクリックして、シリアル モニターを開きます。![シリアル モニター](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

次の結果が表示された場合、サンプル アプリケーションは正常に実行されてます。

* IoT Hub に送信されたメッセージがシリアル モニターに表示されます。
* MXChip IoT DevKit の LED が点滅している。

![シリアル モニターの出力](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Azure IoT Hub によって受信されたテレメトリを確認する

IoT Hub の device-to-cloud (D2C) メッセージは、[Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) を使用して監視できます。

1. Visual Studio Code で、拡張機能マーケットプレースから **Azure IoT Toolkit** を探してインストールします。

1. [Azure portal](https://portal.azure.com/) にログインし、作成した IoT Hub を検索します。
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. **[共有アクセス ポリシー]** ウィンドウで、**iothubowner ポリシー**をクリックし、IoT Hub の接続文字列を書き留めます。
    ![Azure IoT Hub 接続文字列](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. Visual Studio Code の左下隅にある **[AZURE IOT HUB DEVICES]\(Azure IoT Hub デバイス\)** を展開し、**[Set IoT Hub Connection String]\(IoT Hub の接続文字列の設定\)** をクリックします。
    ![Azure IoT Hub 接続文字列の設定](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-conn-string.png)

1. コンテキスト メニューの **[IoT: Start monitoring D2C message]\(IoT: D2C メッセージの監視を開始する\)** をクリックします。

1. **[出力]** ウィンドウで、IoT Hub への受信 D2C メッセージを確認できます。
    ![D2C メッセージ](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-console.png)

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) で解決方法を確認するか、[Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) からお問い合わせください。 このページにコメントを残してフィードバックをお寄せいただくこともできます。

## <a name="next-steps"></a>次の手順

MXChip IoT DevKit を IoT ハブに接続し、キャプチャしたセンサー データを IoT ハブに送信しました。

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
