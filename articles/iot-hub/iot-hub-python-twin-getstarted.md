---
title: Azure IoT Hub デバイス ツインの使用 (Python) | Microsoft Docs
description: Azure IoT Hub デバイス ツインを使用してタグを追加し、IoT Hub クエリを使用する方法。 Azure IoT SDK for Python を使用して、シミュレートされたデバイス アプリと、タグを追加して IoT Hub クエリを実行するサービス アプリを実装します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 12b1d083ae1481f7c8b5fe60cac9156a56aeaa0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87875474"
---
# <a name="get-started-with-device-twins-python"></a>デバイス ツインの概要 (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

このチュートリアルの最後には、次の 2 つの Python コンソール アプリが完成します。

* **AddTagsAndQuery.py**: Python バックエンド アプリ。タグを追加してデバイス ツインのクエリを実行します。

* **ReportConnectivity.py**: Python アプリ。以前作成したデバイス ID を使用して IoT ハブに接続するデバイスをシミュレートし、接続の状態を報告します。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>デバイス アプリを作成する

このセクションでは、**{Device ID}** に関連付けられたデバイス ツインに場所のメタデータを追加する Python コンソール アプリを作成します。 その後、レドモンドにあるデバイスで、携帯ネットワーク接続を報告しているものを選択して、IoT ハブに格納されているデバイス ツインに対してクエリを実行します。

1. 作業ディレクトリでコマンド プロンプトを開き、**Azure IoT Hub Service SDK for Python** をインストールします。

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. テキスト エディターを使用して、新しい **AddTagsAndQuery.py** ファイルを作成します。

3. Service SDK から必要なモジュールをインポートする次のコードを追加します。

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. 次のコードを追加します。 `[IoTHub Connection String]` を、「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列に置き換えます。 `[Device Id]` を、「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」で登録したデバイス ID に置き換えます。
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. 次のコードを **AddTagsAndQuery.py** ファイルに追加します。

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    **IoTHubRegistryManager** オブジェクトに、サービスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 最初のコードで **IoTHubRegistryManager** オブジェクトを初期化した後、**DEVICE_ID** のデバイス ツインを更新し、最後に 2 つのクエリを実行します。 最初のものでは **Redmond43** 工場にあるデバイスのデバイス ツインのみを選択し、2 番目のものでは携帯ネットワーク経由で接続しているデバイスのみを選択します。

6. 次のコードを **AddTagsAndQuery.py** の末尾に追加して、**iothub_service_sample_run** 関数を実装します。

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. 以下を使用してアプリケーションを実行します。

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    **Redmond43** にあるすべてのデバイスを照会するクエリの結果には、1 件のデバイスが表示され、携帯ネットワークを使用するデバイスに絞り込んだ結果には 0 件のデバイスが表示されます。

    ![Redmond にあるすべてのデバイスを表示する最初のクエリ](./media/iot-hub-python-twin-getstarted/service-1.png)

次のセクションでは、接続情報を報告し、前のセクションのクエリの結果を変更するデバイス アプリを作成します。

## <a name="create-the-device-app"></a>デバイス アプリを作成する

このセクションでは、**{Device ID}** としてハブに接続し、デバイス ツインのレポートされるプロパティに携帯ネットワークを使用しているという情報を含めるよう更新する Python コンソール アプリを作成します。

1. 作業ディレクトリのコマンド プロンプトから、**Azure IoT Hub Device SDK for Python** をインストールします。

    ```cmd/sh
    pip install azure-iot-device
    ```

2. テキスト エディターを使用して、新しい **ReportConnectivity.py** ファイルを作成します。

3. Device SDK から必要なモジュールをインポートする次のコードを追加します。

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. 次のコードを追加します。 `[IoTHub Device Connection String]` プレースホルダーの値を、「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」でコピーしておいたデバイス接続文字列に置き換えます。

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. 次のコードを **ReportConnectivity.py** ファイルに追加して、デバイス ツインの機能を実装します。

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    **IoTHubModuleClient** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、**IoTHubModuleClient** オブジェクトを初期化した後、デバイスのデバイス ツインを取得して、報告されるプロパティに接続情報を含めるよう更新します。

6. 次のコードを **ReportConnectivity.py** の末尾に追加して、**iothub_client_sample_run** 関数を実装します。

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. デバイス アプリを実行する:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    デバイス ツインの報告されるプロパティが更新されたこと示す確認メッセージが表示されます。

    ![デバイス アプリから報告されたプロパティを更新する](./media/iot-hub-python-twin-getstarted/device-1.png)

8. これで、デバイスが接続情報を報告したため、両方のクエリで表示されるようになります。 戻って、クエリをもう一度実行します。

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    今回は、 **{Device ID}** が両方のクエリ結果に表示されるはずです。

    ![サービス アプリに対する 2 番目のクエリ](./media/iot-hub-python-twin-getstarted/service-2.png)

    デバイス アプリで、サービス アプリによって送信された必要なプロパティ ツイン パッチが受信されたことを確認するメッセージが表示されます。

    ![デバイス アプリ上で必要なプロパティを受信する](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 バックエンド アプリからデバイスのメタデータをタグとして追加し、シミュレート対象デバイス アプリでデバイス ツインのデバイスの接続情報を報告するよう記述しました。 さらに、レジストリを使用してこの情報を照会する方法も学習しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要](quickstart-send-telemetry-python.md)に関するチュートリアルでデバイスからテレメトリを送信する。

* [必要なプロパティを使用してデバイスを構成する](tutorial-device-twins.md)方法に関するチュートリアルで、デバイス ツインの必要なプロパティを使用してデバイスを構成する。

* [ダイレクト メソッドの使用](quickstart-control-device-python.md)に関するチュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。
