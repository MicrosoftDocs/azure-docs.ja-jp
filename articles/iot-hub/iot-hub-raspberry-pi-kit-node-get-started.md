---
title: Raspberry Pi から クラウドへ (Node.js) - Raspberry Pi の Azure IoT Hub への接続 | Microsoft Docs
description: このチュートリアルでは、Raspberry Pi を使用して Azure クラウド プラットフォームにデータを送信するために、Raspberry Pi を設定し、Azure IoT Hub に接続する方法について学習します。
author: wesmc7777
manager: philmea
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi でクラウドにデータを送信する raspberry pi からクラウドへ
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: e7863e4a0f4cab6f6328de19d1611710f9188de3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838762"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Raspberry Pi の Azure IoT Hub への接続 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

このチュートリアルでは、まず Raspbian を実行する Raspberry Pi の操作の基礎について説明します。 次に、[Azure IoT Hub](about-iot-hub.md) を使って、デバイスをクラウドにシームレスに接続する方法について説明します。 Windows 10 IoT Core サンプルについては、[Windows デベロッパー センター](https://www.windowsondevices.com/)を参照してください。

キットをお持ちでない場合は、 [Raspberry Pi オンライン シミュレーター](iot-hub-raspberry-pi-web-simulator-get-started.md)をお試しください。 または、[こちら](https://azure.microsoft.com/develop/iot/starter-kits)で新しいキットを購入してください。

## <a name="what-you-do"></a>作業内容

* IoT Hub を作成します。

* Pi のデバイスを IoT Hub に登録します。

* Raspberry Pi を設定します。

* Pi でサンプル アプリケーションを実行し、センサー データを IoT Hub に送信します。

## <a name="what-you-learn"></a>学習内容

* Azure IoT Hub を作成し、新しいデバイス接続文字列を取得する方法。

* Pi と BME280 センサーを接続する方法。

* Pi のサンプル アプリケーションを実行して、センサー データを収集する方法。

* センサー データを IoT Hub に送信する方法。

## <a name="what-you-need"></a>必要なもの

![必要なもの](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Raspberry Pi 2 ボードまたは Raspberry Pi 3 ボード。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* Pi に接続するモニター、USB キーボード、およびマウス。

* Mac か、Windows または Linux を実行している PC。

* インターネット接続。

* 16 GB 以上の microSD カード。

* USB-SD アダプターまたは microSD カード (microSD カードに オペレーティング システム イメージを書き込むため)。

* 5V 2A の AC アダプターと約 2 m の micro USB ケーブル。

省略可能な品目を次に示します。

* 温度、気圧、および湿度用の組み立て済み Adafruit BME280 センサー。

* ブレッドボード

* 6 つの F/M ジャンパー ワイヤ。

* 拡散型 10 mm LED。

> [!NOTE]
> 省略可能な項目がない場合は、シミュレートされたセンサー データをご利用いただけます。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi のセットアップ

### <a name="install-the-raspbian-operating-system-for-pi"></a>Pi の Raspbian オペレーティング システムのインストール

microSD カードに Raspbian イメージをインストールするための準備をします。

1. Raspbian をダウンロードします。

   a. [Raspbian Stretch をダウンロードします](https://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (.zip ファイル)。

   > [!WARNING]
   > `raspbian-2017-07-5` zip イメージをダウンロードするには、上のリンクを使用してください。 最新バージョンの Raspbian イメージには Wiring-Pi ノードに関する既知の問題がいくつかあるため、次の手順でエラーが発生するおそれがあります。

   b. コンピューター上のフォルダーに Raspbian イメージを抽出します。

2. microSD カードに Raspbian をインストールします。

   a. [Etcher SD カード書き込みユーティリティをダウンロードしてインストールします](https://etcher.io/)。

   b. Etcher を実行し、手順 1. で抽出した Raspbian イメージを選択します。

   c. microSD カード ドライブを選択します。 Etcher では適切なドライブが既に選択されている場合があります。

   d. [Flash (フラッシュ)] をクリックして、microSD カードに Raspbian をインストールします。

   e. インストールが完了したら、コンピューターから microSD カードを取り出します。 Etcher では完了時に microSD カードを自動的に取り出すか、マウント解除するため、microSD カードを直接取り出しても問題ありません。

   f. microSD カードを Pi に挿入します。

### <a name="enable-ssh-and-i2c"></a>SSH および I2C の有効化

1. Pi をモニター、キーボード、およびマウスに接続します。

2. Pi を起動してから、`pi` をユーザー名として、`raspberry` をパスワードとして使用して Raspbian にサインインします。

3. Raspberry アイコン > **[Preferences]\(設定)**  >  **[Raspberry Pi Configuration]\(Raspberry Pi 構成)** の順にクリックします。

   ![[Raspbian Preferences] (Raspbian 設定)メニュー](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. **[Interfaces]** (インターフェイス) タブで、 **[I2C]** と **[SSH]** を **[Enable]** (有効) に設定し、 **[OK]** をクリックします。 物理センサーがなく、シミュレートされたセンサー データを使用する場合は、この手順は省略可能です。

   ![I2C と SSH を Raspberry Pi で有効にする](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> SSH と I2C を有効にする際の参考ドキュメントは [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) と [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c) に多数あります。

### <a name="connect-the-sensor-to-pi"></a>センサーを Pi に接続する

ブレッドボードとジャンパー ワイヤを使用して、次のように LED と BME280 を Pi に接続します。 センサーがない場合は、[このセクションをスキップ](#connect-pi-to-the-network)します。

![Raspberry Pi とセンサーの接続](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

BME280 センサーでは、温度と湿度のデータを収集できます。 デバイスがメッセージをクラウドに送信すると、LED が点滅します。 

センサーの各ピンで、次のように接続します。

| 開始 (センサーと LED)     | 終了 (ボード)            | ケーブルの色   |
| -----------------------  | ---------------------- | ------------: |
| VDD (ピン 5G)             | 3.3V PWR (ピン 1)       | 白いケーブル   |
| GND (ピン 7G)             | GND (ピン 6)            | 茶色のケーブル   |
| SDI (ピン 10G)            | I2C1 SDA (ピン 3)       | 赤いケーブル     |
| SCK (ピン 8G)             | I2C1 SCL (ピン 5)       | オレンジ色のケーブル  |
| LED VDD (ピン 18F)        | GPIO 24 (ピン 18)       | 白いケーブル   |
| LED GND (ピン 17F)        | GND (ピン 20)           | 黒いケーブル   |

[Raspberry Pi 2 & 3 Pin mappings](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) をクリックして参照します。

BME280 が正常に Raspberry Pi に接続されると、下の図のようになります。

![接続された Pi と BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Pi のネットワークへの接続

micro USB ケーブルと AC アダプターを使って、Pi の電源を入れます。 イーサネット ケーブルを使用して Pi を有線ネットワークに接続するか、[Raspberry Pi Foundation の手順](https://www.raspberrypi.org/learning/software-guide/wifi/)に従って、Pi をワイヤレス ネットワークに接続します。 Pi がネットワークに正常に接続されたら、[Pi の IP アドレス](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)をメモしておく必要があります。

![接続先の有線ネットワーク](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> 必ず Pi をコンピューターと同じネットワークに接続してください。 たとえば、コンピューターがワイヤレス ネットワークに接続され、Pi がワイヤード (有線) ネットワークに接続されている場合、devdisco の出力に IP アドレスが表示されないことがあります。

## <a name="run-a-sample-application-on-pi"></a>Pi でのサンプル アプリケーションの実行

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>サンプル アプリケーションの複製と前提条件となるパッケージのインストール

1. 以下の SSH クライアントのいずれかを使用して、ホスト コンピューターから Raspberry Pi に接続します。

   **Windows ユーザー**
  
   a. Windows 版の [PuTTY](https://www.putty.org/) をダウンロードしてインストールします。 

   b. Pi の IP アドレスをホスト名 (または IP アドレス) セクションにコピーし、接続の種類として SSH を選択します。

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac ユーザーおよび Ubuntu ユーザー**

   Ubuntu または macOS に組み込まれている SSH クライアントを使用します。 SSH を使用して Pi を接続するには、`ssh pi@<ip address of pi>` を実行する必要がある場合があります。

   > [!NOTE]
   > 既定のユーザー名は `pi`、パスワードは`raspberry` です。

2. Pi に Node.js と NPM をインストールします。

   まず、ご使用の Node.js のバージョンを確認します。

   ```bash
   node -v
   ```

   バージョンが 10.x より前であるか、Node.js が Pi にない場合は、最新バージョンをインストールします。

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. サンプル アプリケーションを複製します。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

4. サンプルのすべてのパッケージをインストールします。 このインストールには、Azure IoT device SDK、BME280 センサー ライブラリ、および Wiring Pi ライブラリが含まれています。

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```

   > [!NOTE]
   >ネットワーク接続によっては、このインストール プロセスが終了するまで数分かかる場合があります。

### <a name="configure-the-sample-application"></a>サンプル アプリケーションを構成する

1. 次のコマンドを実行して、config ファイルを開きます。

   ```bash
   nano config.json
   ```

   ![config ファイル](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   このファイルには、構成可能な 2 つの項目があります。 1 つは `interval` で、クラウドに送信するメッセージ間の時間間隔 (ミリ秒) を定義します。 もう 1 つは `simulatedData` で、シミュレートされたセンサー データを使用するかどうかを表すブール値です。

   **センサーがない**場合は、`simulatedData` 値を `true` に設定し、シミュレートされたセンサー データをサンプル アプリケーションで作成して使用します。

2. Ctrl + O キー、Enter キー、Ctrl + X キーの順に押し、保存して終了します。

### <a name="run-the-sample-application"></a>サンプル アプリケーションの実行

次のコマンドを実行して、サンプル アプリケーションを実行します。

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > デバイスの接続文字列をコピーして貼り付け、必ず一重引用符で囲んでください。

IoT Hub に送信されるセンサー データとメッセージを示す次の出力が表示されます。

![出力 - Raspberry Pi から IoT Hub に送信されるセンサー データ](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>ハブに送信されたメッセージを読み取る

デバイスから IoT ハブが受信するメッセージを監視する方法の 1 つに、Azure IoT Tools for Visual Studio Code を使用することがあります。 詳細については、「[Visual Studio Code 用 Azure IoT Tools を使用してデバイスと IoT Hub の間のメッセージを送受信する](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)」を参照してください。

デバイスから送信されたデータを処理する詳しい方法については、次のセクションに進んでください。

## <a name="next-steps"></a>次の手順

サンプル アプリケーションを実行してセンサー データを収集し、IoT Hub に送信します。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
