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
ms.openlocfilehash: bc876fbf0eb15f887d57d4ddcca2301ef7233afa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577341"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK でサポートされているブラウザー

Azure Maps Web SDK は、Web ブラウザーがマップ コントロールの読み込みと表示をサポートするために最低限必要な WebGL 機能が搭載されているかを検出するために、ヘルパー関数 [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) を提供しています。 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>デスクトップ

Azure Maps Web SDK は、次のデスクトップ ブラウザーをサポートしています。

- Microsoft Edge の現在と以前のバージョン 
- Chrome の現在と以前のバージョン 
- Firefox の現在と以前のバージョン 
- Safari (Mac OS X) の現在と以前のバージョン 

「[レガシ ブラウザーを対象にする](#Target-Legacy-Browsers)」も参照してください。

## <a name="mobile"></a>モバイル

Azure Maps Web SDK は、次のモバイル ブラウザーをサポートしています。

-  Android
    * Android 6.0 以降の Chrome の現在のバージョン
    * Android 6.0 以降の Chrome WebView
- iOS
    * 現在と以前のメジャー バージョンの iOS の Mobile Safari
    * 現在と以前のメジャー バージョンの iOS の UIWebView と WKWebView
    * 現在のバージョンの iOS 用 Chrome

> [!TIP]
> WebView コントロールを使用してモバイル アプリケーション内にマップを組み込んでいる場合は、Azure Maps Web SDK の CDN でホストされたバージョンを参照する代わりに、[この SDK の npm パッケージ](https://www.npmjs.com/package/azure-maps-control)を使用することをお勧めします。 SDK が既にユーザーのデバイスに存在し、実行時にダウンロードする必要がないので、読み込み時間が減少します。

## <a name="nodejs"></a>Node.js

次の Web SDK モジュールは、Node.js もサポートします。

- サービス モジュール ([ドキュメント](how-to-use-services-module.md) | [npm モジュール](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>レガシ ブラウザーを対象にする

WebGL がサポートされていないか、またはサポートに制限がある可能性のある古いブラウザーを対象にする必要がある場合は、Azure Maps サービスを [leaflet](https://leafletjs.com/) などのオープン ソースのマップ コントロールと組み合わせて使用することをお勧めします。 


<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Leaflet</a>」Pen を表示します。
</iframe>


## <a name="next-steps"></a>次の手順

Azure Maps Web SDK の詳細について学習します。

> [!div class="nextstepaction"]
> [マップ コントロール](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [サービス モジュール](how-to-use-services-module.md)
