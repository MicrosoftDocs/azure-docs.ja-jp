---
title: Azure IoT Hub モジュール ID とモジュール ツイン (Python) の概要 | Microsoft Docs
description: モジュール ID を作成し、IoT SDKs for Python を使用してモジュール ツインを更新する方法を説明します。
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: menchi
ms.openlocfilehash: d9aeb5130f565c5fc24e2d0ced9c7ce16d2927ef
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001162"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>IoT Hub モジュール ID とモジュール ツイン (Python) の概要

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [モジュール ID とモジュール ツイン](iot-hub-devguide-module-twins.md)は Azure IoT Hub のデバイス ID とデバイス ツインに類似していますが、より細かい粒度で設定できます。 Azure IoT Hub のデバイス ID とデバイス ツインを使用した場合、バックエンド アプリケーションからデバイスを構成し、デバイスの状態を可視化できるのに対し、モジュール ID とモジュール ツインでは、デバイスの各コンポーネントごとにこれらの機能を実現できます。 複数のコンポーネントで構成され、この機能をサポートしているデバイス (オペレーティング システム ベースのデバイスやファームウェア デバイスなど) であれば、各コンポーネントの状態を可視化し、個別に構成することができます。
>

このチュートリアルの最後には、次の 2 つの Python アプリが完成します。

* **CreateIdentities** は、デバイスの ID、モジュール ID と、関連づけられたセキュリティ キーを作成してデバイスおよびモジュール クライアントと接続します。

* **UpdateModuleTwinReportedProperties** は、更新されたモジュール ツインによって報告されたプロパティを IoT Hub に送信します。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>IoT Hub でデバイス ID とモジュール ID を作成する

このセクションでは、IoT ハブの ID レジストリにデバイス ID とモジュール ID を作成する Python アプリを作成します。 IoT ハブに接続するデバイスまたはモジュールは、あらかじめ ID レジストリに登録されている必要があります。 詳しくは、[IoT Hub 開発者ガイド](iot-hub-devguide-identity-registry.md)の "ID レジストリ" に関するセクションをご覧ください このコンソール アプリを実行すると、デバイスとモジュール両方に対して一意のデバイス ID とキーが生成されます。 デバイスとモジュールは、IoT ハブに device-to-cloud メッセージを送信するときにこれらの値を使用して自分自身を識別します。 ID には大文字と小文字の区別があります。

次のコードを Python ファイルに追加します。

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(
        DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID +
          "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(
        DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID +
          "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print("Unexpected error {0}".format(iothub_error))
except KeyboardInterrupt:
    print("IoTHubRegistryManager sample stopped")
```

このアプリは、ID **myFirstDevice** のデバイス ID と ID **myFirstModule** のモジュール ID をデバイス **myFirstDevice** の下に作成します。 (そのモジュール ID が既に ID レジストリに存在する場合は、単にその既存のモジュール情報を取得します。)続けてその ID のプライマリ キーが表示されます。 シミュレートされたモジュール アプリでこのキーを使用し、IoT ハブに接続します。

> [!NOTE]
> IoT ハブの ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID とモジュール ID のみが格納されます。 ID レジストリには、セキュリティ資格情報として使用されるデバイス ID とキーが格納されます。 ID レジストリには、そのデバイスのアクセスを無効にするために使用できる各デバイスの有効/無効フラグも格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 モジュール ID 用の有効/無効フラグはありません。 詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide-identity-registry.md)をご覧ください。
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Python デバイス SDK を使用してモジュール ツインを更新する

このセクションでは、モジュール ツインによって報告されるプロパティを更新する Python アプリをシミュレートされたデバイス上に作成します。

1. **モジュールの接続文字列を取得する** -- [Azure portal](https://portal.azure.com/) にサインインします。 IoT Hub に移動し、[IoT デバイス] をクリックします。 myFirstDevice を検索して開くと、myFirstModule が正常に作成されていることを確認できます。 モジュールの接続文字列をコピーします。 これは、次の手順で必要になります。

   ![Azure Portal モジュールの詳細](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **UpdateModuleTwinReportedProperties アプリを作成する**

   **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

このコード サンプルは、モジュール ツインを取得し、報告されたプロパティを AMQP プロトコルを使って更新する方法を示したものです。

## <a name="get-updates-on-the-device-side"></a>デバイス側を更新する

上記のコードに加え、デバイス上のツイン更新メッセージを取得する以下のコード ブロックを追加できます。

```python
import time
import threading
from azure.iot.device import IoTHubModuleClient

CONNECTION_STRING = "{deviceConnectionString}"


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

## <a name="next-steps"></a>次の手順

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [デバイス管理の概要](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge の概要](../iot-edge/tutorial-simulate-device-linux.md)