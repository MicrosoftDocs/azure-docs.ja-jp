---
title: Azure Maps のマップ スタイル機能 | Microsoft Docs
description: Azure Maps のスタイル関連の機能について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ffed12b9184c7b6a690c30db9826f031fe6c9f9b
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259790"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Azure Maps でマップ スタイルを選択する

Azure Maps には、選択できるマップ スタイルが 4 種類あります。 マップ スタイルの詳細については、[Azure Maps でサポートされているマップ スタイル](./supported-map-styles.md)に関するページを参照してください。 この記事では、スタイル関連の機能を使用して、マップの読み込み時にスタイルを設定する方法、新しいスタイルを設定する方法、スタイル選択コントロールを使用する方法について説明します。

## <a name="set-style-on-map-load"></a>マップの読み込み時にスタイルを設定する

<iframe height='500' scrolling='no' title='マップの読み込み時にスタイルを設定する' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>マップの読み込み時にスタイルを設定する</a>ペンを表示します。
</iframe>

上記のコード ブロックでは、サブスクリプション キーが設定され、スタイルが grayscale_dark に設定されたマップ オブジェクトが作成されます。 マップの作成方法については、「[マップを作成する](./map-create.md)」を参照してください。

## <a name="update-the-style"></a>スタイルを更新する

<iframe height='500' scrolling='no' title='スタイルの更新' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>スタイルを更新する</a>ペンを表示します。
</iframe>

上記のコード ブロックでは、サブスクリプション キーが設定され、スタイルが事前に設定されていないマップ オブジェクトが作成されます。 マップの作成方法については、「[マップを作成する](./map-create.md)」を参照してください。

2 つ目のコード ブロックでは、マップの [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) メソッドが使用され、マップ スタイルが satellite に設定されます。

## <a name="add-the-style-picker"></a>スタイル選択機能を追加する

<iframe height='500' scrolling='no' title='スタイル選択機能の追加' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>スタイル選択機能を追加する</a>ペンを表示します。
</iframe>

最初のコード ブロックでは、サブスクリプション キーが設定され、スタイルが grayscale_dark に事前に設定されたマップ オブジェクトが作成されます。 マップの作成方法については、「[マップを作成する](./map-create.md)」を参照してください。

2 つ目のコード ブロックでは、atlas の [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) コンストラクターを使用してスタイル セレクターが構築されます。

スタイル選択機能を使用すると、マップのスタイルを選択できます。 3 つ目のコード ブロックでは、マップの [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) メソッドを使用してスタイル選択機能がマップに追加されます。 マップが完全に読み込まれた後に読み込まれるようにするために、スタイル セレクターはマップ **イベント リスナー**内にあります。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

マップにコントロールを追加する:

> [!div class="nextstepaction"]
> [マップ コントロールを追加する](./map-add-controls.md)

マップ ピンを追加する:

> [!div class="nextstepaction"]
> [ピンを追加する](./map-add-pin.md)
