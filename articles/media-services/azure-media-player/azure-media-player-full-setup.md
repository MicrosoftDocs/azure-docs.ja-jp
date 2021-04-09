---
title: Azure Media Player のフル セットアップ
description: Azure Media Player のセットアップ方法について説明します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: e6ec59e3cb8d285652935b42493c820a7379eeb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104956079"
---
# <a name="azure-media-player-full-setup"></a>Azure Media Player のフル セットアップ #

Azure Media Player は簡単にセットアップできます。 Azure Media Services アカウントから直接メディア コンテンツの基本的な再生を行うのにほとんど時間はかかりません。 リリースのサンプル ディレクトリに[サンプル](https://github.com/Azure-Samples/azure-media-player-samples)も用意されています。

## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>手順 1:ページの先頭に JavaScript と CSS ファイルを配置する ##

Azure Media Player では、CDN ホスト バージョンからスクリプトにアクセスできます。 `<head>` の代わりに本体終了タグ `<body>` の前に JavaScript を配置するように推奨されることが多くなりましたが、Azure Media Player には 'HTML5 Shiv' が含まれており、古い IE バージョンでビデオ タグが有効な要素と見なされるようにするには、これを先頭に配置する必要があります。

> [!NOTE]
> [Modernizr](https://modernizr.com/) などの HTML5 shiv を既に使用している場合は、Azure Media Player JavaScript をどこにでも含めることができます。 ただし、Modernizr のご使用のバージョンに、ビデオ用の shiv が含まれていることを確認してください。

### <a name="cdn-version"></a>CDN バージョン ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> `latest` バージョンは、必要に応じて変更されることがあるため、運用環境では使用 **しない** でください。 `latest` を Azure Media Player のバージョンに置き換えます。 たとえば、`latest` を `2.1.1` に置き換えます。 Azure Media Player のバージョンは、[こちら](azure-media-player-changelog.md)から照会できます。

> [!NOTE]
> `1.2.0` のリリース以降、フォールバック テクノロジに場所を含める必要がなくなりました (場所は、azuremediaplayer.min.js ファイルの相対パスから自動的に取得されます)。 フォールバック テクノロジの場所は、上記のスクリプトの後の `<head>` に次のスクリプトを追加することで変更できます。

> [!NOTE]
> Flash プラグインおよび Silverlight プラグインの性質上、swf ファイルと xap ファイルは、機密の情報やデータを含まないドメインでホストする必要があります。Azure CDN ホスト バージョンで、これは自動的に処理されます。

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>手順 2:ページに HTML5 ビデオ タグを追加する ##

Azure Media Player では、HTML5 ビデオ タグを使用してビデオを埋め込むことができます。 Azure Media Player は、そのタグを読み取って、HTML5 ビデオをサポートするブラウザーだけではなくすべてのブラウザーで動作するようにします。 Azure Media Player には、基本的なマークアップ以外にもいくつかの追加要素が必要です。

1. `<video>` の `<data-setup>` 属性は、Azure Media Player に対して、ページの準備ができたときにビデオを自動的にセットアップして、属性の (JSON 形式の) 内容を読み取るように指示します。
1. `id` 属性: 必須で、同じページ上のすべてのビデオに対して一意である必要があります。
1. `class` 属性には、次の 2 つのクラスが含まれます。
    - `azuremediaplayer` は、Azure Media Player UI 機能に必要なスタイルを適用します
    - `amp-default-skin` は、HTML5 コントロールに既定のスキンを適用します
1. `<source>` には、次の 2 つの必須属性が含まれます
    - `src` には、Azure Media Services から追加される * *.ism/manifest* ファイルを含めることができます。Azure Media Player は、DASH、SMOOTH、HLS の URL をプレーヤーに自動的に追加します
    - `type` 属性は必須で、ストリームの MIME の種類です。 *".ism/manifest"* に関連付けられた MIME の種類は、 *"application/vnd.ms-sstr+xml"* です
1. `<source>` の *省略可能な* `<data-setup>` 属性は、Azure Media Player に対して、Azure Media Player からのストリーム固有の配信ポリシーがあるかどうかを伝えます。たとえば、暗号化の種類 (AES または PlayReady、Widevine、または FairPlay) やトークンなどがありますが、この限りではありません。

HTML5 ビデオの場合と同様に、属性、設定、ソース、トラックを含めるか、または除外します。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

既定では、ポスターの興味深い部分が隠れないように、大きな再生ボタンは左上隅に配置されます。 大きな再生ボタンを中央に配置したい場合は、`<video>` 要素に、さらに `amp-big-play-centered` `class` を追加できます。

### <a name="alternative-setup-for-dynamically-loaded-html"></a>動的に読み込まれる HTML の代替セットアップ ###

ビデオ タグが、Web ページまたはアプリケーションで動的に読み込まれ (ajax、appendChild など)、ページが読み込まれるときには存在しない可能性がある場合、data-setup 属性を使用せずに手動でプレーヤーをセットアップする必要があります。 このためには、まず、プレーヤーの初期化時に混乱が生じないように、タグから data-setup 属性を削除します。 次に、Azure Media Player JavaScript が読み込まれ、ビデオ タグが DOM に読み込まれた後しばらくしたら、次の JavaScript を実行します。

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            });
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

`amp` 関数の最初の引数は、ビデオ タグの ID です。 これを自分のものと置き換えます。

2 番目の引数は、オプション オブジェクトです。 これを使用すると、data-setup 属性と同様に、追加のオプションを設定できます。

3 番目の引数は、'ready' コールバックです。 Azure Media Player が初期化されると、この関数が呼び出されます。 ready コールバックでは、'この' オブジェクトは、プレーヤー インスタンスを参照します。

要素 ID を使用する代わりに、要素自体への参照を渡すこともできます。

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)
