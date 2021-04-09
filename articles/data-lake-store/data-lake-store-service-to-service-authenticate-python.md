---
title: Python - サービス間認証 - Data Lake Storage Gen1
description: Python から Azure Active Directory を使用して Azure Data Lake Storage Gen1 に対するサービス間認証を行う方法について説明します
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: fe987070438c689e899dee18edaf4f708555ab11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106758"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Azure Data Lake Storage Gen1 に対する Python を使用したサービス間認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK の使用](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API の使用](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

この記事では、Python SDK を使用して、Azure Data Lake Storage Gen1 に対するサービス間認証を行う方法について説明します。 Python を使用した Data Lake Store に対するエンド ユーザー認証については、「[End-user authentication with Data Lake Storage Gen1 using Python](data-lake-store-end-user-authenticate-python.md)」(Data Lake Storage Gen1 での Python を使用したエンドユーザー認証) を参照してください。


## <a name="prerequisites"></a>前提条件

* **Python**。 Python は、[ここ](https://www.python.org/downloads/)からダウンロードできます。 この記事では、Python 3.6.2 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "Web" アプリケーションを作成します**。 [Data Lake Storage Gen1 に対する Azure Active Directory を使用したサービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)の手順を完了している必要があります。

## <a name="install-the-modules"></a>モジュールをインストールする

Python を使用して Data Lake Storage Gen1 を操作するには、3 つのモジュールをインストールする必要があります。

* `azure-mgmt-resource` モジュール。これには、Active Directory 用の Azure モジュールなどが含まれています。
* `azure-mgmt-datalake-store` モジュール。これには、Data Lake Storage Gen1 アカウント管理操作が含まれています。 このモジュールについて詳しくは、[Azure Data Lake Storage Gen1 管理モジュール リファレンス](/python/api/azure-mgmt-datalake-store/)をご覧ください。
* `azure-datalake-store` モジュール。これには、Data Lake Storage Gen1 ファイル システム操作が含まれています。 このモジュールについて詳しくは、[azure-datalake-store ファイルシステム モジュール リファレンス](/python/api/azure-datalake-store/azure.datalake.store.core/)をご覧ください。

モジュールをインストールするには、次のコマンドを使用します。

```
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

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. mysample.py に対する変更を保存します。

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>クライアント シークレットによるサービス間認証を使用したアカウント管理

Data Lake Storage Gen1 アカウントの作成や Data Lake Storage Gen1 アカウントの削除などの Data Lake Storage Gen1 に関するアカウント管理操作のために Azure AD で認証する場合、このスニペットを使用します。次のスニペットは、既存の Azure AD "Web アプリ" アプリケーションのアプリケーション/サービス プリンシパルのクライアント シークレットを使用して、アプリケーションを非対話的に認証するために使用できます。

```python
authority_host_uri = 'https://login.microsoftonline.com'
tenant = '<TENANT>'
authority_uri = authority_host_uri + '/' + tenant
RESOURCE = 'https://management.core.windows.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

context = adal.AuthenticationContext(authority_uri, api_version=None)
mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)
```

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>クライアント シークレットによるサービス間認証を使用したファイルシステム操作

フォルダーの作成、ファイルのアップロードなど、Data Lake Storage Gen1 でのファイル システム操作のために Azure AD で認証する場合、次のスニペットを使用します。次のスニペットは、アプリケーション/サービス プリンシパルのクライアント シークレットを使用して、アプリケーションを非対話的に認証するために使用できます。 これは、既存の Azure AD "Web アプリ" アプリケーションと共に使用します。

```python
tenant = '<TENANT>'
RESOURCE = 'https://datalake.azure.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

adlCreds = lib.auth(tenant_id = tenant,
                client_secret = client_secret,
                client_id = client_id,
                resource = RESOURCE)
```

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>次のステップ
この記事では、サービス間認証を使って、Python を使用して Data Lake Storage Gen1 で認証する方法を説明しました。 これで、Python を使用して Data Lake Storage Gen1 を使用する方法について説明した次の記事に進めるようになりました。

* [Python を使用した Data Lake Storage Gen1 に対するアカウント管理操作](data-lake-store-get-started-python.md)
* [Python を使用した Data Lake Storage Gen1 に対するデータ操作](data-lake-store-data-operations-python.md)