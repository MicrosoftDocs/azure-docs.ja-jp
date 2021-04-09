---
title: Web SDK でサポートされているブラウザー | Microsoft Azure Maps
description: ブラウザーが Azure Maps Web SDK でサポートされているかどうかを確認する方法について説明します。 サポートされているブラウザーの一覧を表示します。 レガシ ブラウザーでマップ サービスを使用する方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6321b96cb3db570102f138dcfd949d9c32daedbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384471"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK でサポートされているブラウザー

Azure Maps の Web SDK は、[atlas.isSupported](/javascript/api/azure-maps-control/atlas#issupported-boolean-)という名前のヘルパー関数を提供します。 この関数は、マップ コントロールのロードとレンダリグをサポートするために必要な WebGL 機能の最小セットが Web ブラウザにあるかどうかを検出します。 この関数の使用方法の例を次に示します。

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
- Apple Safari (macOS X) (現在と以前のバージョン)

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

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>レガシ ブラウザーを対象にする

WebGL をサポートしていない、または制限付きでのみサポートしている古いブラウザーを対象とする場合があります。 このような場合は、[リーフレット](https://leafletjs.com/)などのオープンソース マップ コントロールと一緒に Azure Maps サービスを使うことをお勧めします。 オープン ソースの [Azure Maps Leaflet プラグイン](https://github.com/azure-samples/azure-maps-leaflet)を使用する例を次に示します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Leaflet</a>」Pen を表示します。
</iframe>

Leaflet で Azure Maps を使用した追加のコード サンプルについては、[こちら](https://azuremapscodesamples.azurewebsites.net/?search=leaflet)を参照してください。

Azure Maps チームによって対応するプラグインが作成されている、いくつかのよく知られているオープン ソースのマップ コントロールは[こちら](open-source-projects.md#third-part-map-control-plugins)に記載されています。

## <a name="next-steps"></a>次の手順

Azure Maps Web SDK の詳細について学習します。

[マップ コントロール](how-to-use-map-control.md)

[サービス モジュール](how-to-use-services-module.md)
