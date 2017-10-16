---
title: "IoT DevKit をクラウドへ: IoT DevKit AZ3166 を Azure IoT Hub に接続する | Microsoft Docs"
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
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 253a2d34ea5b3f4f8253900be27c54fe5ca1dacf
ms.contentlocale: ja-jp
ms.lasthandoff: 09/07/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>IoT DevKit AZ3166 をクラウドの Azure IoT Hub に接続する

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を使用すると、Microsoft Azure サービスを活用したモノのインターネット (IoT) ソリューションを開発し、プロトタイプを作成できます。 これには、豊富な周辺機器やセンサーを備えた Arduino 互換ボード、オープン ソース ボード パッケージ、および増え続ける[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が含まれます。

## <a name="what-you-do"></a>作業内容
作成する Azure IoT ハブに [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を接続し、センサーから温度と湿度のデータを収集して、そのデータを IoT ハブに送信します。

DevKit をお持ちでない場合は、 [1 つ取得](https://aka.ms/iot-devkit-purchase)します。

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

ハードウェアをコンピューターに接続します。

次のハードウェアを必要とします。

* DevKit ボード
* マイクロ USB ケーブル

![必要なハードウェア](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit をコンピューターに接続するには、次の操作を実行します。

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

コンピューターまたは携帯電話のブラウザーで、DevKit の画面に表示されている IP アドレスを開いて、DevKit を接続する Wi-Fi ネットワークを選択し、パスワードを入力します。 **[接続]**を選択します。

![[パスワード] ボックスと [接続] ボタン](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

接続に成功すると、DevKit は数秒後に再起動されます。 Wi-Fi 名と IP アドレスが画面に表示されます。

![Wi-Fi 名と IP アドレス](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> 写真に表示されている IP アドレスは、DevKit 画面に表示されている、実際に割り当てられた IP アドレスと一致しない場合があります。 Wi-Fi は DHCP を使用して IP を動的に割り当てるため、これは正常な動作です。

Wi-Fi の構成後は、デバイスを取り外しても、その接続の資格情報がデバイス上で保持されます。 たとえば、自宅で Wi-Fi を構成した DevKit をオフィスに持っていった場合は、(「AP モードの開始」セクション以降の手順を実行して) AP モードを再構成し、オフィスの Wi-Fi に DevKit を接続する必要があります。 

## <a name="start-using-the-devkit"></a>DevKit の使用を開始する

DevKit で実行されている既定のアプリによって、ファームウェアが最新バージョンであるかどうかが確認され、センサー診断データの一部が表示されます。

### <a name="upgrade-to-the-latest-firmware"></a>最新ファームウェアへのアップグレード

ファームウェアのアップグレードが必要な場合は、現在のファームウェア バージョンと最新のファームウェア バージョンが画面に表示されます。 アップグレードするには、[ファームウェアのアップグレード](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) ガイドに従ってください。

![現在および最新のファームウェア バージョンの表示](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> この操作を行う必要があるのは 1 回です。 DevKit での開発を開始して、アプリをアップロードすると、そのアプリに対して最新のファームウェアが適用されます。

### <a name="test-various-sensors"></a>さまざまなセンサーのテスト

B ボタンを押してセンサーをテストします。 B ボタンを押して離す操作を繰り返して、センサーごとのテストを実行します。

![B ボタンとセンサーの表示](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>開発環境の準備

次は、開発環境、つまり魅力的な IoT アプリケーションを構築するためのツールおよびパッケージを設定します。 ご利用のオペレーティング システムに応じて、Windows バージョンまたは macOS バージョンを選択できます。


### <a name="windows"></a>Windows

インストール パッケージを使用して、開発環境を準備することをお勧めします。 問題が発生した場合は、[手動の手順](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/)に従って準備することができます。

#### <a name="download-the-latest-package"></a>最新パッケージのダウンロード

ダウンロードする.zip ファイルには、DevKit 開発に必要なツールとパッケージがすべて含まれています。

> [!div class="button"]
[ダウンロード](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


.zip ファイルに含まれるツールとパッケージは次のとおりです。 既にインストール済みのコンポーネントがある場合、スクリプトによってそのコンポーネントが検出され、スキップされます。

* Node.js と Yarn: セットアップ スクリプトおよび自動化されたタスクのランタイム。
* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): Azure リソースを管理するためのクロスプラットフォーム コマンドライン エクスペリエンス。 MSI には、依存する Python と pip が含まれています。
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): DevKit 開発のための軽量なコード エディター。
* [Arduino の Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): Visual Studio Code での Arduino 開発を可能にする拡張機能。
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): Arduino の拡張機能が依存するツール。
* DevKit ボード パッケージ: DevKit 用のツール チェーン、ライブラリ、およびプロジェクト。
* ST-Link ユーティリティ: 必須のツールとドライバー。

#### <a name="run-the-installation-script"></a>インストール スクリプトの実行

エクスプローラーで .zip ファイルを見つけて展開します。 install.cmd を見つけて右クリックし、**[管理者として実行]** を選択します。

![エクスプローラー](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

インストール中、各ツールまたはパッケージのインストールの進行状況が表示されます。

![Installation progress](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="install-drivers"></a>ドライバーのインストール

Arduino の VS Code 拡張機能では、Arduino IDE が使用されます。 Arduino IDE を初めてインストールする場合は、関連するドライバーをインストールするように求められます。

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

インターネットの速度にもよりますが、インストールは約 10 分かかります。 インストールが完了したら、デスクトップに Visual Studio Code と Arduino IDE のショートカットが表示されます。

> [!NOTE] 
> VS Code を起動するときに、Arduino IDE または関連するボード パッケージが見つからないことを示すエラーが表示される場合があります。 VS Code を閉じて、Arduino IDE を再起動します。 VS Code によって、Arduino IDE のパスが正しく検索されます。


### <a name="macos-preview"></a>macOS (プレビュー)

次の手順に従って、macOS で開発環境を準備します。

#### <a name="install-azure-cli-20"></a>Azure CLI 2.0 のインストール

1. 1 つの `curl` コマンドを使用して Azure CLI 2.0 をインストールします。

   ```bash
   curl -L https://aka.ms/InstallAzureCli | bash
   ```

2. コマンド シェルを再起動して、変更を有効にします。

   ```bash
   exec -l $SHELL
   ```

Azure CLI 2.0 のインストールに関する詳細については、[公式ガイド](https://docs.microsoft.com//cli/azure/install-azure-cli)を参照してください。

#### <a name="install-the-arduino-ide"></a>Arduino IDE のインストール

Visual Studio Code Arduino 拡張機能では、Arduino IDE が使用されます。 [Arduino IDE for macOS](https://www.arduino.cc/en/Main/Software) をダウンロードして、インストールします。

#### <a name="install-visual-studio-code"></a>Visual Studio Code のインストール

[Visual Studio Code for macOS](https://code.visualstudio.com/) をダウンロードして、インストールします。 これは、DevKit IoT アプリケーションを構築するための主要な開発ツールです。

####  <a name="download-the-latest-package"></a>最新パッケージのダウンロード

1. Node.js をインストールします。 インストールには、一般的な macOS パッケージ マネージャー [Homebrew](https://brew.sh/) または[事前構築済みインストーラー](https://nodejs.org/en/download/)を使用できます。

2. VS Code での DevKit 開発に必要なタスク スクリプトを含む .zip ファイルをダウンロードします。

   > [!div class="button"]
   [ダウンロード](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

3. .zip ファイルを見つけて、展開します。 次に、**ターミナル** アプリを起動し、次のコマンドを実行します。

   a. 展開されたフォルダーを、macOS ユーザー フォルダーに移動します。
      ```bash
      mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
      ```
  
   b. npm パッケージをインストールします。
      ```
      npm install
      ```

#### <a name="install-the-vs-code-extension-for-arduino"></a>Arduino の VS Code 拡張機能のインストール

Visual Studio Code で直接 Azure Marketplace の拡張機能をインストールすることができます。 左側のウィンドウで拡張機能アイコンを選択し、**Arduino** を検索して、**[インストール]** を選択します。

![Arduino の拡張機能の検索](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-the-devkit-board-package"></a>DevKit ボード パッケージのインストール

Visual Studio Code でボード マネージャーを使用して、DevKit ボードを追加します。

1. Cmd + Shift + P キーを使用してコマンド パレットを開き、「**Arduino**」と入力します。次に、**[Arduino: Board Manager]\(Arduino: ボード マネージャー\)** を見つけて選択します。

2. 右下の **[Additional URLs]\(追加 URL\)** を選択します。
   ![追加 URL のリンク](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. settings.json ファイルで、**[USER SETTINGS]\(ユーザー設定\)** ウィンドウの末尾に次の 1 行を追加して保存します。
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![[USER SETTINGS]\(ユーザー設定\) ウィンドウに追加されるコード](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. ボード マネージャーで、**az3166** を検索し、最新バージョンをインストールします。
   ![az3166 のインストール](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

これで、macOS に必要なツールとパッケージがすべてインストールされました。


## <a name="open-the-project-folder"></a>プロジェクト フォルダーを開く

### <a name="start-vs-code"></a>VS Code の起動

DevKit が接続されていないことを確認します。 まず VS Code を起動し、DevKit をコンピューターに接続します。 VS Code にって DevKit が自動的に検出され、概要ページが開きます。

![[概要] ページ](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
> VS Code を起動するときに、Arduino IDE または関連するボード パッケージが見つからないことを示すエラーが表示される場合があります。 VS Code を閉じて、Arduino IDE を再起動します。 VS Code によって、Arduino IDE のパスが正しく検索されます。


### <a name="open-the-arduino-examples-folder"></a>[Arduino Examples]\(Arduino の例\) フォルダーを開く

**[Arduino Examples]\(Arduino の例\)** タブで、**[Examples for MXCHIP AZ3166]** > **[AzureIoT]** に移動し、**[GetStarted]** を選択します。

![[Arduino Examples]\(Arduino の例\) タブ](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

ウィンドウを偶然閉じた場合は、再度開くことができます。 Ctrl + Shift + P キー (macOS: Cmd + Shift + P キー) を使用してコマンド パレットを開き、「**Arduino**」と入力します。次に、**[Arduino: Examples]\(Arduino: 例\)** を見つけて選択します。

## <a name="provision-azure-services"></a>Azure サービスのプロビジョニング

ソリューション ウィンドウで、「`task cloud-provision`」を入力し、Ctrl キーを押しながら P キーを押して (macOS: Cmd キーを押しながら P キーを押して) タスクを実行します。

VS Code ターミナルでは、対話型コマンド ラインを使用して、必要な Azure サービスをプロビジョニングできます。

![対話型コマンド ライン](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Arduino スケッチのビルドとアップロード

### <a name="install-the-required-library"></a>必要なライブラリのインストール

1. F1 キーまたは Ctrl + Shift + P キー (macOS: Cmd + Shift + P キー) を選択してコマンド パレットを開き、「**Arduino**」と入力します。次に、**[Arduino: Library Manager]\(Arduino: ライブラリ マネージャー\)** を見つけて選択します。

2. **ArduinoJson** ライブラリを検索し、**[インストール]** を選択します。

### <a name="build-and-upload-the-device-code"></a>デバイス コードのビルドとアップロード

Ctrl キーを押しながら P キーを使用して (macOS: Cmd キーを押しながら P キーを使用して) `task device-upload` を実行します。 ターミナルによって、構成モードを開始するよう求められます。 これを行うには、A ボタンを押しながら、リセット ボタンを押して離します。 画面に**構成**が表示されます。 ここで、`task cloud-provision` ステップから取得した接続文字列を設定します。

その後、ターミナルで、Arduino スケッチの確認とアップロードが開始されます。

![Arduino スケッチの確認とアップロード](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit が再起動され、コードの実行が開始されます。

## <a name="test-the-project"></a>プロジェクトのテスト

VS Code で、ステータス バーの電源プラグ アイコンを選択して、シリアル モニターを開きます。

次の結果が表示された場合、サンプル アプリケーションは正常に実行されてます。

* シリアル モニターに、次のスクリーンショットの内容と同じ情報が表示されている。
* MXChip IoT DevKit の LED が点滅している。

![VS Code での最終出力](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) を検索できます。 このページにコメントを残してフィードバックをお寄せいただくこともできます。

## <a name="next-steps"></a>次のステップ

MXChip IoT DevKit を IoT ハブに接続し、キャプチャしたセンサー データを IoT ハブに送信しました。

引き続き Azure IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

- [iothub-explorer を使用したクラウド デバイス メッセージングの管理](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Azure データ ストレージへの IoT Hub メッセージの保存](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Power BI を使用して Azure IoT Hub からのリアルタイムのセンサー データを視覚化](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Web Apps を使用して Azure IoT Hub からのリアルタイムのセンサー データを視覚化](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Azure Machine Learning で IoT ハブからのセンサー データを使用して天気予報を行う](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [iothub-explorer を使用したデバイス管理](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Logic Apps を使用したリモートの監視と通知](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)

