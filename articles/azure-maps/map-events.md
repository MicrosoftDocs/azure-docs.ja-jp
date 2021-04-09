---
title: マップ イベントを処理する | Microsoft Azure Maps
description: ユーザーがマップとやりとりするときに発生するイベントについて学習します。 サポートされているすべてのマップ イベントの一覧を表示します。 Azure Maps Web SDK を使用してイベントを処理する方法を確認します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8121ceb68bdea9332316a9508bf6f4731e05b0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890787"
---
# <a name="interact-with-the-map"></a>マップの操作

この記事では、[マップ イベント クラス](/javascript/api/azure-maps-control/atlas.map#events)の使用方法を示します。 プロパティでは、マップ上とマップの異なるレイヤー上のイベントを強調表示します。 また、HTML マーカーを操作すると、イベントを強調表示することもできます。

## <a name="interact-with-the-map"></a>マップの操作

以下のマップを使用し、右側で強調表示されている対応するマウス イベントを確認します。 **[JS]** タブをクリックすると、JavaScript コードを表示し編集できます。 また、 **[Edit on CodePen]\(CodePen の編集\)** をクリックして、CodePen 上のコードを編集することもできます。

<br/>

<iframe height='600' scrolling='no' title='マップの連動 – マウス イベント' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>ペンを参照してください <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>マップの連動</a> – <a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によるマウス イベント。
</iframe>

## <a name="interact-with-map-layers"></a>マップの連動

次のコードでは、シンボル レイヤーを操作する際に起動されるイベントを強調表示します。 シンボル、バブル、線、および多角形レイヤーではすべて、同じ一連のイベントがサポートされます。 ヒート マップとタイル レイヤーでは、これらのどのイベントもサポートされていません。

<br/>

<iframe height='600' scrolling='no' title='マップの連動 – レイヤー イベント' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/bQRRPE/'>マップの連動 – レイヤー イベント</a>」Pen を表示します。
</iframe>

## <a name="interact-with-html-marker"></a>HTML マーカーの操作

以下のコードでは、JavaScript マップ イベントを HTML マーカーに追加します。 また、HTML マーカーの操作時に起動されるイベントの名前を強調表示します。

<br/>

<iframe height='500' scrolling='no' title='マップの連動 - HTML マーカー イベント' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/VVzKJY/'>マップの連動 - HTML マーカー イベント</a>」Pen を表示します。
</iframe>

次の表には、サポートされているマップ クラス イベントがすべてリストされています。

| Event               | 説明 |
|---------------------|-------------|
| `boxzoomend`        | "ボックス ズーム" 操作が終了したときに発生します。|
| `boxzoomstart`      | "ボックス ズーム" 操作が開始されたときに発生します。|
| `click`             | ポインティング デバイスが、マップ上の同じポイントで押されて解放されたときに発生します。|
| `close`             | ポップアップが手動またはプログラムによって閉じられたときに発生します。|
| `contextmenu`       | マウスの右ボタンがクリックされたときに発生します。|
| `data`              | マップ データのどれかが読み込まれたか変更されたときに発生します。 |
| `dataadded`         | `DataSource` に図形が追加されたときに発生します。|
| `dataremoved`       | `DataSource` から図形が削除されたときに発生します。|
| `datasourceupdated` | `DataSource` オブジェクトが更新されたときに発生します。|
| `dblclick`          | マップ上の同じポイントでポインティング デバイスが 2 回クリックされたときに発生します。|
| `drag`              | マップ、ポップアップ、または HTML マーカーでの "ドラッグしてパン" 操作中に繰り返し発生します。|
| `dragend`           | マップ、ポップアップ、または HTML マーカーで "ドラッグしてパン" 操作が終了したときに発生します。|
| `dragstart`         | マップ、ポップアップ、または HTML マーカーで "ドラッグしてパン" 操作が開始されたときに発生します。|
| `error`             | エラーが発生したときに発生します。|
| `idle`              | <p>マップが "アイドル" 状態になる前にレンダリングされた最後のフレームの後に発生します。<ul><li>カメラの切り替えが進行中ではありません。</li><li>現在要求されているすべてのタイルが読み込まれました。</li><li>すべてのフェード/切り替えアニメーションが完了しました。</li></ul></p>|
| `keydown`           | キーが押されたときに発生します。|
| `keypress`          | 入力可能な文字を生成するキー (ANSI キー) が押されたときに発生します。|
| `keyup`             | キーが解放されたときに発生します。|
| `layeradded`        | レイヤーがマップに追加されたときに発生します。|
| `layerremoved`      | レイヤーがマップから削除されたときに発生します。|
| `load`              | 必要なリソースがすべてダウンロードされ、マップの最初の視覚的に完全なレンダリングが行われた直後に発生します。|
| `mousedown`         | マップ内で、または要素に重なっているときにポインティング デバイスが押されると発生します。|
| `mouseenter`        | ポインティング デバイスが最初にマップまたは要素に重なったときに発生します。 |
| `mouseleave`        | ポインティング デバイスがマップまたは要素の外に移動したときに発生します。 |
| `mousemove`         | ポインティング デバイスがマップまたは要素の中に移動したときに発生します。|
| `mouseout`          | ポイント デバイスがマップのキャンバスまたは要素から離れたときに発生します。|
| `mouseover`         | ポインティング デバイスがマップまたは要素の手前に移動したときに発生します。|
| `mouseup`           | マップ内で、または要素に重なっているときにポインティング デバイスが離されると発生します。|
| `move`              | ユーザーの操作またはメソッドの結果として、ビュー間でのアニメーション化された遷移中に繰り返し発生します。|
| `moveend`           | ユーザーの操作またはメソッドの結果として、マップでビュー間の遷移が完了した直後に発生します。|
| `movestart`         | ユーザーの操作またはメソッドの結果として、マップでビュー間の遷移が開始される直前に発生します。|
| `open`              | ポップアップが手動またはプログラムによって開かれたときに発生します。|
| `pitch`             | ユーザーの操作またはメソッドの結果として、マップのピッチ (傾斜) が変化するたびに発生します。|
| `pitchend`          | ユーザーの操作またはメソッドの結果として、マップのピッチ (傾斜) の変化が終了した直後に発生します。|
| `pitchstart`        | ユーザーの操作またはメソッドの結果として、マップのピッチ (傾斜) の変化が開始されるたびに発生します。|
| `ready`             | マップのプログラムによる操作の準備が整う前に、最小限必要なマップ リソースが読み込まれるときに発生します。|
| `render`            | <p>次の結果として、マップが画面に描画されるたびに発生します。<ul><li>マップの位置、ズーム、ピッチ、またはベアリングに対する変更。</li><li>マップのスタイルの変更。</li><li>`DataSource` ソースに対する変更。</li><li>ベクター タイル、GeoJSON ファイル、グリフ、またはスプライトの読み込み。</li></ul></p>|
| `resize`            | マップのサイズが変更された直後に発生します。|
| `rotate`            | "ドラッグして回転" 操作中に繰り返し発生します。|
| `rotateend`         | "ドラッグして回転" 操作が終了したときに発生します。|
| `rotatestart`       | "ドラッグして回転" 操作が開始されたときに発生します。|
| `shapechanged`      | 図形オブジェクト プロパティが変更されたときに発生します。|
| `sourcedata`        | マップのソースのいずれかが読み込まれたか変更されたときに発生します。これには、ソースに属するタイルが読み込まれたか変更された場合も含まれます。 |
| `sourceadded`       | `DataSource` または `VectorTileSource` がマップに追加されたときに発生します。|
| `sourceremoved`     | `DataSource` または `VectorTileSource` がマップから削除されたときに発生します。|
| `styledata`         | マップのスタイルが読み込まれたか、変更されたときに発生します。|
| `styleimagemissing` | 存在しない画像スプライトからレイヤーが画像を読み込もうとしたときに発生します |
| `tokenacquired`     | Azure Active Directory のアクセス トークンが取得されたときに発生します。|
| `touchcancel`       | マップ内で `touchcancel` イベントが発生したときに発生します。|
| `touchend`          | マップ内で `touchend` イベントが発生したときに発生します。|
| `touchmove`         | マップ内で `touchmove` イベントが発生したときに発生します。|
| `touchstart`        | マップ内で `touchstart` イベントが発生したときに発生します。|
| `wheel`             | マップ内でマウスの wheel イベントが発生したときに発生します。|
| `zoom`              | ユーザーの操作またはメソッドの結果として、ズーム レベル間のアニメーション化された遷移中に繰り返し発生します。|
| `zoomend`           | ユーザーの操作またはメソッドの結果として、マップでズーム レベル間の遷移が完了した直後に発生します。|
| `zoomstart`         | ユーザーの操作またはメソッドの結果として、マップでズーム レベル間の遷移が開始される直前に発生します。|


## <a name="next-steps"></a>次のステップ

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Maps サービス モジュールの使用](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [コード サンプル](/samples/browse/?products=azure-maps)