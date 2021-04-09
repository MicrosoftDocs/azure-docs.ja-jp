---
title: Python を使用した Azure Data Lake Storage Gen1 アカウントの管理
description: Python SDK を使用して Azure Data Lake Storage Gen1 に対するアカウント管理操作を行う方法について説明します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 35e71b80c6f47bb13f7a2b490b493b0cb42acf04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103511"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Python を使用した Azure Data Lake Storage Gen1 に対するアカウント管理操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Azure Data Lake Storage Gen1 用 Python SDK を使用して、Data Lake Storage Gen1 アカウントの作成、Data Lake Storage Gen1 アカウントの一覧表示などの基本的なアカウント管理操作を実行する方法を説明します。Python を使用して Data Lake Storage Gen1 に対するファイルシステム操作を実行する方法については、[Python を使用した Data Lake Storage Gen1 に対するファイルシステム操作](data-lake-store-data-operations-python.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* **Python**。 Python は、[ここ](https://www.python.org/downloads/)からダウンロードできます。 この記事では、Python 3.6.2 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure リソース グループ**。 手順については、[Azure リソース グループの作成](../azure-resource-manager/management/manage-resource-groups-portal.md)に関するページを参照してください。

## <a name="install-the-modules"></a>モジュールをインストールする

Python を使用して Data Lake Storage Gen1 を操作するには、3 つのモジュールをインストールする必要があります。

* `azure-mgmt-resource` モジュール。これには、Active Directory 用の Azure モジュールなどが含まれています。
* `azure-mgmt-datalake-store` モジュール。これには、Azure Data Lake Storage Gen1 アカウント管理操作が含まれています。 このモジュールについて詳しくは、[Azure Data Lake Storage Gen1 管理モジュール リファレンス](/python/api/azure-mgmt-datalake-store/)をご覧ください。
* `azure-datalake-store` モジュール。これには、Azure Data Lake Storage Gen1 ファイルシステム操作が含まれています。 このモジュールについて詳しくは、[azure-datalake-store ファイルシステム モジュール リファレンス](/python/api/azure-datalake-store/azure.datalake.store.core/)に関するページをご覧ください。

モジュールをインストールするには、次のコマンドを使用します。

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

1. 任意の IDE で、**mysample.py** などの新しい Python アプリケーションを作成します。

2. 必要なモジュールをインポートするには、次のスニペットを追加します

    ```python
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
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

## <a name="create-client-and-data-lake-storage-gen1-account"></a>クライアントと Data Lake Storage Gen1 アカウントの作成

次のスニペットは、まず Data Lake Storage Gen1 アカウントのクライアントを作成します。 これは、クライアント オブジェクトを使用して、Data Lake Storage Gen1 アカウントを作成します。 最後に、スニペットは、ファイル システム クライアント オブジェクトを作成します。

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'
resourceGroup = 'FILL-IN-HERE'
location = 'eastus2'

## Create Data Lake Storage Gen1 account management client object
adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

## Create a Data Lake Storage Gen1 account
adlsAcctResult = adlsAcctClient.account.create(
    resourceGroup,
    adlsAccountName,
    DataLakeStoreAccount(
        location=location
    )
).wait()
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Data Lake Storage Gen1 アカウントの一覧表示

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントの削除

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>次のステップ
* [Python を使用した Data Lake Storage Gen1 に対するファイルシステム操作](data-lake-store-data-operations-python.md)。

## <a name="see-also"></a>関連項目

* [azure-datalake-store Python (ファイルシステム) リファレンス](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Azure Data Lake Storage Gen1 と互換性のあるオープン ソースのビッグ データ アプリケーション](data-lake-store-compatible-oss-other-applications.md)