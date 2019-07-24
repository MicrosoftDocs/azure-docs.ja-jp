---
title: Azure IoT Hub デバイス管理の開始 (Python) | Microsoft Docs
description: IoT Hub デバイス管理を使用してリモート デバイスの再起動を開始する方法。 Azure IoT SDK for Python を使用して、ダイレクト メソッドを含むシミュレートされたデバイス アプリと、ダイレクト メソッドを呼び出すサービス アプリを実装します。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 04fc1da04d9da715acfed8ca9d26e9c325afb403
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786786"
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

このチュートリアルを完了するには、以下が必要です。

* [Python 2.x または 3.x](https://www.python.org/downloads/)。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 インストール中に求められた場合は、プラットフォーム固有の環境変数に Python を追加します。 Python 2.x を使用している場合は、[*pip* (Python パッケージ管理システム) のインストールまたはアップグレード](https://pip.pypa.io/en/stable/installing/)が必要な場合があります。

* `pip install azure-iothub-device-client` コマンドを使用して [azure-iothub-device-client](https://pypi.org/project/azure-iothub-device-client/) パッケージをインストールする

* `pip install azure-iothub-service-client` コマンドを使用して  [azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) パッケージをインストールする

* Windows OS を使用している場合は、[Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/confirmation.aspx?id=48145)によって、Python からネイティブ DLL を使用できます。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT ハブに対する接続文字列を取得する

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成

このセクションでは、次の作業を行います。

* クラウドによって呼び出されたダイレクト メソッドに応答する Python コンソール アプリを作成します。

* デバイスの再起動をシミュレートします。

* 報告されるプロパティを使用して、デバイス ツイン クエリで、デバイスとデバイスの最後の再起動時間を識別できるようにします。

1. テキスト エディターを使用して、**dmpatterns_getstarted_device.py** ファイルを作成します。

2. **dmpatterns_getstarted_device.py** ファイルの先頭に、次の `import` ステートメントを追加します。

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

3. **CONNECTION_STRING** 変数やクライアント初期化などの変数を追加します。  接続文字列を、デバイスの接続文字列に置き換えます。  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

4. デバイスにダイレクト メソッドを実装する次の関数コールバックを追加します。

    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS

        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS

        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
            time.sleep(20)

            print ( "Device rebooted." )

            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            print ( "Updating device twins: rebootTime" )

        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200

        return device_method_return_value
    ```

5. ダイレクト メソッド リスナーを起動し、待機します。

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

6. **dmpatterns_getstarted_device.py** ファイルを保存して閉じます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用してデバイスのリモート再起動をトリガーする

このセクションでは、ダイレクト メソッドを使用してデバイスでのリモート再起動を開始する Python コンソール アプリケーションを作成します。 このアプリは、デバイス ツイン クエリを使用して、そのデバイスの前回の再起動時刻を検出します。

1. テキスト エディターを使用して、**dmpatterns_getstarted_service.py** ファイルを作成します。

2. **dmpatterns_getstarted_service.py** ファイルの先頭に、次の `import` ステートメントを追加します。

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

3. 次の変数宣言を追加します。 _IoTHubConnectionString_ と _deviceId_ のプレース ホルダーについてのみ、値を置き換えてください。

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

4. 次の関数を追加して、ターゲット デバイスを再起動するためのデバイス メソッドを起動した後、デバイス ツインを照会し、前回の再起動時刻を取得します。

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

5. **dmpatterns_getstarted_service.py** ファイルを保存して閉じます。

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。

1. コマンド プロンプトで、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。

    ```
    python dmpatterns_getstarted_device.py
    ```

2. 別のコマンド プロンプトで、次のコマンドを実行してデバイス ツインのリモート再起動とクエリをトリガーして最後の再起動時刻を検索します。

    ```
    python dmpatterns_getstarted_service.py
    ```

3. ダイレクト メソッドに対するデバイスの応答がコンソールに表示されます。

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]