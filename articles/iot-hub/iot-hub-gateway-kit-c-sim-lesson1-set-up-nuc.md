---
title: "シミュレートされたデバイスと Azure IoT Gateway - レッスン 1: NUC の設定 | Microsoft Docs"
description: "センサー情報を収集して IoT Hub に送信するために、Intel NUC をセンサーと Azure IoT Hub 間の IoT ゲートウェイとして機能するようにセットアップします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: "IoT ゲートウェイ, Intel NUC, NUC コンピューター, DE3815TYKE"
ms.assetid: f41d6b2e-9b00-40df-90eb-17d824bea883
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 399ac2d571b65503da7d9cc47d2dec9aa5e4c3d7
ms.lasthandoff: 01/25/2017


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>IoT ゲートウェイとしての Intel NUC のセットアップ

## <a name="what-you-will-do"></a>学習内容

- Intel NUC を IoT ゲートウェイとしてセットアップします。
- Azure IoT Gateway SDK パッケージを Intel NUC にインストールします。
- Intel NUC で "hello_world" サンプル アプリケーションを実行して、ゲートウェイの機能を検証します。
問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-sim-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容

このレッスンでは、次の内容を学習します。

- Intel NUC と周辺機器を接続する方法。
- Smart Package Manager を使用して、必要なパッケージを Intel NUC にインストールしてアップデートする方法。
- "hello_world" サンプルアプリケーションを実行してゲートウェイの機能を検証する方法。

## <a name="what-you-need"></a>必要なもの

- Intel IoT Gateway Software Suite (Wind River Linux *7.0.0.13) がプレインストールされた Intel NUC Kit DE3815TYKE。
- イーサネット ケーブル。
- キーボード。
- HDMI または VGA ケーブル。
- HDMI または VGA ポート付きモニター。

![ゲートウェイ キット](media/iot-hub-gateway-kit-lessons/lesson1/kit_without_sensortag.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Intel NUC と周辺機器の接続

次の画像は、さまざまな周辺機器が接続された Intel NUC の例を示しています。

1. キーボードに接続されています。
2. VGA ケーブルまたは HDMI ケーブルでモニターに接続されています。
3. イーサネット ケーブルで有線ネットワークに接続されています。
4. 電源ケーブルで電源に接続されています。

![周辺機器に接続されている Intel NUC](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>ホスト コンピューターからの Secure Shell (SSH) を使用した Intel NUC システムへの接続

ここでは、NUC デバイスの IP アドレスを取得するために、キーボードとモニターが必要です。 IP アドレスがわかっている場合は、このセクションの手順 3 に進むことができます。

1. 電源ボタンを押して Intel NUC をオンにし、システムにログインします。

   既定のユーザー名とパスワードは、どちらも `root` です。

2. `ifconfig` コマンドを実行して、NUC の IP アドレスを取得します。 この手順は、NUC デバイスで実行されます。

   コマンドの出力の例を次に示します。

   ![NUC IP を示している ifconfig の出力](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   この例では、`inet addr:` に続く値が、ホスト コンピューターからリモートで Intel NUC に接続するときに必要な IP アドレスです。

3. 次の SSH クライアントのいずれかを使用して、ホスト コンピューターから Intel NUC に接続します。

   - Windows では [PuTTY](http://www.putty.org/)。
   - Ubuntu または macOS では組み込みの SSH クライアント。

   効率的で生産性が高いのは、ホスト コンピューターから Intel NUC を操作することです。 SSH クライアント経由で NUC に接続するには、IP アドレス、ユーザー名、およびパスワードが必要です。 MacOS での SSH クライアントの使用例を次に示します。
   ![MacOS で実行中の SSH クライアント](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>Azure IoT Gateway SDK パッケージのインストール

Azure IoT Gateway SDK パッケージには、SDK のコンパイル済みバイナリと、依存関係にあるコンパイル済みのバイナリが含まれます。 これらのバイナリは、Azure IoT Gateway SDK、Azure IoT SDK、および対応ツールです。 パッケージには、ゲートウェイの機能を検証するために使用する "hello_world" サンプル アプリケーションも含まれます。 この SDK がゲートウェイの中核です。 このパッケージをインストールするには、次の手順を実行します。

1. ターミナル ウィンドウで、次のコマンドを実行して、IoT クラウド リポジトリを追加します。

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   `rpm` コマンドは、rpm キーをインポートします。 `smart channel` コマンドは、rpm チャネルを Smart Package Manager に追加します。 `smart update` コマンドを実行する前に、次のような出力が表示されます。

   ![rpm コマンドと smart channel コマンドの出力](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

   ```bash
   smart update
   ```

2. 次のコマンドを実行して、パッケージをインストールします。

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` はパッケージの名前です。 `smart install` コマンドを使用して、パッケージをインストールします。

   パッケージのインストール後に、Intel NUC はゲートウェイとして機能することが期待されます。

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>Azure IoT Gateway SDK の "hello_world" サンプル アプリケーションの実行

`azureiotgatewaysdk/samples` に移動し、"hello_world" サンプル アプリケーションを実行します。 このサンプル アプリケーションは、`hello_world.json` ファイルからゲートウェイを作成し、Azure IoT Gateway SDK アーキテクチャの基本コンポーネントを使用して、5 秒ごとに "hello world" メッセージをファイルに記録します。

"hello_world" サンプル アプリケーションは、次のコマンドを実行することで実行できます。

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

ゲートウェイが正常に機能していれば、サンプル アプリケーションは次の出力を生成します。

![アプリケーションの出力](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

問題が発生した場合は、[トラブルシューティングのページ](iot-hub-gateway-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="summary"></a>まとめ

ご利用ありがとうございます。 Intel NUC をゲートウェイとしてセットアップする操作が完了しました。 これで、次のレッスンに進んで、ホスト コンピューターをセットアップし、Azure IoT ハブを作成し、Azure IoT ハブの論理デバイスを登録することができます。

## <a name="next-steps"></a>次のステップ
[ホスト コンピューターと Azure IoT ハブの準備](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)

