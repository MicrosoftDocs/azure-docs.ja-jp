---
title: Azure Media Player の保護されたコンテンツ
description: Azure Media Player では現在、AES-128 ビット エンベロープで暗号化されたコンテンツおよび一般的な暗号化コンテンツをサポートしています。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 83f144c06c23f3ab5507e3561be4a12350e20a42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91329688"
---
# <a name="protected-content"></a>保護されたコンテンツ #

Azure Media Player では現在、AES-128 ビット エンベロープで暗号化されたコンテンツおよび一般的な暗号化コンテンツ (PlayReady と Widevine を使用)、または FairPlay 経由で暗号化されたコンテンツをサポートしています。 保護されたコンテンツを正しく再生するには、Azure Media Player に `protectionInfo` を認識させる必要があります。 この情報はソースごとに存在し、`data-setup` を介して `<source>` タグに直接追加できます。  また、ソースを動的に設定する場合は、`protectionInfo` をパラメーターとして直接追加することもできます。

`protectionInfo` は JSON オブジェクトを受け入れ、次のものが含まれています。

- `type`: `AES`、`PlayReady`、`Widevine`、または `FairPlay`
- `certificateUrl`: これは、ホストされている FairPlay 証明書への直接リンクである必要があります

- `authenticationToken`: これは、エンコードされていない認証トークンを追加するためのオプション フィールドです

> [!IMPORTANT]
> **certificateUrl** オブジェクトは、FairPlay DRM にのみ必要です。***
>[!NOTE]
> サポートするブラウザー (OSX 8 以降では Safari) で FairPlay コンテンツをネイティブに再生するための新しい技術である `html5FairPlayHLS` に対応するために、既定の techOrder が変更されました。 再生する FairPlay コンテンツがあり、**かつ** アプリケーションで既定の techOrder をカスタムのものに変更している場合は、お使いの techOrder オブジェクトにこの新しい技術を追加する必要があります。 コンテンツが PlayReady で再生されないように、silverlightSS の前にこれを追加することをお勧めします。

## <a name="code-sample"></a>コード サンプル ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

または (複数の DRM を使用)

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> 保護されたコンテンツを再生できないブラウザーやプラットフォームもあります。 サポート内容の詳細については、「[再生テクノロジ](azure-media-player-playback-technology.md)」のセクションを参照してください。
> [!IMPORTANT]
> プレーヤーに渡されるトークンは、セキュリティで保護されたコンテンツ用であり、認証されたユーザーに対してのみ使用されます。 アプリケーションが SSL またはその他の形式のセキュリティ対策を使用していることを前提としています。 また、エンド ユーザーがトークンを誤用しないと信頼できることが前提となります。そうでない場合は、セキュリティの専門家にご相談ください。

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)