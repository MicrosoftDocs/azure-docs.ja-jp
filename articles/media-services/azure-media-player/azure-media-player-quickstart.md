---
title: Azure Media Player クイックスタート
description: Azure Media Player の基本的なセットアップ手順について説明します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: 0d0fa88fdf182ae2214da40ee3fe8b20ced025e4
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956062"
---
# <a name="azure-media-player-quickstart"></a>Azure Media Player クイックスタート
Azure Media Player は簡単にセットアップできます。 たった数分で、Azure Media Services アカウントからメディア コンテンツの基本的な再生機能が利用できます。 このセクションでは、詳細は説明せず、基本的な手順を示します。 以降のセクションで、Azure Media Player を設定および構成する方法の詳細を説明します。  以下の組み込みをドキュメントの `<head>` に追加するだけです。

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> これは必要に応じて変更されることがあるため、運用環境では `latest` バージョンを使用 **しない** でください。 `latest` を Azure Media Player のバージョンに置き換えます。たとえば、`latest` を `1.0.0` に置き換えます。 Azure Media Player のバージョンは、[こちら](azure-media-player-changelog.md)から照会できます。

## <a name="use-the-video-element"></a>video 要素を使用する

後は、通常どおりに `<video>` 要素を使用するだけです。オプションがあれば、`data-setup` 属性に含めて追加します。 これらのオプションには、有効な JSON オブジェクトで任意の Azure Media Services オプションを指定できます。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

自動セットアップの使用を希望しない場合は、`data-setup` 属性を省略し、video 要素を手動で初期化することができます。

```javascript
    var myPlayer = amp('vid1', { /* Options */
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
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player のフル セットアップ](./azure-media-player-full-setup.md)
