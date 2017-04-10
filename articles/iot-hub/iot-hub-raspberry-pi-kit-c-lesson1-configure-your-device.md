---
title: "Azure IoT への Raspberry Pi (C) の接続 - レッスン 1: デバイスの構成 | Microsoft Docs"
description: "Raspberry Pi 3 の初回使用時の構成を行い、Raspbian OS (Raspberry Pi ハードウェア用に最適化されている無料のオペレーティング システム) をインストールします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Raspbian のインストール, Raspbian のダウンロード, Raspbian のインストール方法, Raspbian の設定, Raspberry Pi への Raspbian のインストール, Raspberry Pi への OS のインストール, Raspberry Pi への SD カードの取り付け, Raspberry Pi 接続, Raspberry Pi への接続, Raspberry Pi の接続"
ms.assetid: 8ee9b23c-93f7-43ff-8ea1-e7761eb87a6f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 5b80d471c78973f8c2f6b1ad4e1240c66f0505ef
ms.openlocfilehash: 3e34b8202794bc969bf41765ce519d083037ab46
ms.lasthandoff: 02/21/2017


---
# <a name="configure-your-device"></a>デバイスを構成する
## <a name="what-you-will-do"></a>学習内容
Pi の初回使用時の構成を行い、Raspbian オペレーティング システムをインストールします。 Raspbian は、Raspberry Pi ハードウェア用に最適化された無料のオペレーティング システムです。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-raspberry-pi-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。

* Raspbian を Pi にインストールする方法。
* USB ケーブルを使って Pi の電源を入れる方法。
* イーサネット ケーブルまたはワイヤレス ネットワークを使って Pi をネットワークに接続する方法。
* ブレッドボードに LED を追加して Pi に接続する方法。

## <a name="what-you-need"></a>必要なもの
この操作を完了するには、Raspberry Pi 3 スターター キットの次のものが必要です。

* Raspberry Pi 3 ボード
* 16 GB の microSD カード
* 5V 2A の AC アダプターと約 2 m の micro USB ケーブル
* ブレッドボード
* コネクタ ケーブル
* 560 Ω の抵抗
* 拡散型 10 mm LED
* イーサネット ケーブル

![スターター キットの内容物](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

以下も必要です。

* Pi を接続するためのワイヤード (有線) またはワイヤレス接続。
* USB-SD アダプターまたは miniSD カード (microSD カードに OS イメージを書き込むため)。
* Windows、Mac、または Linux を実行しているコンピューター。 このコンピューターを使って、microSD カードに Raspbian をインストールします。
* インターネット接続 (必要なツールとソフトウェアをダウンロードするため)。

## <a name="install-raspbian-on-the-microsd-card"></a>microSD カードへの Raspbian のインストール
microSD カードに Raspbian イメージをインストールするための準備をします。

1. Raspbian をダウンロードします。
   1. Raspbian Jessie with Pixel の zip ファイルを[ダウンロード](https://www.raspberrypi.org/downloads/raspbian/)します。
   2. コンピューター上のフォルダーに Raspbian イメージを抽出します。
2. microSD カードに Raspbian をインストールします。
   1. Etcher SD カード書き込みユーティリティを[ダウンロード](https://www.etcher.io)してインストールします。
   2. Etcher を実行し、手順 1. で抽出した Raspbian イメージを選択します。
   3. microSD カード ドライブを選択します。
      適切なドライブが既に選択されている場合があります。
   4. **[Flash (フラッシュ)]** をクリックして、microSD カードに Raspbian をインストールします。
   5. インストールが完了したら、コンピューターから microSD カードを取り出します。
      Etcher では完了時に microSD カードを自動的に取り出すか、マウント解除するため、microSD カードを直接取り出しても問題ありません。
   6. microSD カードを Pi に挿入します。

![SD カードの挿入](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="turn-on-pi"></a>Pi の電源の投入
micro USB ケーブルと AC アダプターを使って、Pi の電源を入れます。

![電源を入れる](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [!NOTE]
> Raspberry が正常に動作するために必要な電力が確実に供給されるように、キットに付属している 2A 以上の AC アダプターを使うことが重要です。

## <a name="enable-ssh"></a>SSH を有効にする
2016 年 11 月のリリース時点では、Raspbian の既定で SSH サーバーが無効になっています。 そのため、SSH を手動で有効にする必要があります。 [公式の手順](https://www.raspberrypi.org/documentation/remote-access/ssh/)を参照するか、モニターを接続して **[Preferences] -> [Raspberry Pi Configuration]** に移動して、SSH を有効にします。

## <a name="connect-raspberry-pi-3-to-the-network"></a>ネットワークへの Raspberry Pi 3 の接続
Pi は、ワイヤード (有線) ネットワークまたはワイヤレス ネットワークに接続できます。 必ず Pi をコンピューターと同じネットワークに接続してください。 たとえば、コンピューターが接続しているのと同じスイッチに Pi を接続します。

### <a name="connect-to-a-wired-network"></a>ワイヤード (有線) ネットワークへの接続
イーサネット ケーブルを使って、Pi をワイヤード (有線) ネットワークに接続します。 接続が確立されると、Pi の&2; つの LED が点灯します。

![イーサネット ケーブルを使用した接続](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="connect-to-a-wireless-network"></a>ワイヤレス ネットワークへの接続
Raspberry Pi Foundation の[手順](https://www.raspberrypi.org/learning/software-guide/wifi/)に従って、Pi をワイヤレス ネットワークに接続します。 これらの手順を実行するには、先にモニターとキーボードを Pi に接続する必要があります。

## <a name="connect-the-led-to-pi"></a>Pi への LED の接続
このタスクを完了するには、[ブレッドボード](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard)、コネクタ ケーブル、LED、抵抗を使います。 これらを Pi の[汎用入出力](https://www.raspberrypi.org/documentation/usage/gpio/) (GPIO) ポートに接続します。

![ブレッドボード、LED、抵抗](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. LED の短い足を **GPIO GND (ピン 6)** に接続します。
2. LED の長い足を抵抗の一方の足に接続します。
3. 抵抗のもう一方の足を **GPIO 4 (ピン 7)** に接続します。

LED の極性が重要であることに注意してください。 この極性設定は、一般にアクティブ ローと呼ばれます。

![ピン出力](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

お疲れさまでした。 Pi を適切に構成できました。

## <a name="summary"></a>概要
この記事では、Raspbian をインストールし、Pi をネットワークに接続して、Pi に LED を接続することで、Pi を構成する方法について説明しました。 LED はまだ点灯しません。 次のタスクでは、Pi でサンプル アプリケーションを実行するための準備として、必要なツールとソフトウェアをインストールします。

![ハードウェアの準備完了](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>次のステップ
[ツールの入手](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)


