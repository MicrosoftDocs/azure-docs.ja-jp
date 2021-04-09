---
title: Azure IoT Hub を使用してジョブのスケジュールを設定する (Python) | Microsoft Docs
description: 複数のデバイスでダイレクト メソッドを呼び出すように Azure IoT Hub ジョブのスケジュールを設定する方法。 Azure IoT SDK for Python を使用して、シミュレートされたデバイス アプリと、ジョブを実行するサービス アプリを実装します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.custom: devx-track-python
ms.openlocfilehash: 733e3be21a1a1305b5c7947de1ae54ddce5e0d2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87876684"
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

* ダイレクト メソッド: [ダイレクト メソッドに関する IoT Hub 開発者ガイド](iot-hub-devguide-direct-methods.md)と[ダイレクト メソッドに関するチュートリアル](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルでは、次の操作方法について説明します。

* ソリューション バックエンドから呼び出すことができ、**lockDoor** を可能にするダイレクト メソッドを持つ、Python のシミュレートされたデバイス アプリを作成します。

* ジョブを使用してシミュレートされたデバイス アプリで **lockDoor** ダイレクト メソッドを呼び出し、デバイス ジョブを使用して必要なプロパティを更新する Python コンソール アプリを作成します。

このチュートリアルの最後には、次の 2 つの Python アプリが完成します。

**simDevice.py**。デバイス ID で IoT ハブに接続し、**lockDoor** ダイレクト メソッドを受信します。

**scheduleJobService.py**。シミュレートされたデバイス アプリでダイレクト メソッドを呼び出し、ジョブを使用してデバイス ツインの必要なプロパティを更新します。

> [!NOTE]
> **Azure IoT SDK for Python** では、**ジョブ** 機能は直接サポートされません。 代わりに、このチュートリアルでは、非同期スレッドとタイマーを利用する代替ソリューションを提供します。 さらに更新するには、[Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) に関するページで **サービス クライアント SDK** 機能の一覧をご覧ください。
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成

このセクションでは、クラウドによって呼び出されたダイレクト メソッドに応答する Python コンソール アプリを作成します。このアプリはシミュレートされた **lockDoor** メソッドをトリガーします。

1. コマンド プロンプトで次のコマンドを実行して **azure-iot-device** パッケージをインストールします。

    ```cmd/sh
    pip install azure-iot-device
    ```

2. テキスト エディターを使用して、作業ディレクトリに新しい **simDevice.py** ファイルを作成します。

3. **simDevice.py** ファイルの先頭に、次の `import` ステートメントと変数を追加します。 `deviceConnectionString` を上記で作成したデバイスの接続文字列に置き換えます。

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. **lockDoor** メソッドを処理する次の関数コールバックを追加します。

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. デバイス ツインの更新を処理する別の関数コールバックを追加します。

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. **lockDoor** メソッドのハンドラーを登録する次のコードを追加します。 `main` ルーチンも含めます。

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. **simDevice.py** ファイルを保存して閉じます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
>

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、デバイス上でダイレクト メソッドを呼び出し、デバイス ツインを更新するバックエンド サービスを作成します。 デバイス上でダイレクト メソッドを呼び出すには、サービスに **サービス接続** アクセス許可が必要です。 また、このサービスで ID レジストリの読み取りと書き込みを行うために、**レジストリ読み取り** および **レジストリ書き込み** アクセス許可も必要です。 これらのアクセス許可だけを含んだ既定の共有アクセス ポリシーは存在しないため、共有アクセス ポリシーを独自に作成する必要があります。

**サービス接続**、**レジストリ読み取り**、および **レジストリ書き込み** のアクセス許可を付与する共有アクセス ポリシーを作成し、そのポリシーの接続文字列を取得するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で IoT ハブを開きます。 IoT ハブに移動するための最も簡単な方法は、**[リソース グループ]** を選択し、IoT ハブがあるリソース グループを選択した後、リソースの一覧から目的の IoT ハブを選択することです。

2. IoT ハブの左側のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

3. ポリシーの一覧の上にあるトップ メニューから **[追加]** を選択します。

4. **[共有アクセス ポリシーを追加]** ウィンドウで、対象のポリシーのわかりやすい名前を入力します (例: *serviceAndRegistryReadWrite*)。 **[アクセス許可]** で、**[サービス接続]** と **[レジストリ書き込み]** を選択します (**[レジストリ書き込み]** を選択すると、**[レジストリ読み取り]** が自動的に選択されます)。 **[作成]** を選択します。

    ![新しい共有アクセス ポリシーを追加する方法を示す画面](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. **[共有アクセス ポリシー]** ウィンドウに戻り、ポリシーの一覧から新しいポリシーを選択します。

6. **[共有アクセス キー]** で、 **[接続文字列 - プライマリ キー]** のコピー アイコンを選択し、その値を保存します。

    ![接続文字列を取得する方法を示す画面](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

IoT Hub の共有アクセス ポリシーとアクセス許可の詳細については、「[アクセス制御とアクセス許可](./iot-hub-devguide-security.md#access-control-and-permissions)」を参照してください。

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>ダイレクト メソッドを呼び出し、デバイス ツインのプロパティを更新するジョブのスケジュール

このセクションでは、ダイレクト メソッドを使用してデバイスでリモート **lockDoor** を開始する Python コンソール アプリを作成し、さらにデバイス ツインの必要なプロパティを更新します。

1. コマンド プロンプトで次のコマンドを実行して **azure-iot-hub** パッケージをインストールします。

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. テキスト エディターを使用して、作業ディレクトリに新しい **scheduleJobService.py** ファイルを作成します。

3. **scheduleJobService.py** ファイルの先頭に、次の `import` ステートメントと変数を追加します。 `{IoTHubConnectionString}` プレースホルダーを、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列に置き換えます。 `{deviceId}` プレースホルダーを、「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」で登録したデバイス ID に置き換えます。

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. デバイスのクエリに使用する次の関数を追加します。

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. ダイレクト メソッドとデバイス ツインを呼び出すジョブを実行する次のメソッドを追加します。

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

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

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
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

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ジョブを使用して、デバイスへのダイレクト メソッドと、デバイス ツインのプロパティの更新をスケジュールしました。

IoT Hub およびリモートによるファームウェアのワイヤレス更新などの他のデバイス管理パターンを確認するには、[ファームウェアの更新方法](tutorial-firmware-update.md)に関するページを参照してください。
