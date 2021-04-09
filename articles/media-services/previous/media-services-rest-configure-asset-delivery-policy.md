---
title: Media Services REST API を使用した資産配信ポリシーの構成 | Microsoft Docs
description: このトピックでは、Media Services REST API を使用して、さまざまな資産配信ポリシーを構成する方法を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 65a7b9479dae1826f478ca5e038ef5feb1caefb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017326"
---
# <a name="configuring-asset-delivery-policies"></a>資産配信ポリシーの構成

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

動的に暗号化した資産を配信する場合、Media Services におけるコンテンツ配信ワークフローの手順の 1 つとして、資産の配信ポリシーを構成します。 資産の配信ポリシーは、資産を配信する方法、つまりどのストリーミング プロトコルで資産を動的パッケージングするか (例 : MPEG DASH、HLS、スムーズ ストリーミング、またはすべて)、資産を動的に暗号化するかどうか、どの暗号化方法を使用するか (エンベロープ暗号化または共通暗号化) を Media Services に示します。

このトピックでは、アセットの配信ポリシーを作成して構成する理由と方法をご説明します。

> [!NOTE]
> AMS アカウントの作成時に、**既定** のストリーミング エンドポイントが自分のアカウントに追加され、**停止** 状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが **実行中** 状態である必要があります。 
>
> また、ダイナミック パッケージと動的暗号化を使用するには、資産に一連のアダプティブ ビットレート MP4、またはアダプティブ ビットレート Smooth Streaming ファイルが含まれている必要があります。

1 つの資産にはさまざまなポリシーを適用できます。 たとえば、スムーズ ストリーミングに PlayReady 暗号化を適用し、MPEG DASH と HLS に AES エンベロープ暗号化を適用できます。 配信ポリシーで定義されていないプロトコル (たとえば、プロトコルとして HLS のみを指定する 1 つのポリシーを追加した場合) は、ストリーミングからブロックされます。 ただし、資産配信ポリシーをまったく定義していない場合は例外となります。 この場合、すべてのプロトコルが平文で許可されます。

ストレージ暗号化資産を配信する場合は、資産の配信ポリシーを構成する必要があります。 資産をストリームするには、ストリーミング サーバーでストレージ暗号化を解除し、指定された配信ポリシーを使用してコンテンツをストリームする必要があります。 たとえば、Advanced Encryption Standard (AES) エンベロープ暗号化キーを使用して暗号化された資産を配信するには、ポリシーの種類を **DynamicEnvelopeEncryption** に設定します。 ストレージ暗号化を解除して資産を平文でストリームするには、ポリシーの種類を **NoDynamicEncryption** に設定します。 これらのポリシーの種類を構成する例を次に示します。

資産の配信ポリシーの構成方法に応じて、動的パッケージングと動的暗号化を実行し、次のストリーミング プロトコル (スムーズ ストリーミング、HLS、MPEG DASH ストリーム) を使用してストリームを行うことができます。

次の一覧に、スムーズ、HLS、DASH でストリームを行うために使用するフォーマットを示します。

スムーズ ストリーミング:

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest

HLS:

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{ストリーミング エンドポイント名-Media Services アカウント名}.streaming.mediaservices.windows.net/{ロケーター ID}/{ファイル名}.ism/Manifest(format=mpd-time-csf)


資産を発行し、ストリーミング URL を構築する手順については、「 [Build a streaming URL (ストリーミング URL の構築)](media-services-deliver-streaming-content.md)」をご覧ください。

## <a name="considerations"></a>考慮事項
* 資産に対して OnDemand (ストリーミング) ロケーターが存在するときは、資産に関連付けられている AssetDeliveryPolicy を削除することはできません。 ポリシーを削除する前に、資産からポリシーを削除することをお勧めします。
* 資産配信ポリシーが設定されていない場合、ストレージ暗号化資産のストリーミング ロケーターは作成できません。  資産がストレージ暗号化資産でない場合はロケーターを作成でき、資産配信ポリシーのない暗号化されていない資産がストリーミングされます。
* 1 つの資産に複数の資産配信ポリシーを関連付けることができますが、特定の AssetDeliveryProtocol を処理する方法は 1 つだけ指定できます。  つまり、AssetDeliveryProtocol.SmoothStreaming プロトコルを指定する 2 つの配信ポリシーをリンクしようとすると、エラーが発生します。これは、クライアントが Smooth Streaming 要求を行ったときにどのポリシーを適用するか、システムがわからないためです。
* 既存のストリーミング ロケーターを持つ資産が存在する場合、その資産への新しいポリシーのリンク、資産からの既存のポリシーのリンク解除、または資産に関連付けられている配信ポリシーの更新は実行できません。  先にストリーミング ロケーターを削除し、ポリシーを調整した後、ストリーミング ロケーターを再作成する必要があります。  ストリーミング ロケーターを再作成するときに同じ locatorId を使用できますが、コンテンツが最初の CDN またはダウンストリーム CDN によってキャッシュされる可能性があるため、クライアントで問題が発生しないことを確認する必要があります。

> [!NOTE]
> 
> Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「 [Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。

## <a name="connect-to-media-services"></a>Media Services への接続

AMS API に接続する方法については、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。 

## <a name="clear-asset-delivery-policy"></a>資産の配信ポリシーを解除する
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>資産の配信ポリシーを作成する
次の HTTP 要求により、動的暗号化の適用なしで、MPEG DASH、HLS、Smooth Streaming のいずれかのプロトコルでストリームを配信することを指定する資産配信ポリシーが作成されます。 

AssetDeliveryPolicy を作成する際に指定できる値については、 [AssetDeliveryPolicy を定義するときに使用される種類](#types) セクションをご覧ください。   

要求:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
Host: media.windows.net

{"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null}
```

応答:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 363
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 06:21:27 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
"Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null,
"Created":"2015-02-08T06:21:27.6908329Z",
"LastModified":"2015-02-08T06:21:27.6908329Z"}
```

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>資産を資産配信ポリシーにリンクする
次の HTTP 要求により、指定した資産が資産配信ポリシーにリンクされます。

要求:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
Host: media.windows.net

{"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}
```

応答:

```output
HTTP/1.1 204 No Content
```


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption 資産の配信ポリシー
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>EnvelopeEncryption タイプのコンテンツ キーを作成して資産にリンクする
DynamicEnvelopeEncryption delivery 配信ポリシーを指定する際に、資産を EnvelopeEncryption タイプのコンテンツ キーにリンクする必要があります。 詳細については、「 [コンテンツ キーの作成](media-services-rest-create-contentkey.md)」を参照してください。

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>配信 URL を取得する
前の手順で作成したコンテンツ キーの指定された配信方法向けの配信 URL を取得します。 クライアントは取得した URL を使用し、保護されたコンテンツを再生するために AES キーまたは PlayReady ライセンスを要求します。

HTTP 要求の本文に含める URL の種類を指定します。 PlayReady を使用してコンテンツを保護する場合は、keyDeliveryType に 1 を指定して({"keyDeliveryType":1})、Media Services PlayReady ライセンス取得 URL を要求します。 エンベロープ暗号化を使用してコンテンツを保護する場合は、keyDeliveryType に 2 を指定して ({"keyDeliveryType":2})、キー取得 URL を要求します。

要求:

```console
POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
Content-Type: application/json
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
Host: media.windows.net
Content-Length: 21

{"keyDeliveryType":2}
```

応答:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 198
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
request-id: d26f65d2-fe65-4136-8fcf-31545be68377
x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 21:42:30 GMT

{"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}
```


### <a name="create-asset-delivery-policy"></a>資産の配信ポリシーを作成する
次の HTTP 要求により、**HLS** プロトコルに動的エンベロープ暗号化 (**DynamicEnvelopeEncryption**) を適用するように構成された **AssetDeliveryPolicy** が作成されます (この例では、他のプロトコルはストリーミングからブロックされます)。 

AssetDeliveryPolicy を作成する際に指定できる値については、 [AssetDeliveryPolicy を定義するときに使用される種類](#types) セクションをご覧ください。   

要求:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}
```


応答:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 460
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 09 Feb 2015 05:24:38 GMT

{"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}
```


### <a name="link-asset-with-asset-delivery-policy"></a>資産を資産配信ポリシーにリンクする
「 [資産を資産配信ポリシーにリンクする](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption 資産の配信ポリシー
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>CommonEncryption タイプのコンテンツ キーを作成して資産にリンクする
DynamicCommonEncryption 配信ポリシーを指定する際に、資産を CommonEncryption タイプのコンテンツ キーにリンクする必要があります。 詳細については、「 [コンテンツ キーの作成](media-services-rest-create-contentkey.md)」を参照してください。

### <a name="get-delivery-url"></a>配信 URL を取得する
前の手順で作成されたコンテンツ キーの PlayReady 配信方法向けの配信 URL を取得します。 クライアントは取得した URL を使用し、保護されたコンテンツを再生するために PlayReady ライセンスを要求します。 詳細については、「 [配信 URL を取得する](#get_delivery_url)」をご覧ください

### <a name="create-asset-delivery-policy"></a>資産の配信ポリシーを作成する
次の HTTP 要求により、**Smooth Streaming** プロトコルに動的共通暗号化 (**DynamicCommonEncryption**) を適用するように構成された **AssetDeliveryPolicy** が作成されます (この例では、他のプロトコルはストリーミングからブロックされます)。 

AssetDeliveryPolicy を作成する際に指定できる値については、 [AssetDeliveryPolicy を定義するときに使用される種類](#types) セクションをご覧ください。   

要求:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}
```


Widevine DRM を使用してコンテンツを保護する場合は、WidevineLicenseAcquisitionUrl (値 7 が設定されています) を使用するように AssetDeliveryConfiguration の値を更新し、ライセンス配信サービスの URL を指定します。 AMS パートナー、[Axinom](https://www.axinom.com)、[EZDRM](https://ezdrm.com/)、[castLabs](https://castlabs.com/company/partners/azure/) を通してWidevine ライセンスを提供できます。

次に例を示します。 

```console
{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}
```

> [!NOTE]
> Widevine を使用して暗号化する場合、配信は DASH でのみ実行できます。 資産配信プロトコルに必ず DASH (2) を指定してください。
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>資産を資産配信ポリシーにリンクする
「 [資産を資産配信ポリシーにリンクする](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>AssetDeliveryPolicy の定義に使用する種類

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

次の enum は、資産配信プロトコルに対して設定できる値を表しています。

```csharp
[Flags]
public enum AssetDeliveryProtocol
{
    /// <summary>
    /// No protocols.
    /// </summary>
    None = 0x0,

    /// <summary>
    /// Smooth streaming protocol.
    /// </summary>
    SmoothStreaming = 0x1,

    /// <summary>
    /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
    /// </summary>
    Dash = 0x2,

    /// <summary>
    /// Apple HTTP Live Streaming protocol.
    /// </summary>
    HLS = 0x4,

    ProgressiveDownload = 0x10, 

    /// <summary>
    /// Include all protocols.
    /// </summary>
    All = 0xFFFF
}
```

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

次の enum は、資産配信ポリシーに対して設定できる値を表しています。  

```csharp
public enum AssetDeliveryPolicyType
{
    /// <summary>
    /// Delivery Policy Type not set.  An invalid value.
    /// </summary>
    None,

    /// <summary>
    /// The Asset should not be delivered via this AssetDeliveryProtocol. 
    /// </summary>
    Blocked, 

    /// <summary>
    /// Do not apply dynamic encryption to the asset.
    /// </summary>
    /// 
    NoDynamicEncryption,  

    /// <summary>
    /// Apply Dynamic Envelope encryption.
    /// </summary>
    DynamicEnvelopeEncryption,

    /// <summary>
    /// Apply Dynamic Common encryption.
    /// </summary>
    DynamicCommonEncryption
    }
```

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

次の enum は、クライアントへのコンテンツ キーの配信方法を構成するときに使用できる値を表しています。
    
```csharp
public enum ContentKeyDeliveryType
{
    /// <summary>
    /// None.
    ///
    </summary>
    None = 0,

    /// <summary>
    /// Use PlayReady License acquisition protocol
    ///
    </summary>
    PlayReadyLicense = 1,

    /// <summary>
    /// Use MPEG Baseline HTTP key protocol.
    ///
    </summary>
    BaselineHttp = 2,

    /// <summary>
    /// Use Widevine License acquisition protocol
    ///
    </summary>
    Widevine = 3

}
```


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

次の enum は、資産配信ポリシーの特定の構成を取得するためのキーを構成するときに設定できる値を表しています。

```csharp
public enum AssetDeliveryPolicyConfigurationKey
{
    /// <summary>
    /// No policies.
    /// </summary>
    None,

    /// <summary>
    /// Exact Envelope key URL.
    /// </summary>
    EnvelopeKeyAcquisitionUrl,

    /// <summary>
    /// Base key url that will have KID=<Guid> appended for Envelope.
    /// </summary>
    EnvelopeBaseKeyAcquisitionUrl,

    /// <summary>
    /// The initialization vector to use for envelope encryption in Base64 format.
    /// </summary>
    EnvelopeEncryptionIVAsBase64,

    /// <summary>
    /// The PlayReady License Acquisition Url to use for common encryption.
    /// </summary>
    PlayReadyLicenseAcquisitionUrl,

    /// <summary>
    /// The PlayReady Custom Attributes to add to the PlayReady Content Header
    /// </summary>
    PlayReadyCustomAttributes,

    /// <summary>
    /// The initialization vector to use for envelope encryption.
    /// </summary>
    EnvelopeEncryptionIV,

    /// <summary>
    /// Widevine DRM acquisition url
    /// </summary>
    WidevineLicenseAcquisitionUrl
}
```

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

