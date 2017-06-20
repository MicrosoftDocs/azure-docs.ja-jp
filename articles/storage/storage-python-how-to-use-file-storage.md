---
title: "Python から Azure File Storage を使用する方法 | Microsoft Docs"
description: "Python から Azure File Storage を使用して、ファイルをアップロード、一覧表示、ダウンロード、削除する方法について説明します。"
services: storage
documentationcenter: python
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: fefebeae665ccd14f15b0197241b30d33830fd09
ms.openlocfilehash: 9973da827ea5a9311904d7d6d4c22d59b5e2d0ce


---
# <a name="how-to-use-azure-file-storage-from-python"></a>Python から Azure File Storage を使用する方法
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Overview
この記事では、File Storage を使用して一般的なシナリオを実行する方法について説明します。 サンプルは Python で作成され、 [Microsoft Azure Storage SDK for Python]を使用しています。 紹介するシナリオは、ファイルのアップロード、一覧表示、ダウンロード、および削除です。

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-share"></a>共有を作成する
**FileService** オブジェクトを使うと、共有、ディレクトリ、ファイルを操作できます。 次のコードでは、 **FileService** オブジェクトを作成します。 プログラムを使用して Azure Storage にアクセスするすべての Python ファイルの先頭付近に、次のコードを追加します。

```python
from azure.storage.file import FileService
```

次のコードでは、ストレージ アカウント名とアカウント キーを使用して **FileService** オブジェクトを作成します。  'myaccount' と 'mykey' は、実際のアカウント名とキーに置き換えてください。

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

次のコード サンプルでコンテナーが存在しない場合は、 **FileService** オブジェクトを使用してコンテナーを作成できます。

```python
file_service.create_share('myshare')
```

## <a name="upload-a-file-into-a-share"></a>ファイルを共有にアップロードする
Azure ファイル ストレージ共有には、少なくとも、ファイルが置かれるルート ディレクトリが含まれます。 このセクションでは、ローカル ストレージから共有のルート ディレクトリにファイルをアップロードする方法を紹介します。

ファイルを作成し、データをアップロードするには、**create\_file\_from\_path**、**create\_file\_from\_stream**、**create\_file\_from\_bytes**、**create\_file\_from\_text** メソッドを使用します。 これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

**create\_file\_from\_path** は、指定したパスからファイルの内容をアップロードし、**create\_file\_from\_stream** は既に開いているファイルまたはストリームから内容をアップロードします。 **create\_file\_from\_bytes** はバイト配列をアップロードし、**create\_file\_from\_text** は指定したエンコーディング (既定値から UTF-8) を使用して、指定したテキスト値をアップロードします。

次の例では、**sunset.png** ファイルの内容を **myfile** ファイルにアップロードします。

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="how-to-create-a-directory"></a>ディレクトリを作成する方法
ルート ディレクトリにすべてのファイルを置くのではなく、サブディレクトリ内に置いてストレージを整理することもできます。 Azure ファイル ストレージ サービスでは、自分のアカウントで許可されるだけのディレクトリを作成できます。 下のコードはルート ディレクトリの下に「 **sampledir** 」という名前のサブディレクトリを作成します。

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="how-to-list-files-and-directories-in-a-share"></a>共有のファイルとディレクトリを一覧表示する方法
共有のファイルとディレクトリを一覧表示するには、**list\_directories\_and\_files** メソッドを使用します。 このメソッドは、ジェネレーターを返します。 次のコードでは、共有内の各ファイルとディレクトリの **名前 (name)** をコンソールに出力しています。

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="download-files"></a>ファイルをダウンロードする
ファイルからデータをダウンロードするには、**get\_file\_to\_path**、**get\_file\_to\_stream**、**get\_file\_to\_bytes**、**get\_file\_to\_text** を使用します。 これらは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行する高レベル メソッドです。

次の例は、**get\_file\_to\_path** を使用して **myfile** ファイルの内容をダウンロードし、**out-sunset.png** ファイルに格納する方法を示しています。

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>ファイルを削除する
最後に、ファイルを削除するには、**delete_file** を呼び出します。

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="next-steps"></a>次のステップ
これで、File Storage の基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* [Python デベロッパー センター](/develop/python/)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](http://msdn.microsoft.com/library/azure/dd179355)
* [Azure Storage チーム ブログ]
* [Microsoft Azure Storage SDK for Python]

[Azure Storage チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python



<!--HONumber=Nov16_HO3-->


