---
title: "Azure IoT Hub の使用 (Python) | Microsoft Docs"
description: "IoT SDK for Python を使用して Azure IoT Hub にデバイスからクラウドへのメッセージを送信する方法について説明します。 デバイスを登録し、メッセージを送信して、IoT ハブからメッセージを読み取るために、シミュレートされたデバイスとサービス アプリを作成します。"
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: dkshir
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f0800b70620106f9ae9d19d63b60b726835e338f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>Python を使用してシミュレートされたデバイスを IoT ハブに接続する
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

このチュートリアルの最後には、次の 2 つの Python アプリが完成します。

* **CreateDeviceIdentity.py**。デバイス ID および関連付けられているセキュリティ キーを作成し、シミュレートされたデバイス アプリを接続します。
* **SimulatedDevice.py**。以前に作成したデバイス ID で IoT ハブに接続し、MQTT プロトコルを使用して定期的にテレメトリ メッセージを送信します。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 Azure IoT SDK については、[Azure IoT SDK][lnk-hub-sdks] に関する記事を参照してください。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* [Python 2.x または 3.x][lnk-python-download]。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 インストール中に求められた場合は、プラットフォーム固有の環境変数に Python を追加します。 Python 2.x を使用する場合は、[*pip* (Python パッケージ管理システム) をインストールまたはアップグレード][lnk-install-pip]することが必要な場合があります。
* Windows OS を使用している場合は、[Visual C++ 再頒布可能パッケージ][lnk-visual-c-redist]。これは、Python からネイティブ DLL を使用できるようにするためです。
* [Node.js 4.0 以降][lnk-node-download]。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 これは、[IoT Hub エクスプローラー ツール][lnk-iot-hub-explorer]をインストールするために必要です。
* アクティブな Azure アカウント。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。

> [!NOTE]
> `azure-iothub-service-client` と `azure-iothub-device-client` の *pip* パッケージは現在、Windows OS でのみ利用できます。 Linux/Mac OS については、[Python に必要な開発環境の準備][lnk-python-devbox]に関する記事で、Linux と Mac OS の各セクションを参照してください。
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

IoT Hub の作成は以上です。 以降の作業で IoT Hub ホスト名と IoT Hub 接続文字列を使用します。

> [!NOTE]
> IoT ハブは、Python または Node.js ベースの Azure CLI を使用して、コマンド ラインで簡単に作成することもできます。 「[Azure CLI 2.0 を使用して IoT ハブを作成する][lnk-azure-cli-hub]」では、その簡単な手順を説明しています。 
> 

## <a name="create-a-device-identity"></a>デバイス ID の作成
このセクションでは、IoT ハブの ID レジストリにデバイス ID を作成する Python コンソール アプリを作成する手順を示します。 デバイスは、ID レジストリに登録されている場合のみ、IoT Hub に接続できます。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]の **ID レジストリ**に関するセクションをご覧ください。 このコンソール アプリケーションを実行すると、デバイスからクラウドへのメッセージを IoT Hub に送信するときにそのデバイスを識別する一意の ID とキーが生成されます。

1. コマンド プロンプトを開き、**Azure IoT Hub Service SDK for Python** をインストールします。 SDK をインストールしたら、コマンド プロンプトを閉じます。

    ```
    pip install azure-iothub-service-client
    ```

2. **CreateDeviceIdentity.py** という名前の Python ファイルを作成します。 そのファイルを[任意の Python エディターまたは IDE][lnk-python-ide-list] (既定の [IDLE][lnk-idle] など) で開きます。

3. Service SDK から必要なモジュールをインポートする次のコードを追加します。

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. 次のコードを追加し、`[IoTHub Connection String]` プレースホルダーを、前のセクションで作成した IoT ハブの接続文字列に置き換えます。 `DEVICE_ID` には任意の名前を使用できます。
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. いくつかのデバイス情報を出力する次の関数を追加します。

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. レジストリ マネージャーを使用してデバイス ID を作成する次の関数を追加します。 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. 最後に、次の main 関数を追加し、ファイルを保存します。

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. コマンド プロンプトで、次のように **CreateDeviceIdentity.py** を実行します。

    ```python
    python CreateDeviceIdentity.py
    ```
6. シミュレートされたデバイスが作成されます。 このデバイスの **deviceId** と **primaryKey** をメモしておきます。 これらの値は、後でデバイスとして IoT Hub に接続するアプリケーションを作成するときに必要になります。

    ![デバイスの作成成功][1]

> [!NOTE]
> IoT Hub の ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID のみが格納されます。 セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]をご覧ください。
> 
> 


## <a name="create-a-simulated-device-app"></a>シミュレーション対象デバイス アプリの作成
このセクションでは、デバイスをシミュレートし、デバイスからクラウドへのメッセージを IoT ハブに送信する Python コンソール アプリを作成する手順を示します。

1. 新しいコマンド プロンプトを開き、次のように Azure IoT Hub Device SDK for Python をインストールします。 インストールしたら、コマンド プロンプトを閉じます。

    ```
    pip install azure-iothub-device-client
    ```
2. **SimulatedDevice.py** という名前のファイルを作成します。 そのファイルを任意の Python エディターまたは IDE (IDLE など) で開きます。

3. Device SDK から必要なモジュールをインポートする次のコードを追加します。

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. 次のコードを追加し、`[IoTHub Device Connection String]` プレースホルダーをデバイスの接続文字列に置き換えます。 デバイスの接続文字列は、通常、`HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>` という形式です。 前のセクションで作成したデバイスの **deviceId** と **primaryKey** を使用して、それぞれ `<deviceId>` と `<primaryKey>` を置き換えます。 `<hostName>` を IoT ハブのホスト名に (通常は、`<IoT hub name>.azure-devices.net` という形式で) 置き換えます。

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. 送信確認コールバックを定義する次のコードを追加します。 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. デバイス クライアントを初期化する次のコードを追加します。

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        return client
    ```
7. メッセージを書式設定し、シミュレートされたデバイスから IoT ハブに送信する次の関数を追加します。

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. 最後に、main 関数を追加します。 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. **SimulatedDevice.py** ファイルを保存し、閉じます。 これで、このアプリを実行する準備が整いました。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>シミュレートされたデバイスからのメッセージの受信
デバイスからテレメトリ メッセージを受信するには、IoT Hub によって公開される、[Event Hub][lnk-event-hubs-overview] と互換性のあるエンドポイントを使用する必要があります。これにより、デバイスからクラウドへのメッセージを読み取ります。 IoT Hub の Event Hub と互換性のあるエンドポイントを使用して Event Hubs からのメッセージを処理する方法については、[Event Hubs の使用][lnk-eventhubs-tutorial]に関するチュートリアルを参照してください。 Event Hubs では Python によるテレメトリの利用をまだサポートしていないため、[Node.js](iot-hub-node-node-getstarted.md#D2C_node) または [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) の Event Hubs ベースのコンソール アプリを作成することで、デバイスからクラウドへのメッセージを IoT Hub から読み取ることができます。 このチュートリアルでは、[IoT Hub エクスプローラー ツール][lnk-iot-hub-explorer]を使用して、これらのデバイス メッセージを読み取る方法について説明します。

1. コマンド プロンプトを開き、IoT Hub エクスプローラーをインストールします。 

    ```
    npm install -g iothub-explorer
    ```

2. コマンド プロンプトで次のコマンドを実行して、デバイスからクラウドへのメッセージの監視を開始します。 `--login` の後のプレースホルダーには、IoT ハブの接続文字列を使用します。

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. 新しいコマンド プロンプトを開き、**SimulatedDevice.py** ファイルが含まれているディレクトリに移動します。

4. **SimulatedDevice.py** ファイルを実行します。これにより、テレメトリ データが IoT ハブに定期的に送信されるようになります。 
   
    ```
    python SimulatedDevice.py
    ```
5. 前のセクションで IoT Hub エクスプローラーを実行したコマンド プロンプトでデバイス メッセージを確認します。 

    ![Python でのデバイスからクラウドへのメッセージ][2]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 シミュレーション対象デバイス アプリでデバイスからクラウドへのメッセージを IoT Hub に送信できるようにするために、このデバイス ID を使用しました。 IoT Hub エクスプローラー ツールを利用して、IoT ハブが受信したメッセージを確認しました。 

Python SDK for Azure IoT Hub の使用の詳細については、[こちらの Git Hub リポジトリ][lnk-python-github]を参照してください。 Azure IoT Hub Service SDK for Python のメッセージング機能を確認するには、[iothub_messaging_sample.py][lnk-messaging-sample] をダウンロードして実行してください。 Azure IoT Hub Service SDK for Python を使用したデバイス側のシミュレーションについては、[iothub_client_sample.py][lnk-client-sample] をダウンロードして実行してください。

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [デバイスを接続する][lnk-connect-device]
* [デバイス管理の概要][lnk-device-management]
* [Azure IoT Edge の概要][lnk-iot-edge]

既存の IoT ソリューションを拡張し、デバイスからクラウドへのメッセージを大規模に処理する方法については、[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

