---
title: Python を使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs
description: Azure IoT device SDK for Python を使用して、デバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 77d51b2c839a64567838fa4d6308d203a6bb8b82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501143"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

この記事では、[IoT Hub のファイル アップロード機能](iot-hub-devguide-file-upload.md)を使用してファイルを [Azure Blob Storage](../storage/index.yml) にアップロードする方法を説明します。 このチュートリアルでは、次の操作方法について説明します。

* ファイルをアップロードするためのストレージ コンテナーを安全に提供します。

* Python クライアントを使用して、IoT ハブ経由でファイルをアップロードします。

[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するクイックスタートでは、IoT Hub のデバイスからクラウドへの基本的なメッセージング機能が示されます。 ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。 デバイスからファイルをアップロードする必要がある場合も、IoT Hub のセキュリティを信頼性を使用できます。

このチュートリアルの最後に、Python コンソール アプリを実行します。

* **FileUpload.py** は、Python デバイス SDK を使用してファイルをストレージにアップロードします。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

このセクションでは、IoT Hub にファイルをアップロードするデバイス アプリを作成します。

1. コマンド プロンプトで次のコマンドを実行して **azure-iot-device** パッケージをインストールします。 このパッケージを使用して、IoT ハブでファイルのアップロードを調整します。

    ```cmd/sh
    pip install azure-iot-device
    ```

1. コマンド プロンプトで次のコマンドを実行して [**azure.storage.blob**](https://pypi.org/project/azure-storage-blob/) パッケージをインストールします。 このパッケージを使用して、ファイルのアップロードを実行します。

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. BLOB ストレージにアップロードするテスト ファイルを作成します。

1. テキスト エディターを使用して、作業フォルダーに **FileUpload.py** ファイルを作成します。

1. **FileUpload.py** ファイルの先頭に次の `import` ステートメントと変数を追加します。

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. ファイル内で、`[Device Connection String]` を IoT Hub デバイスの接続文字列に置き換えます。 `[Full path to local file]` を、作成したテスト ファイル、またはアップロードするデバイス上の任意のファイルのパスに置き換えます。

1. ファイルを BLOB ストレージにアップロードする関数を作成します。

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    この関数は、渡された *blob_info* 構造体を解析して、[azure.storage.blob.BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) を初期化するために使用する URL を作成します。 次に、このクライアントを使用して、ファイルが Azure BLOB ストレージにアップロードされます。

1. 次のコードを追加して、クライアントを接続し、ファイルをアップロードします。

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    このコードでは、非同期の **IoTHubDeviceClient** が作成され、次の API を使用して IoT ハブでファイルのアップロードを管理します。

    * **get_storage_info_for_blob** は、前に作成したリンクされたストレージ アカウントに関する情報を IoT ハブから取得します。 この情報には、ホスト名、コンテナー名、BLOB 名、および SAS トークンが含まれます。 ストレージ情報は **store_blob** 関数 (前の手順で作成) に渡されるため、その関数の **BlobClient** は Azure ストレージで認証できます。 **get_storage_info_for_blob** メソッドでは、**notify_blob_upload_status** メソッドで使用される correlation_id も返されます。 correlation_id は、IoT Hub で作業している BLOB をマークする方法です。

    * **notify_blob_upload_status** により、BLOB ストレージ操作の状態が IoT Hub に通知されます。 **get_storage_info_for_blob** メソッドによって取得された correlation_id を渡します。 これは、ファイルのアップロード タスクの状態に関する通知をリッスンしている可能性があるサービスに通知するために IoT Hub によって使用されます。

1. **UploadFile.py** ファイルを保存して閉じます。

## <a name="run-the-application"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. 作業フォルダーのコマンド プロンプトで、次のコマンドを実行します。

    ```cmd/sh
    python FileUpload.py
    ```

2. 次のスクリーン ショットは、**FileUpload** アプリからの出力を示しています。

    ![simulated-device アプリからの出力](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. ポータルを使用して、構成したストレージ コンテナーにアップロードされたファイルを表示できます。

    ![アップロードされたファイル](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub の作成](iot-hub-rm-template-powershell.md)

* [C SDK の概要](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

Azure Blob Storage の詳細については、次のリンク先を参照してください。

* [Azure Blob Storage のドキュメント](../storage/blobs/index.yml)

* [Python API 用 Azure Blob Storage のドキュメント](/python/api/overview/azure/storage-blob-readme)