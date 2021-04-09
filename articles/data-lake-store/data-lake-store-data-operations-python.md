---
title: 'Python: Azure Data Lake Storage Gen1 に対するファイルシステム操作 | Microsoft Docs'
description: Python SDK を使用して Data Lake Storage Gen1 のファイル システムを操作する方法について説明します。
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 8ca455a802b180163579f67104a61f455dd54f94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109223"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Python を使用した Azure Data Lake Storage Gen1 に対するファイルシステム操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

この記事では、Python SDK を使用して Azure Data Lake Storage Gen1 に対するファイルシステム操作を実行する方法について説明します。 Python を使用して Data Lake Storage Gen1 に対するアカウント管理操作を実行する方法については、[Python を使用した Data Lake Storage Gen1 に対するアカウント管理操作](data-lake-store-get-started-python.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* **Python**。 Python は、[ここ](https://www.python.org/downloads/)からダウンロードできます。 この記事では、Python 3.6.2 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Data Lake Storage Gen1 アカウント**。 「[Azure portal で Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。

## <a name="install-the-modules"></a>モジュールをインストールする

Python を使用して Data Lake Storage Gen1 を操作するには、3 つのモジュールをインストールする必要があります。

* `azure-mgmt-resource` モジュール。これには、Active Directory 用の Azure モジュールなどが含まれています。
* `azure-mgmt-datalake-store` モジュール。これには、Azure Data Lake Storage Gen1 アカウント管理操作が含まれています。 このモジュールについて詳しくは、[azure-mgmt-datalake-store モジュール リファレンス](/python/api/azure-mgmt-datalake-store/)をご覧ください。
* `azure-datalake-store` モジュール。これには、Azure Data Lake Storage Gen1 ファイルシステム操作が含まれています。 このモジュールについて詳しくは、[azure-datalake-store ファイルシステム モジュール リファレンス](/python/api/azure-datalake-store/azure.datalake.store.core/)をご覧ください。

モジュールをインストールするには、次のコマンドを使用します。

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

1. 任意の IDE で、**mysample.py** などの新しい Python アプリケーションを作成します。

2. 必要なモジュールをインポートする次の行を追加します。

   ```python
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. mysample.py に対する変更を保存します。

## <a name="authentication"></a>認証

このセクションでは、Azure AD で認証を行うさまざまな方法について説明します。 次の方法を使用できます。

* アプリケーションのエンドユーザー認証については、[Data Lake Storage Gen1 による Python を使用したエンドユーザー認証](data-lake-store-end-user-authenticate-python.md)に関する記事をご覧ください。
* アプリケーションのサービス間認証については、[Data Lake Storage Gen1 による Python を使用したサービス間認証](data-lake-store-service-to-service-authenticate-python.md)に関する記事をご覧ください。

## <a name="create-filesystem-client"></a>ファイルシステム クライアントを作成する

次のスニペットは、まず Data Lake Storage Gen1 アカウントのクライアントを作成します。 これは、クライアント オブジェクトを使用して、Data Lake Storage Gen1 アカウントを作成します。 最後に、スニペットは、ファイル システム クライアント オブジェクトを作成します。

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'

## Create a filesystem client object
adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)
```

## <a name="create-a-directory"></a>ディレクトリを作成する

```python
## Create a directory
adlsFileSystemClient.mkdir('/mysampledirectory')
```

## <a name="upload-a-file"></a>ファイルをアップロードする

```python
## Upload a file
multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```


## <a name="download-a-file"></a>ファイルをダウンロードする

```python
## Download a file
multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```

## <a name="delete-a-directory"></a>ディレクトリを削除する

```python
## Delete a directory
adlsFileSystemClient.rm('/mysampledirectory', recursive=True)
```

## <a name="next-steps"></a>次のステップ
* [Python を使用した Data Lake Storage Gen1 に対するアカウント管理操作](data-lake-store-get-started-python.md)

## <a name="see-also"></a>関連項目

* [Azure Data Lake Storage Gen1 Python (ファイルシステム) リファレンス](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Azure Data Lake Storage Gen1 と互換性のあるオープン ソースのビッグ データ アプリケーション](data-lake-store-compatible-oss-other-applications.md)