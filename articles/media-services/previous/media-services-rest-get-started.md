---
title: REST を使用したオンデマンド コンテンツ配信の概要 | Microsoft Docs
description: このチュートリアルでは、Azure Media Services と REST API を使用したオンデマンド コンテンツ配信アプリケーションの実装手順を紹介します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: ccf4bc18270dcdbcae02f7b45bea2a05ed663227
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008877"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>REST を使用したオンデマンド コンテンツ配信の概要

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

このクイックスタートでは Azure Media Services (AMS) の REST API を使用するビデオ オン デマンド (VoD) コンテンツ配信アプリケーションの実装について手順を追って説明します。

チュートリアルでは Media Services の基本的なワークフローや、Media Services 開発に必要となる一般的なプログラミング オブジェクトやタスクについて紹介します。 このチュートリアルを完了すると、サンプル メディア ファイルをアップロード、エンコード、ダウンロードして、ストリーミングやプログレッシブ ダウンロードを実行できます。

次の図は、Media Services OData モデルに対する VoD アプリケーションの開発時に最もよく使用されるオブジェクトの一部を示しています。

画像をクリックすると、フル サイズで表示されます。  

[![ビデオ オン デマンド アプリケーションを開発するために Azure Media Services オブジェクト データ モデルで最もよく使用されるオブジェクトの一部を示す図。](./media/media-services-rest-get-started/media-services-overview-object-model-small.png)](./media/media-services-rest-get-started/media-services-overview-object-model.png#lightbox)

## <a name="prerequisites"></a>前提条件
REST API を使用して Media Services での開発を始めるには、次の前提条件が必要です。

* Azure アカウント。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。
* Media Services アカウント。 Media Services アカウントを作成するには、[Media Services アカウントを作成する方法](media-services-portal-create-account.md)に関するページを参照してください。
* Media Services REST API を使用して開発する方法を理解します。 詳細については、「[Media Services REST API の概要](media-services-rest-how-to-use.md)」を参照してください。
* HTTP 要求と応答を送信できる任意のアプリケーション。 このチュートリアルでは、 [Fiddler](https://www.telerik.com/download/fiddler)を使用します。

このクイック スタートでは、次のタスクが表示されます。

1. (Azure Portal を使用して) ストリーミング エンドポイントを開始する。
2. REST API で Media Services アカウントに接続する
3. 新しい資産を作成し、REST API を使用してビデオのファイルをアップロードする
4. REST API で一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードする
5. 資産を発行して REST API によるストリーミングとプログレッシブ ダウンロード用 URL を取得する
6. コンテンツの再生

>[!NOTE]
>さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使います (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、[こちらの記事](media-services-dotnet-manage-entities.md#limit-access-policies)を参照してください。

この記事で使用する AMS REST エンティティの詳細については、[Azure Media Services REST API リファレンス](/rest/api/media/operations/azure-media-services-rest-api-reference)に関するページをご覧ください。 また、「[Azure Media Services の概念](media-services-concepts.md)」も参照してください。

>[!NOTE]
>Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「 [Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Azure Portal を使用したストリーミング エンドポイントの開始

アダプティブ ビットレート ストリーミングでのビデオ配信は、Azure Media Services の代表的な用途の 1 つです。 Media Services にはダイナミック パッケージ機能があり、アダプティブ ビットレート MP4 でエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、Smooth Streaming) でそのまますぐに配信することができます。つまり、事前にパッケージされたこれらのストリーミング形式のバージョンを保存しておく必要がありません。

>[!NOTE]
>AMS アカウントの作成時に、**既定** のストリーミング エンドポイントが自分のアカウントに追加され、**停止** 状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが **実行中** 状態である必要があります。

ストリーミング エンドポイントを開始するには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. [設定] ウィンドウで [ストリーミング エンドポイント] をクリックします。
3. 既定のストリーミング エンドポイントをクリックします。

    [DEFAULT STREAMING ENDPOINT DETAILS (既定のストリーミング エンドポイントの詳細)] ウィンドウが表示されます。

4. [開始] アイコンをクリックします。
5. [保存] をクリックして、変更を保存します。

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>REST API で Media Services アカウントに接続する

AMS API に接続する方法については、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>新しいアセットを作成し、REST API を使用してビデオのファイルをアップロードする

Media Services で、デジタル ファイルを資産にアップロードします。 **Asset** エンティティには、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を追加できます。ファイルを資産にアップロードすると、コンテンツがクラウドに安全に保存され、処理したりストリーミングしたりできるようになります。

資産作成時に指定する必要がある値の 1 つに、資産作成オプションがあります。 **Options** プロパティは、アセットの作成に使用できる暗号化オプションを示す列挙値です。 有効な値は以下の一覧の値のいずれかですが、この一覧の値を組み合わせて使用することはできません。

* **None** = **0**: 暗号化は使用されません。 このオプションを使用した場合、送信経路上とストレージ内のいずれにおいてもコンテンツが保護されません。
    プログレッシブ ダウンロードを使用して MP4 を配信する場合はこのオプションを使用します。
* **StorageEncrypted** = **1**: ローカルで AES-256 ビット暗号化を使用し、平文のコンテンツを暗号化したうえで、それを Azure Storage にアップロードします。アップロードされたデータは、暗号化された状態で保存されます。 StorageEncrypted で保護されたアセットは、エンコーディングの前に自動的に暗号化が解除され、暗号化されたファイル システムに配置されます。その後、必要に応じて再度暗号化を適用して、新しい出力アセットとして再びアップロードできます。 StorageEncrypted の主な目的は、高品質の入力メディア ファイルを強力な暗号化によって保護したうえでディスクに保存するというニーズに応えることです。
* **CommonEncryptionProtected** = **2**: 既に Common Encryption や PlayReady DRM で暗号化されて保護されているコンテンツ (PlayReady DRM で保護されたスムーズ ストリーミングなど) をアップロードする場合は、このオプションを使用します。
* **EnvelopeEncryptionProtected** = **4** : AES で暗号化された HLS をアップロードする場合はこのオプションを使用します。 この場合ファイルは、Transform Manager によってあらかじめエンコードされて暗号化されている必要があります。

### <a name="create-an-asset"></a>アセットを作成する
資産は、ビデオ、オーディオ、イメージ、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイルなど、Media Services 内の多様な種類やセットのオブジェクトのためのコンテナーです。 REST API で資産を作成するには、Media Services に POST 要求を送信し、要求本文に、資産に関するプロパティ情報を配置する必要があります。

次の例では、資産を作成する方法を示します。

**HTTP 要求**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 45

{"Name":"BigBuckBunny.mp4", "Options":"0"}
```

**HTTP 応答**

成功した場合は、次の内容が返されます。

```console
HTTP/1.1 201 Created
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
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT

{  
    odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny2.mp4",
   "Options":0,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

### <a name="create-an-assetfile"></a>AssetFile を作成する
[AssetFile](/rest/api/media/operations/assetfile) エンティティは、BLOB コンテナーに格納されているビデオまたはオーディオ ファイルを表します。 資産ファイルは常に資産に関連付けられており、資産には 1 つまたは複数の Assetfile を含む場合があります。 資産ファイル オブジェクトが blob コンテナー内のデジタル ファイルに関連付けられていないと、Media Services のエンコーダー タスクは失敗します。

デジタル メディア ファイルを blob コンテナーにアップロードした後、 **MERGE** HTTP 要求を使用して、メディア ファイル (トピックの後半に表示) に関する情報と共に AssetFile を更新します。

**HTTP 要求**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 164

{  
   "IsEncrypted":"false",
   "IsPrimary":"false",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP 応答**

```console
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
```

### <a name="creating-the-accesspolicy-with-write-permission"></a>書き込みのアクセス許可を持つ AccessPolicy を作成する
すべてのファイルを blob ストレージにアップロードする前に、資産に書き込むためのアクセス ポリシーの権限を設定します。 そのためには、AccessPolicies エンティティ セットへの HTTP 要求を投稿します。 作成時に DurationInMinutes 値を定義します。定義していないと、500 Internal Server エラー メッセージが返されます。 AccessPolicies について詳しくは、[AccessPolicy](/rest/api/media/operations/accesspolicy) をご覧ください。

次の例は、AccessPolicy を作成する方法を示します。

**HTTP 要求**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 74

{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}
```

**HTTP 応答**

成功した場合、次の応答が返されます。

```console
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
X-Powered-By: ASP.NET
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
```

### <a name="get-the-upload-url"></a>アップロード URL を取得する

実際のアップロード URL を受信するには、SAS Locator　を作成します。 Locator は、資産内のファイルにアクセスするクライアントの開始時刻と接続エンドポイントの種類を定義します。 特定の AccessPolicy と Asset ペアに対して複数の　Locator　エンティティを作成して、別のクライアントの要求およびニーズを処理できます。 これらの各 Locator は、AccessPolicy の StartTime 値と DurationInMinutes 値を使用して、URL を使用できる時間の長さを決定します。 詳細については、「 [Locator](/rest/api/media/operations/locator)」をご覧ください。

SAS URL には次の形式があります。

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

いくつかの考慮事項が適用されます。

* 特定の資産に関連付けられている 5 つの一意の Locator を同時に使用することはできません。 
* すぐにファイルをアップロードする必要がある場合は、StartTime 値を現在の時刻の 5 分前に設定する必要があります。 これは、クライアント コンピューターと Media Services の間にクロック スキューがある可能性があるためです。 また、StartTime 値は YYYY-MM-DDTHH:mm:ssZ ("2014-05-23T17:53:50Z" など) の DateTime 形式である必要があります。    
* Locator を作成した後に使用可能になるまで 30 ～ 40 秒の遅延が発生する場合があります。 この問題は、[SAS URL](../../storage/common/storage-sas-overview.md) と Origin Locator の両方に当てはまります。

次の例は、要求本文の Type プロパティで定義されているように、SAS URL Locator を作成する方法を示しています　(SAS ロケーターの場合は "1"、オンデマンド配信元ロケーターの場合は "2")。 返される **Path** プロパティには、ファイルのアップロードに使用する必要がある URL が含まれています。

**HTTP 要求**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 178

{  
   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StartTime":"2015-02-18T16:45:53",
   "Type":1
}
```

**HTTP 応答**

成功した場合、次の応答が返されます。

```console
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
```

### <a name="upload-a-file-into-a-blob-storage-container"></a>Blob ストレージ コンテナーにファイルをアップロードする
AccessPolicy と Locator を設定すると、実際のファイルは、Azure Storage REST API を使用して Azure BLOB ストレージ コンテナーにアップロードされます。 ファイルはブロック BLOB としてをアップロードする必要があります。 ページ BLOB は Azure Media Services ではサポートされていません。  

> [!NOTE]
> 前のセクションで受信したロケーターの **Path** 値にアップロードするファイルのファイル名を追加する必要があります。 たとえば、「 `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?` 」のように入力します。
>
>

Azure ストレージ BLOB の使用の詳細については、 [BLOB サービス REST API](/rest/api/storageservices/blob-service-rest-api)をご覧ください。

### <a name="update-the-assetfile"></a>AssetFile を更新する
ファイルをアップロードしたので、FileAsset サイズ (およびその他) の情報を更新します。 次に例を示します。

```console
MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net

{  
   "ContentFileSize":"1186540",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP 応答**

成功した場合は、次の内容が返されます。

```console
HTTP/1.1 204 No Content
...
```

## <a name="delete-the-locator-and-accesspolicy"></a>Locator と AccessPolicy　を削除します。
**HTTP 要求**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP 応答**

成功した場合は、次の内容が返されます。

```console
HTTP/1.1 204 No Content
...
```

**HTTP 要求**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP 応答**

成功した場合は、次の内容が返されます。

```console
HTTP/1.1 204 No Content
...
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードする

Media Services に取り込んだ資産には、メディアのエンコード、再パッケージ化、透かしの追加などをクライアントへの配信前に適用できます。 高いパフォーマンスと可用性を確保するために、これらの作業は、複数のバックグラウンド ロール インスタンスに対してスケジューリングされて実行されます。 これらのアクティビティはジョブと呼ばれ、各ジョブは、資産ファイルの実際の作業を実行するアトミック タスクで構成されます (詳細については、[ジョブ](/rest/api/media/operations/job)と[タスク](/rest/api/media/operations/task)の説明を参照してください)。

冒頭で述べたように、Azure Media Services の代表的な用途の 1 つは、クライアントに対するアダプティブ ビットレート ストリーミング配信です。 Media Services は、一連のアダプティブ ビットレート MP4 ファイルを次のいずれかの形式に動的にパッケージ化できます:HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH。

次のセクションでは、1 つのエンコード タスクを含むジョブを作成する方法を示します。 このタスクは、 **Media Encoder Standard** を使用して、mezzanine ファイルを一連のアダプティブ ビットレート NP4 にトランスコードするよう指定します。 ジョブの処理の進行状況を監視する方法についても示します。 ジョブが完了すると、資産にアクセスするために必要なロケーターを作成できます。

### <a name="get-a-media-processor"></a>メディア プロセッサを取得する
Media Services では、メディア プロセッサは、メディア コンテンツのエンコード、形式変換、暗号化、または復号化などの特定の処理タスクを扱うコンポーネントです。 このチュートリアルで示したエンコード タスクでは、Media Encoder Standard を使用します。

次のコードでは、エンコーダーの Id を要求します。

**HTTP 要求**

```console
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP 応答**

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 273
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 07:54:09 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
   "value":[  
      {  
         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "Description":"Media Encoder Standard",
         "Name":"Media Encoder Standard",
         "Sku":"",
         "Vendor":"Microsoft",
         "Version":"1.1"
      }
   ]
}
```

### <a name="create-a-job"></a>ジョブの作成
各ジョブは実行する処理の種類に応じて 1 つまたは複数のタスクを持つことができます。 REST API を使って、2 つの方法のいずれかでジョブおよびジョブに関連するタスクを作成できます。タスクは、Job エンティティの Tasks ナビゲーション プロパティまたは OData バッチ処理を使用して、インラインで定義できます。 Media Services SDK は、バッチ処理を使用します。 ただし、この記事のコード例では、読みやすくするためにタスクをインラインで定義します。 バッチ処理の詳細については、 [Open Data Protocol (OData) のバッチ処理](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)に関するページを参照してください。

次の例では、1 つのタスクが設定されたジョブを作成して公開し、特定の解像度と質でビデオをエンコードする方法について説明します。 次のドキュメント セクションには、Media Encoder Standard プロセッサがサポートしている [タスク プリセット](./media-services-mes-presets-overview.md) の一覧が含まれています。  

**HTTP 要求**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 482

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"Adaptive Streaming",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
            <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      }
   ]
}
```

**HTTP 応答**

成功した場合、次の応答が返されます。

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1215
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
Server: Microsoft-IIS/8.5
request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 08:04:35 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
      },
      "Tasks":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
         }
      },
      "OutputMediaAssets":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
         }
      },
      "InputMediaAssets":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
         }
      },
      "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
      "Name":"NewTestJob",
      "Created":"2015-01-19T08:04:34.3287057Z",
      "LastModified":"2015-01-19T08:04:34.3287057Z",
      "EndTime":null,
      "Priority":0,
      "RunningDuration":0,
      "StartTime":null,
      "State":0,
      "TemplateId":null,
      "JobNotificationSubscriptions":{  
         "__metadata":{  
            "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
         },
         "results":[  

         ]
      }
   }
}
```

ジョブ要求について注意が必要ないくつかの重要事項があります。

* TaskBody プロパティでは、リテラル XML を使用して、タスクが使用する資産の入力または出力数を定義する必要があります。 タスクの記事には、XML のための XML スキーマ定義が含まれます。
* TaskBody の定義には各 `<inputAsset>` の内部値と `<outputAsset>` を JobInputAsset(value) または JobOutputAsset(value) として設定する必要があります。
* タスクは、複数の出力資産を持つことができます。 1 つの JobOutputAsset(x) はジョブ内のタスクの出力として一度だけ使用できます。
* タスクの入力資産として、JobInputAsset または JobOutputAsset を指定できます。
* タスクは、サイクルを形成することはできません。
* JobInputAsset または JobOutputAsset に渡す値パラメーターは、Asset のインデックス値を表します。 実際の Asset は、Job エンティティ定義の InputMediaAssets および OutputMediaAssets ナビゲーション プロパティで定義されます。

> [!NOTE]
> Media Services は OData v3 上に構築されるため、 InputMediaAssets および OutputMediaAssets ナビゲーション プロパティ コレクション内の個々の資産は、"__metadata : uri" の名前と値のペアによって参照されます。
>
>

* InputMediaAssets は、Media Services で作成した1 つまたは複数の資産にマップされます。 OutputMediaAssets はシステムによって作成されます。 既存の資産は参照しません。
* OutputMediaAssets は、assetName 属性を使用して名前を付けることができます。 この属性が存在しない場合、OutputMediaAsset の名前は、ジョブ名の値またはジョブ ID の値 (Name プロパティが定義されていない場合) のいずれかのサフィックスを持つ `<outputAsset>` 要素の内部テキストの値になります。 たとえば、"Sample"に assetName の値を設定する場合は、OutputMediaAsset Name プロパティは "Sample" に指定されます。 ただし、assetName の値を設定せずジョブ名を "NewJob" に設定した場合は、OutputMediaAsset Name は "JobOutputAsset(value)_NewJob" になります。

    次の例では、assetName 属性を設定する方法を示します。

    ```console
    "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
    ```
* タスクのチェーンを有効にするには、

  * ジョブに少なくとも 2 つのタスクがある必要があります。
  * タスクの入力がジョブ内の別のタスクの出力である、1 つ以上のタスクがある必要があります。

詳細については、「 [Media Services REST API を使用したエンコード ジョブの作成](media-services-rest-encode-asset.md)」をご覧ください。

### <a name="monitor-processing-progress"></a>処理の進行状況を監視する
次の例に示すように、State プロパティを使用して、ジョブの状態を取得できます。

**HTTP 要求**

```console
GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 0
```

**HTTP 応答**

成功した場合、次の応答が返されます。

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 17
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/7.5
x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Sun, 13 May 2012 05:16:53 GMT

{"d":{"State":2}}
```

### <a name="cancel-a-job"></a>ジョブを取り消す
Media Services では、CancelJob 関数を使用して実行中のジョブを取り消すことができます。 この呼び出しは、ジョブの状態が取り消されている、取り消し中、エラー、または終了しているときにジョブをキャンセルしようとすると 400 エラー コードを返します。

次の例は、CancelJob を呼び出す方法を示します。

**HTTP 要求**

```console
GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
```

成功した場合、メッセージ本文なしで応答コード 204 が返されます。

> [!NOTE]
> ジョブ id (通常は nb:jid:UUID: somevalue) は、CancelJob へのパラメーターとして渡すときは URL エンコードする必要があります。
>
>

### <a name="get-the-output-asset"></a>出力資産を取得する
次のコードは、出力資産 ID を要求する方法を示しています。

**HTTP 要求**

```console
GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP 応答**

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 445
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 08:28:13 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
   "value":[  
      {  
         "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
         "State":0,
         "Created":"2015-01-19T07:52:15.603",
         "LastModified":"2015-01-19T07:52:15.603",
         "AlternateId":null,
         "Name":"Multibitrate MP4s",
         "Options":0,
         "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
         "StorageAccountName":"storagetestaccount001"
      }
   ]
}
```

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>REST API でアセットを発行してストリーミング URL とプログレッシブ ダウンロード URL を取得する

アセットをストリーミングまたはダウンロードするにはまず、ロケーターを作成してアセットを「発行」する必要があります。 アセットに含まれているファイルには、ロケーターを通じてアクセスできます。 Media Services では、2 種類のロケーターがサポートされています。メディアをストリーミングする (MPEG DASH、HLS、Smooth Streaming など) ために使用される OnDemandOrigin ロケーター、およびメディア ファイルをダウンロードするために使用される Access Signature (SAS) ロケーター。 

ロケーターを作成したら、対象ファイルのストリーミングやダウンロードに使用する URL を作成します。

>[!NOTE]
>AMS アカウントの作成時に、**既定** のストリーミング エンドポイントが自分のアカウントに追加され、**停止** 状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが **実行中** 状態である必要があります。

スムーズ ストリーミングのストリーミング URL の形式は次のとおりです。

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest
```

HLS のストリーミング URL の形式は次のとおりです。

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)
```

MPEG DASH のストリーミング URL の形式は次のとおりです。

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)
```

ファイルをダウンロードするための SAS URL の形式は次のとおりです。

```console
{blob container name}/{asset name}/{file name}/{SAS signature}
```

このセクションでは、資産を "発行" するために必要な次のタスクを実行する方法を示します。  

* 読み取りアクセス許可を持つ AccessPolicy を作成する
* コンテンツをダウンロードするための SAS URL を作成する
* ストリーミング コンテンツの配信元 URL を作成する

### <a name="creating-the-accesspolicy-with-read-permission"></a>読み取りアクセス許可を持つ AccessPolicy を作成する
メディア コンテンツをダウンロードおよびストリーミングする前に、まず読み取りアクセス許可を持つ AccessPolicy を定義し、クライアントのために有効にする配信メカニズムの種類を指定する適切な Locator エンティティを作成します。 使用できるプロパティの詳細については、「 [AccessPolicy エンティティ プロパティ](/rest/api/media/operations/accesspolicy#accesspolicy_properties)」をご覧ください。

次の例では、指定された Asset の読み取りアクセス許可のために AccessPolicy を指定する方法を示します。

```console
POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

成功した場合、作成した AccessPolicy エンティティについて記述した 201 サクセス コードが返されます。 これで、配信して Locator エンティティを作成するファイルが含まれている資産 (出力資産など) の Asset Id と AccessPolicy Id を使用できます。

> [!NOTE]
> この基本的なワークフローは (このトピックで既に説明した) 資産の取り込み時にファイルをアップロードするのと同じです。 また、ファイルのアップロードのように、ファイルにすぐにアクセスする必要がある場合は、StartTime 値を現在の時刻より 5 分前に設定します。 この操作は、クライアントと Media Services との間でクロック スキューがある可能性があるために必要です。 StartTime 値は、YYYY-MM-DDTHH:mm:ssZ ("2014-05-23T17:53:50Z" など) の DateTime 形式である必要があります。
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>コンテンツをダウンロードするための SAS URL を作成する
次のコードは、以前に作成されてアップロードされたメディア ファイルをダウンロードするために使用する URL を取得する方法を示します。 AccessPolicy には読み取りアクセス許可が設定され、ロケーター パスは SAS ダウンロード URL を参照します。

```console
POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 182
Expect: 100-continue

{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}
```

成功した場合、次の応答が返されます。

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1150
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
Server: Microsoft-IIS/7.5
x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Mon, 14 May 2012 21:41:32 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
      },
      "AccessPolicy":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
         }
      },
      "Asset":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
         }
      },
      "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
      "ExpirationDateTime":"\/Date(1337049393000)\/",
      "Type":1,
      "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
      "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
      "StartTime":"\/Date(1337031393000)\/"
   }
}
```

返された **Path** プロパティには、SAS URL が含まれています。

> [!NOTE]
> ストレージ暗号化コンテンツをダウンロードする場合は、表示する前に手動で暗号化を解除するか、または処理タスク内の Storage Decryption MediaProcessor を使用して、処理されたファイルを OutputAsset にクリア テキストで出力し、その Asset からダウンロードする必要があります。 処理の詳細については、「Media Services REST API によるエンコード ジョブの作成」をご覧ください。 また、作成した後は SAS URL ロケーターを更新できません。 たとえば、更新された StartTime 値を持つ同じロケーターを再利用することはできません。 これは、SAS URL の作成方法による制限です。 ロケーターの有効期限が切れた後の資産にダウンロードのためにアクセスする場合、新しい StartTime で新しくロケーターを作成する必要があります。
>
>

### <a name="download-files"></a>ファイルのダウンロード
AccessPolicy と Locator を設定したら、Azure Storage REST API を使用してファイルをダウンロードできます。  

> [!NOTE]
> 前のセクションで受信した Locator の **Path** 値にダウンロードするファイルのファイル名を追加する必要があります。 例: `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4`? . . .

Azure ストレージ BLOB の使用の詳細については、 [BLOB サービス REST API](/rest/api/storageservices/blob-service-rest-api)をご覧ください。

以前 (エンコード アダプティブ MP4 セットに) を実行して、エンコード ジョブでの結果としてある複数の MP4 ファイルを段階的にダウンロードできます。 次に例を示します。    

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

### <a name="creating-a-streaming-url-for-streaming-content"></a>ストリーミング コンテンツのストリーミング URL を作成する
次のコードは、ストリーミング URL ロケーターを作成する方法を示します。

```console
POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs
Content-Length: 182
Expect: 100-continue

{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}
```

成功した場合、次の応答が返されます。

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 981
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
Server: Microsoft-IIS/7.5
x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Mon, 14 May 2012 21:41:39 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
      },
      "AccessPolicy":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
         }
      },
      "Asset":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
         }
      },
      "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
      "ExpirationDateTime":"\/Date(1337049395000)\/",
      "Type":2,
      "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
      "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
      "StartTime":"\/Date(1337031395000)\/"
   }
}
```

ストリーミング メディア プレーヤーでスムーズ ストリーミングの配信元 URL をストリームするには、後ろに "/manifest" が続くスムーズ ストリーミング マニフェスト ファイルの名前を Path プロパティに追加する必要があります。

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

HLS をストリーミングするには、"/manifest" の後に (format=m3u8-aapl) を追加します。

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

MPEG DASH をストリーミングするには、"/manifest" の後に (format=mpd-time-csf) を追加します。

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


## <a name="play-your-content"></a><a id="play"></a>コンテンツの再生
ビデオをストリーミングするには、 [Azure Media Services Player](https://aka.ms/azuremediaplayer)を使用します。

プログレッシブ ダウンロードをテストするには、ブラウザー (IE、Chrome、Safari など) に URL を貼り付けます。

## <a name="next-steps-media-services-learning-paths"></a>次のステップ:Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]