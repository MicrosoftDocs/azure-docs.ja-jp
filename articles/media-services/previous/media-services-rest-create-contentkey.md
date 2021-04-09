---
title: REST でコンテンツ キーを作成する | Microsoft Docs
description: この記事では、アセットに安全にアクセスできるコンテンツ キーを作成する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 95e9322b-168e-4a9d-8d5d-d7c946103745
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a9960ece05b7cab4ddd26fdac7516f4adbccf5b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017258"
---
# <a name="create-content-keys-with-rest"></a>REST でコンテンツ キーを作成する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services では、暗号化されたアセットを送信できます。 **ContentKey** により、**アセット** に安全にアクセスすることができます。 

新しいアセットを作成するときは ([ファイルをアップロード](media-services-rest-upload-files.md)する前など)、次の暗号化オプションを指定できます:**StorageEncrypted**、**CommonEncryptionProtected**、または **EnvelopeEncryptionProtected**。 

クライアントにアセットを配信するときは、次の 2 つの暗号化のどちらかを使用して [アセットが動的に暗号化されるように構成できます](media-services-rest-configure-asset-delivery-policy.md):**DynamicEnvelopeEncryption** または **DynamicCommonEncryption**。

暗号化されたアセットには、 **ContentKey** を関連付ける必要があります。 この記事では、コンテンツ キーの作成方法について説明します。

暗号化する資産に関連付けるコンテンツ キーを生成する一般的な手順を次に示します。 

1. 16 バイトの AES キー (CommonEncryption と EnvelopeEncryption 向け) か 32 バイトの AES キー (StorageEncryption 向け) をランダムに生成します。 
   
    これが資産のコンテンツ キーになります。つまり、この資産に関連付けるすべてのファイルは、暗号化の際に同じコンテンツ キーを使う必要があります。 
2. [GetProtectionKeyId](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) メソッドと [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) メソッドをコールして、コンテンツ キーを暗号化するために必要な適切な X.509 証明書を取得します。
3. X.509 証明書の公開キーでコンテンツ キーを暗号化します。 
   
   Media Services .NET SDK では、暗号化の際に OAEP と RSA を使用します。  具体例については、「 [EncryptSymmetricKeyData function](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)」 (EncryptSymmetricKeyData 関数) を参照してください。
4. そのキー識別子とコンテンツ キーを使って計算される、(PlayReady AES キー checksum アルゴリズムに基づく) checksum 値を作成します。 詳細については、 [ここ](https://www.microsoft.com/playready/documents/)にある、『PlayReady Header Object』(PlayReady のヘッダー オブジェクト) ドキュメントの「PlayReady AES Key Checksum Algorithm」(PlayReady AES キーの checksum アルゴリズム) セクションを参照してください。
   
    次の .NET の例では、キー識別子の GUID 部とクリアなコンテンツ キーを使用して checksum を計算しています。

    ```console
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
     {
       byte[] array = null;
         using (AesCryptoServiceProvider aesCryptoServiceProvider = new AesCryptoServiceProvider())
         {
             aesCryptoServiceProvider.Mode = CipherMode.ECB;
             aesCryptoServiceProvider.Key = contentKey;
             aesCryptoServiceProvider.Padding = PaddingMode.None;
             ICryptoTransform cryptoTransform = aesCryptoServiceProvider.CreateEncryptor();
             array = new byte[16];
             cryptoTransform.TransformBlock(keyId.ToByteArray(), 0, 16, array, 0);
         }
         byte[] array2 = new byte[8];
         Array.Copy(array, array2, 8);
         return Convert.ToBase64String(array2);
     }
    ```

5. 前の手順で取得した **EncryptedContentKey** 値 (Base 64 エンコード形式の文字列に変換されます)、**ProtectionKeyId** 値、**ProtectionKeyType** 値、**ContentKeyType** 値、**Checksum** 値を使ってコンテンツ キーを作成します。
6. $links 操作で、**ContentKey** エンティティと **Asset** エンティティを関連付けます。

この記事では、AES キーの生成、キーの暗号化、および checksum の計算方法については説明しません。 

> [!NOTE]
> 
> Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「 [Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。

## <a name="connect-to-media-services"></a>Media Services への接続

AMS API に接続する方法については、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。 

## <a name="retrieve-the-protectionkeyid"></a>ProtectionKeyId の取得
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
charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

## <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>ProtectionKeyId の ProtectionKey の取得
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

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String", "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

## <a name="create-the-contentkey"></a>ContentKey を作成します。
X.509 証明書を取得して、その公開キーを使ってコンテンツ キーを暗号化した後、 **ContentKey** エンティティを作成してプロパティの値を適宜設定します。

コンテンツ キー作成時に設定する必要がある値の一つは type です。 次のいずれかの値を選択します。

```console
public enum ContentKeyType
{
    /// <summary>
    /// Specifies a content key for common encryption.
    /// </summary>
    /// <remarks>This is the default value.</remarks>
    CommonEncryption = 0,

    /// <summary>
    /// Specifies a content key for storage encryption.
    /// </summary>
    StorageEncryption = 1,

    /// <summary>
    /// Specifies a content key for configuration encryption.
    /// </summary>
    ConfigurationEncryption = 2,

    /// <summary>
    /// Specifies a content key for Envelope encryption.  Only used internally.
    /// </summary>
    EnvelopeEncryption = 4
    }
```

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

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
