---
title: "Scheduler 送信認証"
description: "Scheduler 送信認証"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4f2fcfecf0d888997b0b0061dc9ed2a8f862d78b


---
# <a name="scheduler-outbound-authentication"></a>Scheduler 送信認証
Scheduler ジョブでは、認証を必要とするサービスを呼び出すことが必要になる場合があります。 このようにして、呼び出されたサービスは、Scheduler ジョブがそのリソースにアクセスできるかどうかを確認できます。 このようなサービスには、他の Azure サービス、Salesforce.com、Facebook、およびセキュリティで保護されたカスタム Web サイトが含まれます。

## <a name="adding-and-removing-authentication"></a>認証の追加と削除
Scheduler ジョブに認証を追加するのは簡単です。ジョブを作成または更新するときに JSON の子要素 `authentication` を `request` 要素に追加するだけです。 PUT 要求、PATCH 要求、または POST 要求で `authentication` オブジェクトの一部として Scheduler サービスに渡されたシークレットが応答で返されることはありません 応答では、シークレット情報は null に設定されます。または、認証済みのエンティティを表す公開トークンが含まれる場合があります。

認証を削除するには、ジョブに対する PUT または PATCH 操作を明示的に実行して、`authentication` オブジェクトを null に設定します。 応答に認証プロパティが含まれることはありません。

現在サポートされている認証の種類は、(SSL/TLS クライアント証明書を使用するための) `ClientCertificate` モデル、(基本認証用の) `Basic` モデル、(Active Directory の OAuth 認証用の) `ActiveDirectoryOAuth` モデルのみです。

## <a name="request-body-for-clientcertificate-authentication"></a>ClientCertificate 認証の要求本文
`ClientCertificate` モデルを使用して認証を追加する場合は、要求本文に次の要素を指定します。  

| 要素 | 概要 |
|:--- |:--- |
| *authentication (親要素)* |SSL クライアント証明書を使用するための認証オブジェクト。 |
| *type* |必須。 認証の種類。SSL クライアント証明書の場合、値 `ClientCertificate` を使用する必要があります。 |
| *pfx* |必須。 Base64 でエンコードされた PFX ファイルのコンテンツ。 |
| *password* |必須。 PFX ファイルにアクセスするためのパスワード。 |

## <a name="response-body-for-clientcertificate-authentication"></a>ClientCertificate 認証の応答本文
認証情報を含めて要求を送信した場合、応答には次の認証に関連する要素が含まれます。

| 要素 | Description |
|:--- |:--- |
| *authentication (親要素)* |SSL クライアント証明書を使用するための認証オブジェクト。 |
| *type* |認証の種類。 SSL クライアント証明書の場合、値 `ClientCertificate`を使用します。 |
| *certificateThumbprint* |証明書の拇印。 |
| *certificateSubjectName* |証明書のサブジェクト識別名。 |
| *certificateExpiration* |証明書の有効期限日。 |

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>ClientCertificate 認証のサンプル REST 要求
```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>ClientCertificate 認証のサンプル REST 応答
```
HTTP/1.1 200 OK
Cache-Control: no-cache
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
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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

## <a name="request-body-for-basic-authentication"></a>基本認証の要求本文
`Basic` モデルを使用して認証を追加する場合は、要求本文に次の要素を指定します。

| 要素 | Description |
|:--- |:--- |
| *authentication (親要素)* |基本認証を使用するための認証オブジェクト。 |
| *type* |必須。 認証の種類。 基本認証の場合、値 `Basic`を使用する必要があります。 |
| *username* |必須。 認証するユーザー名。 |
| *password* |必須。 認証するパスワード。 |

## <a name="response-body-for-basic-authentication"></a>基本認証の応答本文
認証情報を含めて要求を送信した場合、応答には次の認証に関連する要素が含まれます。

| 要素 | Description |
|:--- |:--- |
| *authentication (親要素)* |基本認証を使用するための認証オブジェクト。 |
| *type* |認証の種類。 基本認証の場合、値 `Basic`を使用します。 |
| *username* |認証されたユーザー名。 |

## <a name="sample-rest-request-for-basic-authentication"></a>基本認証のサンプル REST 要求
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>基本認証のサンプル REST 応答
```
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
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 認証の要求本文
`ActiveDirectoryOAuth` モデルを使用して認証を追加する場合は、要求本文に次の要素を指定します。

| 要素 | Description |
|:--- |:--- |
| *authentication (親要素)* |ActiveDirectoryOAuth 認証を使用するための認証オブジェクト。 |
| *type* |必須。 認証の種類。 ActiveDirectoryOAuth 認証の場合、値 `ActiveDirectoryOAuth`を使用する必要があります。 |
| *tenant* |必須。 Azure AD テナントのテナント ID です。 |
| *audience* |必須。 これは https://management.core.windows.net/ に設定されます。 |
| *clientId* |必須。 Azure AD アプリケーションのクライアント識別子を指定します。 |
| *secret* |必須。 トークンを要求しているクライアントのシークレット。 |

### <a name="determining-your-tenant-identifier"></a>テナント ID の確認
Azure AD テナントのテナント ID は、Azure PowerShell で `Get-AzureAccount` を実行すると確認できます。

## <a name="response-body-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 認証の応答本文
認証情報を含めて要求を送信した場合、応答には次の認証に関連する要素が含まれます。

| 要素 | Description |
|:--- |:--- |
| *authentication (親要素)* |ActiveDirectoryOAuth 認証を使用するための認証オブジェクト。 |
| *type* |認証の種類。 ActiveDirectoryOAuth 認証の場合、値 `ActiveDirectoryOAuth`を使用します。 |
| *tenant* |Azure AD テナントのテナント ID です。 |
| *audience* |これは https://management.core.windows.net/ に設定されます。 |
| *clientId* |Azure AD アプリケーションのクライアント識別子。 |

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 認証のサンプル REST 要求
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 認証のサンプル REST 応答
```
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
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>関連項目
 [What is Scheduler? (Scheduler とは)](scheduler-intro.md)

 [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)

 [Azure ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)

 [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)




<!--HONumber=Nov16_HO3-->


