---
title: "SensorTag デバイスと Azure IoT Gateway - レッスン 1: Intel NUC の設定 | Microsoft Docs"
description: "センサー情報を収集して IoT Hub に送信するために、Intel NUC をセンサーと Azure IoT Hub 間の IoT ゲートウェイとして機能するようにセットアップします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: "IoT ゲートウェイ, Intel NUC, NUC コンピューター, DE3815TYKE"
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: af2dde245fdef2984465f0c8447b558a2c770618
ms.lasthandoff: 03/30/2017


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>IoT ゲートウェイとしての Intel NUC のセットアップ

## <a name="what-you-will-do"></a>学習内容

- Intel NUC を IoT ゲートウェイとしてセットアップします。
- Azure IoT Gateway SDK パッケージを Intel NUC にインストールします。
- Intel NUC で "hello_world" サンプル アプリケーションを実行して、ゲートウェイの機能を検証します。
    
  > 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容

このレッスンでは、次の内容を学習します。

- Intel NUC と周辺機器を接続する方法。
- Smart Package Manager を使用して、必要なパッケージを Intel NUC にインストールしてアップデートする方法。
- "hello_world" サンプルアプリケーションを実行してゲートウェイの機能を検証する方法。

## <a name="what-you-need"></a>必要なもの

- Intel IoT Gateway Software Suite (Wind River Linux *7.0.0.13) がプレインストールされた Intel NUC Kit DE3815TYKE。 [Grove IoT 商用ゲートウェイ キットを購入するには、ここをクリックしてください](https://www.seeedstudio.com/Grove-IoT-Commercial-Gateway-Kit-p-2724.html)。
- イーサネット ケーブル。
- キーボード。
- HDMI または VGA ケーブル。
- HDMI または VGA ポート付きモニター。
- 省略可能: [Texas Instruments Sensor Tag (CC2650STK)](http://www.ti.com/tool/cc2650stk)

![ゲートウェイ キット](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Intel NUC と周辺機器の接続

次の画像は、さまざまな周辺機器が接続された Intel NUC の例を示しています。

1. キーボードに接続されています。
2. VGA ケーブルまたは HDMI ケーブルでモニターに接続されています。
3. イーサネット ケーブルで有線ネットワークに接続されています。
4. 電源ケーブルで電源に接続されています。

![周辺機器に接続されている Intel NUC](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>ホスト コンピューターからの Secure Shell (SSH) を使用した Intel NUC システムへの接続

Intel NUC デバイスの IP アドレスを取得するためのキーボードとモニターが必要です。 IP アドレスがわかっている場合は、このセクションの手順 3 に進むことができます。

1. 電源ボタンを押して Intel NUC をオンにし、ログインします。

   既定のユーザー名とパスワードは、どちらも `root` です。

       > Hit the enter key on your keyboard if you see either of the following errors when you boot: 'A TPM error (7) occurred attempting to read a pcr value.' or 'Timeout, No TPM chip found, activating TPM-bypass!'

2. Intel NUC デバイスで `ifconfig` コマンドを実行して、Intel NUC の IP アドレスを取得します。

   コマンドの出力の例を次に示します。

   ![Intel NUC IP を示している ifconfig の出力](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   この例では、`inet addr:` に続く値が、ホスト コンピューターから Intel NUC に接続するときに必要な IP アドレスです。

3. 次の SSH クライアントのいずれかを使用して、ホスト コンピューターから Intel NUC に接続します。

    - Windows では [PuTTY](http://www.putty.org/)。
    - Ubuntu または macOS では組み込みの SSH クライアント。

   効率的で生産性が高いのは、ホスト コンピューターから Intel NUC を操作することです。 SSH クライアント経由で Intel NUC に接続するには、その Intel NUC の IP アドレス、ユーザー名、およびパスワードが必要です。 macOS での SSH クライアントの使用例を次に示します。
   ![MacOS で実行中の SSH クライアント](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>Azure IoT Gateway SDK パッケージのインストール

Azure IoT Gateway SDK パッケージには、SDK のコンパイル済みバイナリと、依存関係にあるコンパイル済みのバイナリが含まれます。 これらのバイナリは、Azure IoT Gateway SDK、Azure IoT SDK、および対応ツールです。 パッケージには、ゲートウェイの機能を検証するために使用する "hello_world" サンプル アプリケーションも含まれます。 この SDK がゲートウェイの中核です。 

パッケージをインストールするには、次の手順に従います。

1. ターミナル ウィンドウで、次のコマンドを実行して、IoT クラウド リポジトリを追加します。

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   `rpm` コマンドは、rpm キーをインポートします。 `smart channel` コマンドは、rpm チャネルを Smart Package Manager に追加します。 `smart update` コマンドを実行する前に、次のような出力が表示されます。

   ![rpm コマンドと smart channel コマンドの出力](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

2. smart update コマンドを実行します。

   ```bash
   smart update
   ```

3. 次のコマンドを実行して、Azure IoT Gateway パッケージをインストールします。

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` はパッケージの名前です。 `smart install` コマンドを使用して、パッケージをインストールします。


    > "公開キーが使用できない" ことを示すエラーが表示される場合は、次のコマンドを実行します。

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```
   
   パッケージをインストールしたら、Intel NUC はいつでもゲートウェイとして機能します。

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>Azure IoT Gateway SDK の "hello_world" サンプル アプリケーションの実行

次のサンプル アプリケーションは、`hello_world.json` ファイルからゲートウェイを作成し、Azure IoT Gateway SDK アーキテクチャの基本コンポーネントを使用して、5 秒ごとに hello world メッセージをファイル (log.txt) に記録します。

Hello World サンプルを実行するには、次のコマンドを実行します。

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

Hello World アプリケーションを数分実行してから、Enter キーを押して、そのアプリケーションを停止します。
![アプリケーションの出力](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

> Enter キーを押した後に表示される "invalid argument handle(NULL)" エラーはすべて無視できます。

ゲートウェイが正常に実行されたことを確認するには、hello_world フォルダーの log.txt ファイルを開きます。![log.txt ディレクトリ ビュー](media/iot-hub-gateway-kit-lessons/lesson1/logtxtdir.png)

次のコマンドを使用して log.txt を開きます。

```bash
vim log.txt
```

log.txt のコンテンツが表示されます。これは、ゲートウェイの Hello World モジュールによって 5 秒ごとに記述された、ログ メッセージの JSON 形式の出力になります。
![log.txt ディレクトリ ビュー](media/iot-hub-gateway-kit-lessons/lesson1/logtxtview.png)

問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="summary"></a>まとめ

ご利用ありがとうございます。 Intel NUC をゲートウェイとしてセットアップする操作が完了しました。 これで、次のレッスンに進んで、ホスト コンピューターをセットアップし、Azure IoT Hub を作成し、Azure IoT Hub の論理デバイスを登録することができます。

## <a name="next-steps"></a>次のステップ
[ホスト コンピューターと Azure IoT Hub の準備](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)

