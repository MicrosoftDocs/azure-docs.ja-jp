---
title: "IoT DevKit をクラウドへ - IoT DevKit AZ3166 を Azure IoT Hub に接続する | Microsoft Docs"
description: "このチュートリアルでは、IoT DevKit AZ3166 を設定して Azure IoT Hub に接続し、Azure クラウド プラットフォームにデータを送信する方法について説明します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 122fac584ac5b954ef7b33a3121bee2c502ebc63
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>IoT DevKit AZ3166 をクラウドの Azure IoT Hub に接続する

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を使用すると、Microsoft Azure サービスを利用するモノのインターネット (IoT) ソリューションを開発し、プロトタイプを作成できます。 これには、豊富な周辺機器やセンサーを備えた Arduino 対応ボード、オープン ソース ボード パッケージ、および増え続ける[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が含まれます。

## <a name="what-you-do"></a>作業内容
作成する Azure IoT Hub に [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を接続し、センサーから温度と湿度のデータを収集して、そのデータを IoT Hub に送信します。

DevKit をお持ちでない場合は、 [こちら](https://aka.ms/iot-devkit-purchase)から、新しいキットを入手してください。

## <a name="what-you-learn"></a>学習内容

* IoT DevKit をワイヤレス アクセス ポイントに接続し、開発環境を準備する方法。
* IoT Hub を作成し、MXChip IoT DevKit のデバイスを登録する方法。
* MXChip IoT DevKit のサンプル アプリケーションを実行して、センサー データを収集する方法。
* センサー データを IoT Hub に送信する方法。

## <a name="what-you-need"></a>必要なもの

* MXChip IoT DevKit ボードとマイクロ USB ケーブル。 [今すぐ入手する](https://aka.ms/iot-devkit-purchase)
* Windows 10 または macOS 10.10 以上を実行しているコンピューター。
* 有効な Azure サブスクリプション
  * [30 日間の無料試用版 Microsoft Azure アカウント](https://azureinfo.microsoft.com/us-freetrial.html)をアクティブにする

## <a name="prepare-your-hardware"></a>ハードウェアの準備

ハードウェアをコンピューターに接続します。

### <a name="hardware-you-need"></a>必要なハードウェア

* DevKit ボード
* マイクロ USB ケーブル

![getting-started-hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

### <a name="connect-devkit-to-your-computer"></a>コンピューターへのDevKit の接続

1. USB の端を PC に接続します
2. マイクロ USB の端を DevKit に接続します
3. 電源の横にある緑色の LED で接続されていることを確認します

![getting-started-connect](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wifi"></a>WiFi の構成

IoT プロジェクトではインターネット接続を使用します。 次の手順を使用して、DevKit が WiFi に接続できるように構成します。

### <a name="enter-ap-mode"></a>AP モードの開始

B ボタンを押しながら、リセット ボタンを押して離し、その後、B ボタンを離します。DevKit が AP モードに切り替わり、WiFi を構成できるようになります。 DevKit のサービス セット識別子 (SSID) と、構成ポータルの IP アドレスが画面に表示されます。

![getting-started-wifi-ap](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>DevKit AP への接続

ここで、別の WiFi 対応デバイス (PC または携帯電話) を使用して、DevKit SSID (上記のスクリーンショットで強調表示されています) に接続します。パスワードは空のままにしてください。

![getting-started-ssid](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wifi-for-devkit"></a>DevKit の WiFi の構成

PC または携帯電話のブラウザーで、DevKit の画面に表示されている IP アドレスを開いて、DevKit を接続する WiFi ネットワークを選択し、パスワードを入力します。 **[接続]** をクリックして完了します。

![getting-started-wifi-portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

接続に成功すると、DevKit は数秒後に再起動されます。 成功した場合、WiFi 名と IP アドレスが画面に表示されます。

![getting-started-wifi-ip](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
写真に表示されている IP アドレスは、DevKit 画面に表示されている、実際に割り当てられた IP と一致しない場合があります。 WiFi は DHCP を使用して IP を動的に割り当てるため、これは正常な動作です。

WiFi の構成後は、デバイスを取り外しても、その接続の資格情報がデバイス上で保持されます。 たとえば、自宅で WiFi を構成した DevKit をオフィスに持っていった場合は、(「**AP モードの開始**」以降の手順を実行して) AP モードを再構成し、オフィスの WiFi に DevKit を接続する必要があります。 

## <a name="start-using-devkit"></a>DevKit の使用開始

DevKit で実行されている既定のアプリによって、ファームウェアが最新バージョンであるかどうかが確認され、センサー診断データの一部が表示されます。

### <a name="upgrade-to-the-latest-firmware"></a>最新ファームウェアへのアップグレード

アップグレードが必要な場合は、ファームウェアの現在のバージョンと最新バージョンが画面に表示されます。 [ファームウェアのアップグレード](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) ガイドに従って、アップグレードしてください。

![getting-started-firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
この操作を行う必要があるのは 1 回です。DevKit での開発を開始して、アプリをアップロードすると、そのアプリに対して最新のファームウェアが適用されます。

### <a name="test-various-sensors"></a>さまざまなセンサーのテスト

B ボタンを押してセンサーをテストします。センサーごとにテストを実行するには、B ボタンを押して離す操作を繰り返します。

![getting-started-sensors](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-development-environment"></a>開発環境の準備

次は、開発環境、つまり魅力的な IoT アプリケーションを構築するためのツールおよびパッケージを設定します。 オペレーティング システムに応じて、Windows バージョンまたは macOS バージョンを選択できます。


### <a name="windows"></a>Windows

インストール パッケージを使用して、開発環境を準備することをお勧めします。 問題が発生した場合は、[手動の手順](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/)に従って準備することができます。

#### <a name="download-latest-package"></a>最新パッケージのダウンロード

ダウンロードする `.zip` ファイルには、DevKit 開発に必要なツールとパッケージがすべて含まれています。

> [!div class="button"]
[ダウンロード](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


> [!NOTE] 
> `.zip` ファイルに含まれるツールとパッケージは次のとおりです。 既にインストール済みのコンポーネントがある場合、スクリプトによってそのコンポーネントが検出され、スキップされます。
> * Node.js と Yarn: セットアップ スクリプトおよび自動化されたタスクのランタイム
> * [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): Azure リソースを管理するためのクロスプラットフォーム コマンドライン エクスペリエンス。MSI には、依存 Python と pip が含まれています。
> * [Visual Studio Code](https://code.visualstudio.com/): DevKit 開発のための軽量なコード エディター
> * [Arduino の Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): VS Code での Arduino 開発を可能にします
> * [Arduino IDE](https://www.arduino.cc/en/Main/Software): Arduino の拡張機能は、このツールに依存します
> * DevKit ボード パッケージ: DevKit 用のツール チェーン、ライブラリ、およびプロジェクト
> * ST-Link ユーティリティ: 必須のユーティリティとドライバー

#### <a name="run-installation-script"></a>インストール スクリプトの実行

Windows エクスプローラーで、`.zip` を見つけて展開し、`install.cmd` を検索します。このファイルを右クリックし、**[管理者として実行]** を選択して開始します。

![getting-started-run-admin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

インストール中、各ツールまたはパッケージのインストールの進行状況が表示されます。

![getting-started-install](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="confirm-to-install-drivers"></a>ドライバーを確認してインストール

Arduino の VS Code 拡張機能では、Arduino IDE が使用されます。 Arduino IDE を初めてインストールする場合は、関連するドライバーをインストールするよう求められます。

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

インターネットの速度にもよりますが、インストールが完了するまでに約 10 分かかります。 インストールが完了したら、デスクトップに Visual Studio Code と Arduino IDE のショートカットが表示されます。

> [!NOTE] 
VS Code を起動するときに、Arduino IDE または関連するボード パッケージが見つからないことを示すエラーが表示される場合があります。 これを解決するには、VS Code を閉じてから、Arduino IDE を起動します。これにより、VS Code が Arduino IDE パスを正しく見つけることができます。


### <a name="macos-preview"></a>macOS (プレビュー)

次の手順に従って、macOS で開発環境を準備します。

#### <a name="install-azure-cli-20"></a>Azure CLI 2.0 のインストール

[公式ガイド](https://docs.microsoft.com//cli/azure/install-azure-cli)に従って、Azure CLI 2.0 をインストールします。

1 つの `curl` コマンドで Azure CLI 2.0 をインストールします。

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

また、コマンド シェルを再起動して、変更を有効にします。

```bash
exec -l $SHELL
```

#### <a name="install-arduino-ide"></a>Arduino IDE のインストール

Visual Studio Code Arduino 拡張機能では、Arduino IDE が使用されます。 [Arduino IDE for macOS](https://www.arduino.cc/en/Main/Software) をダウンロードして、インストールします。

#### <a name="install-visual-studio-code"></a>Visual Studio Code のインストール

[Visual Studio Code for macOS](https://code.visualstudio.com/) をダウンロードして、インストールします。 これは、DevKit IoT アプリケーションを構築するための主要な開発ツールになります。

####  <a name="download-latest-package"></a>最新パッケージのダウンロード

1. Node.js をインストールします。 インストールには、一般的な macOS パッケージ マネージャー [Homebrew](https://brew.sh/) または[事前構築済みインストーラー](https://nodejs.org/en/download/)を使用できます。

2. VS Code での DevKit 開発に必要なタスク スクリプトを含む `.zip` ファイルをダウンロードします。

   > [!div class="button"]
   [ダウンロード](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

   `.zip` を見つけて、展開します。 次に、**ターミナル** アプリを起動し、次のコマンドを実行して構成します。

   展開されたフォルダーを、macOS ユーザー フォルダーに移動します。
   ```bash
   mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
   ```
  
   `npm` パッケージをインストールします。
   ```
   npm install
   ```

#### <a name="install-vs-code-extension-for-arduino"></a>Arduino の VS Code 拡張機能のインストール

Visual Studio Code を使用すると、Marketplace 拡張機能を直接ツールにインストールできます。左側のメニュー ウィンドウで拡張機能アイコンをクリックし、`Arduino` を検索して、インストールしてください。

![installation-extensions](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-devkit-board-package"></a>DevKit ボード パッケージのインストール

Visual Studio Code でボード マネージャーを使用して、DevKit ボードを追加する必要があります。

1. `Cmd+Shift+P` を使用してコマンド パレットを起動し、「**Arduino**」と入力します。次に、**[Arduino: Board Manager]\(Arduino: ボード マネージャー\)** を見つけて選択します。

2. 右下の **[Additional URLs]\(追加 URL\)** をクリックします。
   ![installation-additional-urls](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. `settings.json` ファイルで、`USER SETTINGS` ウィンドウの下部に次の行を追加して保存します。
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![installation-settings-json](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. ボード マネージャーで、"az3166" を検索し、最新バージョンをインストールします。
   ![installation-az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

これで、macOS に必要なツールとパッケージがすべてインストールされました。


## <a name="open-project-folder"></a>プロジェクト フォルダーを開く

### <a name="launch-vs-code"></a>VS Code を起動します。

DevKit が接続されていないことを確認します。 まず VS Code を起動し、DevKit をコンピューターに接続します。 VS Code によって DevKit が自動的に検索され、概要ページがポップアップ表示されます。

![mini-solution-vscode](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
VS Code を起動するときに、Arduino IDE または関連するボード パッケージが見つからないことを示すエラーが表示される場合があります。 これを解決するには、VS Code を閉じてから、Arduino IDE を再度起動します。これにより、VS Code が Arduino IDE パスを正しく見つけることができます。

### <a name="open-arduino-examples-folder"></a>[Arduino Examples]\(Arduino の例\) フォルダーを開く

**[Arduino Examples]\(Arduino の例\)** タブに切り替えて、`Examples for MXCHIP AZ3166 > AzureIoT` に移動し、`GetStarted` をクリックします。

![mini-solution-examples](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

ウィンドウを閉じてから再度読み込む場合は、`Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) を使用してコマンド パレットを起動し、「**Arduino**」と入力して、**[Arduino: Examples]\(Arduino: 例\)** を見つけて選択します。

## <a name="provision-azure-services"></a>Azure サービスのプロビジョニング

ソリューション ウィンドウで、「task cloud-provision」と入力し、`Ctrl+P` (macOS: `Cmd+P`) を使用してタスクを実行します。

VS Code ターミナルでは、対話型コマンド ラインを使用して、必要な Azure サービスをプロビジョニングできます。

![mini-solution-cloud-provision](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-arduino-sketch"></a>Arduino スケッチのビルドとアップロード

### <a name="install-required-library"></a>必要なライブラリのインストール

1. `F1` または `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) を押してコマンド パレットを起動し、「**Arduino**」と入力して、**[Arduino: Library Manager]\(Arduino: ライブラリ マネージャー\)** を見つけて選択します。

2. `ArduinoJson` ライブラリを検索し、**[インストール]** をクリックします

### <a name="build-and-upload-the-device-code"></a>デバイス コードのビルドとアップロード

`Ctrl+P` (macOS: `Cmd+P`) を使用して、"task device-upload" を実行します。 ターミナルによって、構成モードを開始するよう求められます。 これを行うには、A ボタンを押しながら、リセット ボタンを押して離します。 画面に [構成] が表示されます。 これは、"task cloud-provision" 手順から取得する接続文字列を設定するためです。

その後、Arduino スケッチの確認とアップロードが開始されます。

![mini-solution-device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit が再起動され、コードの実行が開始されます。

## <a name="test-the-project"></a>プロジェクトのテスト

VS Code で、ステータス バーの電源プラグ アイコンをクリックして、シリアル モニターを開きます。

次の結果が表示された場合、サンプル アプリケーションは正常に実行されてます。

* シリアル モニターに、次のスクリーンショットの内容と同じ情報が表示されている。
* MXChip IoT DevKit の LED が点滅している。

![VS Code での最終出力](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) を参照するか、以下のチャネルからお問い合わせください。

## <a name="next-steps"></a>次のステップ

MXChip IoT DevKit を IoT Hub に接続し、キャプチャしたセンサー データを IoT Hub に送信できるようになりました。

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

- [iothub-explorer を使用したクラウド デバイス メッセージングの管理](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Azure データ ストレージへの IoT Hub メッセージの保存](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Power BI を使用して Azure IoT Hub からのリアルタイムのセンサー データを視覚化](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Azure Web Apps を使用して Azure IoT Hub からのリアルタイムのセンサー データを視覚化](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Azure Machine Learning で IoT ハブからのセンサー データを使用して天気予報を行う](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [iothub-explorer を使用したデバイス管理](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Logic Apps を使用したリモートの監視と通知](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
