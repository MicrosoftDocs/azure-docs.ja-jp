---
title: "Intel NUC を使用して Azure IoT Suite にゲートウェイを接続する| Microsoft Docs"
description: "Microsoft IoT 商用ゲートウェイ キットとリモート監視構成済みソリューションを使用します。 Azure IoT Edge ゲートウェイを使用して、SensorTag デバイスがリモート監視ソリューションに接続し、クラウドにテレメトリを送信し、ソリューション ダッシュボードから呼び出されたメソッドに応答できるようにします。"
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
ms.date: 07/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: e97f22340f869ef31d7b4c7b9857bf0d1d5400c5
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017

---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-telemetry-from-a-sensortag"></a>Azure IoT Edge ゲートウェイをリモート監視構成済みソリューションに接続し、SensorTag からテレメトリを送信します

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

このチュートリアルでは、Azure IoT Edge を使用して、温度と湿度のデータを SensorTag デバイスからリモート監視構成済みソリューションに送信する方法を示します。 SensorTag は、Bluetooth を使用して Intel NUC ゲートウェイに接続します。 このチュートリアルでは以下を使用します。

- サンプル ゲートウェイを実装するための Azure IoT Edge。
- クラウド ベース バックエンドとしての IoT Suite リモート監視構成済みソリューション。

## <a name="overview"></a>概要

このチュートリアルでは、次の手順を実行します。

- リモート監視構成済みソリューションのインスタンスを Azure サブスクリプションにデプロイします。 この手順では、複数の Azure サービスが自動的にデプロイおよび構成されます。
- コンピューターとリモート監視ソリューションと通信するように Intel NUC ゲートウェイ デバイスを設定します。
- SensorTag デバイスからテレメトリを受信し、リモート監視ダッシュボードに送信するように Intel NUC ゲートウェイを設定します。

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[Texas Instruments BLE SensorTag][lnk-sensortag]。 このチュートリアルでは、SensorTag デバイスから Bluetooth 経由でテレメトリ データを取得します。

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> リモート監視ソリューションによって、Azure サブスクリプションの Azure サービスのセットがプロビジョニングされます。 デプロイによって現実のエンタープライズ アーキテクチャが反映されます。 不要な Azure 使用料金が発生しないよう、作業が終わったら azureiotsuite.com にある構成済みソリューションのインスタンスを削除します。 構成済みソリューションがもう一度必要になった場合は、簡単に再作成できます。 リモート監視ソリューション実行中の使用料金を削減する方法の詳細については、「[Configuring Azure IoT Suite preconfigured solutions for demo purposes (デモの目的で Azure IoT Suite 構成済みソリューションを構成する)][lnk-demo-config]」をご覧ください。

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

## <a name="configure-bluetooth-connectivity"></a>Bluetooth 接続を構成する

SensorTag デバイスが接続してテレメトリを送信できるように、Intel NUC で Bluetooth を構成します。

### <a name="find-the-mac-address-of-the-sensortag"></a>SensorTag の MAC アドレスを見つける

1. Intel NUC のシェルで、次のコマンドを実行して、Bluetooth サービスのブロックを解除します。

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. 次のコマンドを実行して Intel NUC 上で Bluetooth サービスを開始し、Bluetooth シェルに入ります。

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. 次のコマンドを実行して、Bluetooth コント ローラーの電源をオンにします。

    ```bash
    power on
    ```

    コントローラーがオンになると、**電源オンへの変更が成功した**ことを示すメッセージが表示されます。

1. 次のコマンドを実行して、近くの Bluetooth デバイスをスキャンします。

    ```bash
    scan on
    ```

1. SensorTag の電源ボタンを押して、SensorTag を検出できるようにします。 緑色の LED が点滅します。

1. コントローラーが SensorTag を検出したことを示すメッセージがシェルに表示されたら、デバイスの MAC アドレスを書き留めます。 MAC アドレスは **A0:E6:F8:B5:F6:00** のようになります。 この MAC アドレスは、チュートリアルの後半でゲートウェイを構成するときに必要です。

1. 次のコマンドを実行して、Bluetooth のスキャンをオフにします。

    ```bash
    scan off
    ```

1. 次のコマンドを実行して、SensorTag デバイスに接続できることを確認します。

    ```bash
    connect <SensorTag MAC address>
    ```

    正常に接続している場合は、**接続成功**を示すメッセージがシェルに表示され、SensorTag デバイスに関する情報が出力されます。 接続できない場合は、SensorTag の電源がまだオンであることを確認してください。

1. 次のコマンドを実行して、SensorTag から切断し、Bluetooth シェルを終了します。

    ```bash
    disconnect
    exit
    ```

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>カスタムの IoT Edge モジュールをビルドする

次に、ゲートウェイがリモート監視ソリューションにメッセージを送信できるようにするカスタムの IoT エッジ モジュールをビルドできます。 ゲートウェイと IoT Edge モジュールの構成の詳細については、[Azure IoT Edge の概念][lnk-gateway-concepts]に関するページを参照してください。

次のコマンドを使用して、GitHub からカスタムの IoT エッジ モジュールのソース コードをダウンロードします。

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

次のコマンドを使用して、カスタムの IoT Edge モジュールを作成します。

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

このビルド スクリプトは、libsensor2remotemonitoring.so というカスタム IoT Edge モジュールを build フォルダーに配置します。

## <a name="configure-and-run-the-iot-edge-gateway"></a>IoT Edge ゲートウェイを構成して実行する

次に、SensorTag デバイスからリモート監視ダッシュボードにテレメトリを送信するように IoT Edge ゲートウェイを構成できます。 ゲートウェイと IoT Edge モジュールの構成の詳細については、[Azure IoT Edge の概念][lnk-gateway-concepts]に関するページを参照してください。

> [!TIP]
> このチュートリアルでは、Intel NUC の標準的な `vi` テキスト エディターを使用しています。 `vi` の使用経験がない場合は、入門チュートリアル ([Unix - The vi Editor Tutorial (Unix - vi エディターのチュートリアル)][lnk-vi-tutorial]など) を完了して、エディターを理解しておく必要があります。 または、`smart install nano -y` コマンドを使用して、ユーザー フレンドリーな [nano](https://www.nano-editor.org/) エディターをインストールできます。

次のコマンドを使用して、**vi** エディターでサンプル構成ファイルを開きます。

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic/remote_monitoring.json
```

構成内の IoTHub モジュール用の次の行を見つけます。

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

プレースホルダーの値を、このチュートリアルの開始時に作成して保存した IoT Hub の情報に置き換えます。 IoTHubName の値は **yourrmsolution37e08** のようになり、IoTSuffix の値は通常は **azure-devices.net** です。

構成内のマッピング モジュール用の次の行を見つけます。

```json
args": [
  {
    "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

**macAddress** プレース ホルダを、先ほど書き留めておいた SensorTag の MAC アドレスに置き換えます。 プレースホルダーの **deviceID** と **deviceKey** を、前にリモート管理ソリューションで作成した 2 台のデバイスの ID とキーに置き換えます。

構成内の SensorTag モジュール用の次の行を見つけます。

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  ...
}
```

**device\_mac\_address** プレース ホルダを、先ほど書き留めておいた SensorTag の MAC アドレスに置き換えます。

変更を保存します。

次のコマンドを使用して、ゲートウェイを実行できます。

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
/usr/share/azureiotgatewaysdk/samples/ble_gateway/ble_gateway remote_monitoring.json
```

Intel NUC 上で IoT Edge ゲートウェイが起動し、SensorTag からリモート監視ソリューションにテレメトリを送信します。

![IoT Edge ゲートウェイが SensorTag からテレメトリを送信している][img-telemetry]

**Ctrl + C** キーを押してプログラムを終了します (終了のタイミングは任意)。

## <a name="view-the-telemetry"></a>テレメトリを表示する

現在、IoT Edge ゲートウェイは、SensorTag からテレメトリを送信しています。 テレメトリはソリューション ダッシュボードで確認できます。 ソリューション ダッシュボードからゲートウェイを介して SensorTag デバイスにコマンドを送信することもできます。

- ソリューション ダッシュボードに移動します。
- **[表示するデバイス]** ドロップダウンから、ゲートウェイに構成した SensorTag を表すデバイスを選択します。
- SensorTag から送信されたテレメトリがダッシュボードに表示されます。

![SensorTag から送信されたテレメトリの表示][img-telemetry-display]

> [!WARNING]
> Azure アカウントでリモート監視ソリューションを実行したままにしておくと、実行時間分が課金されます。 リモート監視ソリューション実行中の使用料金を削減する方法の詳細については、「[Configuring Azure IoT Suite preconfigured solutions for demo purposes (デモの目的で Azure IoT Suite 構成済みソリューションを構成する)][lnk-demo-config]」をご覧ください。 ソリューションの使用を終了するときに、Azure アカウントから構成済みソリューションを削除してください。


## <a name="next-steps"></a>次のステップ

Azure IoT のその他のサンプルとドキュメントについては、「[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot/)」をご覧ください。

[img-telemetry]: ./media/iot-suite-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-sensortag/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started
