---
title: クイック スタート:Azure Blob Storage ライブラリ v12 - Python
description: このクイックスタートでは、Python 用 Azure Blob Storage クライアント ライブラリ バージョン 12 を使用して、BLOB (オブジェクト) ストレージ内にコンテナーと BLOB を作成する方法について説明します。 次に、ローカル コンピューターに BLOB をダウンロードする方法と、コンテナー内のすべての BLOB を一覧表示する方法について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: dce42f1b8bfe58e4d147ba1c982cb770590dfa92
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87845121"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>クイック スタート:Python v12 SDK で BLOB を管理する

このクイックスタートでは、Python を使用して BLOB を管理する方法について説明します。 BLOB は、大量のテキストやバイナリ データ (画像、ドキュメント、ストリーミング メディア、アーカイブ データなど) を保持できるオブジェクトです。 ここでは、BLOB のアップロード、ダウンロード、一覧表示のほか、コンテナーの作成と削除を行います。

その他のリソース:

* [API リファレンス ドキュメント](/python/api/azure-storage-blob)
* [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [パッケージ (Python Package Index)](https://pypi.org/project/azure-storage-blob/)
* [サンプル](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure Storage のアカウント [ストレージ アカウントの作成](../common/storage-account-create.md)。
- [Python](https://www.python.org/downloads/) 2.7、3.5 以降

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>設定

このセクションでは、Python 用 Azure Blob Storage クライアント ライブラリ v12 を操作するためのプロジェクトの準備について説明します。

### <a name="create-the-project"></a>プロジェクトを作成する

*blob-quickstart-v12* という名前の Python アプリケーションを作成します。

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) で、プロジェクト用に新しいディレクトリを作成します。

    ```console
    mkdir blob-quickstart-v12
    ```

1. 新しく作成された *blob-quickstart-v12* ディレクトリに切り替えます。

    ```console
    cd blob-quickstart-v12
    ```

1. *blob-quickstart-v12* ディレクトリ内に、*data* という別のディレクトリを作成します。 BLOB データ ファイルは、ここに作成され格納されます。

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`pip install` コマンドを使用して、Python 用の Azure Blob Storage クライアント ライブラリ パッケージをインストールします。

```console
pip install azure-storage-blob
```

このコマンドでは、Python パッケージ用 Azure Blob Storage クライアント ライブラリとそれに依存しているすべてのライブラリをインストールします。 この場合、これは Python 用の Azure コア ライブラリになります。

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. コード エディターで新しいテキスト ファイルを開きます
1. `import` ステートメントを追加します
1. 基本的な例外処理を含め、プログラムの構造を作成します

    コードは次のとおりです。

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. *blob-quickstart-v12* ディレクトリに新しいファイルを *blob-quickstart-v12.py* として保存する

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>オブジェクト モデル

Azure Blob Storage は、大量の非構造化データを格納するために最適化されています。 非構造化データとは、特定のデータ モデルや定義に従っていないデータであり、テキスト データやバイナリ データなどがあります。 Blob Storage には、3 種類のリソースがあります。

* ストレージ アカウント
* ストレージ アカウント内のコンテナー
* コンテナー内の BLOB

次の図に、これらのリソースの関係を示します。

![Blob Storage のアーキテクチャ図](./media/storage-blobs-introduction/blob1.png)

これらのリソースとやり取りするには、以下の Python クラスを使用します。

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient):`BlobServiceClient` クラスを使用して、Azure Storage リソースと BLOB コンテナーを操作できます。
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient):`ContainerClient` クラスを使用して、Azure Storage コンテナーとその BLOB を操作できます。
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient):`BlobClient` クラスを使用して、Azure Storage BLOB を操作できます。

## <a name="code-examples"></a>コード例

以下のサンプル コード スニペットは、Python 用 Azure Blob Storage クライアント ライブラリを使用して以下を実行する方法を示します。

* [接続文字列を取得する](#get-the-connection-string)
* [コンテナーの作成](#create-a-container)
* [コンテナーに BLOB をアップロードする](#upload-blobs-to-a-container)
* [コンテナー内の BLOB を一覧表示する](#list-the-blobs-in-a-container)
* [BLOB をダウンロードする](#download-blobs)
* [コンテナーの削除](#delete-a-container)

### <a name="get-the-connection-string"></a>接続文字列を取得する

以下のコードでは、「[ストレージ接続文字列の構成](#configure-your-storage-connection-string)」セクションで作成した環境変数から、ストレージ アカウントに対する接続文字列を取得します。

このコードを `try` ブロック内に追加します。

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>コンテナーを作成する

新しいコンテナーの名前を決定します。 次のコードでは、確実に一意になるように、コンテナー名に UUID 値を追加します。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前付けの詳細については、「[Naming and Referencing Containers, Blobs, and Metadata (コンテナー、BLOB、メタデータの名前付けと参照)](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

[from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) メソッドを呼び出して、[BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) クラスのインスタンスを作成します。 次に、[create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) メソッドを呼び出し、実際にストレージ アカウントにコンテナーを作成します。

`try` ブロックの末尾に、次のコードを追加します。

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>コンテナーに BLOB をアップロードする

次のコード スニペット:

1. ローカル ディレクトリにテキスト ファイルを作成します。
1. 「[コンテナーを作成する](#create-a-container)」セクションからの [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) の [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) メソッドを呼び出すことで、[BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) オブジェクトへの参照を取得します。
1. [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) メソッドを呼び出して、ローカル テキスト ファイルを BLOB にアップロードします。

`try` ブロックの末尾に、次のコードを追加します。

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

[list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) メソッドを呼び出して、コンテナー内の BLOB を一覧表示します。 この場合、コンテナーに BLOB が 1 つだけ追加されているので、一覧表示操作ではその 1 つの BLOB だけが返されます。

`try` ブロックの末尾に、次のコードを追加します。

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>BLOB をダウンロードする

[download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) メソッドを呼び出して、以前に作成した BLOB をダウンロードします。 サンプル コードでは、ローカル ファイル システム内で両方のファイルを見ることができるように、ファイル名に "DOWNLOAD" というサフィックスを追加します。

`try` ブロックの末尾に、次のコードを追加します。

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>コンテナーを削除する

次のコードでは、[delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) メソッドを使用して、コンテナー全体を削除することによって、アプリが作成したリソースがクリーンアップされます。 必要に応じてローカル ファイルを削除することもできます。

アプリでは、BLOB、コンテナー、およびローカル ファイルを削除する前に、`input()` を呼び出すことで、ユーザーの入力を一時停止します。 このとき、リソースが削除される前に、正しく作成されたことを確認できます。

`try` ブロックの末尾に、次のコードを追加します。

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>コードの実行

このアプリでは、ローカル フォルダーにテスト ファイルが作成され、BLOB ストレージにアップロードされます。 次に、コンテナー内の BLOB を一覧表示し、ファイルを新しい名前でダウンロードして、古いファイルと新しいファイルを比較できるようにします。

*blob-quickstart-v12.py* ファイルが格納されているディレクトリに移動し、次の `python` コマンドを実行してアプリを実行します。

```console
python blob-quickstart-v12.py
```

アプリの出力は、次の例のようになります。

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

クリーンアップ プロセスを開始する前に、*data* フォルダー内の 2 つのファイルをチェックします。 それらを開いて、同じであるかどうかを確認します。

ファイルを確認した後、**Enter** キーを押してテスト ファイルを削除し、デモを終了します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Python を使用して BLOB をアップロード、ダウンロード、一覧表示する方法について説明しました。

BLOB ストレージのサンプル アプリを確認するには、以下に進んでください。

> [!div class="nextstepaction"]
> [Azure BLOB ストレージ SDK v12 の Python サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* 詳細については、「[Python 用 Azure Storage クライアント ライブラリ](/azure/developer/python/sdk/storage/overview?view=storage-py-v12)」を参照してください。
* チュートリアル、サンプル、クイックスタートなどのドキュメントについては、「[Python 開発者向けの Azure](/azure/python/)」を参照してください。
