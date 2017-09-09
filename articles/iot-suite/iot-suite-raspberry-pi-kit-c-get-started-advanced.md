---
title: "C を使用して Raspberry Pi を Azure IoT Suite に接続しファームウェアを更新する | Microsoft Docs"
description: "Raspberry Pi 3 と Azure IoT Suite に対応した Microsoft Azure IoT スタート キットを使用します。 C を使用して Raspberry Pi をリモート監視ソリューションに接続し、センサーから受信したテレメトリをクラウドに送信して、リモート ファームウェア更新を実行します。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 971f23a01b53ed6d7d19438567392e0b43b57120
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017

---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-c"></a>C を使用して Raspberry Pi 3 をリモート監視ソリューションに接続し、リモート ファームウェア更新を有効にする

[!INCLUDE [iot-suite-raspberry-pi-kit-selector](../../includes/iot-suite-raspberry-pi-kit-selector.md)]

このチュートリアルでは、Raspberry Pi 3 に対応した Microsoft Azure IoT スタート キットを使用して以下を行う方法について説明します。

* クラウドと通信できる、気温・湿度リーダーを作成します。
* リモート ファームウェア更新を有効にして実行し、Raspberry Pi 上のクライアント アプリケーションを更新します。

このチュートリアルでは以下を使用します。

* サンプル デバイスを実装するための Raspbian OS、C プログラミング言語、C 用 Microsoft Azure IoT SDK。
* クラウド ベース バックエンドとしての IoT Suite リモート監視構成済みソリューション。

## <a name="overview"></a>概要

このチュートリアルでは、次の手順を実行します。

* リモート監視構成済みソリューションのインスタンスを Azure サブスクリプションにデプロイします。 この手順では、複数の Azure サービスが自動的にデプロイおよび構成されます。
* お使いのコンピューターやリモート監視ソリューションと通信するようにデバイスとセンサーを設定します。
* サンプル デバイス コードを更新することでリモート監視ソリューションに接続し、テレメトリを送信してソリューションのダッシュボードに表示できるようにします。
* サンプル デバイス コードを使用して、クライアント アプリケーションを更新します。

[!INCLUDE [iot-suite-raspberry-pi-kit-prerequisites](../../includes/iot-suite-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> リモート監視ソリューションによって、Azure サブスクリプションの Azure サービスのセットがプロビジョニングされます。 デプロイによって現実のエンタープライズ アーキテクチャが反映されます。 不要な Azure 使用料金が発生しないよう、作業が終わったら azureiotsuite.com にある構成済みソリューションのインスタンスを削除します。 構成済みソリューションがもう一度必要になった場合は、簡単に再作成できます。 リモート監視ソリューション実行中の使用料金を削減する方法の詳細については、「[Configuring Azure IoT Suite preconfigured solutions for demo purposes (デモ用に Azure IoT Suite 構成済みソリューションを構成する)][lnk-demo-config]」をご覧ください。

[!INCLUDE [iot-suite-raspberry-pi-kit-view-solution](../../includes/iot-suite-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

Raspberry Pi のリモート監視クライアント アプリケーションをダウンロードして構成できます。

### <a name="clone-the-repositories"></a>リポジトリの複製

まだリポジトリを複製していない場合は、Pi 上で次のコマンドを実行して必要なリポジトリを複製します。

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>デバイスの接続文字列を更新する

次のコマンドを使用して、**nano** エディターのサンプル構成ファイルを開きます。

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

プレースホルダーの値を、このチュートリアルの最初で作成し保存したデバイス ID と IoT Hub 情報に置き換えます。

完了すると、Deviceinfo ファイルの内容は次の例のようになります。

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

変更を保存し (**Ctrl + O** キー、**Enter** キー)、エディターを終了します (**Ctrl + X** キー)。

## <a name="build-the-sample"></a>サンプルをビルドする

C 用 Microsoft Azure IoT device SDK の前提条件となるパッケージをまだインストールしていない場合は、Raspberry Pi 上のターミナルで次のコマンドを実行してインストールします。

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Raspberry Pi 上にサンプル ソリューションをビルドできます。

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
```

Raspberry Pi 上でサンプル プログラムを実行できます。 次のコマンドを入力します。

  ```sh
  sudo ~/cmake/remote_monitoring/remote_monitoring
  ```

次のサンプル出力は、Raspberry Pi 上のコマンド プロンプトに表示される出力の例です。

![Raspberry Pi アプリからの出力][img-raspberry-output]

**Ctrl + C** キーを押してプログラムを終了します (終了のタイミングは任意)。

[!INCLUDE [iot-suite-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-raspberry-pi-kit-view-telemetry-advanced.md)]

1. ソリューションのダッシュボードで、**[デバイス]** をクリックし、**[デバイス]** ページにアクセスします。 **[デバイス一覧]** で Raspberry Pi を選択します。 次に、**[方法]** を選択します。

    ![ダッシュボードでデバイスを一覧表示する][img-list-devices]

1. **[メソッドの呼び出し]** ページで、**[方法]** ドロップダウンの **[InitiateFirmwareUpdate]** を選択します。

1. **[FWPackageURI]** フィールドで、「 **https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit/raw/master/advanced/2.0/package/remote_monitoring.zip** 」と入力します。 このアーカイブ ファイルには、ファームウェアのバージョン 2.0 の実装が含まれています。

1. **[InvokeMethod]** を選択します。 Raspberry Pi 上のアプリは、ソリューション ダッシュボードに受信確認を送信します。 次に、ファームウェアの新しいバージョンをダウンロードしてファームウェアの更新プロセスが開始します。

    ![メソッドの履歴を表示する][img-method-history]

## <a name="observe-the-firmware-update-process"></a>ファームウェアの更新プロセスを監視する

ファームウェア更新プロセスは、デバイス上での実行中にソリューション ダッシュボードに報告されたプロパティを確認することで、監視できます。

1. Raspberry Pi 上の更新プロセスの進行状況を確認できます。

    ![更新の進行状況を表示する][img-update-progress]

    > [!NOTE]
    > 更新が完了すると、リモート監視アプリがサイレント モードで再起動します。 `ps -ef` コマンドを使用して、アプリが実行されていることを確認してください。 プロセスを終了する場合は、`kill` コマンドとプロセス ID を使用します。

1. ソリューション ポータルで、デバイスから報告されるファームウェア更新の状態を確認できます。 次のスクリーンショットでは、更新プロセスの各ステージの状態と期間、新しいファームウェアのバージョンなどを示しています。

    ![ジョブの状態を表示する][img-job-status]

    ダッシュボードに戻ると、ファームウェア更新プログラムに従ってデバイスがまだテレメトリを送信していることを確認できます。

> [!WARNING]
> Azure アカウントでリモート監視ソリューションを実行したままにしておくと、実行時間分が課金されます。 リモート監視ソリューション実行中の使用料金を削減する方法の詳細については、「[Configuring Azure IoT Suite preconfigured solutions for demo purposes (デモ用に Azure IoT Suite 構成済みソリューションを構成する)][lnk-demo-config]」をご覧ください。 ソリューションの使用を終了するときに、Azure アカウントから構成済みソリューションを削除してください。

## <a name="next-steps"></a>次のステップ

Azure IoT のその他のサンプルとドキュメントについては、「[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot/)」をご覧ください。


[img-raspberry-output]: ./media/iot-suite-raspberry-pi-kit-c-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-raspberry-pi-kit-c-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-raspberry-pi-kit-c-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-raspberry-pi-kit-c-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-raspberry-pi-kit-c-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
