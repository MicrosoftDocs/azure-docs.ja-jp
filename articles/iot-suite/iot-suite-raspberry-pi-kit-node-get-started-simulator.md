---
title: "Node.js を使用して Raspberry Pi を Azure IoT Suite に接続し、シミュレートされたテレメトリを送信する | Microsoft Docs"
description: "Raspberry Pi 3 と Azure IoT Suite に対応した Microsoft Azure IoT スタート キットを使用します。 Node.js を使用して Raspberry Pi をリモート監視ソリューションに接続し、シミュレートされたテレメトリをクラウドに送信して、ソリューション ダッシュボードから呼び出されたメソッドに応答します。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: node.js
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 3d41a3665ffb2217ecbe1ad53853f0e9d30fac34
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017

---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-nodejs"></a>Node.js を使用して Raspberry Pi 3 をリモート監視ソリューションに接続し、シミュレートされたテレメトリを送信する

[!INCLUDE [iot-suite-raspberry-pi-kit-selector](../../includes/iot-suite-raspberry-pi-kit-selector.md)]

このチュートリアルでは、Raspberry Pi 3 を使用してシミュレートした気温・湿度データをクラウドに送信する方法について説明します。 このチュートリアルでは以下を使用します。

- サンプル デバイスを実装するための Raspbian OS、Node.js プログラミング言語、Node.js 用 Microsoft Azure IoT SDK。
- クラウド ベース バックエンドとしての IoT Suite リモート監視構成済みソリューション。

[!INCLUDE [iot-suite-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> リモート監視ソリューションによって、Azure サブスクリプションの Azure サービスのセットがプロビジョニングされます。 デプロイによって現実のエンタープライズ アーキテクチャが反映されます。 不要な Azure 使用料金が発生しないよう、作業が終わったら azureiotsuite.com にある構成済みソリューションのインスタンスを削除します。 構成済みソリューションがもう一度必要になった場合は、簡単に再作成できます。 リモート監視ソリューション実行中の使用料金を削減する方法の詳細については、「[Configuring Azure IoT Suite preconfigured solutions for demo purposes (デモの目的で Azure IoT Suite 構成済みソリューションを構成する)][lnk-demo-config]」をご覧ください。

[!INCLUDE [iot-suite-raspberry-pi-kit-view-solution](../../includes/iot-suite-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-raspberry-pi-kit-prepare-pi-simulator.md)]

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

Raspberry Pi のリモート監視クライアント アプリケーションをダウンロードして構成できます。

### <a name="install-nodejs"></a>Node.js のインストール

まだ Node.js をインストールしていない場合は、Raspberry Pi にインストールします。 Node.js 用 IoT SDK には、Node.js バージョン 0.11.5 以降が必要です。 次の手順では、Node.js v6.10.2 を Raspberry Pi にインストールする方法を示しています。

1. 次のコマンドを使用して Raspberry Pi を更新します。

    ```sh
    sudo apt-get update
    ```

1. 次のコマンドを使用して、Node.js のバイナリを Raspberry Pi にダウンロードします。

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. 次のコマンドを使用して、バイナリをインストールします。

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. 次のコマンドを使用して、Node.js v6.10.2 が正常にインストールされたことを確認します。

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>リポジトリの複製

まだレポジトリを複製していない場合は、Pi 上のターミナルで次のコマンドを実行して必要なレポジトリを複製します。

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>デバイスの接続文字列を更新する

次のコマンドを使用して、**nano** エディターのサンプル ソース ファイルを開きます。

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

次の行を見つけます。

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

プレースホルダーの値を、このチュートリアルの最初で作成し保存したデバイスと IoT Hub の情報に置き換えます。 変更を保存し (**Ctrl + O** キー、**Enter** キー)、エディターを終了します (**Ctrl + X** キー)。

## <a name="run-the-sample"></a>サンプルの実行

次のコマンドを実行して、サンプルの前提条件となるパッケージをインストールします。

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator
npm install
```

Raspberry Pi 上でサンプル プログラムを実行できます。 次のコマンドを入力します。

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

次のサンプル出力は、Raspberry Pi 上のコマンド プロンプトに表示される出力の例です。

![Raspberry Pi アプリからの出力][img-raspberry-output]

**Ctrl + C** キーを押してプログラムを終了します (終了のタイミングは任意)。

[!INCLUDE [iot-suite-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>次のステップ

Azure IoT のその他のサンプルとドキュメントについては、「[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot/)」をご覧ください。

[img-raspberry-output]: ./media/iot-suite-raspberry-pi-kit-node-get-started-simulator/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

