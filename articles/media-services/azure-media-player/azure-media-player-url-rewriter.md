---
title: Azure Media Player URL リライター
description: Azure Media Player は、Azure Media Services から受け取った特定の URL を書き換えて、SMOOTH、DASH、HLS v3、HLS v4 のストリーミングに対応します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b037eec13fda0b5ec16a4f2f53ad2a64fb5f8da1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91275559"
---
# <a name="url-rewriter"></a>URL リライター #

Azure Media Player は、Azure Media Services から受け取った特定の URL を書き換えて、SMOOTH、DASH、HLS v3、HLS v4 のストリーミングに既定で対応します。 たとえば、次のようにソースが指定されている場合、Azure Media Player は、前述したすべてのプロトコルの再生を試みます。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

ただし URL リライターの使用を希望しない場合、`disableUrlRewriter` プロパティをパラメーターに追加すれば、URL リライターは使用されません。 つまり、ソースに渡されたすべての情報は、変更されずに直接プレーヤーに渡されます。  次の例では、DASH と SMOOTH Streaming の 2 つのソースをプレーヤーに追加しています。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

or

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

また、必要であれば、Azure Media Player で特定のストリーミング形式に書き換えるよう、`streamingFormats` パラメーターを使用して指定することもできます。 オプションには、`DASH`、`SMOOTH`、`HLSv3`、`HLSv4`、`HLS` があります。 HLS と HLSv3 (および v4) の違いは、HLS 形式では FairPlay コンテンツの再生がサポートされる点です。 v3 と v4 では、FairPlay がサポートされません。 これは、特定のプロトコルに使用できる配信ポリシーがない場合に役立ちます。  対象の資産で DASH プロトコルが有効でない場合の例を次に示します。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

or

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

特定の資産に応じて両者を相互に組み合わせて使用すれば、さまざまな環境に対応できます。

> [!NOTE]
> Widevine の保護情報は、DASH プロトコルでのみ保持されます。

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)