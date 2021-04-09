---
title: REST での Azure Media Services テレメトリの構成 | Microsoft Docs
description: この記事では、REST API を使用して Azure Media Services テレメトリを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e1a314fb-cc05-4a82-a41b-d1c9888aab09
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 37276240835fe1a06928ee54383f81fa95690ece
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015491"
---
# <a name="configuring-azure-media-services-telemetry-with-rest"></a>REST での Azure Media Services テレメトリの構成

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

このトピックでは、REST API を使用して Azure Media Services (AMS) テレメトリを構成するときの一般的な手順について説明します。 

>[!NOTE]
>AMS テレメトリの概念と使用方法の詳細については、[概要](media-services-telemetry-overview.md)に関するトピックを参照してください。

このトピックでは、次の手順について説明します。

- Media Services に関連付けられたストレージ アカウントを取得する
- 通知エンドポイントを取得する
- 監視するための通知エンドポイントを作成する 

    通知エンドポイントを作成するには、EndPointType を AzureTable (2) に設定し、endPontAddress をストレージ テーブル (例: https:\//telemetryvalidationstore.table.core.windows.net/) に設定します。
  
- 監視構成を取得する

    監視するサービスの監視構成設定を作成します。 許可される監視構成設定は 1 つだけです。 

- 監視構成を追加する


 
## <a name="get-the-storage-account-associated-with-a-media-services-account"></a>Media Services に関連付けられたストレージ アカウントを取得する

### <a name="request"></a>Request

```console
GET https://wamsbnp1clus001rest-hs.cloudapp.net/api/StorageAccounts HTTP/1.1
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Host: wamsbnp1clus001rest-hs.cloudapp.net
Response
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 370
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 8206e222-2a59-482c-a6a9-de6b8bda57fb
x-ms-request-id: 8206e222-2a59-482c-a6a9-de6b8bda57fb
X-Content-Type-Options: nosniff
DataServiceVersion: 2.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:40 GMT
    
{"d":{"results":[{"__metadata":{"id":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/StorageAccounts('telemetryvalidationstore')","uri":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/StorageAccounts('telemetryvalidationstore')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.StorageAccount"},"Name":"telemetryvalidationstore","IsDefault":true,"BytesUsed":null}]}}
```

## <a name="get-the-notification-endpoints"></a>通知エンドポイントを取得する

### <a name="request"></a>Request

```console
GET https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints HTTP/1.1
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Host: wamsbnp1clus001rest-hs.cloudapp.net
```

### <a name="response"></a>Response

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 20
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: c68de2b3-0be1-4823-b622-6ca6f94a96b5
x-ms-request-id: c68de2b3-0be1-4823-b622-6ca6f94a96b5
X-Content-Type-Options: nosniff
DataServiceVersion: 2.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:40 GMT
    
{  
    "d":{  
        "results":[]
    }
}
 ```

## <a name="create-a-notification-endpoint-for-monitoring"></a>監視するための通知エンドポイントを作成する

### <a name="request"></a>Request

```console
POST https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints HTTP/1.1
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Content-Type: application/json; charset=utf-8
Host: wamsbnp1clus001rest-hs.cloudapp.net
Content-Length: 115

{  
    "Name":"monitoring",
    "EndPointAddress":"https:\//telemetryvalidationstore.table.core.windows.net/",
    "EndPointType":2
}
```

> [!NOTE]
> "https:\//telemetryvalidationstore.table.core.windows.net" 値は、必ず実際に使用するストレージ アカウントに変更してください。

### <a name="response"></a>Response

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 578
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')
Server: Microsoft-IIS/8.5
request-id: e8fa5a60-7d8b-4b00-a7ee-9b0f162fe0a9
x-ms-request-id: e8fa5a60-7d8b-4b00-a7ee-9b0f162fe0a9
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:42 GMT
    
{"d":{"__metadata":{"id":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')","uri":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.NotificationEndPoint"},"Id":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4","Name":"monitoring","Created":"\/Date(1449033042667)\/","EndPointAddress":"https://telemetryvalidationstore.table.core.windows.net/","EndPointType":2}}
 ```

## <a name="get-the-monitoring-configurations"></a>監視構成を取得する

### <a name="request"></a>Request

```console
GET https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations HTTP/1.1
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Host: wamsbnp1clus001rest-hs.cloudapp.net
```

### <a name="response"></a>Response

```console  
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 20
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 00a3ee37-bb19-4fca-b5c7-a92b629d4416
x-ms-request-id: 00a3ee37-bb19-4fca-b5c7-a92b629d4416
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:42 GMT
    
{"d":{"results":[]}}
```

## <a name="add-a-monitoring-configuration"></a>監視構成を追加する

### <a name="request"></a>Request

```console
POST https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations HTTP/1.1
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Content-Type: application/json; charset=utf-8
Host: wamsbnp1clus001rest-hs.cloudapp.net
Content-Length: 133
    
{  
   "NotificationEndPointId":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4",
   "Settings":[  
      {  
     "Component":"Channel",
     "Level":"Normal"
      }
   ]
}
```

### <a name="response"></a>Response

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 825
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')
Server: Microsoft-IIS/8.5
request-id: daede9cb-8684-41b0-a921-a3af66430cbe
x-ms-request-id: daede9cb-8684-41b0-a921-a3af66430cbe
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:43 GMT
    
{"d":{"__metadata":{"id":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')","uri":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.MonitoringConfiguration"},"Id":"nb:mcid:UUID:1a8931ae-799f-45fd-8aeb-9641740295c2","NotificationEndPointId":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4","Created":"2015-12-02T05:10:43.7680396Z","LastModified":"2015-12-02T05:10:43.7680396Z","Settings":{"__metadata":{"type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.ComponentMonitoringSettings)"},"results":[{"Component":"Channel","Level":"Normal"},{"Component":"StreamingEndpoint","Level":"Disabled"}]}}}
```

## <a name="stop-telemetry"></a>テレメトリを停止する

### <a name="request"></a>Request

```console
DELETE https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Content-Type: application/json; charset=utf-8
Host: wamsbnp1clus001rest-hs.cloudapp.net
```

## <a name="consuming-telemetry-information"></a>テレメトリ情報の使用

テレメトリ情報の使用については、[こちら](media-services-telemetry-overview.md)のトピックを参照してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
