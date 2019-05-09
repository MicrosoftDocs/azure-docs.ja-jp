---
title: Azure Maps マウス イベントの処理 | Microsoft Docs
description: マップ イベントを使用して対話型の Javascript マップを作成する方法
author: jingjing-z
ms.author: jinzh
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4fce8eae25942d098bb3f3277938bfaa3dafa00b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499195"
---
# <a name="interact-with-the-map---mouse-events"></a>マップの連動 – マウス イベント

この記事では、[マップ クラス](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [イベント](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)のプロパティを使用して、マップ上とマップの別のレイヤー上のイベントを強調表示する方法を説明します。 マップ クラス イベント プロパティを使用して、HTML マーカーの操作時にイベントを強調表示する方法も説明します。

## <a name="interact-with-the-map"></a>マップの操作

<iframe height='600' scrolling='no' title='マップの連動 – マウス イベント' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>ペンを参照してください <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>マップの連動</a> – <a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によるマウス イベント。
</iframe>

上記のマップを使用し、右側で強調表示されている対応するマウス イベントを確認します。 **[JS]** タブをクリックすると、JavaScript コードを表示し編集できます。 **[Edit on CodePen]** \(CodePen の編集\) ボタンをクリックしても、CodePen のコードを編集できます。

## <a name="interact-with-map-layers"></a>マップの連動

<iframe height='600' scrolling='no' title='マップの連動 – レイヤー イベント' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/bQRRPE/'>マップの連動 – レイヤー イベント</a>」Pen を表示します。
</iframe>

上記のコードでは、シンボル レイヤーの操作時に起動されるイベントの名前を強調表示します。 シンボル、バブル、線、および多角形レイヤーではすべて、同じ一連のイベントがサポートされます。 タイル レイヤーでは、いずれのイベントもサポートされません。

## <a name="interact-with-html-marker"></a>HTML マーカーの操作

<iframe height='500' scrolling='no' title='マップの連動 - HTML マーカー イベント' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/VVzKJY/'>マップの連動 - HTML マーカー イベント</a>」Pen を表示します。
</iframe>

上記のコードは、JavaScript マップ イベントを HTML マーカーに追加します。 また、HTML マーカーの操作時に起動されるイベントの名前を強調表示します。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Maps サービス モジュールの使用](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [コード サンプル ページ](https://aka.ms/AzureMapsSamples)
