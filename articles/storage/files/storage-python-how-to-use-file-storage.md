---
title: Python での Azure Files 用の開発 | Microsoft Docs
description: Azure Files を使ってファイル データを格納する Python アプリケーションとサービスを開発する方法を説明します。
services: storage
author: roygara
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f3a741216b50811868687b124463e10e65355094
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360054"
---
# <a name="develop-for-azure-files-with-python"></a>Python での Azure Files 用の開発
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

このチュートリアルでは、ファイル データの格納に Azure Files を使うアプリケーションまたはサービスを開発するための Python の基本的な使い方を示します。 このチュートリアルでは、単純なコンソール アプリケーションを作成し、Python と Azure Files による次のような基本的な操作の実行方法を示します。

* Azure ファイル共有を作成する
* ディレクトリを作成する
* Azure ファイル共有のファイルとディレクトリを列挙する
* ファイルのアップロード、ダウンロード、および削除

> [!Note]  
> Azure Files は SMB 経由でアクセスできるため、ファイル I/O の標準 Python I/O クラスと関数を使って Azure ファイル共有にアクセスする簡単なアプリケーションを作成できます。 この記事では、Azure Storage Python SDK を使うアプリケーションを記述する方法を説明します。この SDK は、Azure Files との通信に [Azure Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) を使います。

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Microsoft Azure Storage SDK for Python をダウンロードしてインストールする

[Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) には、Python 2.7、3.3、3.4、3.5、または 3.6 が必要です。
 
## <a name="install-via-pypi"></a>PyPi でインストールする

Python Package Index (PyPI) でインストールするには、次のように入力します。

```bash
pip install azure-storage-file
```

> [!NOTE]
> Azure Storage SDK for Python 0.36 以前のバージョンからアップグレードする場合は、`pip uninstall azure-storage` を使用して以前の SDK をアンインストールしてから、最新のパッケージをインストールします。

別のインストール方法については、[GitHub の Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/) に関するページをご覧ください。

## <a name="view-the-sample-application"></a>サンプル アプリケーションを表示する
Python を Azure Files と一緒に使用する方法を示すサンプル アプリケーションを表示して実行するには、[Azure Storage:Python での Azure Files の使用開始](https://github.com/Azure-Samples/storage-file-python-getting-started)に関するページを参照してください。 

サンプル アプリケーションを実行するには、`azure-storage-file` パッケージと `azure-storage-common` パッケージの両方がインストールされていることを確認します。

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files を使用するようにアプリケーションを設定する
プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Azure Files への接続を設定する 
`FileService` オブジェクトを使うと、共有、ディレクトリ、ファイルを操作できます。 次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、`FileService` オブジェクトを作成します。 `<myaccount>` と `<mykey>` をアカウント名とキーに置き換えます。

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
次のコード サンプルで共有が存在しない場合は、`FileService` オブジェクトを使用して共有を作成できます。

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>ディレクトリを作成する
ルート ディレクトリにすべてのファイルを置くのではなく、サブディレクトリ内に置いてストレージを整理することもできます。 Azure Files では、自分のアカウントで許可されるだけのディレクトリを作成できます。 下のコードはルート ディレクトリの下に「 **sampledir** 」という名前のサブディレクトリを作成します。

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure ファイル共有のファイルとディレクトリを列挙する
共有のファイルとディレクトリを一覧表示するには、**list\_directories\_and\_files** メソッドを使用します。 このメソッドは、ジェネレーターを返します。 次のコードでは、共有内の各ファイルとディレクトリの **名前 (name)** をコンソールに出力しています。

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>ファイルをアップロードする 
Azure ファイル共有には、少なくとも、ファイルが置かれるルート ディレクトリが含まれます。 このセクションでは、ローカル ストレージから共有のルート ディレクトリにファイルをアップロードする方法を紹介します。

ファイルを作成してデータをアップロードするには、`create_file_from_path`、`create_file_from_stream`、`create_file_from_bytes`、または`create_file_from_text` メソッドを使用します。 これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

`create_file_from_path` は、指定したパスからファイルの内容をアップロードし、`create_file_from_stream` は既に開いているファイルまたはストリームから内容をアップロードします。 `create_file_from_bytes` はバイト配列をアップロードし、`create_file_from_text` は指定したエンコーディング (既定値から UTF-8) を使って、指定したテキスト値をアップロードします。

次の例では、**sunset.png** ファイルの内容を **myfile** ファイルにアップロードします。

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>ファイルをダウンロードする
ファイルからデータをダウンロードするには、`get_file_to_path`、`get_file_to_stream`、`get_file_to_bytes`、または `get_file_to_text` を使用します。 これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

次の例は、`get_file_to_path` を使用して **myfile** ファイルの内容をダウンロードし、**out-sunset.png** ファイルに格納する方法を示しています。

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>ファイルを削除する
最後に、ファイルを削除するには、`delete_file` を呼び出します。

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>共有スナップショットの作成
ファイル共有全体の特定の時点のコピーを作成できます。

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**メタデータでの共有スナップショットの作成**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>共有とスナップショットの一覧表示 
特定の共有のすべてのスナップショットを一覧表示できます。

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>共有スナップショットの参照
各共有スナップショットのコンテンツを参照して、その時点からのファイルとディレクトリを取得することができます。

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>共有スナップショットからのファイルの取得
復元シナリオでは共有スナップショットからファイルをダウンロードできます。

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>単一の共有スナップショットの削除  
単一の共有スナップショットを削除できます。

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>共有スナップショットが存在する場合の共有の削除
スナップショットが含まれる共有は、すべてのスナップショットを最初に削除しない限り削除できません。

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>次の手順
Python での Azure Files を操作する方法を習得したので、詳細について次のリンクを参照してください。

* [Python デベロッパー センター](https://azure.microsoft.com/develop/python/)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)
