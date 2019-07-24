---
title: Azure IoT Hub を使用してジョブのスケジュールを設定する (Python) | Microsoft Docs
description: 複数のデバイスでダイレクト メソッドを呼び出すように Azure IoT Hub ジョブのスケジュールを設定する方法。 Azure IoT SDK for Python を使用して、シミュレートされたデバイス アプリと、ジョブを実行するサービス アプリを実装します。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: kgremban
ms.openlocfilehash: c15db0766da3b4c18c306106ffdd5fc75a9143aa
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608811"
---
# <a name="schedule-and-broadcast-jobs-python"></a>ジョブのスケジュールとブロードキャスト (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub は、数百万台のデバイスをスケジュールおよび更新するジョブをバックエンド アプリで作成したり追跡したりできるようにするフル マネージドのサービスです。  ジョブは次のアクションに使用できます。

* 必要なプロパティを更新する
* タグを更新する
* ダイレクト メソッドを呼び出す

概念的には、ジョブはこれらのアクションのいずれかをラップし、デバイス ツイン クエリで定義される一連のデバイスに対して実行の進行状況を追跡します。  たとえば、バックエンド アプリでは、ジョブを使用して、10,000 台のデバイスに対して reboot メソッドを呼び出すことができます。これは、デバイス ツイン クエリで指定され、将来の時刻にスケジュールされます。  次に、このアプリケーションを使用して、これらの各デバイスが reboot メソッドを受信し実行する進行状況を追跡できます。

これらの各機能について詳しくは、次の記事をご覧ください。

* デバイス ツインとプロパティ: [デバイス ツインの概要](iot-hub-python-twin-getstarted.md)および[チュートリアル: デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)

* ダイレクト メソッド: [IoT Hub 開発者ガイド - ダイレクト メソッド](iot-hub-devguide-direct-methods.md)および[チュートリアル: ダイレクト メソッド](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルでは、次の操作方法について説明します。

* ソリューション バックエンドから呼び出すことができ、**lockDoor** を可能にするダイレクト メソッドを持つ、Python のシミュレートされたデバイス アプリを作成します。

* ジョブを使用してシミュレートされたデバイス アプリで **lockDoor** ダイレクト メソッドを呼び出し、デバイス ジョブを使用して必要なプロパティを更新する Python コンソール アプリを作成します。

このチュートリアルの最後には、次の 2 つの Python アプリが完成します。

**simDevice.py**。デバイス ID で IoT ハブに接続し、**lockDoor** ダイレクト メソッドを受信します。

**scheduleJobService.py**。シミュレートされたデバイス アプリでダイレクト メソッドを呼び出し、ジョブを使用してデバイス ツインの必要なプロパティを更新します。

このチュートリアルを完了するには、以下が必要です。

* [Python 2.x または 3.x](https://www.python.org/downloads/)。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 インストール中に求められた場合は、プラットフォーム固有の環境変数に Python を追加します。 Python 2.x を使用している場合は、[*pip* (Python パッケージ管理システム) のインストールまたはアップグレード](https://pip.pypa.io/en/stable/installing/)が必要な場合があります。

* Windows OS を使用している場合は、[Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/confirmation.aspx?id=48145)によって、Python からネイティブ DLL を使用できます。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

> [!NOTE]
> **Azure IoT SDK for Python** では、**ジョブ**機能は直接サポートされません。 代わりに、このチュートリアルでは、非同期スレッドとタイマーを利用する代替ソリューションを提供します。 さらに更新するには、[Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) に関するページで**サービス クライアント SDK** 機能の一覧をご覧ください。 
>

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT ハブに対する接続文字列を取得する

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成

このセクションでは、クラウドによって呼び出されたダイレクト メソッドに応答する Python コンソール アプリを作成します。このアプリはシミュレートされた **lockDoor** メソッドをトリガーします。

1. コマンド プロンプトで次のコマンドを実行して **azure-iot-device-client** パッケージをインストールします。

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. テキスト エディターを使用して、作業ディレクトリに新しい **simDevice.py** ファイルを作成します。

3. **simDevice.py** ファイルの先頭に、次の `import` ステートメントと変数を追加します。 `deviceConnectionString` を上記で作成したデバイスの接続文字列に置き換えます。

    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. **lockDoor** メソッドを処理する次の関数コールバックを追加します。

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. デバイス ツインの更新を処理する別の関数コールバックを追加します。

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. **lockDoor** メソッドのハンドラーを登録する次のコードを追加します。 `main` ルーチンも含めます。

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

            while True:
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
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

7. **simDevice.py** ファイルを保存して閉じます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
>

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>ダイレクト メソッドを呼び出し、デバイス ツインのプロパティを更新するジョブのスケジュール

このセクションでは、ダイレクト メソッドを使用してデバイスでリモート **lockDoor** を開始する Python コンソール アプリを作成し、デバイス ツインのプロパティを更新します。

1. コマンド プロンプトで次のコマンドを実行して **azure-iot-service-client** パッケージをインストールします。

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

2. テキスト エディターを使用して、作業ディレクトリに新しい **scheduleJobService.py** ファイルを作成します。

3. **scheduleJobService.py** ファイルの先頭に、次の `import` ステートメントと変数を追加します。

    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. デバイスのクエリに使用する次の関数を追加します。

    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)

        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

5. ダイレクト メソッドとデバイス ツインを呼び出すジョブを実行する次のメソッドを追加します。

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. ジョブをスケジュールし、ジョブの状態を更新する次のコードを追加します。 `main` ルーチンも含めます。

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. **scheduleJobService.py** ファイルを保存して閉じます。

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. 作業ディレクトリのコマンド プロンプトで、次のコマンドを実行して再起動のダイレクト メソッドのリッスンを開始します。

    ```cmd/sh
    python simDevice.py
    ```

2. 作業ディレクトリのコマンド プロンプトで、次のコマンドを実行して、ドアをロックしてツインを更新するジョブをトリガーします。
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. ダイレクト メソッドとデバイス ツインの更新に対するデバイスの応答がコンソールに表示されます。

    ![IoT Hub ジョブ サンプル 1 -- デバイスの出力](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub ジョブ サンプル 2 -- デバイスの出力](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ジョブを使用して、デバイスへのダイレクト メソッドと、デバイス ツインのプロパティの更新をスケジュールしました。

IoT Hub およびリモートによるファームウェアのワイヤレス更新などの他のデバイス管理パターンを確認するには、[ファームウェアの更新方法](tutorial-firmware-update.md)に関するページを参照してください。