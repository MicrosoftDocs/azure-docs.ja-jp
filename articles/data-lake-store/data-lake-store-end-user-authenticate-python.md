---
title: エンドユーザー認証 - Python と Data Lake Storage Gen1 - Azure
description: Python と Azure Active Directory を使用した Azure Data Lake Storage Gen1 によるエンドユーザー認証を行う方法について説明します
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: c7ee58dec4e8bc04eb07695378e0d4dc973b6705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109087"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Azure Data Lake Storage Gen1 による Python を使用したエンドユーザー認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK の使用](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-end-user-authenticate-python.md)
> * [REST API の使用](data-lake-store-end-user-authenticate-rest-api.md)
>
>

この記事では、Python SDK を使用して、Azure Data Lake Storage Gen1 に対するエンドユーザー認証を行う方法について説明します。 エンドユーザー認証は、2 つのカテゴリに分割できます。

* 多要素認証なしのエンドユーザー認証
* 多要素認証によるエンドユーザー認証

この記事では、両方のオプションについて説明します。 Python を使用した Data Lake Storage Gen1 によるサービス間認証については、「[Service-to-service authentication with Data Lake Storage Gen1 using Python (Python を使用した Data Lake Storage Gen1 によるサービス間認証)](data-lake-store-service-to-service-authenticate-python.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* **Python**。 Python は、[ここ](https://www.python.org/downloads/)からダウンロードできます。 この記事では、Python 3.6.2 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "ネイティブ" アプリケーションを作成します**。 「[End-user authentication with Data Lake Storage Gen1 using Azure Active Directory (Azure Active Directory を使用した Data Lake Storage Gen1 でのエンドユーザー認証)](data-lake-store-end-user-authenticate-using-active-directory.md)」のステップを完了している必要があります。

## <a name="install-the-modules"></a>モジュールをインストールする

Python を使用して Data Lake Storage Gen1 を操作するには、3 つのモジュールをインストールする必要があります。

* `azure-mgmt-resource` モジュール。これには、Active Directory 用の Azure モジュールなどが含まれています。
* `azure-mgmt-datalake-store` モジュール。これには、Azure Data Lake Storage Gen1 アカウント管理操作が含まれています。 このモジュールについて詳しくは、[Azure Data Lake Storage Gen1 管理モジュール リファレンス](/python/api/azure-mgmt-datalake-store/)をご覧ください。
* `azure-datalake-store` モジュール。これには、Azure Data Lake Storage Gen1 ファイルシステム操作が含まれています。 このモジュールについて詳しくは、[azure-datalake-store ファイルシステム モジュール リファレンス](/python/api/azure-datalake-store/azure.datalake.store.core/)をご覧ください。

モジュールをインストールするには、次のコマンドを使用します。

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

1. 任意の IDE で、**mysample.py** などの新しい Python アプリケーションを作成します。

2. 必要なモジュールをインポートするには、次のスニペットを追加します

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. mysample.py に対する変更を保存します。

## <a name="end-user-authentication-with-multi-factor-authentication"></a>多要素認証によるエンドユーザー認証

### <a name="for-account-management"></a>アカウント管理を行う場合

Data Lake Storage Gen1 アカウントに対するアカウント管理操作のために Azure AD で認証する場合、次のスニペットを使用します。 次のスニペットは、多要素認証を使用してアプリケーションを認証するために使用できます。 既存の Azure AD **ネイティブ** アプリケーションに対して以下の値を指定します。

```python
authority_host_url = "https://login.microsoftonline.com"
tenant = "FILL-IN-HERE"
authority_url = authority_host_url + '/' + tenant
client_id = 'FILL-IN-HERE'
redirect = 'urn:ietf:wg:oauth:2.0:oob'
RESOURCE = 'https://management.core.windows.net/'

context = adal.AuthenticationContext(authority_url)
code = context.acquire_user_code(RESOURCE, client_id)
print(code['message'])
mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)
```

### <a name="for-filesystem-operations"></a>ファイルシステム操作を行う場合

Data Lake Storage Gen1 アカウントに対するファイルシステム操作のために Azure AD で認証する場合、これを使用します。 次のスニペットは、多要素認証を使用してアプリケーションを認証するために使用できます。 既存の Azure AD **ネイティブ** アプリケーションに対して以下の値を指定します。

```console
adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')
```

## <a name="end-user-authentication-without-multi-factor-authentication"></a>多要素認証なしのエンドユーザー認証

これは非推奨です。 詳細については、[Python SDK を使用した Azure 認証](/azure/python/python-sdk-azure-authenticate)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
この記事では、Azure Data Lake Storage Gen1 に対し、Python からエンドユーザー認証を使って認証を行う方法について説明しました。 これで、Python を使用して Azure Data Lake Storage Gen1 を使用する方法について説明した次の記事に進めるようになりました。

* [Python を使用した Data Lake Storage Gen1 に対するアカウント管理操作](data-lake-store-get-started-python.md)
* [Python を使用した Data Lake Storage Gen1 に対するデータ操作](data-lake-store-data-operations-python.md)