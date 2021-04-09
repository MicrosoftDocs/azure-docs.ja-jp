---
title: Azure IoT Central にデバイス テレメトリを送信するクイックスタート (Python)
description: このクイックスタートでは、Python 用 Azure IoT Hub デバイス SDK を使用して、デバイスから IoT Central にテレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: d0dcca0c4be801f385a48afcd41b6a547bab3fbe
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654711"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>クイックスタート: デバイスから Azure IoT Central にテレメトリを送信する (Python)

**適用対象**: [デバイス アプリケーション開発](about-iot-develop.md#device-application-development)

このクイックスタートでは、基本的な IoT デバイス アプリケーション開発のワークフローについて説明します。 まず、Azure IoT Central を使用してクラウド アプリケーションを作成します。 次に、Azure IoT Python SDK を使用して、シミュレートされたデバイスの作成、IoT Central への接続、device-to-cloud テレメトリの送信を行います。 

## <a name="prerequisites"></a>前提条件
- [Python 3.7 以上](https://www.python.org/downloads/)。 サポートされる他のバージョンの Python については、「[Azure IoT デバイスの機能](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)」を参照してください。
    
    Python のバージョンが最新であることを確認するために `python --version` を実行します。 Python 2 と Python 3 の両方がインストールされた状態で Python 3 環境を使用する場合は、`pip3` を使用して、すべてのライブラリをインストールしてください。 `pip3` を実行することで、確実に Python 3 ランタイムに対するライブラリがインストールされます。
    > [!IMPORTANT]
    > Python インストーラーで、 **[Add Python to PATH]\(PATH に Python を追加する\)** オプションを選択します。 既に Python 3.7 以上がインストールされている場合は、`PATH` 環境変数に Python のインストール フォルダーを追加してあることを確認してください。

## <a name="create-an-application"></a>アプリケーションの作成
このセクションでは、IoT Central アプリケーションを作成します。 IoT Central は、ポータルベースの IoT アプリケーション プラットフォームです。IoT ソリューションの開発と管理に伴う複雑さとコストを低減するのに役立ちます。

Azure IoT Central アプリケーションを作成するには:
1. ブラウザーで [Azure IoT Central](https://apps.azureiotcentral.com/) に移動し、Microsoft の個人用アカウントか、職場または学校アカウントでサインインします。
1. **[ビルド]** に移動し、 **[カスタム アプリ]** を選択します。
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="IoT Central のスタート ページ":::
1. **[アプリケーション名]** に一意の名前を入力するか、生成された名前を使用します。
1. **[URL]** に、アプリケーションの覚えやすい URL プレフィックスを入力するか、生成された URL プレフィックスを使用します。
1. **[アプリケーション テンプレート]** の設定は *[カスタム アプリケーション]* のままにしてください。 アカウントに既存のテンプレートがある場合、ドロップダウンに他のオプションが表示されます。
1. **[価格プラン]** オプションを選択します。 
    - アプリケーションを 7 日間無料で使用するには、 **[Free]\(無料\)** を選択します。 無料のアプリケーションは、有効期限が切れる前に標準価格に変換することができます。
    - 必要に応じて標準価格プランを選択可能です。 標準価格を選択した場合は、表示されるオプションが多くなり、 **[ディレクトリ]** 、 **[Azure サブスクリプション]** 、 **[場所]** を設定する必要があります。 価格については、「[Azure IoT Central の価格](https://azure.microsoft.com/pricing/details/iot-central/)」を参照してください。 
        - "**ディレクトリ**" は、アプリケーションを作成する Azure Active Directory です。 Azure Active Directory には、ユーザー ID、資格情報、およびその他の組織情報が含まれています。 Azure Active Directory を持っていない場合は、Azure サブスクリプションを作成するときに作成されます。
        - **Azure サブスクリプション** を使用すると、Azure サービスのインスタンスを作成できます。 IoT Central では、リソースが自分のサブスクリプション内にプロビジョニングされます。 Azure サブスクリプションがない場合は、[無料で作成する](https://azure.microsoft.com/free/)ことができます。 サブスクリプションの作成後、IoT Central の **[新しいアプリケーション]** ページに戻ります。 新しいサブスクリプションが **[Azure サブスクリプション]** ドロップダウンに表示されます。
        - **[場所]** は、アプリケーションの作成先となる [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)です。 最適なパフォーマンスを得るには、ご利用のデバイスに物理的に最も近い場所を選択してください。 いったん場所を選択すると、後でアプリケーションを別の場所に移動することはできません。

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="IoT Central の [新しいアプリケーション] ダイアログ":::
1. **［作成］** を選択します
    
    IoT Central によってアプリケーションが作成された後、アプリケーション ダッシュボードにリダイレクトされます。
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="IoT Central の [新しいアプリケーション] ダッシュボード":::

## <a name="add-a-device"></a>デバイスを追加する
このセクションでは、IoT Central アプリケーションに新しいデバイスを追加します。 デバイスとは、アプリケーションに接続する実際のデバイスまたはシミュレートされたデバイスを表す、デバイス テンプレートのインスタンスです。 

新しいデバイスを作成するには:
1. 左ペインで **[デバイス]** を選択し、 **[+ 新規]** を選択します。 [新規デバイス] ダイアログが開きます。
1. **[デバイス テンプレート]** の設定は *[Unassigned]\(未割り当て\)* のままにしてください。

    > [!NOTE]
    > このクイックスタートでは、簡潔にするために、未割り当てのテンプレートを使用するシミュレートされたデバイスを接続します。 引き続き IoT Central を使用してデバイスを管理する場合は、デバイス テンプレートの使用に関する知識が必要です。 デバイス テンプレートを使った作業の概要については、「[クイックスタート: IoT Central アプリケーションにシミュレートされたデバイスを追加する](../iot-central/core/quick-create-simulated-device.md)」を参照してください。
1. わかりやすい **デバイス名** と **デバイス ID** を選択します。 必要に応じて、生成された値を使用します。
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="IoT Central の新しいデバイス ダイアログ":::
1. **［作成］** を選択します

    作成したデバイスが **[すべてのデバイス]** 一覧に表示されます。
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="IoT Central の [すべてのデバイス] 一覧":::
    
新しいデバイスの接続の詳細を取得するには:
1. **[すべてのデバイス]** 一覧で、デバイス名のリンクをダブルクリックすると詳細が表示されます。 
1. 上部のメニューで、**[接続]** を選択します。

    **[デバイス接続]** ダイアログに、接続の詳細が表示されます。:::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="IoT Central デバイスの接続の詳細":::
1. **[デバイス接続]** ダイアログから以下の値をコピーして、安全な場所に保存します。 これらは、次のセクションでデバイスを IoT Central に接続する際に使用します。
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>メッセージを送信してテレメトリを監視する
このセクションでは、Python SDK を使用して、シミュレートされたデバイスを作成し、テレメトリを自分の IoT Central アプリケーションに送信します。 

1. Windows CMD、PowerShell、Bash (Windows 用または Linux 用) のいずれかを使用してターミナルを開きます。 ターミナルを使用して Python SDK をインストールし、環境変数を更新して、Python コード サンプルを実行します。

1. [Azure IoT Python SDK デバイス サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)をローカル コンピューターにコピーします。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. *azure-iot-sdk-python/azure-iot-device/samples* ディレクトリに移動します。

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Azure IoT Python SDK をインストールします。

    ```console
    pip install azure-iot-device
    ```

1. シミュレートされたデバイスを IoT Central に接続できるよう、次の各環境変数を設定します。 `ID_SCOPE`、`DEVICE_ID`、`DEVICE_KEY` には、IoT Central の *[デバイス接続]* ダイアログから保存した値を使用してください。

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > Windows CMD では、接続文字列などの変数の値を囲む引用符は入力しません。

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux または Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. ターミナルで、サンプル ファイル *simple_send_temperature.py のコードを実行します。 このコードは、シミュレートされた IoT デバイスにアクセスして、IoT Central にメッセージを送信します。

    ターミナルから Python サンプルを実行するには:
    ```console
    python ./simple_send_temperature.py
    ```

    必要に応じて、Python IDE でサンプルの Python コードを実行できます。
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Python コードによってデバイスから IoT Central アプリケーションにメッセージが送信されると、IoT Central でデバイスの **[生データ]** タブにそのメッセージが表示されます。 場合によっては、最新のメッセージを表示するにはページを最新の情報に更新する必要があります。

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="IoT Central の生データ出力のスクリーンショット":::

デバイスが安全に接続され、Azure IoT にテレメトリを送信するようになりました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
以後、このチュートリアルで作成した IoT Central リソースが不要であれば、IoT Central ポータルからそれらを削除することができます。 引き続きこのガイドのドキュメントに従う場合は、必要に応じて、作成したアプリケーションを保持して他のサンプルに再利用することもできます。

Azure IoT Central のサンプル アプリケーションとそのデバイスおよびリソースをすべて削除するには:
1. **[管理]**  >  **<自分のアプリケーション>** を選択します。
1. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、クラウドに対してデバイスを安全に接続し、device-to-cloud テレメトリを送信するための Azure IoT アプリケーションの基本的なワークフローについて説明しました。 Azure IoT Central を使用してアプリケーションとデバイスを作成した後、Azure IoT Python SDK を使用してシミュレートされたデバイスを作成し、テレメトリを送信しました。 また、IoT Central を使用してテレメトリを監視しました。

次のステップとして、アプリケーション サンプルを通じて Azure IoT Python SDK を掘り下げて見てみましょう。

- [非同期サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): このディレクトリには、その他の IoT Hub シナリオを想定した非同期 Python サンプルが含まれています。
- [同期サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): このディレクトリには、Python 2.7 (または Python 3.5 以上における同期の互換性シナリオ) 用の Python サンプルが含まれています。
- [IoT Edge サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): このディレクトリには、Edge モジュールとダウンストリーム デバイスを扱うための Python サンプルが含まれています。
