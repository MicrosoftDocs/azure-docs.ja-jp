---
title: Azure Media Services で Video.js プレーヤーを使用する方法
description: この記事では、Azure Media Services で HTML ビデオ オブジェクトと JavaScript を使用する方法について説明します
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-javascript
ms.openlocfilehash: 2730c6ce523e618110cd29b13ba2f37115e2cbd0
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267685"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Azure Media Services で Video.js プレーヤーを使用する方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>概要

Video.js は、HTML5 用に構築された Web ビデオプレーヤーです。 ブラウザーでアダプティブ メディア形式 (DASH や HLS など) を再生できます。プラグインや Flash は使用されません。 代わりに、Video.js では、オープン Web 標準の MediaSource Extensions と Encrypted Media Extensions が使用されています。 さらに、デスクトップやモバイル デバイスでのビデオの再生もサポートされています。

公式ドキュメントについては、[https://docs.videojs.com/](https://docs.videojs.com/) をご覧ください。

## <a name="sample-code"></a>サンプル コード
この記事のサンプル コードは、[Azure-Samples/media-services-3rdparty-player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples) で入手できます。

## <a name="implement-the-player"></a>プレーヤーを実装する

1. プレーヤーをホストする `index.html` ファイルを作成します。 次のコード行を追加します (より新しいバージョンがある場合は、置き換えることができます)。

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. 次のコードを使用して、`index.js` ファイルを追加します。

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. `manifestUrl` を、ご自分のアセットのストリーミング ロケーターの HLS または DASH の URL に置き換えます。この URL は、Azure portal のストリーミング ロケーターのページにあります。
    ![ストリーミング ロケーターの URL](media/how-to-shaka-player/streaming-urls.png)

4. `protocolType` を次のオプションに置き換えます。

    - HLS プロトコルの場合は "application/x-mpegURL"
    - DASH プロトコルの場合は "application/dash+xml"

### <a name="set-up-captions"></a>キャプションを設定する

`addRemoteTextTrack` メソッドを実行し、次のように置き換えます。

- `subtitleKind` を、`"captions"`、`"subtitles"`、`"descriptions"`、`"metadata"` のいずれかに  
- `caption` を .vtt ファイル パスに (CORS エラーを回避するには、vtt ファイルを同じホストに配置する必要があります)
- `subtitleLang` を言語の BCP 47 コードに (例: 英語の場合は `"eng"` に、スペイン語の場合は `"es"` に置き換え)
- `subtitleLabel` を希望するキャプションの表示名に

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>トークン認証を設定する

トークンは、要求のヘッダーの認証フィールドで設定する必要があります。 CORS に関する問題を回避するために、このトークンは、URL に `'keydeliver'` を含む要求でのみ設定する必要があります。 この作業は、次のコード行で行う必要があります。

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

次に、上記の関数を `videojs.Hls.xhr.beforeRequest` イベントにアタッチする必要があります。

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>AES-128 暗号化を設定する

Video.js では、AES 128 暗号化がサポートされており、追加の構成は必要ありません。 

> [!NOTE] 
> 現在、暗号化と HLS または DASH の CMAF コンテンツに[問題](https://github.com/videojs/video.js/issues/6717)があり、再生できません。

### <a name="set-up-drm-protection"></a>DRM 保護を設定する

DRM 保護をサポートするには、[videojs-contrib-eme](https://github.com/videojs/videojs-contrib-eme) 公式拡張機能を追加する必要があります。 CDN バージョンも同様に機能します。

1. 上で説明した `index.js` ファイルでは、ビデオのソースを追加する "*前に*" `videoJS.eme();` を追加して、EME 拡張機能を初期化する必要があります。

   ```javascript
    videoJS.eme();
   ```

2. これで、DRM サービスの URL と、対応するライセンスの URL を次のように定義できます。

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>ライセンス URL を取得する

ライセンス URL を取得するには、次の方法があります。

- DRM プロバイダーの構成を参照する
- または、サンプルを使用している場合は、VOD の PowerShell スクリプト *setup-vod.ps1* を実行したときに生成された `output.json` ドキュメント、またはライブ ストリームの *start-live.ps1* スクリプトを参照します。 このファイル内にも KID があります。

#### <a name="using-tokenized-drm"></a>トークン化された DRM を使用する

トークン化された DRM 保護をサポートするには、プレーヤーの `src` プロパティに次の行を追加する必要があります。

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>次のステップ

- [Azure Media Player を使用する](../azure-media-player/azure-media-player-overview.md)  
- [クイック スタート: コンテンツの暗号化](encrypt-content-quickstart.md)
