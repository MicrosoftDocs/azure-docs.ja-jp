---
title: Azure Media Services で Shaka プレーヤーを使用する方法
description: この記事では、Azure Media Services で Shaka プレーヤーを使用する方法について説明します
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
ms.openlocfilehash: 5988a3ca2d44df161a27144d7bfb47ecab1e236e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267311"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>Azure Media Services で Shaka プレーヤーを使用する方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>概要

Shaka プレーヤーは、アダプティブ メディア用のオープンソースの JavaScript ライブラリです。 ブラウザーでアダプティブ メディア形式 (DASH や HLS など) を再生できます。プラグインや Flash は使用されません。 代わりに、Shaka プレーヤーでは、オープン Web 標準の Media Source Extensions と Encrypted Media Extensions が使用されています。

[Mux.js](https://github.com/videojs/mux.js/) を使用することをお勧めします。そうしないと、Shaka プレーヤーによって HLS CMAF 形式はサポートされますが、HLS TS はサポートされません。

公式のドキュメントは、[Shaka プレーヤーのドキュメント](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html)に関するページで見つかります。

## <a name="sample-code"></a>サンプル コード
この記事のサンプル コードは、[Azure-Samples/media-services-3rdparty-player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples) で入手できます。

## <a name="implementing-the-player"></a>プレーヤーの実装

プレーヤーの独自のインスタンスを実装する必要がある場合は、次の手順のようにします。

1. プレーヤーをホストする `index.html` ファイルを作成します。 次のコード行を追加します (より新しいバージョンがある場合は、置き換えることができます)。

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. 次のコードを使用して、JavaScript ファイルを追加します。

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. `manifestUrl` を、ご自分のアセットのストリーミング ロケーターの HLS または DASH の URL に置き換えます。この URL は、Azure portal のストリーミング ロケーターのページにあります。
    ![ストリーミング ロケーターの URL](media/how-to-shaka-player/streaming-urls.png)

1. サーバーを実行すると (たとえば、`npm http-server` を使用して)、プレーヤーが動作するようになります。

## <a name="set-up-captions"></a>キャプションを設定する

### <a name="set-up-vod-captions"></a>VOD キャプションを設定する

次のコード行を実行します。`captionUrl` は実際の .vtt ディレクトリに置き換え (CORS エラーを回避するため、vtt ファイルは同じホストに置く必要があります)、`lang` は 2 文字の言語コードに置き換え、`type` は `caption` または `subtitle` に置き換えます。

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>ライブ ストリーム キャプションを設定する

ライブ ストリームでのキャプションを有効にするには、次のコード行を追加します。

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>トークン認証を設定する

次のコード行を実行します。`token` はトークン文字列に置き換えます。

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>AES-128 暗号化を設定する

現在、Shaka プレーヤーで AES-128 暗号化はサポートされていません。

この機能の状態をフォローするための、GitHub [イシュー](https://github.com/google/shaka-player/issues/850)へのリンク。

## <a name="set-up-drm-protection"></a>DRM 保護を設定する

Shaka プレーヤーでは Encrypted Media Extensions (EME) が使用されており、これにはセキュリティ保護された URL を使用する必要があります。 そのため、DRM で保護されたコンテンツをテストするには、https を使用する必要があります。 サイトで https が使用されている場合は、マニフェストとすべてのセグメントでも https を使用する必要があります。 これは、コンテンツの要件が混在しているためです。

Shaka によるライセンス サーバーの URL の管理の優先順位:

1. デバッグに使用される ClearKey config を、他のすべてより優先する必要があります。 (アプリケーションでは、ClearKey ライセンス サーバーを指定することもできます)。
2. アプリケーションによって構成されたサーバー (存在する場合) は、マニフェストからのすべてのものより優先する必要があります。
3. マニフェストで提供されたライセンス サーバーは、他のものが何も指定されていない場合にのみ使用されます。

Widevine または PlayReady に対してライセンス サーバーの URL を指定するには、次のコードを使用します。

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

すべての FairPlay コンテンツでは、サーバー証明書を設定する必要があります。 それは、プレーヤーの構成で設定されています。

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

詳細については、[Shaka プレーヤーの DRM 保護に関するドキュメント](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Media Player を使用する](../azure-media-player/azure-media-player-overview.md)
* [クイック スタート: コンテンツの暗号化](encrypt-content-quickstart.md)
