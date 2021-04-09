---
title: AMS REST API を使用したストレージ暗号化によるコンテンツの暗号化
description: AMS REST API を使用したストレージ暗号化によるコンテンツの暗号化について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 885390d9246937247107128114e9242aa5e2dc01
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015473"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>ストレージ暗号化によるコンテンツの暗号化

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。   > Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。
>

この記事では、AMS ストレージの暗号化の概要を紹介し、ストレージ暗号化が実行されたコンテンツをアップロードする方法を示します。

* コンテンツ キーを作成します。
* 資産を作成します。 資産を作成するときに、AssetCreationOption を StorageEncryption に設定します。
  
     暗号化された資産が、コンテンツ キーに関連付けられます。
* コンテンツ キーを資産にリンクします。  
* AssetFile エンティティで暗号化関連のパラメーターを設定します。

## <a name="considerations"></a>考慮事項 

ストレージ暗号化資産を配信する場合は、資産の配信ポリシーを構成する必要があります。 資産をストリームするには、ストリーミング サーバーでストレージ暗号化を解除し、指定された配信ポリシーを使用してコンテンツをストリームする必要があります。 詳細については、「 [資産配信ポリシーの構成](media-services-rest-configure-asset-delivery-policy.md)」をご覧ください。

Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「 [Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。 

### <a name="storage-side-encryption"></a>ストレージ側の暗号化

|暗号化オプション|説明|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services のストレージの暗号化|AES-256 暗号化、Media Services によって管理されるキー|サポートされています<sup>(1)</sup>|サポートされていません<sup>(2)</sup>|
|[Storage Service Encryption for Data at Rest](../../storage/common/storage-service-encryption.md)|Azure Storage によって提供されるサーバー側暗号化、Azure またはお客様が管理するキー|サポートされています|サポートされています|
|[ストレージ クライアント側暗号化](../../storage/common/storage-client-side-encryption.md)|Azure Storage によって提供されるクライアント側暗号化、お客様が Key Vault で管理するキー|サポートされていません|サポートされていません|

<sup>1</sup> Media Services は、クリアな、どのような形式でも暗号化されていないコンテンツの処理をサポートしますが、そうすることは推奨されません。

<sup>2</sup> Media Services v3 では、ストレージの暗号化 (AES-256 暗号化) は、Media Services v2 で資産を作成した場合の下位互換性のためにのみサポートされています。 つまり、v3 は、既存のストレージの暗号化済み資産では動作しますが、そのような資産を新規作成することはできません。

## <a name="connect-to-media-services"></a>Media Services への接続

AMS API に接続する方法については、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。 

## <a name="storage-encryption-overview"></a>ストレージ暗号化の概要
AMS の記憶域暗号化は、ファイル全体に **AES-CTR** モードの暗号化を適用します。  AES-CTR モードは、任意の長さのデータを暗号化できるブロック暗号です。埋め込みの必要はありません。 AES アルゴリズムを使用してカウンター ブロックを暗号化し、AES の出力と、暗号化または復号化するデータの排他論理和をとるという演算です。  使用されるカウンター ブロックを構築するには、InitializationVector の値をカウンター値のバイト 0 から 7 にコピーし、カウンター値のバイト 8 から 15 はゼロに設定します。 16 バイトのカウンター ブロックのうち、バイト 8 から 15 (つまり、下位バイト) は単純な符号なし 64 ビット整数として使用されます。それ以降に処理されるデータのブロックごとに 1 ずつ増分され、ネットワーク バイト順は維持されます。 整数が最大値 (0xFFFFFFFFFFFFFFFF) に達すると、増分によってゼロにリセットされます (バイト 8 から 15)。残りの 64 ビットのカウンター (バイト 0 から 7) には影響がありません。   AES-CTR モード暗号化のセキュリティを維持するには、コンテンツ キーごとに指定されたキー識別子の InitializationVector 値をファイルごとに一意にする必要があります。また、ファイルの長さを 2^64 ブロック未満にする必要があります。  この一意の値によって、カウンター値が特定のキーに再利用されないようにすることができます。 CTR モードの詳細については、[こちらの wiki ページ](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) ("InitializationVector" ではなく "Nonce" という用語を使用する wiki 記事) を参照してください。

**ストレージ暗号化** で AES 256 ビット暗号化を使用してクリア コンテンツをローカルに暗号化し、それを Azure Storage にアップロードすると、コンテンツが保存時に暗号化された状態で格納されます。 ストレージの暗号化で保護された資産は、エンコーディングの前に自動的に暗号化が解除され、暗号化されたファイル システムに置かれます。その後、新しい出力資産として再度アップロードする前に必要に応じて再度暗号化されます。 ストレージ暗号化の主な使用事例としては、高品質の入力メディア ファイルをディスクに保存するときに強力な暗号化を使用してセキュリティを保護する場合が挙げられます。

ストレージで暗号化された資産を配信するためには、資産の配信ポリシーを構成して、コンテンツの配信方法を Media Services に指示する必要があります。 資産をストリーミングするには、ストリーミング サーバーでストレージ暗号化を解除し、指定された配信ポリシー (AES、共通暗号化、暗号化なしなど) を使用してコンテンツをストリーミングする必要があります。

## <a name="create-contentkeys-used-for-encryption"></a>暗号化に使用する ContentKey を作成する
暗号化された資産が、ストレージ暗号化キーに関連付けられています。 資産ファイルを作成する前に、暗号化に使用するコンテンツ キーを作成します。 このセクションでは、コンテンツ キーの作成方法について説明します。

暗号化する資産に関連付けるコンテンツ キーを生成する一般的な手順を次に示します。 

1. ストレージ暗号化の場合、32 バイトの AES キーがランダムに生成されます。 
   
    32 バイトの AES キーは資産のコンテンツ キーです。つまり、この資産に関連付けるすべてのファイルは、暗号化の際に同じコンテンツ キーを使う必要があります。 
2. [GetProtectionKeyId](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) メソッドと [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) メソッドをコールして、コンテンツ キーを暗号化するために必要な適切な X.509 証明書を取得します。
3. X.509 証明書の公開キーでコンテンツ キーを暗号化します。 
   
   Media Services .NET SDK では、暗号化の際に OAEP と RSA を使用します。  具体的な .NET の例については、「 [EncryptSymmetricKeyData function](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)」 (EncryptSymmetricKeyData 関数) を参照してください。
4. キー識別子とコンテンツ キーを使用して計算されたチェックサム値を作成します。 次の .NET の例では、キー識別子の GUID 部とクリアなコンテンツ キーを使用して checksum を計算しています。

    ```csharp
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
    {
        const int ChecksumLength = 8;
        const int KeyIdLength = 16;

        byte[] encryptedKeyId = null;

        // Checksum is computed by AES-ECB encrypting the KID
        // with the content key.
        using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
        {
            rijndael.Mode = CipherMode.ECB;
            rijndael.Key = contentKey;
            rijndael.Padding = PaddingMode.None;

            ICryptoTransform encryptor = rijndael.CreateEncryptor();
            encryptedKeyId = new byte[KeyIdLength];
            encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
        }

        byte[] retVal = new byte[ChecksumLength];
        Array.Copy(encryptedKeyId, retVal, ChecksumLength);

        return Convert.ToBase64String(retVal);
    }
    ```

5. 前の手順で取得した **EncryptedContentKey** 値 (Base 64 エンコード形式の文字列に変換されます)、**ProtectionKeyId** 値、**ProtectionKeyType** 値、**ContentKeyType** 値、**Checksum** 値を使ってコンテンツ キーを作成します。

    ストレージ暗号化では、次のプロパティを要求本文に含める必要があります。

    要求本文のプロパティ    | 説明
    ---|---
    Id | "nb:kid:UUID:\<NEW GUID>" 形式を使用して生成される ContentKey ID です。
    ContentKeyType | コンテンツ キーの種類は、キーを定義する整数です。 ストレージ暗号化形式の場合、値は 1 です。
    EncryptedContentKey | 256 ビット (32 バイト) の値の新しいコンテンツ キー値を作成します。 このキーは、GetProtectionKeyId および GetProtectionKey メソッド用に HTTP GET 要求を実行して Microsoft Azure Media Services から取得する、ストレージ暗号化 X.509 証明書を使用して暗号化します。 たとえば、次の .NET コードをご覧ください。**EncryptSymmetricKeyData** メソッドの定義は [こちら](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)にあります。
    ProtectionKeyId | コンテンツ キーの暗号化に使用したストレージ暗号化 X.509 証明書の保護キー ID です。
    ProtectionKeyType | コンテンツ キーの暗号化に使用した保護キーの暗号化の種類です。 例では、この値には StorageEncryption(1) を使用しています。
    Checksum |コンテンツ キー用に MD5 で計算されたチェックサムです。 コンテンツ ID をコンテンツ キーで暗号化してコンピューティングします。 コード例では、チェックサムの計算方法を示しています。


### <a name="retrieve-the-protectionkeyid"></a>ProtectionKeyId の取得
次の例では、コンテンツ キーを暗号化するときに必要な ProtectionKeyId と証明書の拇印を取得する方法を示します。 この手順を実行してコンピューターに適切な証明書があることを確認します。

要求:

```console
GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
```

応答:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 139
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>ProtectionKeyId の ProtectionKey の取得
次の例では、前の手順で受け取った ProtectionKeyId を使用して、X.509 証明書を取得する方法を示します。

要求:

```console
GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
Host: media.windows.net
```

応答:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 1227
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Thu, 05 Feb 2015 07:52:30 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
"value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

### <a name="create-the-content-key"></a>コンテンツ キーを作成する
X.509 証明書を取得して、その公開キーを使ってコンテンツ キーを暗号化した後、 **ContentKey** エンティティを作成してプロパティの値を適宜設定します。

コンテンツ キー作成時に設定する必要がある値の一つは type です。 ストレージ暗号化を使用する場合、値は "1" に設定する必要があります。 

次の例では、**ContentKeyType** をストレージの暗号化 ("1") に設定し、保護キー ID が X.509 証明書の拇印であることを示すために **ProtectionKeyType** を "0" に設定して、**ContentKey** を作成する方法を示します。  

Request

```console
POST https://media.windows.net/api/ContentKeys HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
{
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
"ContentKeyType":"1", 
"ProtectionKeyType":"0",
"EncryptedContentKey":"your encrypted content key",
"Checksum":"calculated checksum"
}
```

応答:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 777
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
Server: Microsoft-IIS/8.5
request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:37:46 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
"Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
"LastModified":"2015-02-04T02:37:46.9684379Z",
"ContentKeyType":1,
"EncryptedContentKey":"your encrypted content key",
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
"ProtectionKeyType":0,
"Checksum":"calculated checksum"}
```

## <a name="create-an-asset"></a>アセットを作成する
次の例では、資産を作成する方法を示します。

**HTTP 要求**

```console
POST https://media.windows.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"Name":"BigBuckBunny" "Options":1}
```

**HTTP 応答**

成功した場合、次の応答が返されます。

```console
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
   "Options":1,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

## <a name="associate-the-contentkey-with-an-asset"></a>ContentKey をアセットに関連付ける
ContentKey を作成した後、次の例に示すように $links 演算子を使用して ContentKey をアセットに関連付けます。

要求:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}
```

応答:

```console
HTTP/1.1 204 No Content 
```

## <a name="create-an-assetfile"></a>AssetFile を作成する
[AssetFile](/rest/api/media/operations/assetfile) エンティティは、BLOB コンテナーに格納されているビデオまたはオーディオ ファイルを表します。 資産ファイルは、常に資産に関連付けられており、資産には 1 つまたは複数の資産ファイルが含まれている可能性があります。 資産ファイル オブジェクトが blob コンテナー内のデジタル ファイルに関連付けられていないと、Media Services のエンコーダー タスクは失敗します。

**AssetFile** インスタンスと実際のメディア ファイルは、別々の 2 つのオブジェクトです。 AssetFile インスタンスには、メディア ファイルに関するメタデータが含まれており、メディア ファイルには実際のメディア コンテンツが含まれています。

デジタル メディア ファイルを BLOB コンテナーにアップロードした後、 **MERGE** HTTP 要求を使用して、メディア ファイルに関する情報とともに AssetFile を更新します (この記事では説明しません)。 

**HTTP 要求**

```console
POST https://media.windows.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
Content-Length: 164

{  
   "IsEncrypted":"true",
   "EncryptionScheme" : "StorageEncryption", 
   "EncryptionVersion" : "1.0",       
   "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector" : "397304628502661816</d:InitializationVector",
   "Options":0,
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
   "EncryptionVersion": "1.0",
   "EncryptionScheme": "StorageEncryption",
   "IsEncrypted":true,
   "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector":"397304628502661816</d:InitializationVector",
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```
