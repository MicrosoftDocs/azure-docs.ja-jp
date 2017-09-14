---
title: "Azure IoT Edge を使用してデバイスをシミュレートする (Windows) | Microsoft Docs"
description: "Windows 上で Azure IoT Edge を使用して、Azure IoT Edge ゲートウェイを介して IoT Hub にテレメトリを送信するシミュレートされたデバイスを作成する方法。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/09/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: e7eb2931993daf3f0aecbd4a43d27ebd5adc10b0
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017


---

# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Azure IoT Edge を使用して、シミュレートされたデバイスに D2C メッセージを送信する (Windows)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="how-to-run-the-sample"></a>サンプルを実行する方法

**build.cmd** スクリプトは、**iot-edge** リポジトリのローカル コピーの **build** フォルダーに出力を生成します。 この出力には、このサンプルで使用する 4 つの IoT Edge モジュールが含まれています。

ビルド スクリプトにより、次のファイルとフォルダーが配置されます。

* **logger.dll** が **build\\modules\\logger\\Debug** フォルダーに配置されます。
* **iothub.dll** が **build\\modules\\iothub\\Debug** フォルダーに配置されます。
* **identity\_map.dll** が **build\\modules\\identitymap\\Debug** フォルダーに配置されます。
* **simulated\_device.dll** が **build\\modules\\simulated\_device\\Debug** フォルダーに配置されます。

次の JSON 設定ファイルに示されているように、**モジュール パス**の値には、これらのパスを使用します。

simulated\_device\_cloud\_upload\_sample プロセスは、コマンド ラインの引数として JSON 構成ファイルのパスを取ります。 次のサンプル JSON ファイルは SDK リポジトリの **samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_sample\_win.json** にあります。 既定以外の場所に IoT Edge モジュールまたはサンプルの実行可能ファイルを配置するようにビルド スクリプトを変更していない限り、この構成ファイルはそのままで動作します。

> [!NOTE]
> モジュール パスは、simulated\_device\_cloud\_upload\_sample.exe が配置されているディレクトリに対して相対的です。 サンプルの JSON 構成ファイルは、既定で現在の作業ディレクトリの 'deviceCloudUploadGatewaylog.log' に書き込みます。

テキスト エディターで、**iot-edge** レポジトリのローカル コピーの **samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_win.json** ファイルを開きます。 このファイルは、サンプル ゲートウェイの IoT Edge モジュールを構成します。

* **IoTHub** モジュールは、IoT Hub に接続します。 データを IoT Hub に送信するようにモジュールを構成します。 具体的には、**IoTHubName** 値を実際の IoT Hub の名前に設定し、**IoTHubSuffix** 値を **azure-devices.net** に設定します。 **Transport** の値を **HTTP**、**AMQP**、または **MQTT** のいずれかに設定します。 現在、すべてのデバイス メッセージで 1 つの TCP 接続を共有するのは **HTTP** のみです。 値を **AMQP** または **MQTT** に設定すると、ゲートウェイは各デバイスで IoT Hub に対する TCP 接続を別個に維持します。
* **mapping** モジュールは、シミュレートされたデバイスの MAC アドレスを IoT Hub のデバイス ID にマップします。 **deviceId** 値が IoT Hub に追加した 2 つのデバイスの ID と一致し、**deviceKey** 値に 2 つのデバイスのキーが含まれていることを確認します。
* **BLE1** モジュールと **BLE2** モジュールは、シミュレートされたデバイスです。 モジュールの MAC アドレスが **mapping** モジュールの MAC アドレスとどのように一致しているかに注意してください。
* **Logger** モジュールは、ゲートウェイのアクティビティをファイルに記録します。
* **モジュール パス**は、simulated\_device\_cloud\_upload\_sample.exe が配置されているディレクトリに対して相対的です。
* JSON ファイルの下部にある **links** 配列は、**BLE1** モジュールと **BLE2** モジュールを **mapping** モジュールに、**mapping** モジュールを **IoTHub** モジュールに接続します。 また、すべてのメッセージが **Logger** モジュールによってログに記録されます。

```json
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01"
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02"
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

構成ファイルに加えた変更を保存します。

サンプルを実行するには:

1. コマンド プロンプトで、**iot-edge** レポジトリのローカル コピーの **build** フォルダーに移動します。
2. 次のコマンドを実行します。
   
    ```cmd
    samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe ..\samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. [デバイス エクスプローラー][lnk-device-explorer]または [iothub-explorer][lnk-iothub-explorer] ツールを使用して、IoT Hub がゲートウェイから受信するメッセージを監視できます。 たとえば、iothub-explorer を利用し、デバイスとクラウドの間のメッセージを次のコマンドで監視できます。

    ```cmd
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>次のステップ

IoT Edge に関する理解をさらに深め、実際にコード例に触れてみたいという場合は、以下の開発者向けチュートリアルとリソースをご覧ください。

* [IoT Edge を使用して物理デバイスから D2C メッセージを送信する][lnk-physical-device]
* [Azure IoT Edge][lnk-iot-edge]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

<!-- Links -->
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-physical-device]: iot-hub-iot-edge-physical-device.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
