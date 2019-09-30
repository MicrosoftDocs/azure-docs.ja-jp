---
title: Azure IoT Hub を使用したクラウドからデバイスへのメッセージ (Python) | Microsoft Docs
description: Azure IoT SDK for Python を使って、クラウドからデバイスへのメッセージを Azure IoT Hub からデバイスへ送信する方法。 クラウドからデバイスへのメッセージを受信するためにシミュレートされたデバイス アプリを変更し、クラウドからデバイスへのメッセージを送信するためにバックエンド アプリを変更します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 4cda59448856630468076ef63c51b8a216a31bd0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001937"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>IoT Hub を使用したクラウドからデバイスへのメッセージの送信 (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するクイックスタートには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、および device-to-cloud メッセージを送信するシミュレートされたデバイス アプリをコード化する方法が示されています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルは、[デバイスから IoT ハブへのテレメトリ送信](quickstart-send-telemetry-python.md)に関する記事に基づいて作成されています。 次の方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。

* クラウドからデバイスへのメッセージをデバイスで受信する。

* ソリューション バックエンドから、IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を要求する。

cloud-to-device メッセージの詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide-messaging.md)を参照してください。

このチュートリアルの最後に、次の 2 つの Python コンソール アプリを実行します。

* **SimulatedDevice.py**。[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するページで作成されたアプリの修正版であり、IoT ハブに接続し、cloud-to-device メッセージを受信します。

* **SendCloudToDeviceMessage.py**。クラウドからデバイスへのメッセージを IoT Hub を介してシミュレートされたデバイス アプリに送信し、その配信確認を受け取ります。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="receive-messages-in-the-simulated-device-app"></a>シミュレートされたデバイス アプリでメッセージを受信する

このセクションでは、デバイスをシミュレートして、クラウドからデバイスへのメッセージを IoT Hub から受信する、Python コンソール アプリを作成します。

1. テキスト エディターを使って、**SimulatedDevice.py** ファイルを作成します。

2. **SimulatedDevice.py** ファイルの先頭に、次の `import` ステートメントと変数を追加します。

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. 次のコードを **SimulatedDevice.py** ファイルに追加します。 "{deviceConnectionString}" プレースホルダーの値を、[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するクイック スタートで作成したデバイスのデバイス接続文字列に置き換えます。

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 受信したメッセージをコンソールに出力するための次の関数を追加します。

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. クライアントを初期化してクラウドからデバイスへのメッセージの受信を待機する次のコードを追加します。

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. 次の main 関数を追加します。

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. **SimulatedDevice.py** ファイルを保存して閉じます。

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するページで作成した IoT ハブを介して cloud-to-device メッセージを送信するバックエンド サービスを作成します。 cloud-to-device メッセージを送信するサービスには、**サービス接続**のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する

このセクションでは、クラウドからデバイスへのメッセージをシミュレートされたデバイスのアプリに送信する Python コンソール アプリを作成します。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するクイックスタートで追加したデバイスのデバイス ID が必要です。 また、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列も必要です。

1. テキスト エディターを使って、**SendCloudToDeviceMessage.py** ファイルを作成します。

2. **SendCloudToDeviceMessage.py** ファイルの先頭に、次の `import` ステートメントと変数を追加します。

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. 次のコードを **SendCloudToDeviceMessage.py** ファイルに追加します。 プレースホルダー "{iot hub connection string}" と "{device id}" の値は、先ほどメモした IoT ハブ接続文字列とデバイス ID に置き換えてください。

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. フィードバック メッセージをコンソールに出力するための次の関数を追加します。

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. デバイスにメッセージを送信し、クラウドからデバイスへのメッセージが配信されたことの確認応答がデバイスからあったときにフィードバック メッセージを処理するための次のコードを追加します。

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. 次の main 関数を追加します。

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. **SendCloudToDeviceMessage.py** ファイルを保存して閉じます。

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. コマンド プロンプトを開き、**Azure IoT Hub Device SDK for Python** をインストールします。

    ```shell
    pip install azure-iothub-device-client
    ```

2. コマンド プロンプトで次のコマンドを実行し、クラウドからデバイスへのメッセージを待機します。

    ```shell
    python SimulatedDevice.py
    ```

    ![シミュレーション済みデバイス アプリを実行する](./media/iot-hub-python-python-c2d/simulated-device.png)

3. 新しいコマンド プロンプトを開き、**Azure IoT Hub Service SDK for Python** をインストールします。

    ```shell
    pip install azure-iothub-service-client
    ```

4. コマンド プロンプトで次のコマンドを実行し、クラウドからデバイスへのメッセージを送信して、メッセージのフィードバックを待機します。

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![クラウドからデバイスへのコマンドを送信するアプリを実行する](./media/iot-hub-python-python-c2d/send-command.png)

5. デバイスで受信したメッセージを確認します。

    ![受信したメッセージ](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT リモート監視ソリューション アクセラレータ](https://azure.microsoft.com/documentation/suites/iot-suite/)に関するページをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)をご覧ください。
