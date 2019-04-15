---
title: 'IoT DevKit をクラウドへ: IoT DevKit AZ3166 を Azure IoT Hub に接続する | Microsoft Docs'
description: このチュートリアルでは、IoT DevKit AZ3166 を設定して Azure IoT Hub に接続し、Azure クラウド プラットフォームにデータを送信する方法について説明します。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/21/2019
ms.author: wesmc
ms.openlocfilehash: 0c6189dfd02a4bdd3662f4fa50dbb812fe995884
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438484"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 を Azure IoT Hub に接続する

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を使用すると、Microsoft Azure サービスを活用したモノのインターネット (IoT) ソリューションを開発し、プロトタイプを作成できます。 これには、豊富な周辺機器やセンサーを備えた Arduino 互換ボード、オープン ソース ボード パッケージ、および増え続ける[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が含まれます。

## <a name="what-you-do"></a>作業内容

この記事では、プラットフォームに依存しないソース コード エディターである [Visual Studio Code](https://code.visualstudio.com/) と [Azure IoT Tools](https://aka.ms/azure-iot-tools) 拡張パックを使用します。

作成した Azure IoT Hub に DevKit を接続します。 その後、センサーから温度と湿度のデータを収集して、そのデータを IoT ハブに送信します。

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

![AP モードの設定](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-ap-mode.gif)

### <a name="connect-to-devkit-ap"></a>DevKit AP への接続

ここで、別の Wi-Fi 対応デバイス (コンピューターまたは携帯電話) を使用して、DevKit SSID (前の図で強調表示されています) に接続します。 パスワードは空のままにしてください。

![ネットワーク情報と [接続] ボタン](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>DevKit 用に Wi-Fi を構成する

コンピューターまたは携帯電話のブラウザーで、DevKit の画面に表示されている IP アドレスを開いて、DevKit を接続する Wi-Fi ネットワークを選択し、パスワードを入力します。 **[接続]** を選択します。

![[パスワード] ボックスと [接続] ボタン](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

接続に成功すると、DevKit は数秒後に再起動されます。 Wi-Fi 名と IP アドレスが画面に表示されます。

![Wi-Fi 名と IP アドレス](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE]
> IoT DevKit を動作させるためには、2.4 GHz ネットワークが必要です。 IoT DevKit の Wi-Fi モジュールは、5 GHz ネットワークと互換性がありません。 詳細については、[FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) を参照してください。

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

### <a name="install-azure-iot-tools"></a>Azure IoT Tools のインストール

このセクションでは、[Arduino IDE](https://www.arduino.cc/en/Main/Software) とプラットフォームに依存しないソース コード エディターである [Visual Studio Code](https://code.visualstudio.com/) をインストールします。

Visual Studio Code 向けの [Azure IoT Tools](https://aka.ms/azure-iot-tools) 拡張パックもインストールします。 DevKit でのアプリケーション開発には、Visual Studio Code 用の [Azure IoT Tools](https://aka.ms/azure-iot-tools) 拡張機能パックを使用することをお勧めします。 Azure IoT Tools 拡張パックには、[Azure IoT Device Workbench](https://aka.ms/iot-workbench) が含まれています。このワークベンチは、さまざまな IoT DevKit デバイスの開発とデバッグに使用されます。 [Azure IoT Hub Toolkit](https://aka.ms/iot-toolkit) は Azure IoT Tools 拡張パックにも含まれていますが、Azure IoT Hubs を管理し、それとやりとりする目的で使用されます。

これらの機能の概要については、これらの[チャネル 9](https://channel9.msdn.com/) のビデオをご覧ください。
* [VS Code の新しい IoT Workbench 拡張機能の概要](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code)
* [VS Code 用 IoT Toolkit 拡張機能の新機能](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code)

次の手順に従って、DevKit の開発環境を準備します。

1. [Arduino IDE](https://www.arduino.cc/en/Main/Software) をインストールします。 Arduino コードをコンパイルしたりアップロードしたりするために必要なツールチェーンが備わっています。
    * **Windows**:Windows インストーラー バージョンを使用してください。 App Store からはインストールしないでください。
    * **macOS**:抽出した **Arduino.app** を `/Applications` フォルダーにドラッグ アンド ドロップします。
    * **Ubuntu**:`$HOME/Downloads/arduino-1.8.8` などのフォルダーにファイルを解凍します

2. IntelliSense コード補完やデバッグ機能など強力な開発者ツールを備えたクロス プラットフォームのソース コード エディターである [Visual Studio Code](https://code.visualstudio.com/) をインストールします。

3. VS Code を起動し、拡張機能マーケットプレースから **Arduino** を探してインストールします。 この拡張機能は、Arduino プラットフォームでの開発用に強化されたエクスペリエンスを提供します。
    ![Arduino のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 拡張機能マーケットプレースから **Azure IoT Tools** を探してインストールします。
    ![Azure IoT Tools のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    > [!div class="nextstepaction"]
    > [Azure IoT Tools 拡張パックのインストール](vscode:extension/vsciot-vscode.azure-iot-tools)

5. Arduino 設定で VS Code を構成します。

    Visual Studio Code で、**[ファイル] > [優先設定] > [設定]** の順にクリックします。 次に、**[...]** をクリックし、**settings.json を開きます**。
    ![Azure IoT Tools のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    プラットフォームに応じて、次の行を追加して Arduino を構成します。 

    * **Windows**:
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:
    
        下の **{username}** プレースホルダーはユーザー名に置き換えてください。

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. `F1` をクリックしてコマンド パレットを開き、**Arduino: ボード マネージャー** を入力して選択します。 **AZ3166** を検索し、最新バージョンをインストールします。
    ![DevKit SDK のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link のドライバーのインストール

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) は、IoT DevKit が開発マシンとの通信に使用する USB インターフェイスです。 コンパイル済みのデバイス コードを DevKit にフラッシュするには、これを Windows 上にインストールする必要があります。 OS ごとの手順に従って、対象デバイスに対するマシンのアクセスを許可してください。

* **Windows**:[直接リンク](https://aka.ms/stlink-v2-windows)の [STMicroelectronics Web サイト](https://www.st.com/en/development-tools/stsw-link009.html)から USB ドライバーをダウンロードしてインストールします。
* **macOS**:macOS の場合、ドライバーは必要ありません。
* **Ubuntu**:ターミナルからコマンドを実行し、グループの変更を反映するために一度サインアウトしてからサインインします。
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

### <a name="open-sample-code-from-sample-gallery"></a>サンプル ギャラリーからサンプル コードを開く

1. IoT DevKit がお使いのコンピューターに接続されて**いない**ことを確認します。 まず VS Code を起動し、DevKit をコンピューターに接続します。

1. `F1` をクリックしてコマンド パレットを開き、**[Azure IoT Device Workbench:Open Examples...]\(Azure IoT Device Workbench: 例を開く...\)** を入力して選択します。次に、**[IoT DevKit]** をボードとして選択します。

1. [IoT Workbench Examples]\(IoT Workbench の例\) ページで **[Get Started]\(はじめに\)** を探して **[Open Sample]\(サンプルを開く\)** をクリックします。 次に、サンプル コードをダウンロードするための既定のパスを選択します。
    ![サンプルを開く](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Azure IoT Hub とデバイスをプロビジョニングする

1. 新しく開いたプロジェクト ウィンドウで、`F1` をクリックしてコマンド パレットを開き、**[Azure IoT Device Workbench:Azure サービスのプロビジョニング...]** を入力して選択します。ステップ バイ ステップ ガイドに従って、Azure IoT Hub のプロビジョニングと IoT Hub デバイスの作成を完了します。
    ![プロビジョニング コマンド](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Azure にサインインしていない場合。 ポップアップ通知に従い、サインインします。

1. 使用するサブスクリプションを選択します。
    ![サブスクリプションの選択](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. [リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)を選択するか、新しく作成します。
    ![リソース グループの選択](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. 指定したリソース グループで、ガイドに従い、Azure IoT Hub を選択するか、新しく作成します。
    ![IoT Hub 選択手順](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT Hub の選択](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![選択された IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. 出力ウィンドウで Azure IoT Hub がプロビジョニングされたことを確認できます。![IoT Hub がプロビジョニングされた](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. プロビジョニングした Azure IoT Hub でデバイスを選択するか、新しく作成します。
    ![IoT デバイスの選択手順](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![プロビジョニングされた IoT デバイスを選択する](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. これで Azure IoT Hub がプロビジョニングされ、その中にデバイスが作成されました。 また、デバイス接続文字列が VS Code に保存されます。これは後で IoT DevKit を構成するために使用されます。
    ![プロビジョニング完了](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>デバイス コードを構成し、コンパイルする

1. 右下のステータス バーで、選択したボードとして **MXCHIP AZ3166** が表示されていること、またシリアル ポートと **STMicroelectronics** が使用されていることを確認します。
    ![ボードと COM を選択](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. `F1` をクリックしてコマンド パレットを開き、**[Azure IoT Device Workbench:Configure Device Settings...]\(Azure IoT Workbench: デバイス設定の構成)** を入力して選択した後、**[Config Device Connection String]\(デバイス接続文字列の構成) > [Select IoT Hub Device Connection String]\(IoT Hub デバイス接続文字列の選択)** の順に選択します。

1. DevKit で**ボタン A** を押しながら、**リセット** ボタンを押して離した後、**ボタン A** を離します。DevKit が構成モードに移行し、接続文字列が保存されます。
    ![接続文字列](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. `F1` を再度クリックして **[Azure IoT Device Workbench:Upload Device Code]\(デバイス コードのアップロード\)** を入力して選択します。 これにより、コンパイルと DevKit へのコードのアップロードが開始されます。
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

IoT Hub の device-to-cloud (D2C) メッセージは、[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して監視できます。

1. [Azure portal](https://portal.azure.com/) にサインインし、作成した IoT Hub を検索します。
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. **[共有アクセス ポリシー]** ウィンドウで、**iothubowner ポリシー**をクリックし、IoT Hub の接続文字列を書き留めます。
    ![Azure IoT Hub 接続文字列](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. VS Code で、`F1` をクリックし、**[Azure IoT Hub:IoT Hub 接続文字列を設定する]** を入力して選択します。 その中に接続文字列をコピーします。
    ![Azure IoT Hub 接続文字列の設定](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 右側の **[AZURE IOT HUB DEVICES]\(Azure IoT Hub デバイス)** ウィンドウを展開し、作成したデバイス名を右クリックして、**[Start Monitoring D2C Message]\(D2C メッセージの監視の開始)** を選択します。
    ![D2C メッセージの監視](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. **[出力]** ウィンドウで、IoT Hub への受信 D2C メッセージを確認できます。
    ![D2C メッセージ](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>コードの確認

`GetStarted.ino` は Arduino のメイン スケッチ ファイルです。

![D2C メッセージ](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

デバイス テレメトリが Azure IoT Hub に送信されるしくみを見るには、同じフォルダーで `utility.cpp` ファイルを開きます。 [API リファレンス](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/)を見ると、IoT DevKit でセンサーや周辺機器を使用する方法がわかります。

使用されている `DevKitMQTTClient` は **iothub_client** のラッパーであり、Azure IoT Hub とやりとりします。iothub_client の詳細は「[Microsoft Azure IoT SDKs and libraries for C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client)」 (C 向けの Microsoft Azure IoT SDK およびライブラリ) にあります。

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) で解決方法を確認するか、[Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) からお問い合わせください。 このページにコメントを残してフィードバックをお寄せいただくこともできます。

## <a name="next-steps"></a>次の手順

MXChip IoT DevKit を IoT ハブに接続し、キャプチャしたセンサー データを IoT ハブに送信しました。

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
