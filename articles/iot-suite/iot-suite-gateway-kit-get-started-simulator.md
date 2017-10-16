---
title: "Intel NUC を使用して Azure IoT Suite にゲートウェイを接続する| Microsoft Docs"
description: "Microsoft IoT 商用ゲートウェイ キットとリモート監視構成済みソリューションを使用します。 Azure IoT Edge ゲートウェイを使用して、リモート監視ソリューションに接続し、シミュレートされたテレメトリをクラウドに送信し、ソリューション ダッシュボードから呼び出されたメソッドに応答できるようにします。"
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
ms.openlocfilehash: 9ed57d3c23e2adbd42c054f33c8ed46e3d6c9792
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>Azure IoT Edge ゲートウェイをリモート監視構成済みソリューションに接続し、シミュレートされたテレメトリを送信する

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

このチュートリアルでは、Azure IoT Edge を使用して、シミュレートされた温度と湿度のデータをリモート監視構成済みソリューションに送信する方法を示します。 このチュートリアルでは以下を使用します。

- サンプル ゲートウェイを実装するための Azure IoT Edge。
- クラウド ベース バックエンドとしての IoT Suite リモート監視構成済みソリューション。

## <a name="overview"></a>概要

このチュートリアルでは、次の手順を実行します。

- リモート監視構成済みソリューションのインスタンスを Azure サブスクリプションにデプロイします。 この手順では、複数の Azure サービスが自動的にデプロイおよび構成されます。
- コンピューターとリモート監視ソリューションと通信するように Intel NUC ゲートウェイ デバイスを設定します。
- ソリューション ダッシュボードに表示できるシミュレートされたテレメトリを送信するように IoT Edge ゲートウェイを構成します。

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> リモート監視ソリューションによって、Azure サブスクリプションの Azure サービスのセットがプロビジョニングされます。 デプロイによって現実のエンタープライズ アーキテクチャが反映されます。 不要な Azure 使用料金が発生しないよう、作業が終わったら azureiotsuite.com にある構成済みソリューションのインスタンスを削除します。 構成済みソリューションがもう一度必要になった場合は、簡単に再作成できます。 リモート監視ソリューション実行中の使用料金を削減する方法の詳細については、「[Configuring Azure IoT Suite preconfigured solutions for demo purposes (デモの目的で Azure IoT Suite 構成済みソリューションを構成する)][lnk-demo-config]」をご覧ください。

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

前の手順を繰り返して、2 つ目のデバイスを追加します。デバイス ID には **device02** などを使用します。 このサンプルでは、ゲートウェイの 2 つのシミュレートされたデバイスからリモート監視ソリューションにデータを送信します。

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

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
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

このビルド スクリプトは、libsimulator.so というカスタム IoT Edge モジュールを build フォルダーに配置します。

## <a name="configure-and-run-the-iot-edge-gateway"></a>IoT Edge ゲートウェイを構成して実行する

次に、シミュレートされたテレメトリをリモート監視ダッシュボードに送信するように IoT Edge ゲートウェイを構成できます。 ゲートウェイと IoT Edge モジュールの構成の詳細については、[Azure IoT Edge の概念][lnk-gateway-concepts]に関するページを参照してください。

> [!TIP]
> このチュートリアルでは、Intel NUC の標準的な `vi` テキスト エディターを使用しています。 `vi` の使用経験がない場合は、入門チュートリアル ([Unix - The vi Editor Tutorial (Unix - vi エディターのチュートリアル)][lnk-vi-tutorial]など) を完了して、エディターを理解しておく必要があります。 または、`smart install nano -y` コマンドを使用して、ユーザー フレンドリーな [nano](https://www.nano-editor.org/) エディターをインストールできます。

次のコマンドを使用して、**vi** エディターでサンプル構成ファイルを開きます。

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
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
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

プレースホルダーの **deviceID** と **deviceKey** を、前にリモート管理ソリューションで作成した 2 台のデバイスの ID とキーに置き換えます。

変更を保存します。

次のコマンドを使用して、IoT Edge ゲートウェイを実行できます。

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

Intel NUC 上で IoT Edge ゲートウェイが起動し、シミュレートされたテレメトリをリモート監視ソリューションに送信します。

![IoT Edge ゲートウェイはシミュレートされたテレメトリを生成する][img-simulated telemetry]

**Ctrl + C** キーを押してプログラムを終了します (終了のタイミングは任意)。

## <a name="view-the-telemetry"></a>テレメトリを表示する

これで、IoT Edge ゲートウェイからリモート監視ソリューションにテレメトリが送信されるようになりました。 テレメトリはソリューション ダッシュボードで確認できます。

- ソリューション ダッシュボードに移動します。
- **[表示するデバイス]** ドロップダウンから、ゲートウェイに構成した 2 つのデバイスのどちらかを選択します。
- ゲートウェイ デバイスから送信されたテレメトリがダッシュボードに表示されます。

![シミュレートされたゲートウェイ デバイスから送信されたテレメトリの表示][img-telemetry-display]

> [!WARNING]
> Azure アカウントでリモート監視ソリューションを実行したままにしておくと、実行時間分が課金されます。 リモート監視ソリューション実行中の使用料金を削減する方法の詳細については、「[Configuring Azure IoT Suite preconfigured solutions for demo purposes (デモの目的で Azure IoT Suite 構成済みソリューションを構成する)][lnk-demo-config]」をご覧ください。 ソリューションの使用を終了するときに、Azure アカウントから構成済みソリューションを削除してください。

## <a name="next-steps"></a>次のステップ

Azure IoT のその他のサンプルとドキュメントについては、「[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot/)」をご覧ください。

[img-simulated telemetry]: ./media/iot-suite-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started