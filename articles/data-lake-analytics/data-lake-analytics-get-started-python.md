---
title: "Python で Azure Data Lake Analytics の使用を開始する | Microsoft Docs"
description: "Python を使用して Data Lake Store アカウントを作成し、ジョブを送信する方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5618650671badfc54860c3ad8af5d1e727d3d8c9
ms.openlocfilehash: 40ccfc59cccd86a7634ec89656571b3cd23566b4
ms.lasthandoff: 11/23/2016


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>チュートリアル: Python で Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Python を使用して、Azure Data Lake Analytics アカウントを作成し、[U-SQL](data-lake-analytics-u-sql-get-started.md) で Data Lake Analytics ジョブを定義して、Data Lake Analytics アカウントにジョブを送信する方法について説明します。 Data Lake Analytics の詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

このチュートリアルでは、タブ区切り値 (TSV) ファイルを読み取り、コンマ区切り値 (CSV) ファイルに変換するジョブを開発します。 サポートされている他のツールを使用する同じチュートリアルを読み進めるには、このセクションの上部にあるタブをクリックします。

##<a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
- **Azure Active Directory アプリケーション**。 Azure AD アプリケーションを使用して、Azure AD で Data Lake Store アプリケーションを認証します。 Azure AD での認証方法には、エンド ユーザー認証とサービス間認証があります。 認証方法の手順と詳しい情報については、「 [Authenticate with Data Lake Store using Azure Active Directory (Azure Active Directory を使用した Data Lake Store)](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)」を参照してください。
- **[Python](https://www.python.org/downloads/)**。 64 ビット版を使用する必要があります。 この記事では、Python バージョン 3.5.2 を使用します。


## <a name="install-azure-python-sdk"></a>Azure Python SDK をインストールする

Python を使用して Data Lake Store を操作するには、3 つのモジュールをインストールする必要があります。

azure-mgmt-datalake-store モジュールには、Azure Data Lake Store アカウント管理操作が含まれています。 azure-mgmt-resource モジュールには、Active Directory 用のその他の Azure モジュールなどが含まれています。azure-datalake-store モジュールには、Azure Data Lake Store ファイル システム操作が含まれています。 azure-datalake-analytics モジュールには、Azure Data Lake Analytics 操作が含まれています。 モジュールをインストールするには、次のコマンドを使用します。

    pip install azure-mgmt-resource
    pip install azure-mgmt-datalake-store
    pip install azure-mgmt-datalake-analytics
    pip install azure-datalake-store

## <a name="create-a-python-application"></a>Python アプリケーションを作成する

1. 任意の IDE を使用して、mysample.py などの新しい Python アプリケーションを作成します。

2. 必要なモジュールをインポートする次の行を追加します。

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeAnalyticsAccountProperties, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. Python アプリケーション ファイルに変更を保存します。

## <a name="authentication"></a>認証

認証するには、次のどちらかの方法を使用します。

### <a name="end-user-authentication-for-account-management"></a>エンド ユーザー認証を使用したアカウント管理

アカウント管理操作 (Data Lake Store アカウントの作成、削除など) のために Azure AD で認証する場合、この方法を使用します。 Azure AD ユーザーのユーザー名とパスワードを指定する必要があります。 ユーザー アカウントを多要素認証用に構成することはできません。また、アカウントとして Microsoft アカウントや Live ID (例: @outlook.com,、@live.com.) を使用することもできません。

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>クライアント シークレットによるサービス間認証を使用したアカウント管理

アカウント管理操作 (Data Lake Store アカウントの作成、削除など) のために Azure AD で認証する場合、この方法を使用します。 次のスニペットは、アプリケーション/サービス プリンシパルのクライアント シークレットを使用して、アプリケーションを非対話的に認証するために使用できます。 これは、既存の Azure AD "Web アプリ" アプリケーションと共に使用します。

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>リソース管理グループを作成する

Azure リソース グループを作成するには、次のコード スニペットを使用します。

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>Data Lake Store アカウントを作成する

各 Data Lake Analytics アカウントには、Data Lake Store アカウントが必要です。 手順については、「[Azure Data Lake Store アカウントを作成する](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account)」をご覧ください。



## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>Data Lake Analytics ジョブを送信する

Data Lake Analtyics ジョブは U-SQL 言語で記述されます。 U-SQL の詳細については、[U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)に関する記事および「[U-SQL Language Reference (U-SQL 言語リファレンス)](http://go.microsoft.com/fwlink/?LinkId=691348)」をご覧ください。

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>次のステップ

- 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
- より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
- U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
- U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
- 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
- Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。


