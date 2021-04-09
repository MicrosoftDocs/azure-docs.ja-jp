---
title: Azure IoT Hub を使用したクラウドからデバイスへのメッセージ (Python) | Microsoft Docs
description: Azure IoT SDK for Python を使って、クラウドからデバイスへのメッセージを Azure IoT Hub からデバイスへ送信する方法。 クラウドからデバイスへのメッセージを受信するためにシミュレートされたデバイス アプリを変更し、クラウドからデバイスへのメッセージを送信するためにバックエンド アプリを変更します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: ad6399a4713520ca0550d143cf3f19f87d55337c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87876803"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>IoT Hub を使用したクラウドからデバイスへのメッセージの送信 (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するクイックスタートには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、および device-to-cloud メッセージを送信するシミュレートされたデバイス アプリをコード化する方法が示されています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルは、[デバイスから IoT ハブへのテレメトリ送信](quickstart-send-telemetry-python.md)に関する記事に基づいて作成されています。 次の方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。

* クラウドからデバイスへのメッセージをデバイスで受信する。

cloud-to-device メッセージの詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide-messaging.md)を参照してください。

このチュートリアルの最後に、次の 2 つの Python コンソール アプリを実行します。

* **SimulatedDevice.py**。[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するページで作成されたアプリの修正版であり、IoT ハブに接続し、cloud-to-device メッセージを受信します。

* **SendCloudToDeviceMessage.py**。クラウドからデバイスへのメッセージを IoT Hub 経由でシミュレートされたデバイス アプリに送信します。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="receive-messages-in-the-simulated-device-app"></a>シミュレートされたデバイス アプリでメッセージを受信する

このセクションでは、デバイスをシミュレートして、クラウドからデバイスへのメッセージを IoT Hub から受信する、Python コンソール アプリを作成します。

1. 作業ディレクトリのコマンド プロンプトから、**Azure IoT Hub Device SDK for Python** をインストールします。

    ```cmd/sh
    pip install azure-iot-device
    ```

1. テキスト エディターを使用して、**SimulatedDevice.py** という名前のファイルを作成します。

1. **SimulatedDevice.py** ファイルの先頭に、次の `import` ステートメントと変数を追加します。

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. 次のコードを **SimulatedDevice.py** ファイルに追加します。 `{deviceConnectionString}` のプレースホルダー値を、[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するクイック スタートで作成したデバイスのデバイス接続文字列に置き換えます。

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. 受信したメッセージをコンソールに出力するための次の関数を追加します。

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. クライアントを初期化してクラウドからデバイスへのメッセージの受信を待機する次のコードを追加します。

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. 次の main 関数を追加します。

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. **SimulatedDevice.py** ファイルを保存し、閉じます。

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するページで作成した IoT ハブを介して cloud-to-device メッセージを送信するバックエンド サービスを作成します。 cloud-to-device メッセージを送信するサービスには、**サービス接続** のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する

このセクションでは、クラウドからデバイスへのメッセージをシミュレートされたデバイスのアプリに送信する Python コンソール アプリを作成します。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するクイックスタートで追加したデバイスのデバイス ID が必要です。 また、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列も必要です。

1. 作業ディレクトリでコマンド プロンプトを開き、**Azure IoT Hub Service SDK for Python** をインストールします。

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. テキスト エディターを使用して、**SendCloudToDeviceMessage.py** という名前のファイルを作成します。

1. **SendCloudToDeviceMessage.py** ファイルの先頭に、次の `import` ステートメントと変数を追加します。

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. 次のコードを **SendCloudToDeviceMessage.py** ファイルに追加します。 `{iot hub connection string}` と `{device id}` のプレースホルダー値を、前に書き留めた IoT ハブ接続文字列とデバイス ID に置き換えます。

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. デバイスにメッセージを送信するための次のコードを追加します。

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. 次の main 関数を追加します。

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. **SendCloudToDeviceMessage.py** ファイルを保存して閉じます。

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. 作業ディレクトリのコマンド プロンプトで、次のコマンドを実行して、クラウドからデバイスへのメッセージをリッスンします。

    ```shell
    python SimulatedDevice.py
    ```

    ![シミュレーション済みデバイス アプリを実行する](./media/iot-hub-python-python-c2d/device-1.png)

1. 作業ディレクトリで新しいコマンド プロンプトを開き、次のコマンドを実行して、クラウドからデバイスへのメッセージを送信します。

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![クラウドからデバイスへのコマンドを送信するアプリを実行する](./media/iot-hub-python-python-c2d/service.png)

1. デバイスによって受信されたメッセージを確認します。

    ![受信したメッセージ](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT リモート監視ソリューション アクセラレータ](https://azure.microsoft.com/documentation/suites/iot-suite/)に関するページをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)をご覧ください。
