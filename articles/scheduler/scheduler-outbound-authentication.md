---
title: 送信認証
description: Azure Scheduler の送信認証を設定または削除する方法について説明します
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 0a8d79af9f45731971cb1be1f39fc193f9d0f0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80878971"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Azure Scheduler の送信認証

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、できるだけ早く [Azure Logic Apps に移行](../scheduler/migrate-from-scheduler-to-logic-apps.md)してください。 
>
> Scheduler は Azure portal で利用できなくなりましたが、現時点では [REST API](/rest/api/scheduler) と [Azure Scheduler PowerShell コマンドレット](scheduler-powershell-reference.md)がまだ使用できるので、お客様はジョブとジョブ コレクションを管理することができます。

Azure Scheduler ジョブは、他の Azure サービス、Salesforce.com、Facebook、セキュリティで保護されたカスタム Web サイトなど、認証が必要なサービスを呼び出す必要があります。 呼び出されたサービスは、Scheduler ジョブが必要なリソースにアクセスできるかどうかを確認できます。 

Scheduler では、これらの認証モデルがサポートされています。 

* *クライアント証明書* SSL/TLS クライアント証明書を使用する際の認証
* *基本* 認証
* *Active Directory OAuth* 認証

## <a name="add-or-remove-authentication"></a>認証の追加または削除

* Scheduler ジョブに認証を追加するには、ジョブを作成または更新する際に、`authentication` JavaScript Object Notation (JSON) の子要素を `request` 要素に追加します。 

  応答で、`authentication` オブジェクトの PUT 要求、PATCH 要求、または POST 要求を介して Scheduler サービスに渡されたシークレットが返されることはありません。 
  応答では、シークレット情報は null に設定されます。または、認証済みのエンティティを表す公開トークンが使用される場合があります。 

* Scheduler ジョブから認証を削除するには、ジョブで PUT 要求または PATCH 要求を明示的に実行し、`authentication` オブジェクトを null に設定します。 応答には、認証プロパティは含まれません。

## <a name="client-certificate"></a>クライアント証明書

### <a name="request-body---client-certificate"></a>要求本文 - クライアント証明書

`ClientCertificate` モデルを使用して認証を追加する場合は、要求本文にこれらの追加要素を指定します。  

| 要素 | 必須 | 説明 |
|---------|----------|-------------|
| **authentication** (親要素) | SSL/TLS クライアント証明書を使用するための認証オブジェクト |
| **type** | はい | 認証の種類。 SSL/TLS クライアント証明書の場合、値は `ClientCertificate` です。 |
| **pfx** | はい | Base64 でエンコードされた PFX ファイルのコンテンツ |
| **password** | はい | PFX ファイルにアクセスするためのパスワード |
||| 

### <a name="response-body---client-certificate"></a>応答本文 - クライアント証明書 

認証情報を含めて要求を送信した場合、応答にはこれらの認証要素が含まれます。

| 要素 | 説明 | 
|---------|-------------| 
| **authentication** (親要素) | SSL/TLS クライアント証明書を使用するための認証オブジェクト |
| **type** | 認証の種類。 SSL/TLS クライアント証明書の場合、値は `ClientCertificate` です。 |
| **certificateThumbprint** |証明書のサムプリント |
| **certificateSubjectName** |証明書のサブジェクト識別名 |
| **certificateExpiration** | 証明書の有効期限日 |
||| 

### <a name="sample-rest-request---client-certificate"></a>サンプル REST 要求 - クライアント証明書

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>サンプル REST 応答 - クライアント証明書

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Basic

### <a name="request-body---basic"></a>要求本文 - 基本

`Basic` モデルを使用して認証を追加する場合は、要求本文にこれらの追加要素を指定します。

| 要素 | 必須 | 説明 |
|---------|----------|-------------|
| **authentication** (親要素) | 基本認証を使用するための認証オブジェクト | 
| **type** | はい | 認証の種類。 基本認証の場合、値 `Basic`を使用します。 | 
| **username** | はい | 認証するユーザー名 | 
| **password** | はい | 認証するパスワード |
|||| 

### <a name="response-body---basic"></a>応答本文 - 基本

認証情報を含めて要求を送信した場合、応答にはこれらの認証要素が含まれます。

| 要素 | 説明 | 
|---------|-------------|
| **authentication** (親要素) | 基本認証を使用するための認証オブジェクト |
| **type** | 認証の種類。 基本認証の場合、値は `Basic` です。 |
| **username** | 認証されたユーザー名 |
||| 

### <a name="sample-rest-request---basic"></a>サンプル REST 要求 - 基本

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>サンプル REST 応答 - 基本

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>要求本文 - Active Directory OAuth 

`ActiveDirectoryOAuth` モデルを使用して認証を追加する場合は、要求本文にこれらの追加要素を指定します。

| 要素 | 必須 | 説明 |
|---------|----------|-------------|
| **authentication** (親要素) | はい | ActiveDirectoryOAuth 認証を使用するための認証オブジェクト |
| **type** | はい | 認証の種類。 ActiveDirectoryOAuth 認証の場合、値 `ActiveDirectoryOAuth`を使用します。 |
| **tenant** | はい | Azure AD テナントのテナント ID です。 Azure AD テナントのテナント ID を確認するには、Azure PowerShell で `Get-AzureAccount` を実行します。 |
| **audience** | はい | この値は `https://management.core.windows.net/` に設定されます。 | 
| **clientId** | はい | Azure AD アプリケーションのクライアント識別子 | 
| **secret** | はい | トークンを要求しているクライアントのシークレット | 
|||| 

### <a name="response-body---active-directory-oauth"></a>応答本文 - Active Directory OAuth

認証情報を含めて要求を送信した場合、応答にはこれらの認証要素が含まれます。

| 要素 | 説明 |
|---------|-------------|
| **authentication** (親要素) | ActiveDirectoryOAuth 認証を使用するための認証オブジェクト |
| **type** | 認証の種類。 ActiveDirectoryOAuth 認証の場合、値 `ActiveDirectoryOAuth`を使用します。 | 
| **tenant** | Azure AD テナントのテナント識別子。 |
| **audience** | この値は `https://management.core.windows.net/` に設定されます。 |
| **clientId** | Azure AD アプリケーションのクライアント識別子 |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>サンプル REST 要求 - Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>サンプル REST 応答 - Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="next-steps"></a>次のステップ

* [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
* [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)
* [Azure Scheduler REST API リファレンス](/rest/api/scheduler)
* [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)