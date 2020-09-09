---
title: チュートリアル - 汎用の Python クライアント アプリを Azure IoT Central に接続する | Microsoft Docs
description: このチュートリアルでは、デバイス開発者を対象に、Python クライアント アプリが実行されているデバイスを Azure IoT Central アプリケーションに接続する方法を説明します。 デバイス機能モデルをインポートしてデバイス テンプレートを作成し、接続されているデバイスを対話的に操作できるビューを追加します。
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: devx-track-python
ms.openlocfilehash: b79dce7755d6fab633a442839ae3ff8643d0b554
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876701"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>チュートリアル:クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

"*この記事は、ソリューション ビルダーおよびデバイス開発者を対象としています。* "

このチュートリアルでは、デバイス開発者を対象に、Python クライアント アプリケーションを Azure IoT Central アプリケーションに接続する方法を説明します。 Python アプリケーションは、環境センサー デバイスの動作をシミュレートします。 IoT Central で、サンプル "_デバイス機能モデル_" を使用して "_デバイス テンプレート_" を作成します。 デバイス テンプレートにビューを追加して、オペレーターがデバイスと対話できるようにします。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイス機能モデルをインポートしてデバイス テンプレートを作成する。
> * 既定のビューとカスタム ビューをデバイス テンプレートに追加する。
> * デバイス テンプレートを発行し、Azure IoT Central アプリケーションに実デバイスを追加する。
> * Python デバイス コードを作成して実行し、それが IoT Central アプリケーションに接続されることを確認する。
> * デバイスから送信される、シミュレートされたテレメトリを表示する。
> * ビューを使用してデバイス プロパティを管理する。
> * 同期と非同期のコマンドを呼び出してデバイスを制御する。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* **カスタム アプリケーション** テンプレートを使用して作成された Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](quick-deploy-iot-central.md)に関するページをご覧ください。 アプリケーションは、2020 年 7 月 14 日以降に作成されている必要があります。
* [Python](https://www.python.org/) バージョン 3.7 以降がインストールされた開発用マシン。 コマンドラインで `python3 --version` を実行して、お使いのバージョンを確認できます。 Python は、さまざまなオペレーティング システムで使用できます。 このチュートリアルの手順では、Windows コマンド プロンプトで **python3** コマンドを実行していることを前提としています。

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Python アプリケーションを作成する

以降の手順では、アプリケーションに追加した実デバイスに接続する Python クライアント アプリケーションの作成方法を説明します。 この Python アプリケーションは、実デバイスの動作をシミュレートします。

1. コマンドライン環境で、先ほど作成した `environmental-sensor` フォルダーに移動します。

1. 必要なライブラリをインストールするには、次のコマンドを実行します。

    ```cmd
    pip install azure-iot-device
    ```

1. `environmental-sensor` フォルダーに、**environmental_sensor.py** という名前のファイルを作成します。

1. **environmental_sensor.py** ファイルの先頭に、次の `import` ステートメントを追加します。

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. 次の非同期 `main` 関数と変数の宣言をファイルに追加します。

    ```python
    async def main():
      # In a production environment, don't store
      # connection information in the code.
      provisioning_host = 'global.azure-devices-provisioning.net'
      id_scope = '{your Scope ID}'
      registration_id = '{your Device ID}'
      symmetric_key = '{your Primary Key}'

      delay = 2

      # All the remaining code is nested within this main function

    if __name__ == '__main__':
    asyncio.run(main())
    ```

    `{your Scope ID}`、`{your Device ID}`、`{your Primary Key}` の各プレースホルダーを、前に書き留めた値で更新します。 実際のアプリケーションでは、この情報をアプリケーションにハード コーディングしないでください。

    次のすべての関数の定義とコードは、`main` 関数内で入れ子になっています。

1. デバイスを登録して IoT Central アプリケーションに接続するには、`main` 関数内に次の 2 つの関数を追加します。 登録では、Azure デバイス プロビジョニング サービスを使用します。

    ```python
      async def register_device():
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
          provisioning_host=provisioning_host,
          registration_id=registration_id,
          id_scope=id_scope,
          symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print(f'Registration result: {registration_result.status}')

        return registration_result

      async def connect_device():
        device_client = None
        try:
          registration_result = await register_device()
          if registration_result.status == 'assigned':
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
              symmetric_key=symmetric_key,
              hostname=registration_result.registration_state.assigned_hub,
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. Azure IoT Central アプリケーションにテレメトリを送信するために、次の関数を `main` 関数内に追加します。

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    テレメトリ項目の名前 (`temp` と `humid`) は、デバイス テンプレートで使用されている名前と一致している必要があります。

1. IoT Central アプリケーションから呼び出されるコマンドを処理するために、次の関数を `main` 関数内に追加します。

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

      async def turnon_command(request):
        print('Turning on the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      async def turnoff_command(request):
        print('Turning off the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      commands = {
        'blink': blink_command,
        'rundiagnostics': diagnostics_command,
        'turnon': turnon_command,
        'turnoff': turnoff_command,
      }

      # Define behavior for handling commands
      async def command_listener():
        while True:
          method_request = await device_client.receive_method_request()  # Wait for commands
          await commands[method_request.name](method_request)
    ```

    コマンドの名前 (`blink`、`turnon`、`turnoff`、`rundiagnostics`) は、デバイス テンプレートで使用されている名前と一致している必要があります。

    現在、IoT Central では、デバイス機能モデルで定義されている応答スキーマを使用しません。 同期コマンドの場合、応答ペイロードは任意の有効な JSON にすることができます。 非同期コマンドの場合、デバイスはすぐに 202 応答を返し、その後、作業が完了したときに reported プロパティの更新を返します。 reported プロパティの更新の形式は次のとおりです。

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    オペレーターは、コマンド履歴で応答ペイロードを表示できます。

1. IoT Central アプリケーションから送信されるプロパティの更新を処理するために、次の関数を `main` 関数内に追加します。 [書き込み可能なプロパティの更新](concepts-telemetry-properties-commands.md#writeable-property-types)に応答してデバイスから送信されるメッセージには、`av` フィールドと `ac` フィールドが含まれている必要があります。 `ad` フィールドは省略可能です。

    ```python
      async def name_setting(value, version):
        await asyncio.sleep(1)
        print(f'Setting name value {value} - {version}')
        await device_client.patch_twin_reported_properties({'name' : {'value': value, 'ad': 'completed', 'ac': 200, 'av': version}})

      async def brightness_setting(value, version):
        await asyncio.sleep(5)
        print(f'Setting brightness value {value} - {version}')
        await device_client.patch_twin_reported_properties({'brightness' : {'value': value, 'ad': 'completed', 'ac': 200, 'av': version}})

      settings = {
        'name': name_setting,
        'brightness': brightness_setting
      }

      # define behavior for receiving a twin patch
      async def twin_patch_listener():
        while True:
          patch = await device_client.receive_twin_desired_properties_patch() # blocking
          to_update = patch.keys() & settings.keys()
          await asyncio.gather(
            *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
          )
    ```

    オペレーターが IoT Central アプリケーションで書き込み可能なプロパティを設定すると、アプリケーションではデバイス ツインによって必要とされるプロパティを使用して、デバイスに値を送信します。 次に、デバイスによってデバイス ツインの reported プロパティが使用され、応答が行われます。 IoT Central は、reported プロパティの値を受け取ると、**同期済み**の状態でプロパティ ビューを更新します。

    プロパティの名前 (`name` と `brightness`) は、デバイス テンプレートで使用されている名前と一致している必要があります。

1. アプリケーションを制御するために、次の関数を `main` 関数内に追加します。

    ```python
      # Define behavior for halting the application
      def stdin_listener():
        while True:
          selection = input('Press Q to quit\n')
          if selection == 'Q' or selection == 'q':
            print('Quitting...')
            break

      device_client = await connect_device()

      if device_client is not None and device_client.connected:
        print('Send reported properties on startup')
        await device_client.patch_twin_reported_properties({'state': 'true', 'processorArchitecture': 'ARM', 'swVersion': '1.0.0'})
        tasks = asyncio.gather(
          send_telemetry(),
          command_listener(),
          twin_patch_listener(),
        )

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)

        # Wait for user to indicate they are done listening for method calls
        await user_finished

        # Cancel tasks
        tasks.add_done_callback(lambda r: r.exception())
        tasks.cancel()
        await device_client.disconnect()

      else:
        print('Device could not connect')
    ```

1. **environmental_sensor.py** ファイルを保存します。

## <a name="run-your-python-application"></a>Python アプリケーションを実行する

デバイス クライアント アプリケーションを起動するには、コマンドライン環境で次のコマンドを実行します。

```cmd
python3 environmental_sensor.py
```

デバイスが Azure IoT Central アプリケーションに接続して、テレメトリを送信し始めたことがわかります。

![クライアント アプリケーションを実行する](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

コマンドとプロパティの更新に対してデバイスがどのように応答するかを確認できます。

![クライアント アプリケーションを観察する](media/tutorial-connect-device-python/run-application-2.png)

## <a name="view-raw-data"></a>生データを表示する

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>次のステップ

デバイス開発者として、Python を使用してデバイスを作成する方法の基本を学習しました。推奨される次の手順は以下のとおりです。

* デバイス コードを実装する際のデバイス テンプレートの役割について詳しくは、「[デバイス テンプレートとは](./concepts-device-templates.md)」をご覧ください。
* IoT Central にデバイスを登録する方法と IoT Central でデバイスの接続を保護する方法の詳細については、「[Azure IoT Central に接続する](./concepts-get-connected.md)」を参照してください。

一連の IoT Central チュートリアルを続行し、IoT Central ソリューションの構築方法の詳細を確認するには、次のページを参照してください。

> [!div class="nextstepaction"]
> [ゲートウェイ デバイス テンプレートを作成する](./tutorial-define-gateway-device-type.md)
