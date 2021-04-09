---
title: クイック スタート:Python 用 Azure Blob Storage クライアント ライブラリ v2.1
description: このクイック スタートでは、ストレージ アカウントとコンテナーをオブジェクト (BLOB) ストレージ内に作成します。 その後、Python 用のストレージ クライアント ライブラリ v2.1 を使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 34205caf03d2f2d7255f75ea6203c5572c4c429b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95523308"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>クイック スタート:Python v2.1 SDK で BLOB を管理する

このクイックスタートでは、Python を使用して BLOB を管理する方法について説明します。 BLOB は、大量のテキストやバイナリ データ (画像、ドキュメント、ストリーミング メディア、アーカイブ データなど) を保持できるオブジェクトです。 ここでは、BLOB のアップロード、ダウンロード、一覧表示のほか、コンテナーの作成と削除を行います。

> [!NOTE]
> このクイックスタートでは、Azure Blob Storage クライアント ライブラリのレガシー バージョンを使用します。 最新バージョンで始めるには、「[クイックスタート: Python v12 SDK で BLOB を管理する](storage-quickstart-blobs-python.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure Storage のアカウント [ストレージ アカウントの作成](../common/storage-account-create.md)。
- [Python](https://www.python.org/downloads/)。
- [Azure Storage SDK for Python](https://github.com/Azure/azure-sdk-for-python)。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイック スタートの[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git)は、基本的な Python アプリケーションです。  

次の [git](https://git-scm.com/) コマンドを使用して、アプリケーションを開発環境にダウンロードします。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Python プログラムを確認するには、リポジトリのルートにある *example.py* ファイルを開きます。  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

アプリケーションで、ストレージ アカウント名とアカウント キーを指定して `BlockBlobService` オブジェクトを作成します。

1. お使いの IDE のソリューション エクスプローラーから *example.py* ファイルを開きます。

1. `accountname` と `accountkey` の値をストレージ アカウント名とキーに置き換えます。

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. ファイルを保存して閉じます。

## <a name="run-the-sample"></a>サンプルを実行する

このサンプル プログラムでは、*Documents* フォルダーにテスト ファイルを作成し、BLOB ストレージにファイルをアップロードし、ファイル内の BLOB を一覧表示し、新しい名前でファイルをダウンロードします。

1. 依存関係をインストールする:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. サンプル アプリケーションに移動します。

    ```console
    cd storage-blobs-python-quickstart
    ```

1. サンプルを実行します。

    ```console
    python example.py
    ```

    次の出力のようなメッセージが表示されます。
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. 続行する前に、*Documents* フォルダーに移動して、2 つのファイルがあることを確認します。

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_\<universally-unique-identifier\>_DOWNLOADED*

1. それらを開くと、内容が同じであることがわかります。

    [Azure Storage Explorer](https://storageexplorer.com) などのツールを使用することもできます。 BLOB ストレージ内のファイルを表示するために適しています。 Azure Storage Explorer は、ストレージ アカウント情報にアクセスできる無料のクロスプラットフォーム ツールです。 

1. ファイルを確認したら、任意のキーを押してサンプルを終了し、テスト ファイルを削除します。

## <a name="learn-about-the-sample-code"></a>サンプル コードを確認する

サンプルの機能がわかったら、*example.py* ファイルを開いてコードを確認します。

### <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する

このセクションでは、オブジェクトをインスタンス化し、新しいコンテナーを作成した後、BLOB がパブリックになるようにコンテナーに対するアクセス許可を設定します。 コンテナー `quickstartblobs` を呼び出します。 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

最初に、Blob Storage へのアクセスと管理のために使用されるオブジェクトへの参照を作成します。 これらのオブジェクトはお互いを基にして作成され、各オブジェクトは、一覧で次にあるオブジェクトによって使われます。

* お使いのストレージ アカウントの Blob service を指す **BlockBlobService** オブジェクトをインスタンス化します。 

* アクセスしているコンテナーを表す **CloudBlobContainer** オブジェクトをインスタンス化します。 システムでは、コンピューターでフォルダーを使ってファイルを整理する場合と同じように、BLOB を整理するためにコンテナーが使われます。

クラウド BLOB コンテナーを作成した後は、関心がある特定の BLOB を指す **CloudBlockBlob** オブジェクトをインスタンス化します。 これにより、必要に応じて BLOB をアップロード、ダウンロード、コピーできるようになります。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」 (コンテナー、BLOB、およびメタデータの名前付けと参照) を参照してください。

### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 ブロック BLOB の最大サイズは 4.7 TB であり、Excel スプレッドシートから大きなビデオ ファイルまで何にでも使うことができます。 追加 BLOB は、ファイルに書き込んでからも情報を追加し続ける場合のログ記録に使用できます。 ページ BLOB は、主に、サービスとしてのインフラストラクチャ仮想マシン (IaaS VM) をサポートする仮想ハード ディスク (VHD) ファイルに使用されます。 ブロック BLOB は最もよく使用されます。 このクイックスタートでは、ブロック BLOB を使用します。

ファイルを BLOB にアップロードするには、ディレクトリ名をローカル ドライブ上のファイル名と結合してファイルの完全なパスを取得します。 その後、`create_blob_from_path` メソッドを使用して、指定したパスにファイルをアップロードできます。 

このサンプル コードでは、システムからアップロードとダウンロードに使用されるローカル ファイルを作成し、システムからアップロードされたファイルを *full_path_to_file* という名前で格納し、BLOB に *local_file_name* と名前を付けます。 この例では、`quickstartblobs` という名前のコンテナーにファイルをアップロードします。

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

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

次のコードでは、`list_blobs` メソッドの `generator` を作成します。 このコードでは、コンテナー内の BLOB の一覧をループ処理し、名前をコンソールに出力します。

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>BLOB のダウンロード


`get_blob_to_path` メソッドを使用して、ローカル ディスクに BLOB をダウンロードします。
次のコードでは、以前にアップロードした BLOB をダウンロードします。 ローカル ディスク上で両方のファイルを表示できるように、システムによって BLOB 名に *_DOWNLOADED* が付加されます。

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>リソースをクリーンアップする
このクイック スタートでアップロードした BLOB が不要になった場合は、`delete_container` メソッドを使ってコンテナー全体を削除できます。 代わりに個々のファイルを削除するには、`delete_blob` メソッドを使用します。

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>BLOB を使用する Python アプリケーションを開発するためのリソース

BLOB ストレージを使用する Python 開発の詳細については、以下の追加リソースを参照してください。

### <a name="binaries-and-source-code"></a>バイナリとソース コード

- GitHub で Azure Storage 用の [Python クライアント ライブラリ ソース コード](https://github.com/Azure/azure-storage-python)を確認し、ダウンロードしてインストールします。

### <a name="client-library-reference-and-samples"></a>クライアント ライブラリ リファレンスとサンプル

- Python クライアント ライブラリの詳細については、[Python 用 Azure Storage ライブラリ](/python/api/overview/azure/storage)に関する記事を参照してください。
- Python クライアント ライブラリを使用して記述された [Blob Storage のサンプル](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob)を確認します。

## <a name="next-steps"></a>次のステップ
 
このクイック スタートでは、Python を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 

Storage Explorer と BLOB の詳細については、「[Storage Explorer を使用した Azure BLOB ストレージリソースの管理](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。