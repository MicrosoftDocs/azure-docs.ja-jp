---
title: "Media Services REST API を使用して Media Services アカウントに接続する | Microsoft Docs"
description: "このトピックでは、Media Services REST API を使用して Media Services に接続する方法について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 79dc64f1-15d8-4a81-b9d9-3d3c44d2e1e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 4feb0eb81823835e8e0b701463d85b27f5598019
ms.lasthandoff: 03/14/2017


---
# <a name="connecting-to-media-services-account-using-media-services-rest-api"></a>Media Services REST API を使用して Media Services アカウントに接続する
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-connect-programmatically.md)
> * [REST ()](media-services-rest-connect-programmatically.md)
> 
> 

このトピックでは、Media Services REST API でプログラミングしている場合に、Microsoft Azure Media Services にプログラムで接続する方法について説明します。

Microsoft Azure Media Services にアクセスする際には、Azure Access Control Service (ACS) から提供されるアクセス トークン、および Media Services 自体の URI の&2; つが必要です。 Media Services にコールする際、正しいヘッダー値を指定して適切にアクセス トークンに渡す限り、どのような方法でもこれらのリクエストを作成できます。

次の手順では、Media Services REST API を使用して Media Services に接続するときの最も一般的なワークフローについて説明します。

1. アクセス トークンの取得 
2. Media Services URI への接続 
   
   > [!NOTE]
   > Https://media.windows.net に正常に接続されると、別の Media Services の URI を指定する 301 リダイレクトを受け取ります。 その新しい URI に再度コールする必要があります。
   > ODATA API メタデータの説明が含まれる HTTP/1.1 200 応答が表示される場合もあります。
   > 
   > 
3. 続けて、新しい URL に API コールを行います。 
   
    たとえば、接続しようとした後に次のようなメッセージが表示されます。
   
        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
   
    この場合、続けて、「https://wamsbayclus001rest-hs.cloudapp.net/api/」へ API コールを行う必要があります。

    >[!NOTE]
    >さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、 [こちらの](media-services-dotnet-manage-entities.md#limit-access-policies) トピックを参照してください。

## <a name="access-control-address"></a>アクセス制御アドレス
Media Services のアクセス制御アドレスは https://wamsprodglobal001acs.accesscontrol.windows.net です。ただし、中国北部リージョンの場合は https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn です。

## <a name="getting-an-access-token"></a>アクセス トークンの取得
REST API から直接 Media Services にアクセスするには、ACS からアクセス トークンを取得して、Media Services に行う各 HTTP 要求で使用します。 このトークンは、OAuth v2 プロトコルを使用して、HTTP 要求のヘッダーで提供されるアクセス要求に基づいて ACS で提供されるその他のトークンに似ています。 Media Services に直接接続するための前提条件は、これ以外にはありません。

次の例は、トークンを取得するために使用される HTTP 要求のヘッダーと本文を示します。

**ヘッダー**:

    POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Host: wamsprodglobal001acs.accesscontrol.windows.net
    Content-Length: 120
    Expect: 100-continue
    Connection: Keep-Alive
    Accept: application/json


**本文**:

この要求の本文で、client_id と client_secret の値を指定する必要があります。client_id と client_secret はそれぞれ、AccountName と AccountKey の値に対応します。 Media Services では、アカウントを設定したときにこれらの値が提供されます。 

Media Services アカウントの AccountKey は、アクセス トークン要求で client_secret 値として使用されるときは URL エンコード ([Percent-Encoding](http://tools.ietf.org/html/rfc3986#section-2.1) を参照) されている必要があります。

    grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


次に例を示します。 

    grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


次の例は、本文にアクセス トークンを含む HTTP 応答を示します。

    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    request-id: a65150f5-2784-4a01-a4b7-33456187ad83
    X-Content-Type-Options: nosniff
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 15 Jan 2015 08:07:20 GMT
    Content-Length: 670

    {  
       "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }


> [!NOTE]
> "access_token " と "expires_in " の値は外部ストレージにキャッシュすることをお勧めします。 後でそのストレージからトークンのデータを取り出し、Media Services REST API コールで再利用できます。 これはトークンが複数のプロセスやコンピューターで安全に共有される場合に、特に便利です。
> 
> 

アクセス トークンの "expires_in" 値を確認し、必要に応じて新しいトークンで REST API コールを更新してください。

### <a name="connecting-to-the-media-services-uri"></a>Media Services URI への接続
メディア サービスのルート URI は「https://media.windows.net/」です。 まず、この URI に接続して、301 リダイレクトが返された場合は続けて新しい URI にコールする必要があります。 加えて、要求に auto-redirect/follow ロジックを使うことはできません。 HTTP 動詞や応答本文はその新しい URI に転送されません。

アセット ファイルをアップロードしたりダウンロードしたりするルート URI は「https://yourstorageaccount.blob.core.windows.net/」です。ストレージ アカウント名には、メディア サービス アカウントで設定したものと同じものが使われます。

次の例は、Media Services ルート URI (https://media.windows.net/) への HTTP 要求を示します。 要求に対して 301 リダイレクトが返されています。 後続の要求では、新しい URI (https://wamsbayclus001rest-hs.cloudapp.net/api/) が使用されています。     

**HTTP 要求**:

    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: media.windows.net


**HTTP 応答**:

    HTTP/1.1 301 Moved Permanently
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
    Server: Microsoft-IIS/8.5
    request-id: 987d7652-497a-44e5-b815-f492e02aef97
    x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:53 GMT
    Content-Length: 164

    <html><head><title>Object moved</title></head><body>
    <h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
    </body></html>


**HTTP 要求** (新しい URI を使用):

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP 応答**:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1250
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}



> [!NOTE]
> 新しい URI を取得したら、その URI を使用して Media Services と通信します。 
> 
> 

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


