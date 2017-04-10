---
title: "Python を使用して Azure BLOB ストレージ間でデータを移動する | Microsoft Docs"
description: "Python を使用して Azure BLOB ストレージ間でデータを移動する"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 24276252-b3dd-4edf-9e5d-f6803f8ccccc
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 9b32c0d9c3bc19a187873eebd6ab21036ee06db8
ms.openlocfilehash: fb34986d947175ae4b4212e5bd0a1e90836ed170
ms.lasthandoff: 12/07/2016


---
# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Python を使用して Azure Blob Storage 間でデータを移動する
このトピックでは、Python API を使用して BLOB の一覧表示、アップロード、ダウンロードを行う方法について説明します。 Azure SDK で提供される Python API を使用して、以下のことを行うことができます。

* コンテナーを作成する
* コンテナーに BLOB をアップロードする
* BLOB をダウンロードする
* コンテナー内の BLOB を一覧表示する
* BLOB を削除する

Python API を使用する方法の詳細については、「 [Python から Azure BLOB ストレージを使用する方法](../storage/storage-python-how-to-use-blob-storage.md)」を参照してください。

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> [Azure のデータ サイエンス用仮想マシン](machine-learning-data-science-virtual-machines.md)によって提供されるスクリプトを使用してセットアップされた VM を使用している場合、AzCopy は既に VM にインストールされています。
> 
> [!NOTE]
> Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../storage/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。
> 
> 

## <a name="prerequisites"></a>前提条件
このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。 データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

* Azure サブスクリプションを設定するには、 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* ストレージ アカウントの作成と、アカウントとキー情報の取得についての手順については、「 [Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

## <a name="upload-data-to-blob"></a>BLOB へのデータのアップロード
プログラム的に Azure ストレージにアクセスするためのすべての Python コードの先頭付近に、次のスニペットを追加します。

    from azure.storage.blob import BlobService

**BlobService** オブジェクトを使用して、コンテナーおよび BLOB を操作できます。 次のコードは、ストレージ アカウントの名前とアカウント キーを使用して、BlobService オブジェクトを作成します。 アカウント名とアカウント キーは実際のアカウントとキーに置き換えてください。

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

データを BLOB にアップロードするには、次のメソッドを使用します。

1. put\_block\_blob\_from\_path (指定されたパスからファイルの内容をアップロードします)
2. put\_block_blob\_from\_file (既に開かれているファイル/ストリームから内容をアップロードします)
3. put\_block\_blob\_from\_bytes (バイトの配列をアップロードします)
4. put\_block\_blob\_from\_text (指定されたエンコーディングを使用して、指定されたテキスト値をアップロードします)

次のサンプル コードではコンテナーにローカル ファイルをアップロードします。

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

次のサンプル コードでは、ローカル ディレクトリのすべてのファイル (ディレクトリを除く) を BLOB ストレージにアップロードします。

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>BLOB からのデータのダウンロード
BLOB からデータをダウンロードするには、次のメソッドを使用します。

1. get\_blob\_to\_path
2. get\_blob\_to\_file
3. get\_blob\_to\_bytes
4. get\_blob\_to\_text

これらのメソッドは、データのサイズが 64 MB を超過した場合に必要なチャンクを実行します。

次のサンプル コードでは、コンテナー内の BLOB の内容をローカル ファイルにダウンロードします。

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

次のサンプル コードでは、コンテナーからすべての BLOB をダウンロードします。 list\_blobs を使用して、コンテナーで使用可能な BLOB の一覧を取得し、それらをローカル ディレクトリにダウンロードします。

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name

