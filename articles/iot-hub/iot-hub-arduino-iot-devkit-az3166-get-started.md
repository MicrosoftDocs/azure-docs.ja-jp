---
title: 'IoT DevKit をクラウドへ: IoT DevKit AZ3166 を Azure IoT Hub に接続する | Microsoft Docs'
description: このチュートリアルでは、IoT DevKit AZ3166 を設定して Azure IoT Hub に接続し、Azure クラウド プラットフォームにデータを送信する方法について説明します。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 4017a3be5e03e1a9b85b4002b8069a1adc3a6b83
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551577"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 を Azure IoT Hub に接続する

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を使用すると、Microsoft Azure サービスを活用したモノのインターネット (IoT) ソリューションを開発し、プロトタイプを作成できます。 これには、豊富な周辺機器やセンサーを備えた Arduino 互換ボード、オープン ソース ボード パッケージ、および充実した[サンプル ギャラリー](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が含まれます。

## <a name="what-you-learn"></a>学習内容

* IoT ハブを作成し、MXChip IoT DevKit のデバイスを登録する方法。
* IoT DevKit を Wi-Fi に接続して、IoT ハブの接続文字列を構成する方法。
* IoT ハブに DevKit センサーのテレメトリ データを送信する方法。
* 開発環境を準備し、IoT DevKit 用のアプリケーションを開発する方法。

DevKit をお持ちでない場合は、 [DevKit シミュレーター](https://azure-samples.github.io/iot-devkit-web-simulator/)を使用するか、[DevKit を購入](https://aka.ms/iot-devkit-purchase)してください。

すべての DevKit チュートリアルのソース コードは、[IoTDevEnvExamples](https://github.com/IoTDevEnvExamples) リポジトリにあります。

## <a name="what-you-need"></a>必要なもの

* MXChip IoT DevKit ボードとマイクロ USB ケーブル。 [こちら](https://aka.ms/iot-devkit-purchase)から今すぐ入手できます。
* Windows 10、macOS 10.10 以上、または Ubuntu 18.04 以上が実行されているコンピューター。
* 有効な Azure サブスクリプション [30 日間の無料試用版 Microsoft Azure アカウント](https://azureinfo.microsoft.com/us-freetrial.html)をアクティブにします。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>クイック スタート:DevKit から IoT ハブにテレメトリを送信する

クイック スタートでは、IoT ハブにテレメトリを送信するために、プリコンパイル済みの DevKit ファームウェアを使います。 それを実行する前に、IoT ハブを作成し、デバイスをハブに登録します。

### <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyNodeDevice**: 登録するデバイスの名前。 示されているように、**MyNodeDevice** を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用し、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新する必要があります。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```
   > [!NOTE]
   > `device-identity` の実行中にエラーが発生した場合、詳細を得るには [Azure IOT Extension for Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) インストールします。
  
1. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

### <a name="send-devkit-telemetry"></a>DevKit のテレメトリを送信する

DevKit は、IoT ハブ上のデバイス固有のエンドポイントに接続し、温度と湿度のテレメトリを送信します。

1. IoT DevKit 用の最新バージョンの [GetStarted ファームウェア](https://aka.ms/devkit/prod/getstarted/latest)をダウンロードします。

1. IoT DevKit が USB 経由でコンピューターに接続していることを確認します。 エクスプローラーを開くと、**AZ3166** という名前の USB 大容量記憶装置があります。
    ![エクスプローラーを開く](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. ダウンロードしたファームウェアをドラッグして大容量記憶装置にドロップすると、自動的にフラッシュされます。
    ![ファームウェアをコピーする](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. DevKit で **B** キーを押しながら、**リセット** ボタンを押して離した後、**B** キーを離します。DevKit が AP モードになります。 確認するには、DevKit のサービス セット識別子 (SSID) と、構成ポータルの IP アドレスが画面に表示されます。
    ![リセット ボタン、B キー、および SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![AP モードの設定](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. 別の Wi-Fi 対応デバイス (コンピューターまたは携帯電話) の Web ブラウザーを使って、前のステップで表示されている IoT DevKit SSID に接続します。 パスワードの入力を求められたら、空白のままにします。
    ![SSID に接続する](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. ブラウザーで **192.168.0.1** を開きます。 IoT DevKit を接続する Wi-Fi を選択し、Wi-Fi のパスワードを入力して、前に記録しておいたデバイスの接続文字列を貼り付けます。 その後、[保存] をクリックします。
    ![構成 UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit では、2.4 GHz のネットワークのみがサポートされます。 詳細については、[FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) を参照してください。

1. 結果ページを表示すると、Wi-Fi の情報とデバイスの接続文字列が IoT DevKit に保存されます。
    ![構成の結果](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Wi-Fi の構成後は、デバイスを取り外しても、その接続の資格情報がデバイス上で保持されます。

1. IoT DevKit は数秒で再起動します。 DevKit の画面に、DevKit の IP アドレスに続いて、温度と湿度の値および Azure IoT Hub に送信されたメッセージ数を含むテレメトリ データが表示されます。
    ![Wi-Fi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![データの送信](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Azure に送信されたテレメトリ データを確認するには、Azure Cloud Shell で次のコマンドを実行します。
    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>開発環境の準備

次の手順に従って、DevKit の開発環境を準備します。

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Visual Studio Code と Azure IoT Tools 拡張機能パッケージをインストールする

1. [Arduino IDE](https://www.arduino.cc/en/Main/Software) をインストールします。 Arduino コードをコンパイルしたりアップロードしたりするために必要なツールチェーンが備わっています。
    * **Windows**:Windows インストーラー バージョンを使用してください。 App Store からはインストールしないでください。
    * **macOS**:抽出した **Arduino.app** を `/Applications` フォルダーにドラッグ アンド ドロップします。
    * **Ubuntu**:`$HOME/Downloads/arduino-1.8.8` などのフォルダーにファイルを解凍します

2. 強力な IntelliSense、コード補完、デバッグ サポート、および豊富な拡張機能などを備えたクロス プラットフォームのソース コード エディターである [Visual Studio Code](https://code.visualstudio.com/) を、マーケットプレースからインストールできます。

3. VS Code を起動し、拡張機能マーケットプレースから **Arduino** を探してインストールします。 この拡張機能は、Arduino プラットフォームでの開発用に強化されたエクスペリエンスを提供します。
    ![Arduino のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 拡張機能マーケットプレースから [Azure IoT Tools](https://aka.ms/azure-iot-tools) を探してインストールします。
    ![Azure IoT Tools のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    または、次の直接リンクを使用します。
    > [!div class="nextstepaction"]
    > [Azure IoT Tools 拡張パックのインストール](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Azure IoT Tools 拡張パックには、[Azure IoT Device Workbench](https://aka.ms/iot-workbench) が含まれています。このワークベンチは、さまざまな IoT DevKit デバイスの開発とデバッグに使用されます。 [Azure IoT Hub Toolkit](https://aka.ms/iot-toolkit) は Azure IoT Tools 拡張パックにも含まれていますが、Azure IoT Hubs を管理し、それとやりとりする目的で使用されます。

5. Arduino 設定で VS Code を構成します。

    Visual Studio Code で、 **[ファイル] > [優先設定] > [設定]** の順にクリックします。 次に、 **[...]** をクリックし、**settings.json を開きます**。
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

* **Windows**:[STMicroelectronics Web サイト](https://www.st.com/en/development-tools/stsw-link009.html)または[直接リンク](https://aka.ms/stlink-v2-windows)から USB ドライバーをダウンロードしてインストールします。
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

これで必要な開発環境の準備と構成がすべて完了しました。 実行した GetStarted サンプルをビルドしましょう。

## <a name="build-your-first-project"></a>初めてのプロジェクトを作成する

### <a name="open-sample-code-from-sample-gallery"></a>サンプル ギャラリーからサンプル コードを開く

IoT DevKit には豊富なサンプルのギャラリーが含まれており、それを使って、さまざまな Azure サービスへの DevKit の接続を学習できます。

1. IoT DevKit がお使いのコンピューターに接続されて**いない**ことを確認します。 まず VS Code を起動し、DevKit をコンピューターに接続します。

1. `F1` をクリックしてコマンド パレットを開き、 **[Azure IoT Device Workbench:Open Examples...]\(Azure IoT Device Workbench: 例を開く...\)** を入力して選択します。次に、 **[IoT DevKit]** をボードとして選択します。

1. [IoT Workbench Examples]\(IoT Workbench の例\) ページで **[Get Started]\(はじめに\)** を探して **[Open Sample]\(サンプルを開く\)** をクリックします。 次に、サンプル コードをダウンロードするための既定のパスを選択します。
    ![サンプルを開く](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Azure IoT Hub とデバイスをプロビジョニングする

Azure portal から Azure IoT Hub とデバイスをプロビジョニングする代わりに、開発環境を離れることなく VS Code 内でそれを行うことができます。

1. 新しく開いたプロジェクト ウィンドウで、`F1` をクリックしてコマンド パレットを開き、 **[Azure IoT Device Workbench:Azure サービスのプロビジョニング...]** を入力して選択します。ステップ バイ ステップ ガイドに従って、Azure IoT Hub のプロビジョニングと IoT Hub デバイスの作成を完了します。
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

1. `F1` をクリックしてコマンド パレットを開き、 **[Azure IoT Device Workbench:Configure Device Settings...]\(Azure IoT Workbench: デバイス設定の構成)** を入力して選択した後、 **[Config Device Connection String]\(デバイス接続文字列の構成) > [Select IoT Hub Device Connection String]\(IoT Hub デバイス接続文字列の選択)** の順に選択します。

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

1. VS Code で、`F1` をクリックし、 **[Azure IoT Hub:IoT Hub 接続文字列を設定する]** を入力して選択します。 その中に接続文字列をコピーします。
    ![Azure IoT Hub 接続文字列の設定](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 右側の **[AZURE IOT HUB DEVICES]\(Azure IoT Hub デバイス)** ウィンドウを展開し、作成したデバイス名を右クリックして、 **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視の開始\)** を選択します。
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
