---
title: Azure Maps のマップ スタイル機能 | Microsoft Docs
description: Azure Maps のスタイル関連の機能について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: eb667c398be0bd51e05a6b65d416d5bce54e4386
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881965"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Azure Maps でマップ スタイルを選択する

[Azure Maps でサポートされているマップ スタイル](./supported-map-styles.md)の多くは、Web SDK で利用できます。 この記事では、スタイル関連の機能を使用して、マップの読み込み時にスタイルを設定する方法、新しいスタイルを設定する方法、スタイル選択コントロールを使用する方法について説明します。

## <a name="set-style-on-map-load"></a>マップの読み込み時にスタイルを設定する

次のコードでは、マップの `style` オプションが初期化時に `grayscale_dark` に設定されます。

<br/>

<iframe height='500' scrolling='no' title='マップの読み込み時にスタイルを設定する' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>マップの読み込み時にスタイルを設定する</a>ペンを表示します。
</iframe>

## <a name="update-the-style"></a>スタイルを更新する

次のコードでは、マップ インスタンスが読み込まれた後、マップの [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 関数を使用して、マップのスタイルが `road` から `satellite` に更新されます。

<br/>

<iframe height='500' scrolling='no' title='スタイルの更新' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>スタイルを更新する</a>ペンを表示します。
</iframe>

## <a name="add-the-style-picker"></a>スタイル選択機能を追加する

次のコードでは、ユーザーが異なるマップ スタイルに簡単に切り替えることができるように、マップに [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) を追加しています。 

<br/>

<iframe height='500' scrolling='no' title='スタイル選択機能の追加' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>スタイル選択機能を追加する</a>ペンを表示します。
</iframe>

> [!TIP]
> 既定では、スタイルの選択ツール コントロールには、Azure Maps の S0 価格レベルを使用しているときに利用できるすべてのスタイルが一覧表示されます。 この一覧に表示されるスタイルの数を減らすには、一覧に表示するスタイルの配列をスタイルの選択ツールの `mapStyle` オプションに渡します。 S1 を使用していて、使用できるすべてのスタイルを表示する場合は、スタイル選択ツールの `mapStyles` オプションを `"all"` に設定します。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

マップにコントロールを追加する場合は、以下を参照してください。

> [!div class="nextstepaction"]
> [マップ コントロールを追加する](map-add-controls.md)

> [!div class="nextstepaction"]
> [ピンを追加する](map-add-pin.md)
