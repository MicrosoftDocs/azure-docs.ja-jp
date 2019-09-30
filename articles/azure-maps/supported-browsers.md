---
title: Web SDK でサポートされているブラウザー - Azure Maps | Microsoft Docs
description: Azure Maps Web SDK でサポートされているブラウザーについて説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: d867e9a1afcb495aee7e8b0e0b3b001104f48a4c
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844812"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK でサポートされているブラウザー

Azure Maps の Web SDK は、[atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)という名前のヘルパー関数を提供します。 この関数は、マップ コントロールのロードとレンダリグをサポートするために必要な WebGL 機能の最小セットが Web ブラウザにあるかどうかを検出します。 この関数の使用方法の例を次に示します。

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>デスクトップ

Azure Maps Web SDK は、次のデスクトップ ブラウザーをサポートしています。

- Microsoft Edge (現在と以前のバージョン)
- Google Chrome (現在と以前のバージョン)
- Mozilla Firefox (現在と以前のバージョン)
- Apple Safari (Mac OS X) (現在と以前のバージョン)

このアーティクルで後述する「[Target legacy browsers](#Target-Legacy-Browsers) (レガシ ブラウザーをターゲット)」も参照してください。

## <a name="mobile"></a>モバイル

Azure Maps Web SDK は、次のモバイル ブラウザーをサポートしています。

- Android
  - Android 6.0 以降の Chrome の現在のバージョン
  - Android 6.0 以降の Chrome WebView
- iOS
  - 現在と以前のメジャー バージョンの iOS の Mobile Safari
  - 現在と以前のメジャー バージョンの iOS の UIWebView と WKWebView
  - 現在のバージョンの iOS 用 Chrome

> [!TIP]
> WebView コントロールを使用してモバイル アプリケーション内にマップを組み込んでいる場合は、Azure Content Delivery Network でホストされた SDK のバージョンを参照する代わりに、[Azure Maps Web SDK の npm パッケージ](https://www.npmjs.com/package/azure-maps-control)を使用することをお勧めします。 このアプローチでは、既に SDK がユーザーのデバイスにあり実行時にダウンロードする必要がないため、読み込み時間が短縮されます。

## <a name="nodejs"></a>Node.js

次の Web SDK モジュールは、Node.js もサポートします。

- サービス モジュール ([ドキュメント](how-to-use-services-module.md) | [npm モジュール](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>レガシ ブラウザーを対象にする

WebGL をサポートしていない、または制限付きでのみサポートしている古いブラウザーを対象とする場合があります。 このような場合は、[リーフレット](https://leafletjs.com/)などのオープンソース マップ コントロールと一緒に Azure Maps サービスを使うことをお勧めします。 次に例を示します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Leaflet</a>」Pen を表示します。
</iframe>


## <a name="next-steps"></a>次の手順

Azure Maps Web SDK の詳細について学習します。

> [!div class="nextstepaction"]
> [マップ コントロール](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [サービス モジュール](how-to-use-services-module.md)
