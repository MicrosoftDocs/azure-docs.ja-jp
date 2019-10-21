---
title: Azure IoT Hub デバイス管理の開始 (Python) | Microsoft Docs
description: IoT Hub デバイス管理を使用してリモート デバイスの再起動を開始する方法。 Azure IoT SDK for Python を使用して、ダイレクト メソッドを含むシミュレートされたデバイス アプリと、ダイレクト メソッドを呼び出すサービス アプリを実装します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: eb5085db10c5763a4173f460eabde6afcccd5aff
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000435"
---
# <a name="get-started-with-device-management-python"></a>デバイス管理の開始 (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

このチュートリアルでは、次の操作方法について説明します。

* Azure Portal を使用して IoT Hub を作成し、IoT Hub 内にデバイス ID を作成します。

* デバイスを再起動するダイレクト メソッドを含む、シミュレション済みデバイス アプリを作成します。 ダイレクト メソッドは、クラウドから呼び出されます。

* シミュレートされたデバイス アプリで再起動ダイレクト メソッドを IoT Hub 経由で呼び出す Python コンソール アプリを作成します。

このチュートリアルの最後には、次の 2 つの Python コンソール アプリが完成します。

* **dmpatterns_getstarted_device.py**: IoT Hub を、作成済みのデバイス ID に接続し、再起動ダイレクト メソッドを受信し、物理的な再起動をシミュレートし、最後の再起動時間を報告します。

* **dmpatterns_getstarted_service.py**: シミュレートされたデバイス アプリでダイレクト メソッドを呼び出し、応答を表示し、更新された報告されるプロパティを表示します。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成

このセクションでは、次の作業を行います。

* クラウドによって呼び出されたダイレクト メソッドに応答する Python コンソール アプリを作成します。

* デバイスの再起動をシミュレートします。

* 報告されるプロパティを使用して、デバイス ツイン クエリで、デバイスとデバイスの最後の再起動時間を識別できるようにします。

1. コマンド プロンプトで次のコマンドを実行して **azure-iot-device** パッケージをインストールします。

    ```cmd/sh
    pip install azure-iot-device
    ```

   > [!NOTE]
   > azure-iothub-service-client 用の pip パッケージは、Windows OS でのみ利用できます。 Linux/Mac OS については、[Python 用の開発環境の準備](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)に関する記事で、Linux と Mac OS の各セクションを参照してください。
   >

2. テキスト エディターを使用して、**dmpatterns_getstarted_device.py** というファイルを作業ディレクトリに作成します。

3. **dmpatterns_getstarted_device.py** ファイルの先頭に、次の `import` ステートメントを追加します。

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. **CONNECTION_STRING** 変数を追加します。 `{deviceConnectionString}` プレースホルダーの値をデバイスの接続文字列に置き換えます。 この接続文字列は、先ほど「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」でコピーしたものです。  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. デバイスにダイレクト メソッドを実装する次の関数コールバックを追加します。

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. ダイレクト メソッド リスナーを起動し、待機します。

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. **dmpatterns_getstarted_device.py** ファイルを保存して閉じます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用してデバイスのリモート再起動をトリガーする

このセクションでは、ダイレクト メソッドを使用してデバイスでのリモート再起動を開始する Python コンソール アプリケーションを作成します。 このアプリは、デバイス ツイン クエリを使用して、そのデバイスの前回の再起動時刻を検出します。

1. コマンド プロンプトで次のコマンドを実行して **azure-iot-service-client** パッケージをインストールします。

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > azure-iothub-service-client と azure-iothub-device-client の pip パッケージは、現在 Windows OS でのみ利用できます。 Linux/Mac OS については、[Python 用の開発環境の準備](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)に関する記事で、Linux と Mac OS の各セクションを参照してください。
   >

2. テキスト エディターを使用して、**dmpatterns_getstarted_service.py** というファイルを作業ディレクトリに作成します。

3. **dmpatterns_getstarted_service.py** ファイルの先頭に、次の `import` ステートメントを追加します。

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

4. 次の変数宣言を追加します。 `{IoTHubConnectionString}` プレースホルダーの値を、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列に置き換えます。 `{deviceId}` プレースホルダーの値を、「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」で登録したデバイス ID に置き換えます。

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. 次の関数を追加して、ターゲット デバイスを再起動するためのデバイス メソッドを起動した後、デバイス ツインを照会し、前回の再起動時刻を取得します。

    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. **dmpatterns_getstarted_service.py** ファイルを保存して閉じます。

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。

1. コマンド プロンプトで、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. 別のコマンド プロンプトで、次のコマンドを実行してデバイス ツインのリモート再起動とクエリをトリガーして最後の再起動時刻を検索します。

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. ダイレクト メソッドに対するデバイスの応答がコンソールに表示されます。

   再起動ダイレクト メソッドに対するデバイスの応答を次に示します。

   ![シミュレートされたデバイス アプリの出力](./media/iot-hub-python-python-device-management-get-started/device.png)

   再起動ダイレクト メソッドを呼び出してデバイス ツインの状態をポーリングするサービスを次に示します。

   ![再起動サービスの出力のトリガー](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]