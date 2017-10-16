---
title: "Intel Edison からクラウドへの接続 (Node.js) - Intel Edison を Azure IoT Hub に接続する | Microsoft Docs"
description: "このチュートリアルでは、Intel Edison を Azure IoT Hub に接続し、Intel Edison で Azure クラウド プラットフォームにデータを送信する方法について説明します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot intel edison, intel edison iot hub, intel edison からクラウドへのデータの送信, intel edison からクラウドへの接続"
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/15/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5a31efba704045196b5563f7bc467c773bea7805
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="connect-intel-edison-to-azure-iot-hub-nodejs"></a>Intel Edison を Azure IoT Hub に接続する (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

このチュートリアルでは、Intel Edison の操作の基本を学習することから始めます。 次に、[Azure IoT Hub](iot-hub-what-is-iot-hub.md) を使って、デバイスをクラウドにシームレスに接続する方法について説明します。

キットをお持ちでない場合は、 [ここ](https://azure.microsoft.com/develop/iot/starter-kits)から開始

## <a name="what-you-do"></a>作業内容

* Intel Edison と Grove モジュールをセットアップします。
* IoT Hub を作成します。
* Edison のデバイスを IoT Hub に登録します。
* Edison でサンプル アプリケーションを実行して、センサー データを IoT Hub に送信します。

作成した IoT Hub に Intel Edison を接続します。 次に、Edison でサンプル アプリケーションを実行して、Grove 温度センサーから気温と湿度のデータを収集します。 最後に、センサー データを IoT Hub に送信します。

## <a name="what-you-learn"></a>学習内容

* Azure IoT Hub を作成し、新しいデバイス接続文字列を取得する方法。
* Edison を Grove 温度センサーと接続する方法。
* Edison でサンプル アプリケーションを実行してセンサー データを収集する方法。
* センサー データを IoT Hub に送信する方法。

## <a name="what-you-need"></a>必要なもの

![必要なもの](media/iot-hub-intel-edison-kit-node-get-started/0_kit.png)

* Intel Edison ボード
* Arduino 拡張ボード
* 有効な Azure サブスクリプション Azure アカウントがない場合は、[無料試用版の Azure アカウント](https://azure.microsoft.com/free/)を数分で作成できます。
* Mac PC または Windows か Linux を実行している PC。
* インターネット接続。
* マイクロ B - タイプ A のUSB ケーブル
* 直流 (DC) 電源。 電源の定格は以下のとおり。
  - 7-15V DC
  - 1500mA 以上
  - 中心/内部ピンが電源の正極

省略可能な品目を次に示します。

* Grove ベース シールド V2
* Grove 温度センサー
* Grove ケーブル
* パッケージに含まれているスペーサー バーやネジ (拡張ボードにモジュールを固定するための2本のネジ、ネジ、プラスチック スペーサー 4 セットなど)

> [!NOTE] 
サンプル コードはシミュレートされたセンサー データをサポートしているため、これらの品目は省略可能です。

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Intel Edison をセットアップする

### <a name="assemble-your-board"></a>ボードを取り付ける

このセクションでは、Intel® Edison モジュールを拡張ボードに接続する手順を説明します。

1. Intel® Edison モジュールを拡張ボードの白枠内に置いて、拡張ボードにあるネジをモジュールの穴に通します。

2. カチッとはまるまで、モジュールの `What will you make?` という文字のすぐ下を押し下げます。

   ![assemble board 2](media/iot-hub-intel-edison-kit-node-get-started/1_assemble_board2.jpg)

3. 2 本の六角ナット (パッケージに同梱) を使用して、モジュールを拡張ボードに固定します。

   ![assemble board 3](media/iot-hub-intel-edison-kit-node-get-started/2_assemble_board3.jpg)

4. 拡張ボードの四隅にある穴の 1 つにネジを差し込みます。 ネジをプラスチック スペーサーの 1 つでしっかり固定します。

   ![assemble board 4](media/iot-hub-intel-edison-kit-node-get-started/3_assemble_board4.jpg)

5. 他の 3 つの隅でも同様に繰り返します。

   ![assemble board 5](media/iot-hub-intel-edison-kit-node-get-started/4_assemble_board5.jpg)

ボードを取り付けました。

   ![assemble board 5](media/iot-hub-intel-edison-kit-node-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Grove ベース シールドと温度センサーを接続する

1. Grove ベース シールドをボードに取り付けます。 すべてのピンがボードにしっかり差し込まれていることを確認します。
   
   ![Grove ベース シールド](media/iot-hub-intel-edison-kit-node-get-started/6_grove_base_sheild.jpg)

2. Grove ケーブルを使用して、Grove 温度センサーを Grove ベース シールドの **A0** ポートに接続します。

   ![温度センサーへの接続](media/iot-hub-intel-edison-kit-node-get-started/7_temperature_sensor.jpg)

   ![Edison とセンサーの接続](media/iot-hub-intel-edison-kit-node-get-started/16_edion_sensor.png)

これでセンサーの準備ができました。

### <a name="power-up-edison"></a>Edison の電源をオンにする

1. 電源を接続します。

   ![Plug in power supply](media/iot-hub-intel-edison-kit-node-get-started/8_plug_power.jpg)

2. (Arduino * 拡張ボードで DS1 というラベルが付いている) 緑色の LED が点灯して、そのまま点灯を続けます。

3. ボードの起動が完了するまで 1 分待ちます。

   > [!NOTE]
   > DC 電源を使用していない場合でも、USB ポートを介してボードに電源を供給できます。 詳細については、`Connect Edison to your computer` セクションをご覧ください。 この方法でボードに給電すると、Wi-Fi またはモーターを使用している場合は特に、ボードで予期せぬ動作が発生することがあります。

### <a name="connect-edison-to-your-computer"></a>Edison をコンピューターに接続する

1. Edison がデバイス モードになるように、マイクロ スイッチを 2 つのマイクロ USB ポート側に切り替えます。 デバイス モードとホスト モードの違いについては、[こちら](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode)をご覧ください。

   ![Toggle down the microswitch](media/iot-hub-intel-edison-kit-node-get-started/9_toggle_down_microswitch.jpg)

2. 上のマイクロ USB ポートにマイクロ USB ケーブルを接続します。

   ![上部のマイクロ USB ポート](media/iot-hub-intel-edison-kit-node-get-started/10_top_usbport.jpg)

3. USB ケーブルのもう一方の端をコンピューターに接続します。

   ![Computer USB](media/iot-hub-intel-edison-kit-node-get-started/11_computer_usb.jpg)

4. お使いのコンピューターに新しいドライブがマウントされると、ボードが完全に初期化されていることがわかります (コンピューターに SD カードを挿入するのと同様)。

## <a name="download-and-run-the-configuration-tool"></a>構成ツールをダウンロードして実行する
`Installers` という見出しの下に一覧表示されている[こちらのリンク](https://software.intel.com/en-us/iot/hardware/edison/downloads)から最新の構成ツールを入手します。 ツールを実行し、画面の指示に従って、必要に応じて [次へ] をクリックします。

### <a name="flash-firmware"></a>ファームウェアのフラッシュ
1. `Set up options` ページで `Flash Firmware` をクリックします。
2. 次のいずれかを実行して、ボードにフラッシュするイメージを選択します。
   - Intel から入手できる最新のファームウェア イメージをダウンロードしてボードをフラッシュするには、`Download the latest image version xxxx` を選択します。
   - お使いのコンピューターに保存してあるイメージでボードをフラッシュするには、`Select the local image` を選択します。 ボードにフラッシュする画像を探して選択します。
3. セットアップ ツールがボードのフラッシュを試みます。 フラッシュのプロセス全体は最大 10 分を要します。

### <a name="set-password"></a>パスワードの設定
1. `Set up options` ページで `Enable Security` をクリックします。
2. Intel® Edison ボードのカスタム名を設定できます。 これは省略可能です。
3. ボードのパスワードを入力してから、`Set password` をクリックします。
4. 後で使用するため、パスワードをメモしておきます。

### <a name="connect-wi-fi"></a>Wi-Fi の接続
1. `Set up options` ページで `Connect Wi-Fi` をクリックします。 コンピューターが利用できる Wi-Fi ネットワークをスキャンするまで、最大1分間待ちます。
2. `Detected Networks` ドロップダウン リストで、ネットワークを選択します。
3. `Security` ドロップダウン リストで、ネットワークのセキュリティ タイプを選択します。
4. ログイン名とパスワード情報を入力してから、`Configure Wi-Fi` をクリックします。
5. 後で使用するため、IP アドレスをメモしておきます。

> [!NOTE]
> 必ず Edison をコンピューターと同じネットワークに接続してください。 お使いのコンピューターは、IP アドレスを使用して Edison に接続します。

   ![温度センサーへの接続](media/iot-hub-intel-edison-kit-node-get-started/12_configuration_tool.png)

ご利用ありがとうございます。 Edison を適切に構成できました。

## <a name="run-a-sample-application-on-intel-edison"></a>Intel Edison でサンプル アプリケーションを実行する

### <a name="prepare-the-azure-iot-device-sdk"></a>Azure IoT Device SDK を準備する

1. 次の SSH クライアントのいずれかを使用して、ホスト コンピューターから Intel Edison に接続します。 構成ツールの IP アドレスと、そのツールで設定済みのパスワードを使用します。
    - Windows では [PuTTY](http://www.putty.org/)。
    - Ubuntu または macOS では組み込みの SSH クライアント。

2. サンプル クライアント アプリケーションをデバイスに複製します。 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-intel-edison-client-app
   ```

3. レポジトリ フォルダーに移動し、次のコマンドを実行してすべてのパッケージをインストールします。インストールが完了するまで数分かかることがあります。
   
   ```bash
   cd iot-hub-node-intel-edison-client-app
   npm install
   ```


### <a name="configure-and-run-the-sample-application"></a>サンプル アプリケーションを構成して実行する

1. 次のコマンドを実行して、config ファイルを開きます。

   ```bash
   nano config.json
   ```

   ![config ファイル](media/iot-hub-intel-edison-kit-node-get-started/13_configure_file.png)

   このファイルには、構成可能な 2 つのマクロがあります。 1 つ目は `INTERVAL` で、クラウドに送信する 2 つのメッセージの時間間隔を定義します。 2 つ目は `simulatedData` で、シミュレートされたセンサー データを使用するかどうかを表すブール値です。

   **センサーがない**場合は、`simulatedData` 値を `true` に設定し、シミュレートされたセンサー データをサンプル アプリケーションで作成して使用します。

1. Control + O キー、Enter キー、Control + X キーの順に押し、保存して終了します。


1. 次のコマンドを実行して、サンプル アプリケーションを実行します。

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   デバイスの接続文字列をコピーして貼り付け、必ず一重引用符で囲んでください。

IoT Hub に送信されるセンサー データとメッセージを示す次の出力が表示されます。

![出力 - Intel Edison から IoT Hub に送信されるセンサー データ](media/iot-hub-intel-edison-kit-node-get-started/15_message_sent.png)

## <a name="next-steps"></a>次のステップ

サンプル アプリケーションを実行してセンサー データを収集し、IoT Hub に送信します。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
