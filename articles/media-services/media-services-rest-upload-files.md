---
title: "REST を使用した Media Services アカウントへのファイルのアップロード | Microsoft Docs"
description: "資産を作成し、アップロードすることによって、Media Services にメディア コンテンツを取得する方法について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 41df7cbe-b8e2-48c1-a86c-361ec4e5251f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: c0ea95ed12a704116e8cdff257dacd7768b45708
ms.lasthandoff: 03/14/2017


---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>REST を使用して Media Services アカウントにファイルをアップロードする
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST ()](media-services-rest-upload-files.md)
> * [ポータル](media-services-portal-upload-files.md)
> 
> 

Media Services で、デジタル ファイルを資産にアップロードします。 [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) エンティティには、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を追加できます。ファイルを資産にアップロードすると、コンテンツがクラウドに安全に保存され、処理したりストリーミングしたりできるようになります。 

> [!NOTE]
> 次の考慮事項が適用されます。
> 
> * Media Services は、ストリーミング コンテンツ (たとえば、http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters) の URL を構築する際に、IAssetFile.Name プロパティの値を使用します。このため、パーセントエンコーディングは利用できません。 **Name** プロパティの値には、[パーセント エンコーディング予約文字](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) (!*'();:@&=+$,/?%#[]") は使用できません。 また、ファイル名拡張子で使用できる "." は&1; つのみです。
> * 名前は 260 文字以内で指定する必要があります。
> * Media Services での処理についてサポートされている最大ファイル サイズには制限があります。 ファイル サイズの制限の詳細については、[こちら](media-services-quotas-and-limitations.md)のトピックを参照してください。
> 

資産をアップロードする基本的なワークフローは、次のセクションに分けられます。

* 資産を作成する
* 資産を暗号化する (オプション)
* ファイルを BLOB ストレージにアップロードする

AMS では、資産を一括でアップロードすることもできます。 詳細については、 [こちらの](media-services-rest-upload-files.md#upload_in_bulk) セクションを参照してください。

## <a name="upload-assets"></a>資産をアップロードする
### <a name="create-an-asset"></a>資産を作成する
> [!NOTE]
> Media Services REST API を使用する場合は、次のことに考慮します。
> 
> Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「 [Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。
> 
> https://media.windows.net に正常に接続されると、別の Media Services の URI を指定する 301 リダイレクトを受け取ります。 「 [Media Services REST API を使用して Media Services アカウントに接続する](media-services-rest-connect-programmatically.md)」で説明するとおり、続けて新しい URI を呼び出す必要があります。 
> 
> 

資産は、ビデオ、オーディオ、イメージ、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイルなど、Media Services 内の多様な種類やセットのオブジェクトのためのコンテナーです。 REST API で資産を作成するには、Media Services に POST 要求を送信し、要求本文に、資産に関するプロパティ情報を配置する必要があります。

資産を作成するときに指定できるプロパティの&1; つは **Options** です。 **Options** は、資産の作成に使用できる暗号化オプションを説明する列挙値です。 有効な値は、以下の一覧の値のいずれかです。値の組み合わせではありません。 

* **None** = **0**: 暗号化は使用されません。 これが既定値です。 このオプションを使用した場合、送信経路上とストレージ内のいずれにおいてもコンテンツが保護されないので注意してください。
    プログレッシブ ダウンロードを使用して MP4 を配信する場合はこのオプションを使用します。 
* **StorageEncrypted** = **1**: AES-256 ビット暗号化で暗号化してファイルをアップロードおよび格納する場合に指定します。
  
    資産がストレージで暗号化されている場合は、資産配信ポリシーを構成する必要があります。 詳細については、「 [資産配信ポリシーの構成](media-services-rest-configure-asset-delivery-policy.md)」をご覧ください。
* **CommonEncryptionProtected** = **2**: 共通の暗号化方式 (PlayReady など) で保護されているファイルをアップロードする場合に指定します。 
* **EnvelopeEncryptionProtected** = **4**: AES で暗号化された HLS ファイルをアップロードする場合はこのオプションを使用します。 この場合ファイルは、Transform Manager によってあらかじめエンコードされて暗号化されている必要があります。

> [!NOTE]
> アセットに暗号化を使用する場合は、 **ContentKey** を作成し、[ContentKey の作成方法](media-services-rest-create-contentkey.md)に関するページの説明に従ってアセットにリンクする必要があります。 ファイルを資産にアップロードした後、**AssetFile** エンティティの暗号化プロパティを **Asset** 暗号化中に取得した値に更新する必要があります。 **MERGE** HTTP 要求を使用して実行します。 
> 
> 

次の例では、資産を作成する方法を示します。

**HTTP 要求**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net

    {"Name":"BigBuckBunny.mp4"}


**HTTP 応答**

成功した場合は、次の内容が返されます。

    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>AssetFile を作成する
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) エンティティは、BLOB コンテナーに格納されているビデオまたはオーディオ ファイルを表します。 資産ファイルは、常に資産に関連付けられており、資産には&1; つまたは複数の資産ファイルが含まれている可能性があります。 資産ファイル オブジェクトが blob コンテナー内のデジタル ファイルに関連付けられていないと、Media Services のエンコーダー タスクは失敗します。

**AssetFile** インスタンスと実際のメディア ファイルは次の&2; つの異なるオブジェクトであることに注意してください。 AssetFile インスタンスには、メディア ファイルに関するメタデータが含まれており、メディア ファイルには実際のメディア コンテンツが含まれています。

デジタル メディア ファイルを BLOB コンテナーにアップロードした後、 **MERGE** HTTP 要求を使用して、メディア ファイル (トピックの後半で説明します) に関する情報で AssetFile を更新します。 

**HTTP 要求**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP 応答**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>書き込みのアクセス許可を持つ AccessPolicy を作成する

>[!NOTE]
>さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、 [こちらの](media-services-dotnet-manage-entities.md#limit-access-policies) トピックを参照してください。

すべてのファイルを blob ストレージにアップロードする前に、資産に書き込むためのアクセス ポリシーの権限を設定します。 そのためには、AccessPolicies エンティティ セットへの HTTP 要求を投稿します。 作成時に DurationInMinutes 値を定義します。定義していないと、500 Internal Server エラー メッセージが返されます。 AccessPolicies について詳しくは、[AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) をご覧ください。

次の例は、AccessPolicy を作成する方法を示します。

**HTTP 要求**

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**HTTP 要求**

    If successful, the following response is returned:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>アップロード URL を取得する
実際のアップロード URL を受信するには、SAS Locator　を作成します。 Locator は、資産内のファイルにアクセスするクライアントの開始時刻と接続エンドポイントの種類を定義します。 特定の AccessPolicy と Asset ペアに対して複数の　Locator　エンティティを作成して、別のクライアントの要求およびニーズを処理できます。 これらの各 Locator は、AccessPolicy の StartTime 値と DurationInMinutes 値を使用して、URL を使用できる時間の長さを決定します。 詳細については、「 [Locator](https://docs.microsoft.com/rest/api/media/operations/locator)」をご覧ください。

SAS URL には次の形式があります。

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

いくつかの考慮事項が適用されます。

* 特定の資産に関連付けられている&5; つの一意の Locator を同時に使用することはできません。 詳細については、「Locator」をご覧ください。
* すぐにファイルをアップロードする必要がある場合は、StartTime 値を現在の時刻の&5; 分前に設定する必要があります。 これは、クライアント コンピューターと Media Services の間にクロック スキューがある可能性があるためです。 また、StartTime 値の DateTime 形式は、YYYY-MM-DDTHH:mm:ssZ とする必要があります (たとえば、"2014-05-23T17:53:50Z")。    
* Locator を作成した後に使用可能になるまで 30 ～ 40 秒の遅延が発生する場合があります。 この問題は、SAS URL と Origin Locator の両方に当てはまります。

次の例は、要求本文の Type プロパティで定義されているように、SAS URL Locator を作成する方法を示しています　(SAS ロケーターの場合は "1"、オンデマンド配信元ロケーターの場合は "2")。 返される **Path** プロパティには、ファイルのアップロードに使用する必要がある URL が含まれています。

**HTTP 要求**

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP 応答**

成功した場合、次の応答が返されます。

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Blob ストレージ コンテナーにファイルをアップロードする
AccessPolicy と Locator を設定すると、実際のファイルは、Azure Storage REST API を使用して Azure Blob ストレージ コンテナーにアップロードされます。 ファイルはブロック BLOB としてをアップロードする必要があります。 ページ BLOB は Azure Media Services ではサポートされていません。  

> [!NOTE]
> 前のセクションで受信したロケーターの **Path** 値にアップロードするファイルのファイル名を追加する必要があります。 例: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? に関するページを参照してください。 が必要です。 」をご覧ください。 
> 
> 

Azure ストレージ BLOB の使用の詳細については、 [BLOB サービス REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/Blob-Service-REST-API)をご覧ください。

### <a name="update-the-assetfile"></a>AssetFile を更新する
ファイルをアップロードしたので、FileAsset サイズ (およびその他) の情報を更新します。 次に例を示します。

    MERGE https://media.windows.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP 応答**

成功した場合は、「HTTP/1.1 204 No Content」が返されます。

### <a name="delete-the-locator-and-accesspolicy"></a>Locator と AccessPolicy　を削除します。
**HTTP 要求**

    DELETE https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net


**HTTP 応答**

成功した場合は、次の内容が返されます。

    HTTP/1.1 204 No Content 
    ...

**HTTP 要求**

    DELETE https://media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net

**HTTP 応答**

成功した場合は、次の内容が返されます。

    HTTP/1.1 204 No Content 
    ...

## <a id="upload_in_bulk"></a>資産を一括でアップロードする
### <a name="create-the-ingestmanifest"></a>IngestManifest を作成する
IngestManifest は、資産、資産 ファイル、および静的情報のセット用のコンテナーで、このセットの一括取り込みの進行状況を把握するのに使用できす。

**HTTP 要求**

    POST https:// media.windows.net/API/IngestManifests HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 36
    Expect: 100-continue

    { "Name" : "ExampleManifestREST" }

### <a name="create-assets"></a>資産を作成する
IngestManifestAsset を作成する前に、一括取り込みで完成する資産を作成する必要があります。 資産は、ビデオ、オーディオ、イメージ、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイルなど、Media Services 内の多様な種類やセットのオブジェクトのためのコンテナーです。 REST API の資産を作成するには、Microsoft Azure Media Services に HTTP POST 要求を送信し、要求本文に、資産に関する任意のプロパティ情報を配置します。この例では、要求本文に含まれる StorageEncrption(1) を使用して資産が作成されています。

**HTTP 応答**

    POST https://media.windows.net/API/Assets HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 55
    Expect: 100-continue

    { "Name" : "ExampleManifestREST_Asset", "Options" : 1 }

### <a name="create-the-ingestmanifestassets"></a>IngestManifestAssets を作成する
IngestManifestAssets は一括取り込みで使用される IngestManifest 内の Asset を示します。 これでは、基本的に資産をマニフェストにリンクします。 Azure Media Services は、IngestManifestAsset に関連付けられている IngestManifestFiles コレクションに基づいて、内部でファイルのアップロードを監視します。 これらのファイルがアップロードされると、資産が完成します。 新しい IngestManifestAsset は、HTTP POST 要求を使用して作成できます。 要求本文に、一括取り込みのために IngestManifestAsset がリンクをする必要のある IngestManifest ID と Asset ID を含めます。

**HTTP 応答**

    POST https://media.windows.net/API/IngestManifestAssets HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 152
    Expect: 100-continue
    { "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "Asset" : { "Id" : "nb:cid:UUID:b757929a-5a57-430b-b33e-c05c6cbef02e"}}


### <a name="create-the-ingestmanifestfiles-for-each-asset"></a>各資産の IngestManifestFiles を作成する
IngestManifestFile とは、資産の一括取り込みの一環としてアップロードされる実際のビデオまたはオーディオ BLOB オブジェクトです。 暗号化関連のプロパティは、資産で暗号化オプションを使用しない限り不要です。 このセクションの例では、StorageEncryption を使用する IngestManifestFile を作成済みの資産用に作成する方法を示します。

**HTTP 応答**

    POST https://media.windows.net/API/IngestManifestFiles HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 367
    Expect: 100-continue

    { "Name" : "REST_Example_File.wmv", "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "ParentIngestManifestAssetId" : "nb:maid:UUID:beed8531-9a03-9043-b1d8-6a6d1044cdda", "IsEncrypted" : "true", "EncryptionScheme" : "StorageEncryption", "EncryptionVersion" : "1.0", "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510" }

### <a name="upload-the-files-to-blob-storage"></a>Blob ストレージにファイルをアップロードする
資産ファイルを BLOB ストレージ コンテナーの URI にアップロードする機能のある任意の高速クライアント アプリケーションを使用できます。これは、IngestManifest の BlobStorageUriForUpload プロパティから提供します。 有名な高速アップロードサービスには、[Aspera On Demand for Azure アプリケーション](http://go.microsoft.com/fwlink/?LinkId=272001)などがあります。

### <a name="monitor-bulk-ingest-progress"></a>一括取り込みの進捗状況を監視する
IngestManifest の Statistics プロパティをポーリングすることによって、IngestManifest の一括取り込み操作の進行状況を監視できます。 このプロパティは複合型の [IngestManifestStatistics](https://docs.microsoft.com/rest/api/media/operations/ingestmanifeststatistics)です。 Statistics プロパティをポーリングするには、IngestManifest ID を渡して、HTTP GET 要求を送信します。

## <a name="create-contentkeys-used-for-encryption"></a>暗号化に使用する ContentKey を作成する
資産で暗号化を使用する場合、資産ファイルを作成する前に、暗号化に使用する ContentKey を作成しておく必要があります。 ストレージ暗号化では、次のプロパティを要求本文に含める必要があります。

| 要求本文のプロパティ | Description |
| --- | --- |
| ID |"nb:kid:UUID:<NEW GUID>" 形式を使用して生成する ContentKey ID です。 |
| ContentKeyType |整数によるこのコンテンツ キーの種類です。 ストレージの暗号化には、値 1 を渡します。 |
| EncryptedContentKey |256 ビット (32 バイト) の値の新しいコンテンツ キー値を作成します。 このキーは、GetProtectionKeyId および GetProtectionKey メソッド用に HTTP GET 要求を実行して Microsoft Azure Media Services から取得する、ストレージ暗号化 X.509 証明書を使用して暗号化します。 |
| ProtectionKeyId |コンテンツ キーの暗号化に使用したストレージ暗号化 X.509 証明書の保護キー ID です。 |
| ProtectionKeyType |コンテンツ キーの暗号化に使用した保護キーの暗号化の種類です。 例では、この値には StorageEncryption(1) を使用しています。 |
| Checksum |コンテンツ キー用に MD5 で計算されたチェックサムです。 コンテンツ ID をコンテンツ キーで暗号化してコンピューティングします。 コード例では、チェックサムの計算方法を示しています。 |

**HTTP 応答**

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 572
    Expect: 100-continue

    {"Id" : "nb:kid:UUID:316d14d4-b603-4d90-b8db-0fede8aa48f8", "ContentKeyType" : 1, "EncryptedContentKey" : "Y4NPej7heOFa2vsd8ZEOcjjpu/qOq3RJ6GRfxa8CCwtAM83d6J2mKOeQFUmMyVXUSsBCCOdufmieTKi+hOUtNAbyNM4lY4AXI537b9GaY8oSeje0NGU8+QCOuf7jGdRac5B9uIk7WwD76RAJnqyep6U/OdvQV4RLvvZ9w7nO4bY8RHaUaLxC2u4aIRRaZtLu5rm8GKBPy87OzQVXNgnLM01I8s3Z4wJ3i7jXqkknDy4VkIyLBSQvIvUzxYHeNdMVWDmS+jPN9ScVmolUwGzH1A23td8UWFHOjTjXHLjNm5Yq+7MIOoaxeMlKPYXRFKofRY8Qh5o5tqvycSAJ9KUqfg==", "ProtectionKeyId" : "7D9BB04D9D0A4A24800CADBFEF232689E048F69C", "ProtectionKeyType" : 1, "Checksum" : "TfXtjCIlq1Y=" }

### <a name="link-the-contentkey-to-the-asset"></a>ContentKey を資産にリンクする
ContentKey は、HTTP POST 要求を送信することによって&1; つ以上の資産に関連付けます。 次では、ID を使用して、ContentKey の例と資産の例をリンクする要求の例を示します。

**HTTP 応答**

    POST https://media.windows.net/API/Assets('nb:cid:UUID:b3023475-09b4-4647-9d6d-6fc242822e68')/$links/ContentKeys HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 113
    Expect: 100-continue

    { "uri": "https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A32e6efaf-5fba-4538-b115-9d1cefe43510')"}

**HTTP 応答**

    GET https://media.windows.net/API/IngestManifests('nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net

## <a name="next-steps"></a>次のステップ

これで、アップロードした資産をエンコードできます。 詳細については、 [資産のエンコード](media-services-portal-encode.md)に関するページをご覧ください。

また、Azure Functions を使用すると、構成したコンテナーに到着するファイルに基づいてエンコード ジョブをトリガーすることもできます。 詳細については、[このサンプル](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )を参照してください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[How to Get a Media Processor]: media-services-get-media-processor.md


