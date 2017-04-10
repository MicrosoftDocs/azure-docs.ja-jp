---
title: "Python SDK で Azure Data Lake Store の使用を開始する | Microsoft Docs"
description: "Python SDK を使用して Data Lake Store アカウントとファイル システムの使用を開始する方法について説明します。"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 8a3f3d8bfe670f2a4d1a4642b2380764aa6daeb4
ms.lasthandoff: 01/24/2017


---

# <a name="get-started-with-azure-data-lake-store-using-python"></a>Python で Azure Data Lake Store の使用を開始する

> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Python SDK for Azure と Azure Data Lake Store を使用して、フォルダーの作成、データ ファイルのアップロードとダウンロードなどの基本操作を実行する方法について説明します。Data Lake の詳細については、[Azure Data Lake Store](data-lake-store-overview.md) に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* **Python**。 Python は、[ここ](https://www.python.org/downloads/)からダウンロードできます。 この記事では、Python 3.5.2 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory アプリケーションを作成する**。 Azure AD アプリケーションを使用して、Azure AD で Data Lake Store アプリケーションを認証します。 Azure AD での認証方法には、**エンドユーザー認証**と**サービス間認証**があります。 認証方法の手順と詳しい情報については、「 [Authenticate with Data Lake Store using Azure Active Directory (Azure Active Directory を使用した Data Lake Store)](data-lake-store-authenticate-using-active-directory.md)」を参照してください。

## <a name="install-the-modules"></a>モジュールをインストールする

Python を使用して Data Lake Store を操作するには、3 つのモジュールをインストールする必要があります。

* `azure-mgmt-resource` モジュール。 これには、Active Directory 用の Azure モジュールなどが含まれています。
* `azure-mgmt-datalake-store` モジュール。 これには、Azure Data Lake Store アカウント管理操作が含まれています。 このモジュールの詳細については、[Azure Data Lake Store 管理モジュール リファレンス](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)を参照してください。
* `azure-datalake-store` モジュール。 これには、Azure Data Lake Store ファイルシステム操作が含まれています。 このモジュールの詳細については、[Azure Data Lake Store ファイルシステム モジュール リファレンス](http://azure-datalake-store.readthedocs.io/en/latest/)を参照してください。

モジュールをインストールするには、次のコマンドを使用します。

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

1. 任意の IDE で、**mysample.py** などの新しい Python アプリケーションを作成します。

2. 必要なモジュールをインポートする次の行を追加します。

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
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

* エンドユーザー認証
* サービス間認証
* 多要素認証

アカウント管理モジュールとファイル システム管理モジュールの両方で、これらの認証オプションを使用する必要があります。

### <a name="end-user-authentication-for-account-management"></a>エンド ユーザー認証を使用したアカウント管理

アカウント管理操作 (Data Lake Store アカウントの作成、削除など) のために Azure AD で認証する場合、これを使用します。 Azure AD ユーザーのユーザー名とパスワードを指定する必要があります。 ユーザーは多要素認証用に構成しないように注意してください。

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="end-user-authentication-for-filesystem-operations"></a>エンドユーザー認証を使用したファイルシステム操作

ファイルシステム操作 (フォルダーの作成、ファイルのアップロードなど) のために Azure AD で認証するために、これを使用します。 これは、既存の Azure AD **ネイティブ クライアント** アプリケーションと共に使用します。 資格情報を指定した Azure AD ユーザーは、多要素認証用に構成しないようにしてください。

    tenant_id = 'FILL-IN-HERE'
    client_id = 'FILL-IN-HERE'
    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    token = lib.auth(tenant_id, user, password, client_id)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>クライアント シークレットによるサービス間認証を使用したアカウント管理

アカウント管理操作 (Data Lake Store アカウントの作成、削除など) のために Azure AD で認証する場合、これを使用します。 次のスニペットは、アプリケーション/サービス プリンシパルのクライアント シークレットを使用して、アプリケーションを非対話的に認証するために使用できます。 これは、既存の Azure AD "Web アプリ" アプリケーションと共に使用します。

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

### <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>クライアント シークレットによるサービス間認証を使用したファイルシステム操作

ファイルシステム操作 (フォルダーの作成、ファイルのアップロードなど) のために Azure AD で認証するために、これを使用します。 次のスニペットは、アプリケーション/サービス プリンシパルのクライアント シークレットを使用して、アプリケーションを非対話的に認証するために使用できます。 これは、既存の Azure AD "Web アプリ" アプリケーションと共に使用します。

    token = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE')

### <a name="multi-factor-authentication-for-account-management"></a>多要素認証を使用したアカウント管理

アカウント管理操作 (Data Lake Store アカウントの作成、削除など) のために Azure AD で認証する場合、これを使用します。 次のスニペットは、多要素認証を使用してアプリケーションを認証するために使用できます。 これは、既存の Azure AD "Web アプリ" アプリケーションと共に使用します。

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
    credentials = AADTokenCredentials(mgmt_token, client_id)

### <a name="multi-factor-authentication-for-filesystem-management"></a>多要素認証を使用したファイル システム管理

ファイルシステム操作 (フォルダーの作成、ファイルのアップロードなど) のために Azure AD で認証するために、これを使用します。 次のスニペットは、多要素認証を使用してアプリケーションを認証するために使用できます。 これは、既存の Azure AD "Web アプリ" アプリケーションと共に使用します。

    token = lib.auth(tenant_id='FILL-IN-HERE')

## <a name="create-an-azure-resource-group"></a>Azure リソース グループを作成する

Azure リソース グループを作成するには、次のコード スニペットを使用します。

    ## Declare variables
    subscriptionId= 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'
    
    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )
    
    ## Create an Azure Resource Group
    resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )

## <a name="create-clients-and-data-lake-store-account"></a>クライアントと Data Lake Store アカウントを作成する

次のスニペットは、まず Data Lake Store アカウントのクライアントを作成します。 これは、クライアント オブジェクトを使用して、Data Lake Store アカウントを作成します。 最後に、スニペットは、ファイル システム クライアント オブジェクトを作成します。

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(credentials, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(token, store_name=adlsAccountName)

## <a name="list-the-data-lake-store-accounts"></a>Data Lake Store アカウントを一覧表示する

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="create-a-directory"></a>ディレクトリを作成する

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>ファイルをアップロードする


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>ファイルをダウンロードする

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>ディレクトリを削除する

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="see-also"></a>関連項目

- [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
- [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Store .NET SDK リファレンス](https://msdn.microsoft.com/library/mt581387.aspx)
- [Data Lake Store REST リファレンス](https://msdn.microsoft.com/library/mt693424.aspx)

