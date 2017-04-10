---
title: "Azure IoT への Intel Edison (C) の接続 - レッスン 1: デバイスの構成 | Microsoft Docs"
description: "Intel Edison の初回使用時の構成を行います。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Arduino のセットアップ, PC への Arduino 接続, Arduino ボード"
ms.assetid: bb8aa45b-d3ff-4438-b9d6-a9725a45ade1
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 800f1aed6d30d2bb871a6a9b55b6b95308932211
ms.lasthandoff: 01/25/2017


---
# <a name="configure-your-intel-edison"></a>Intel Edison を構成する
## <a name="what-you-will-do"></a>学習内容
ボードを取り付け、電源をオンにして構成ツールをデスクトップ OS にインストールして Intel Edisonの初回使用時の構成を行い、Edison のファームウェアをフラッシュし、パスワードを設定して Wi-Fi に接続します。 問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策をお探しください。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。

* Edison ボードを取り付けて電源をオンにする方法。
* Edison のファームウェアをフラッシュし、パスワードを設定して Wi-Fi に接続する方法。

## <a name="what-you-need"></a>必要なもの
この操作を完了するには、Intel Edison スターター キットの次のものが必要です。

* Intel® Edison モジュール
* Arduino 拡張ボード
* パッケージに含まれているスペーサー バーやネジ (拡張ボードにモジュールを固定するための2本のネジ、ネジ、プラスチック スペーサー&4; セットなど)
* マイクロ B - タイプ A のUSB ケーブル
* 直流 (DC) 電源。 電源の定格は以下のとおり。
  - 7-15V DC
  - 1500mA 以上
  - 中心/内部ピンが電源の正極

  ![スターター キットの内容物](media/iot-hub-intel-edison-lessons/lesson1/kit.png)

以下も必要です。

* Windows、Mac、または Linux を実行しているコンピューター。
* Edison を接続するためのワイヤレス接続。
* インターネット接続 (構成ツールをダウンロードするため)。

## <a name="assemble-your-board"></a>ボードを取り付ける

このセクションでは、Intel® Edison モジュールを拡張ボードに接続する手順を説明します。

1. Intel® Edison モジュールを拡張ボードの白枠内に置いて、拡張ボードにあるネジをモジュールの穴に通します。

2. カチッとはまるまで、モジュールの `What will you make?` という文字のすぐ下を押し下げます。

   ![assemble board 2](media/iot-hub-intel-edison-lessons/lesson1/assemble_board2.jpg)

3. 2 本の六角ナット (パッケージに同梱) を使用して、モジュールを拡張ボードに固定します。

   ![assemble board 3](media/iot-hub-intel-edison-lessons/lesson1/assemble_board3.jpg)

4. 拡張ボードの四隅にある穴の&1; つにネジを差し込みます。 ネジをプラスチック スペーサーの&1; つでしっかり固定します。

   ![assemble board 4](media/iot-hub-intel-edison-lessons/lesson1/assemble_board4.jpg)

5. 他の&3; つの隅でも同様に繰り返します。

   ![assemble board 5](media/iot-hub-intel-edison-lessons/lesson1/assemble_board5.jpg)

ボードを取り付けました。

   ![assemble board&5;](media/iot-hub-intel-edison-lessons/lesson1/assembled_board.jpg)

## <a name="power-up-edison"></a>Edison の電源をオンにする

1. 電源を接続します。

   ![Plug in power supply](media/iot-hub-intel-edison-lessons/lesson1/plug_power.jpg)

2. (Arduino * 拡張ボードで DS1 というラベルが付いている) 緑色の LED が点灯して、そのまま点灯を続けます。

3. ボードの起動が完了するまで&1; 分待ちます。

   > [!NOTE]
   > DC 電源を使用していない場合でも、USB ポートを介してボードに電源を供給できます。 詳細については、`Connect Edison to your computer` セクションをご覧ください。 この方法でボードに給電すると、Wi-Fi またはモーターを使用している場合は特に、ボードで予期せぬ動作が発生することがあります。

## <a name="connect-edison-to-your-computer"></a>Edison をコンピューターに接続する

1. Edison がデバイス モードになるように、マイクロ スイッチを&2; つのマイクロ USB ポート側に切り替えます。 デバイス モードとホスト モードの違いについては、[こちら](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode)をご覧ください。

   ![Toggle down the microswitch](media/iot-hub-intel-edison-lessons/lesson1/toggle_down_microswitch.jpg)

2. 上のマイクロ USB ポートにマイクロ USB ケーブルを接続します。

   ![上部のマイクロ USB ポート](media/iot-hub-intel-edison-lessons/lesson1/top_usbport.jpg)

3. USB ケーブルのもう一方の端をコンピューターに接続します。

   ![Computer USB](media/iot-hub-intel-edison-lessons/lesson1/computer_usb.jpg)

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

ご利用ありがとうございます。 Edison を適切に構成できました。

## <a name="summary"></a>概要
この記事では、Edison ボードを取り付けて、構成ツールを使用してファームウェアをフラッシュし、パスワードを設定し、Wi-Fi に接続する方法を説明しました。 LED はまだ点灯しません。 次のタスクでは、Edison でサンプル アプリケーションを実行するための準備として、必要なツールとソフトウェアをインストールします。

## <a name="next-steps"></a>次のステップ
[ツールの入手][get-the-tools]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
