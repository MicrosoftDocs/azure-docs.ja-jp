---
title: "ESP8266 をクラウドへ - Sparkfun ESP8266 Thing Dev を Azure IoT Hub に接続する | Microsoft Docs"
description: "このチュートリアルでは、Sparkfun ESP8266 Thing Dev を Azure IoT Hub に接続し、Sparkfun ESP8266 Thing Dev で Azure クラウド プラットフォームにデータを送信する方法について説明します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 557f0cdf375b345e0dbe0526f5a5bd3c050dec38
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Sparkfun ESP8266 Thing Dev をクラウドの Azure IoT Hub に接続する

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![DHT22 (Thing Dev) と IoT Hub の間の接続](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>学習内容

Sparkfun ESP8266 Thing Dev を、作成する Azure IoT Hub に接続します。 次に、DHT22 センサーから気温と湿度のデータを収集するために、ESP8266 でサンプル アプリケーションを実行します。 最後に、センサー データを IoT Hub に送信します。

> [!NOTE]
> 他の ESP8266 ボードをお使いでも、以下の手順に従って IoT Hub に接続できます。 ただし、お使いの ESP8266 ボードによっては、`LED_PIN` の再構成が必要になる場合があります。 たとえば、AI-Thinker の ESP8266 をお使いであれば `0` から `2` への変更が必要になる場合があります。 キットをお持ちでない場合は、[こちら](http://azure.com/iotstarterkits)をクリックしてください。

## <a name="what-you-will-learn"></a>学習内容

* IoT Hub を作成し、Thing Dev のデバイスを登録する方法。
* センサー付き Thing Dev をコンピューターに接続する方法。
* Thing Dev のサンプル アプリケーションを実行して、センサー データを収集する方法。
* センサー データを IoT Hub に送信する方法。

## <a name="what-you-will-need"></a>前提条件

![このチュートリアルに必要なもの](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

この操作を完了するには、Thing Dev スターター キットの次のものが必要です。

* Sparkfun ESP8266 Thing Dev ボード
* マイクロ USB - タイプ A の USB ケーブル

また、開発環境に次のものが必要です。

* 有効な Azure サブスクリプション Azure アカウントがない場合は、[無料試用版の Azure アカウント](https://azure.microsoft.com/free/)を数分で作成できます。
* Mac PC または Windows か Ubuntu をインストールした PC。
* Sparkfun ESP8266 Thing Dev を接続するワイヤレス ネットワーク。
* 構成ツールをダウンロードするためのインターネット接続。
* [Arduino IDE](https://www.arduino.cc/en/main/software) バージョン 1.6.8 (以降)。それ以前のバージョンでは AzureIoT ライブラリを使用できません。

次のものは、センサーがない場合のオプションです。 シミュレートされたセンサー データを使用するオプションもあります。

* Adafruit DHT22 気温・湿度センサー
* ブレッドボード
* M/M ジャンパー ワイヤ

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>センサー付き ESP8266 Thing Dev をコンピューターに接続する

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>DHT22 気温・湿度センサー ESP8266 Thing Dev に接続する

次のように、接続にはブレッドボードとジャンパー ワイヤを使用します。 センサーがない場合は、シミュレートされたセンサー データを代わりに使用できますので、このセクションをスキップしてください。

![接続の参照](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

センサー ピンには次のケーブルを使用します。

| 開始 (センサー)           | 終了 (ボード)           | ケーブルの色   |
| -----------------------  | ---------------------- | ------------: |
| VDD (ピン 27F)            | 3V (ピン 8A)           | 赤いケーブル     |
| DATA (ピン 28F)           | GPIO 2 (ピン 9A)       | 白いケーブル    |
| GND (ピン 30F)            | GND (ピン 7J)          | 黒いケーブル   |


- 詳細については、[DHT22 センサーの設定](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf)に関するページと [Sparkfun ESP8266 Thing Dev の仕様](https://www.sparkfun.com/products/13711)のページをご覧ください

これで、Sparkfun ESP8266 Thing Dev が動作中のセンサーに接続されたはずです。

![dht22 と ESP8266 Thing Dev を接続する](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Sparkfun ESP8266 Thing Dev をコンピューターに接続する

マイクロ USB - タイプ A の USB ケーブルを使用して、次のように Sparkfun ESP8266 Thing Dev をコンピューターに接続します。

![Feather HUZZAH をコンピューターに接続する](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>シリアル ポートのアクセス許可を追加する – Ubuntu の場合のみ

Ubuntu の場合は、必ず通常のユーザーに Sparkfun ESP8266 Thing Dev の USB ポートを操作するアクセス許可を付与してください。 通常のユーザーにシリアル ポートのアクセス許可を追加するには、次の手順に従います。

1. ターミナルで次のコマンドを実行します。

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   次の出力のどちらかを取得します。

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   この出力では、`uucp` または `dialout` が USB ポートのグループの所有者名になっていることを確認してください。

1. 次のコマンドを実行して、グループにユーザーを追加します。

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` は先ほどの手順で取得したグループの所有者名です。 `<username>` は Ubuntu のユーザー名です。

1. Ubuntu からログアウトし、変更を反映するためにもう一度ログインします。

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>センサー データを収集し IoT Hub に送信する

このセクションでは、Sparkfun ESP8266 Thing Dev でサンプル アプリケーションをデプロイして実行します。 このサンプル アプリケーションによって、Sparkfun ESP8266 Thing Dev の LED が点滅し、DHT22 センサーから収集された気温と湿度のデータが IoT Hub に送信されます。

### <a name="get-the-sample-application-from-github"></a>サンプル アプリケーションを GitHub から取得する

このサンプル アプリケーションは GitHub でホストされています。 GitHub から、このサンプル アプリケーションが含まれているサンプル リポジトリを複製します。 サンプル リポジトリを複製するには、次の手順に従います。

1. コマンド プロンプトまたはターミナル ウィンドウを開きます。
1. サンプル アプリケーションを格納するフォルダーに移動します。
1. 次のコマンドを実行します。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Arduino IDE の Sparkfun ESP8266 Thing Dev パッケージをインストールします。

1. サンプル アプリケーションが格納されているフォルダーを開きます。
1. Arduino IDE のアプリ フォルダーにある app.ino ファイルを開きます。

   ![Arduino IDE のサンプル アプリケーションを開く](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. Arduino IDE で、**[ファイル]** > **[環境設定]** をクリックします。
1. **[環境設定]** ダイアログ ボックスで、**[追加のボードマネージャのURL]** テキスト ボックスの横にあるアイコンをクリックします。
1. ポップアップ ウィンドウで次の URL を入力し、**[OK]** をクリックします。

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Arduino IDE で パッケージの URL を指定する](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. **[環境設定]** ダイアログ ボックスで、**[OK]** をクリックします。
1. 次に**[ツール]** > **[ボード]** > **[ボードマネージャ]** をクリックし、「esp8266」を検索します。
   ESP8266 バージョン 2.2.0 以降をインストールする必要があります。

   ![ESP8266 のインストール](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. **[ツール]** > **[ボード]** > **[Sparkfun ESP8266 Thing Dev]** の順にクリックします。

### <a name="install-necessary-libraries"></a>必要なライブラリをインストールする

1. Arduino IDE で、**[スケッチ]** > **[ライブラリをインクルード]** > **[ライブラリを管理]** をクリックします。
1. 1 つずつ次のライブラリ名を検索します。 検索したライブラリごとに、**[インストール]** をクリックします。
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>DHT22 センサーがない場合

DHT22 センサーがない場合は、サンプル アプリケーションで気温と湿度をシミュレートできます。 シミュレートされたデータをサンプル アプリケーションで使用できるようにするには、次の手順に従います。

1. `app` フォルダー内の `config.h` ファイルを開きます。
1. 次のコード行を見つけて、値を `false` から `true` に変更します。
   ```c
   define SIMULATED_DATA true
   ```
   ![シミュレートされたデータをサンプル アプリケーションで使用するための構成](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. `Control-s` で保存します。

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>サンプル アプリケーションを Sparkfun ESP8266 Thing Dev にデプロイする

1. Arduino IDEで、**[ツール]** > **[ポート]** をクリックし、Sparkfun ESP8266 Thing Dev のシリアル ポートをクリックします。
1. **[スケッチ]** > **[マイコンボードに書き込む]** をクリックし、サンプル アプリケーションを Sparkfun ESP8266 Thing Dev にビルドし、デプロイします。

> [!Note]
> macOS を使用している場合、アップロード中、多くの場合、次のメッセージが表示されます:  `warning: espcomm_sync failed`、`error: espcomm_open failed`。 この問題を解決するには、ターミナル ウィンドウを開いて、以下のアクションを実行します。
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>資格情報を入力する

アップロードが正常に完了したら、次の手順に従って資格情報を入力します。

1. Arduino IDE で、**[ツール]** > **[シリアルモニタ]** をクリックします。
1. [シリアルモニタ] ウィンドウで、右下隅の 2 つのドロップダウン リストを確認します。
1. 左のドロップダウン リストで **[改行なし]** を選択します。
1. 右のドロップダウン リストで **[115200 baud]** を選択します。
1. [シリアルモニタ] ウィンドウの上部にある入力ボックスで、次の情報を求められた場合は入力して **[送信]** をクリックします。
   * Wi-Fi SSID
   * Wi-Fi パスワード
   * デバイスの接続文字列

> [!Note]
> 資格情報は Sparkfun ESP8266 Thing Dev の EEPROM に格納されています。 Sparkfun ESP8266 Thing Dev ボードのリセット ボタンをクリックすると、資格情報を削除するかどうかを確認するメッセージが表示されます。 `Y` を入力して資格情報を削除すると、資格情報をもう一度入力するよう求められます。

### <a name="verify-the-sample-application-is-running-successfully"></a>サンプル アプリケーションが正常に実行されていることを確認する

シリアル モニター ウィンドウで次のような出力を確認し、Sparkfun ESP8266 Thing Dev の LED が点滅していることを確認できれば、サンプル アプリケーションは正常に実行されています。

![Arduino IDE での最終出力](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>次のステップ

Sparkfun ESP8266 Thing Dev を IoT Hub に接続し、キャプチャしたセンサー データを IoT Hub に送信できるようになりました。 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

