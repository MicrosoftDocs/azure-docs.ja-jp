---
title: Azure Maps マウス イベントの処理 | Microsoft Docs
description: マップ イベントを使用して対話型 Web SDK マップを作成する方法
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 37a3fc3178fe5caeacedfd355a6065ee189a5890
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976545"
---
# <a name="interact-with-the-map---mouse-events"></a>マップの連動 – マウス イベント

この記事では、[マップ クラス イベント](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)のプロパティを使用して、マップ上とマップの別のレイヤー上のイベントを強調表示する方法を説明します。 マップ クラス イベント プロパティを使用して、HTML マーカーの操作時にイベントを強調表示する方法も説明します。

## <a name="interact-with-the-map"></a>マップの操作

以下のマップを使用し、右側で強調表示されている対応するマウス イベントを確認します。 **[JS]** タブをクリックすると、JavaScript コードを表示し編集できます。 **[Edit on CodePen]** \(CodePen の編集\) ボタンをクリックしても、CodePen のコードを編集できます。

<br/>

<iframe height='600' scrolling='no' title='マップの連動 – マウス イベント' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>ペンを参照してください <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>マップの連動</a> – <a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によるマウス イベント。
</iframe>

## <a name="interact-with-map-layers"></a>マップの連動

以下のコードでは、シンボル レイヤーの操作時に起動されるイベントの名前を強調表示します。 シンボル、バブル、線、および多角形レイヤーではすべて、同じ一連のイベントがサポートされます。 ヒート マップとタイル レイヤーでは、これらのイベントのいずれもサポートされていません。

<br/>

<iframe height='600' scrolling='no' title='マップの連動 – レイヤー イベント' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/bQRRPE/'>マップの連動 – レイヤー イベント</a>」Pen を表示します。
</iframe>

## <a name="interact-with-html-marker"></a>HTML マーカーの操作

以下のコードでは、JavaScript マップ イベントを HTML マーカーに追加します。 また、HTML マーカーの操作時に起動されるイベントの名前を強調表示します。

<br/>

<iframe height='500' scrolling='no' title='マップの連動 - HTML マーカー イベント' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/VVzKJY/'>マップの連動 - HTML マーカー イベント</a>」Pen を表示します。
</iframe>

## <a name="next-steps"></a>次の手順

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Maps サービス モジュールの使用](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-maps)
