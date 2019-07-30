---
title: Azure クイック スタート - Python を使用してオブジェクト ストレージに BLOB を作成する | Microsoft Docs
description: このクイック スタートでは、ストレージ アカウントとコンテナーをオブジェクト (BLOB) ストレージ内に作成します。 その後、Python 用のストレージ クライアント ライブラリを使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: 5fe011d740b1c08ae3b9cf4e3ea67d2cdd4fee66
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360100"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>クイック スタート:Python を使用して BLOB をアップロード、ダウンロード、および一覧表示する

このクイック スタートでは、Python を使用して、Azure Blob Storage 内のコンテナーでブロック BLOB のアップロード、ダウンロード、一覧取得を行う方法を説明します。 BLOB は、任意のサイズのテキストまたはバイナリ データ (画像、ドキュメント、ストリーミング メディア、アーカイブ データなど) を格納できるオブジェクトであり、Azure Storage において、ファイル共有、スキーマレス テーブル、メッセージ キューとは明確に区別されます。 詳細については、[Azure Storage の概要](/azure/storage/common/storage-introduction)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

次の追加の前提条件がインストールされていることを確認してください。

* [Python](https://www.python.org/downloads/)
* [Azure Storage SDK for Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード
このクイック スタートの[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git)は、基本的な Python アプリケーションです。  

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

このコマンドを実行すると、*Azure-Samples/storage-blobs-python-quickstart* リポジトリがローカルの git フォルダーに複製されます。 Python プログラムを実行するには、リポジトリのルートにある *example.py* ファイルを開きます。  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成
アプリケーションで、ストレージ アカウント名とアカウント キーを指定して `BlockBlobService` オブジェクトを作成します。 お使いの IDE のソリューション エクスプローラーから *example.py* ファイルを開きます。 `accountname` と `accountkey` の値をアカウント名とキーに置き換えます。 

```python
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')
```

## <a name="run-the-sample"></a>サンプルを実行する
このサンプルでは、"*ドキュメント*" フォルダーにテスト ファイルを作成します。 サンプル プログラムは、Blob Storage にテスト ファイルをアップロードし、コンテナー内の BLOB を一覧表示し、新しい名前を付けてファイルをダウンロードします。 

最初に、`pip install` を実行して依存関係をインストールします。

```python
    pip install azure-storage-blob
```

次に、サンプルを実行します。 次の出力のようなメッセージが表示されます。
  
```output
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
続行する前に、"*ドキュメント*" フォルダーに 2 つのファイルがあることを確認します。 それらを開くと、内容が同じであることがわかります。

[Azure Storage Explorer](https://storageexplorer.com) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。 

ファイルを確認した後、任意のキーを押してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、*example.py* ファイルを開いてコードを確認します。 

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

それでは、しくみを理解するためにサンプル コードを見ていきましょう。

### <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する
最初に、Blob Storage へのアクセスと管理のために使用されるオブジェクトへの参照を作成します。 これらのオブジェクトはお互いを基にして作成され、各オブジェクトは、一覧で次にあるオブジェクトによって使われます。

* お使いのストレージ アカウントの Blob service を指す **BlockBlobService** オブジェクトをインスタンス化します。 

* アクセスしているコンテナーを表す **CloudBlobContainer** オブジェクトをインスタンス化します。 コンテナーは、コンピューターでフォルダーを使ってファイルを整理するのと同じように、BLOB を整理するために使われます。

クラウド BLOB コンテナーを作成した後は、関心がある特定の BLOB を指す **CloudBlockBlob** オブジェクトをインスタンス化します。 これにより、必要に応じて BLOB をアップロード、ダウンロード、コピーできるようになります。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[Naming and referencing Containers, Blobs, and Metadata (コンテナー、BLOB、およびメタデータの名前付けと参照)](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

このセクションでは、オブジェクトをインスタンス化し、新しいコンテナーを作成した後、BLOB がパブリックになるようにコンテナーに対するアクセス許可を設定します。 コンテナーの名前は **quickstartblobs** です。 

```python
# Create the BlockBlockService that is used to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage では、ブロック BLOB、追加 BLOB、およびページ BLOB がサポートされています。 最もよく使われるのはブロック BLOB であり、このクイックスタートでもそれを使います。  

ファイルを BLOB にアップロードするには、ディレクトリ名をローカル ドライブ上のファイル名と結合してファイルの完全なパスを取得します。 その後、`create_blob_from_path` メソッドを使用して、指定したパスにファイルをアップロードできます。 

サンプル コードは、アップロードとダウンロードに使うローカル ファイルを作成し、アップロードするファイルを *full_path_to_file* として、BLOB の名前を *local_file_name* として格納します。 次の例では、ファイルを **quickstartblobs** という名前のコンテナーにアップロードします。

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Blob Storage では複数のアップロード方法を使うことができます。 たとえば、メモリ ストリームがある場合は、`create_blob_from_path` ではなく `create_blob_from_stream` メソッドを使用できます。 

ブロック BLOB の最大サイズは 4.7 TB であり、Excel スプレッドシートから大きなビデオ ファイルまで何にでも使うことができます。 ページ BLOB は、主に、IaaS VM のバックアップ用の VHD ファイルに使われます。 追加 BLOB は、ファイルに書き込んでから詳細情報を追加し続ける場合などの、ログ記録に使用されます。 BLOB ストレージに格納されているほとんどのオブジェクトはブロック BLOB です。

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

`list_blobs` メソッドを使用して、コンテナー内のファイルの一覧を取得します。 このメソッドは、ジェネレーターを返します。 次のコードは、BLOB の一覧を取得し、&mdash;ループ処理&mdash;して、コンテナー内に見つかった BLOB の名前を表示します。  

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>BLOB のダウンロード

`get_blob_to_path` メソッドを使用して、ローカル ディスクに BLOB をダウンロードします。 次のコードは、前のセクションでアップロードされた BLOB をダウンロードします。 BLOB の名前にサフィックスとして *_DOWNLOADED* が追加されるため、ローカル ディスク上に両方のファイルが存在します。 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(
    local_file_name, '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイック スタートでアップロードした BLOB が不要になった場合は、`delete_container` メソッドを使ってコンテナー全体を削除できます。 代わりに個々のファイルを削除するには、`delete_blob` メソッドを使用します。

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>BLOB を使用する Python アプリケーションを開発するためのリソース

Blob Storage を使用する Python 開発の詳細については、以下の追加リソースを参照してください。

### <a name="binaries-and-source-code"></a>バイナリとソース コード

- GitHub で Azure Storage 用の [Python クライアント ライブラリ ソース コード](https://github.com/Azure/azure-storage-python)を確認し、ダウンロードしてインストールします。

### <a name="client-library-reference-and-samples"></a>クライアント ライブラリ リファレンスとサンプル

- Python クライアント ライブラリの詳細については、[Python API リファレンス](https://docs.microsoft.com/python/api/overview/azure/storage)を参照してください。
- Python クライアント ライブラリを使用して記述された [Blob Storage のサンプル](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob)を確認します。

## <a name="next-steps"></a>次の手順
 
このクイック スタートでは、Python を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 Blob Storage の操作の詳細を学習するには、Blob Storage の操作方法に進みます。

> [!div class="nextstepaction"]
> [Blob Storage の操作方法](./storage-python-how-to-use-blob-storage.md)
 
Storage Explorer と BLOB について詳しくは、「[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。
