---
title: Azure IoT Hub モジュール ID とモジュール ツイン (Python)
description: モジュール ID を作成し、IoT SDKs for Python を使用してモジュール ツインを更新する方法を説明します。
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 665281adc892e6b3655c0b1d0533cb3148e62940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139404"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>IoT Hub モジュール ID とモジュール ツイン (Python) の概要

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [モジュール ID とモジュール ツイン](iot-hub-devguide-module-twins.md)は、Azure IoT Hub のデバイス ID とデバイス ツインに似ていますが、より細かい粒度を指定できます。 Azure IoT Hub のデバイス ID とデバイス ツインでは、バックエンド アプリケーションがデバイスを構成し、そのデバイスの状態に関する可視性を提供できるのに対して、モジュール ID とモジュール ツインでは、これらの機能がデバイスの個々のコンポーネントに対して提供されます。 複数のコンポーネントで構成される対応デバイス (オペレーティング システム ベースのデバイスやファームウェア デバイスなど) では、構成や状態をコンポーネントごとに分離できます。
>

このチュートリアルの最後には、次の 3 つの Python アプリが完成します。

* **CreateModule**。デバイスとモジュール クライアントを接続するためのデバイス ID、モジュール ID、関連付けられたセキュリティ キーを作成します。

* **UpdateModuleTwinDesiredProperties**。更新されたモジュール ツインの必要なプロパティを IoT Hub に送信します。

* **ReceiveModuleTwinDesiredPropertiesPatch**。デバイスでモジュール ツインの必要なプロパティの修正プログラムを受信します。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、ID レジストリにデバイスを追加してそのデバイスにモジュールを追加するバックエンド サービスを作成します。 このサービスには、**レジストリ書き込み** のアクセス許可 (これには、**レジストリ読み取り** も含まれます) が必要です。 また、必要なプロパティを新しく作成されたモジュールのモジュール ツインに追加するサービスも作成します。 このサービスには、**サービス接続** のアクセス許可が必要です。 これらのアクセス許可を個別に付与する既定の共有アクセス ポリシーが存在しますが、このセクションでは、これらのアクセス許可の両方を含むカスタム共有アクセス ポリシーを作成します。

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>IoT Hub でデバイス ID とモジュール ID を作成する

このセクションでは、IoT ハブの ID レジストリにデバイス ID とモジュール ID を作成する Python サービス アプリを作成します。 ID レジストリにエントリがない限り、デバイスまたはモジュールは IoT ハブに接続できません。 詳細については、「[IoT Hub の ID レジストリを理解する](iot-hub-devguide-identity-registry.md)」を参照してください。 このコンソール アプリを実行すると、デバイスとモジュール両方に対して一意のデバイス ID とキーが生成されます。 デバイスとモジュールは、IoT ハブに device-to-cloud メッセージを送信するときにこれらの値を使用して自分自身を識別します。 ID には大文字と小文字の区別があります。

1. コマンド プロンプトで次のコマンドを実行して **azure-iot-hub** パッケージをインストールします。

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. コマンド プロンプトで、次のコマンドを実行して **msrest** パッケージをインストールします。 このパッケージは、**HTTPOperationError** 例外をキャッチするために必要です。

    ```cmd/sh
    pip install msrest
    ```

1. テキスト エディターを使用して、**CreateModule.py** という名前のファイルを作業ディレクトリに作成します。

1. Python ファイルに次のコードを追加します。 *YourIoTHubConnectionString* を、「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーした接続文字列に置き換えます。

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. コマンド プロンプトで、次のコマンドを実行します。

    ```cmd/sh
    python CreateModule.py
    ```

このアプリは、ID **myFirstDevice** のデバイス ID と ID **myFirstModule** のモジュール ID をデバイス **myFirstDevice** の下に作成します。 (デバイスまたはモジュール ID が ID レジストリに既に存在する場合、コードは単純に、既存のデバイスまたはモジュール情報を取得します。)アプリには、ID と各 ID のプライマリ キーが表示されます。

> [!NOTE]
> IoT ハブの ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID とモジュール ID のみが格納されます。 ID レジストリには、セキュリティ資格情報として使用されるデバイス ID とキーが格納されます。 ID レジストリには、そのデバイスのアクセスを無効にするために使用できる各デバイスの有効/無効フラグも格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 モジュール ID 用の有効/無効フラグはありません。 詳細については、「[IoT Hub の ID レジストリを理解する](iot-hub-devguide-identity-registry.md)」を参照してください。
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Python サービス SDK を使用してモジュール ツインを更新する

このセクションでは、モジュール ツインの必要なプロパティを更新する Python サービス アプリを作成します。

1. コマンド プロンプトで、次のコマンドを実行して **azure-iot-hub** パッケージをインストールします。 前のセクションで **azure-iot-hub** パッケージをインストールした場合は、この手順をスキップできます。

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. テキスト エディターを使用して、**UpdateModuleTwinDesiredProperties.py** という名前のファイルを作業ディレクトリに作成します。

1. Python ファイルに次のコードを追加します。 *YourIoTHubConnectionString* を、「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーした接続文字列に置き換えます。

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>デバイス側を更新する

このセクションでは、デバイスでモジュール ツインの必要なプロパティの更新プログラムを取得する Python アプリを作成します。

1. モジュールの接続文字列を取得します。 [Azure portal](https://portal.azure.com/) で、IoT Hub に移動し、左側のウィンドウで **[IoT デバイス]** を選択します。 デバイスの一覧から **[myFirstDevice]** を選択し、それを開きます。 **[モジュール ID]** で、 **[myFirstModule]** を選択します。 モジュールの接続文字列をコピーします。 これは後の手順で必要になります。

   ![Azure Portal モジュールの詳細](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. コマンド プロンプトで次のコマンドを実行して **azure-iot-device** パッケージをインストールします。

    ```cmd/sh
    pip install azure-iot-device
    ```

1. テキスト エディターを使用して、**ReceiveModuleTwinDesiredPropertiesPatch.py** という名前のファイルを作業ディレクトリに作成します。

1. Python ファイルに次のコードを追加します。 *YourModuleConnectionString* を、手順 1 でコピーしたモジュールの接続文字列に置き換えます。

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>アプリの実行

このセクションでは、**ReceiveModuleTwinDesiredPropertiesPatch** デバイス アプリを実行した後、**UpdateModuleTwinDesiredProperties** サービス アプリを実行して、モジュールの必要なプロパティを更新します。

1. コマンド プロンプトを開き、デバイス アプリを実行します。

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![デバイス アプリの最初の出力](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. 別のコマンド プロンプトを開き、サービス アプリを実行します。

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    サービス アプリの出力の更新されたモジュール ツインに、必要なプロパティ **TelemetryInterval** が表示されることに注意してください。

   ![サービス アプリの出力](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    同じプロパティが、デバイス アプリの出力の受信された必要なプロパティの修正プログラムに表示されます。

   ![デバイス アプリの出力に、必要なプロパティの修正プログラムが表示される](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>次のステップ

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [デバイス管理の概要](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge の概要](../iot-edge/quickstart-linux.md)