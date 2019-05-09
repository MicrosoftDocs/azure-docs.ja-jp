---
title: ESP8266 をクラウドへ - Feather HUZZAH ESP8266 を Azure IoT Hub に接続する | Microsoft Docs
description: このチュートリアルでは、Adafruit Feather HUZZAH ESP8266 を Azure IoT Hub に接続し、Azure クラウド プラットフォームにデータを送信する方法について説明します。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 293901aca3fa1a94c9c6340d2e04f47914db0e07
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524465"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Adafruit Feather HUZZAH ESP8266 をクラウドの Azure IoT Hub に接続する

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![DHT22、Feather HUZZAH ESP8266、IoT Hub 間の接続](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>作業内容

Adafruit Feather HUZZAH ESP8266 を、作成する IoT Hub に接続します。 次に、ESP8266 でサンプル アプリケーションを実行して、DHT22 センサーから気温と湿度のデータを収集します。 最後に、センサー データを IoT Hub に送信します。

> [!NOTE]
> 他の ESP8266 ボードを使っている場合でも、以下の手順に従って IoT Hub に接続できます。 ただし、お使いの ESP8266 ボードによっては、`LED_PIN` の再構成が必要になることがあります。 たとえば、AI-Thinker の ESP8266 をお使いであれば `0` から `2` への変更が必要になることがあります。 キットをお持ちでない場合は、 [Azure Web サイト](https://azure.com/iotstarterkits) から入手できます。

## <a name="what-you-learn"></a>学習内容

* IoT Hub を作成し、Feather HUZZAH ESP8266 のデバイスを登録する方法
* センサー付きの Feather HUZZAH ESP8266 を、お使いのコンピューターに接続する方法
* Feather HUZZAH ESP8266 のサンプル アプリケーションを実行して、センサー データを収集する方法
* センサー データを IoT Hub に送信する方法

## <a name="what-you-need"></a>必要なもの

![このチュートリアルに必要なもの](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

この操作を完了するには、Feather HUZZAH ESP8266 スターター キットの次のものが必要です。

* Feather HUZZAH ESP8266 ボード
* マイクロ USB - タイプ A の USB ケーブル

また、開発環境に次のものが必要です。

* 有効な Azure サブスクリプション Azure アカウントがない場合は、[無料試用版の Azure アカウント](https://azure.microsoft.com/free/)を数分で作成できます。
* Mac PC または Windows か Ubuntu をインストールした PC。
* Feather HUZZAH ESP8266 の接続先となるワイヤレス ネットワーク。
* 構成ツールをダウンロードするためのインターネット接続。
* [Ansible 用 Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino)。

> [!Note]
> Ansible 用 Visual Studio Code 拡張機能で使用する Arduino IDE のバージョンは、1.6.8 以降でなければなりません。 それより前のバージョンでは AzureIoT ライブラリを使用できません。

次のものは、センサーがない場合のオプションです。 シミュレートされたセンサー データを使用するオプションもあります。

* Adafruit DHT22 気温・湿度センサー
* ブレッドボード
* M/M ジャンパー ワイヤ

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT ハブに対する接続文字列を取得する

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>センサー付きの HUZZAH ESP8266 をコンピューターに接続する

このセクションでは、センサーをボードに接続します。 その後、デバイスをコンピューターに接続して、さらに使用します。

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Feather HUZZAH ESP8266 に DHT22 気温・湿度センサーを接続する

次のように、接続にはブレッドボードとジャンパー ワイヤを使用します。 センサーがない場合は、シミュレートされたセンサー データを代わりに使用できますので、このセクションをスキップしてください。

![接続の参照](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

センサーの各ピンで、次のように接続します。

| 開始 (センサー)           | 終了 (ボード)            | ケーブルの色   |
| -----------------------  | ---------------------- | ------------  |
| VDD (ピン 31F)            | 3V (ピン 58 H)           | 赤いケーブル     |
| DATA (ピン 32F)           | GPIO 2 (ピン 46A)       | 青いケーブル    |
| GND (ピン 34F)            | GND (ピン 56I)          | 黒いケーブル   |

詳細については、[Adafruit 社の DHT22 センサーの設定に関するページ](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor)と [Adafruit 社の Feather HUZZAH Esp8266 のピン配列に関するページ](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts)をご覧ください。

これで、Feather Huzzah ESP8266 が動作中のセンサーに接続されたはずです。

![DHT22 と Feather HUZZAH を接続する](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Feather HUZZAH ESP8266 をコンピューターに接続する

次に示すように、マイクロ USB - タイプ A の USB ケーブルを使用して、Feather HUZZAH ESP8266 をコンピューターに接続します。

![Feather HUZZAH をコンピューターに接続する](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>シリアル ポートのアクセス許可を追加する (Ubuntu のみ)

Ubuntu を使用する場合は、Feather HUZZAH ESP8266 の USB ポートを操作するアクセス許可があることを確認します。 シリアル ポートのアクセス許可を追加するには、次の手順に従います。

1. ターミナルで次のコマンドを実行します。

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   次の出力のどちらかを取得します。

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   この出力では、`uucp` または `dialout` が USB ポートのグループの所有者名になっていることを確認してください。

2. 次のコマンドを実行して、グループにユーザーを追加します。

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` は先ほどの手順で取得したグループの所有者名です。 `<username>` は Ubuntu のユーザー名です。

3. 変更を表示するために、Ubuntu からサインアウトし、もう一度サインインします。

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>センサー データを収集し IoT Hub に送信する

このセクションでは、Feather HUZZAH ESP8266 でサンプル アプリケーションをデプロイし実行します。 このサンプル アプリケーションによって、Feather HUZZAH ESP8266 の LED が点滅し、DHT22 センサーから収集された気温と湿度のデータが IoT Hub に送信されます。

### <a name="get-the-sample-application-from-github"></a>サンプル アプリケーションを GitHub から取得する

このサンプル アプリケーションは GitHub でホストされています。 GitHub から、このサンプル アプリケーションが含まれているサンプル リポジトリを複製します。 サンプル リポジトリを複製するには、次の手順に従います。

1. コマンド プロンプトまたはターミナル ウィンドウを開きます。

2. サンプル アプリケーションを格納するフォルダーに移動します。

3. 次のコマンドを実行します。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   次に、Visual Studio Code に Feather HUZZAH ESP8266 のパッケージをインストールします。

4. サンプル アプリケーションが格納されているフォルダーを開きます。

5. Visual Studio Code のアプリ フォルダーにある app.ino ファイルを開きます。

   ![Visual Studio Code でサンプル アプリケーションを開く](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. Visual Studio Code で、`F1` を入力します。

7. 「**Arduino**」と入力し、**[Arduino: Board Manager]\(Arduino: Board Manager\)** を入力して選択します。

8. **[Arduino Board Manager]\(Arduino Board Manager\)** タブで、**[追加の URL]** をクリックします。

   ![VS Code Arduino Board Manager](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. **[ユーザー設定]** ウィンドウで、以下の内容をコピーして、ファイルの最後に貼り付けます。

   ```json
   "arduino.additionalUrls": "http://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![VS Code で Arduino パッケージの URL を構成する](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. ファイルを保存して **[ユーザー設定]** タブを閉じます。

11. **[Refresh Package Indexes]\(パッケージ インデックスの更新\)** をクリックします。 更新が完了したら、**esp8266** を検索します。

12. esp8266 の **[インストール]** ボタンをクリックします。

    ESP8266 バージョン 2.2.0 以降がインストールされていることがボード マネージャーに表示されます。

    ![ESP8266 のインストール](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. 「`F1`」と入力してから、「**Arduino**」と入力し、**[Arduino:Board Config]** を選択します。

14. **[Selected Board:]\(選択済みのボード:\)** のボックスをクリックし、「**esp8266**」と入力してから、**[Adafruit HUZZAH ESP8266 (esp8266)]\(Adafruit HUZZAH ESP8266 (esp8266)\)** を選択します。

    ![esp8266 ボードを選択する](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>必要なライブラリをインストールする

1. Visual Studio Code で、「`F1`」と入力してから、「**Arduino**」と入力し、**[Arduino:Library Manager]** を選択します。

2. 1 つずつ次のライブラリ名を検索します。 検索した各ライブラリで、**[インストール]** をクリックします。
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>DHT22 センサーがない場合

DHT22 センサーがない場合は、サンプル アプリケーションで気温と湿度をシミュレートできます。 シミュレートされたデータを使用するようにサンプル アプリケーションを設定するには、次の手順に従います。

1. `app` フォルダー内の `config.h` ファイルを開きます。

2. 次のコード行を見つけて、値を `false` から `true` に変更します。

   ```c
   define SIMULATED_DATA true
   ```

   ![シミュレートされたデータをサンプル アプリケーションで使用するための構成](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. ファイルを保存します。

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>サンプル アプリケーションを Feather HUZZAH ESP8266 にデプロイする

1. Visual Studio Code のステータス バーにある **\<Select Serial Port>** をクリックし、Feather HUZZAH ESP8266 のシリアル ポートをクリックします。

2. 「`F1`」と入力してから、「**Arduino**」と入力し、**[Arduino:Upload]** を選択して、サンプル アプリケーションを Feather HUZZAH ESP8266 にビルドし、デプロイします。

### <a name="enter-your-credentials"></a>資格情報を入力する

アップロードが正常に完了したら、次の手順に従って資格情報を入力します。

1. Arduino IDE を開き、**[ツール]** > **[シリアルモニタ]** をクリックします。

2. [シリアルモニタ] ウィンドウで、右下隅の 2 つのドロップダウン リストを確認します。

3. 左のドロップダウン リストで **[改行なし]** を選択します。

4. 右のドロップダウン リストで **[115200 baud]** を選択します。

5. [シリアルモニタ] ウィンドウの上部にある入力ボックスで、次の情報を求められた場合は入力して **[送信]** をクリックします。

   * Wi-Fi SSID
   * Wi-Fi パスワード
   * デバイスの接続文字列

> [!Note]
> 資格情報は Feather HUZZAH ESP8266 の EEPROM に格納されています。 Feather HUZZAH ESP8266 ボードのリセット ボタンをクリックすると、資格情報を削除するかどうかサンプル アプリケーションから質問されます。 この情報を消去するには `Y` を入力します。 情報を再度提供することを求められます。

### <a name="verify-the-sample-application-is-running-successfully"></a>サンプル アプリケーションが正常に実行されていることを確認する

[シリアルモニタ] ウィンドウで次のような出力を確認し、Feather HUZZAH ESP8266 の LED が点滅していることを確認できれば、サンプル アプリケーションは正常に実行されています。

![Arduino IDE での最終出力](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>ハブに送信されたメッセージを読み取る

デバイスから IoT ハブが受信するメッセージを監視する方法の 1 つに、Azure IoT Tools for Visual Studio Code を使用することがあります。 詳細については、「[Visual Studio Code 用 Azure IoT Tools を使用してデバイスと IoT Hub の間のメッセージを送受信する](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)」を参照してください。

デバイスから送信されたデータを処理する詳しい方法については、次のセクションに進んでください。

## <a name="next-steps"></a>次の手順

Feather HUZZAH ESP8266 を IoT Hub に接続し、キャプチャしたセンサー データを IoT Hub に送信できるようになりました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]