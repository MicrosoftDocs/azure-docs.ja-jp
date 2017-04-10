---
title: "REST API で Data Lake Analytics の使用を開始する | Microsoft Docs"
description: "WebHDFS REST API を使用して Data Lake Analytics に対する操作を実行する"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5e133d92-baaa-44c9-890c-ab2d85c91122
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2fea3686b1484406d31c5447c7d3d7e2451b827e
ms.openlocfilehash: 1898b3d6aa1a9ccbc9f4427cf994c02f9fa35abd


---
# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>REST API で Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

WebHDFS REST API と Data Lake Analytics REST API を使用して、Data Lake Analytics のアカウント、ジョブ、カタログを管理する方法について説明します。 

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Active Directory アプリケーションを作成する**。 Azure AD アプリケーションを使用して、Azure AD で Data Lake Analytics アプリケーションを認証します。 Azure AD での認証方法には、**エンドユーザー認証**と**サービス間認証**があります。 認証方法の手順と詳細については、[Azure Active Directory を使用した Data Lake Analytics での認証](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)に関する記事をご覧ください。
* [cURL](http://curl.haxx.se/)。 この記事では、cURL を使用して、Data Lake Analytics アカウントに対して REST API 呼び出しを行う方法を説明します。

## <a name="authenticate-with-azure-active-directory"></a>Azure Active Directory を使用して認証する
Azure Active Directory を使用して認証する場合、2 つの方法があります。

### <a name="end-user-authentication-interactive"></a>エンド ユーザー認証 (対話型)
この方法を使用すると、アプリケーションはユーザーにログインを要求し、すべての操作がユーザーのコンテキストで実行されます。 

対話型認証を行うには、次の手順に従います。

1. アプリケーションでユーザーを次の URL にリダイレクトします。
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI> は、URL で使用できるようにエンコードする必要があります。 そのため、https://localhost の場合、`https%3A%2F%2Flocalhost` を使用します。
   > 
   > 
   
    このチュートリアルでは、上記 URL のプレースホルダーの値を置換し、Web ブラウザーのアドレス バーに貼り付けることができます。 ユーザーは、Azure ログインを使用して認証するためにリダイレクトされます。 正常にログインすると、ブラウザーのアドレス バーに応答が表示されます。 応答は次の形式になります。
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. 応答から承認コードをキャプチャします。 このチュートリアルでは、Web ブラウザーのアドレス バーから承認コードをコピーして、次に示すように POST 要求でトークン エンドポイントに渡すことができます。
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > この場合、\<REDIRECT-URI> をエンコードする必要はありません。
   > 
   > 
3. 応答は、アクセス トークン (例: `"access_token": "<ACCESS_TOKEN>"`) および更新トークン (例: `"refresh_token": "<REFRESH_TOKEN>"`) を含む JSON オブジェクトです。 アプリケーションでは、Azure Data Lake Store にアクセスするときにアクセス トークンを使用し、アクセス トークンの有効期限が切れたときに別のアクセス トークンを取得するために更新トークンを使用します。
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. アクセス トークンの有効期限が切れたときは、次のように更新トークンを使用して新しいアクセス トークンを要求できます。
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

対話型ユーザー認証の詳細については、 [承認コード付与フロー](https://msdn.microsoft.com/library/azure/dn645542.aspx)に関するページを参照してください。

### <a name="service-to-service-authentication-non-interactive"></a>サービス間認証 (非対話型)
この方法を使用すると、操作を実行するための独自の資格情報をアプリケーションが提供します。 この場合、次に示すように POST 要求を発行する必要があります。 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

この要求の出力には、後で REST API 呼び出しで渡す認証トークン (以下の出力の `access-token` で示される) が取り込まれます。 この認証トークンをテキスト ファイルに保存します。この記事の後半で必要となります。

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

この記事では、 **非対話型** のアプローチを使用します。 非対話型 (サービス間呼び出し) の詳細については、 [資格情報を使用したサービス間呼び出し](https://msdn.microsoft.com/library/azure/dn645543.aspx)に関するページを参照してください。

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する
Data Lake Analytics アカウントを作成するには、Azure リソース グループと Data Lake Store アカウントを作成しておく必要があります。  「[Data Lake Store アカウントを作成する](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account)」をご覧ください。

次の Curl コマンドは、アカウントを作成する方法を示しています。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

\<`REDACTED`\> を承認トークンに、\<`AzureSubscriptionID`\> を使用するサブスクリプション ID に、\<`AzureResourceGroupName`\> を既存の Azure リソース グループ名に、\<`NewAzureDataLakeAnalyticsAccountName`\> を新しい Data Lake Analytics アカウント名にそれぞれ置き換えます。 このコマンドの要求ペイロードは、上記の `-d` パラメーターで指定した **CreateDatalakeAnalyticsAccountRequest.json** ファイルに含まれています。 input.json ファイルの内容は、次のようになります。

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>サブスクリプション内の Data Lake Analytics アカウントを一覧表示する
次の Curl コマンドは、サブスクリプション内のアカウントを一覧表示する方法を示しています。

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

\<`REDACTED`\> を承認トークンに置き換え、\<`AzureSubscriptionID`\> を使用するサブスクリプション ID に置き換えます。 次のように出力されます。

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントに関する情報を取得する
次の Curl コマンドは、アカウント情報を取得する方法を示しています。

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

\<`REDACTED`\> を承認トークンに、\<`AzureSubscriptionID`\> を使用するサブスクリプション ID に、\<`AzureResourceGroupName`\> を既存の Azure リソース グループ名に、\<`DataLakeAnalyticsAccountName`\> を既存の Data Lake Analytics アカウント名にそれぞれ置き換えます。 次のように出力されます。

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントの Data Lake Store を一覧表示する
次の Curl コマンドは、アカウントの Data Lake Store を一覧表示する方法を示しています。

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

\<`REDACTED`\> を承認トークンに、\<`AzureSubscriptionID`\> を使用するサブスクリプション ID に、\<`AzureResourceGroupName`\> を既存の Azure リソース グループ名に、\<`DataLakeAnalyticsAccountName`\> を既存の Data Lake Analytics アカウント名にそれぞれ置き換えます。 次のように出力されます。

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>U-SQL ジョブを送信する
次の Curl コマンドは、U-SQL ジョブを送信する方法を示しています。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

\<`REDACTED`\> を承認トークンに置き換え、\<`DataLakeAnalyticsAccountName`\> を既存の Data Lake Analytics アカウント名に置き換えます。 このコマンドの要求ペイロードは、上記の `-d` パラメーターで指定した **SubmitADLAJob.json** ファイルに含まれています。 input.json ファイルの内容は、次のようになります。

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

次のように出力されます。

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>U-SQL ジョブを一覧表示する
次の Curl コマンドは、U-SQL ジョブを一覧表示する方法を示しています。

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

\<`REDACTED`\> を承認トークンに置き換え、\<`DataLakeAnalyticsAccountName`\> を既存の Data Lake Analytics アカウント名に置き換えます。 

次のように出力されます。

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>カタログ項目を取得する
次の Curl コマンドは、カタログからデータベースを取得する方法を示しています。

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

次のように出力されます。

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>関連項目
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
* Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。
* 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
* 診断情報のログについては、「 [Accessing diagnostics logs for Azure Data Lake Analytics (Azure Data Lake Analytics の診断ログへのアクセス)](data-lake-analytics-diagnostic-logs.md)




<!--HONumber=Feb17_HO1-->


