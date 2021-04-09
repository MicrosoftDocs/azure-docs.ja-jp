---
title: Azure Maps を使用してマップを作成する | Microsoft Azure Maps
description: Azure Maps Web SDK を使用して Web ページにマップを追加する方法について説明します。 アニメーション、スタイル、カメラ、サービス、ユーザー操作のオプションについて説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 833b6413cc5dfde1129075a286e5fe93a06e159f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890923"
---
# <a name="create-a-map"></a>マップを作成する

この記事では、マップを作成し、マップをアニメーション化する方法を示します。  

## <a name="loading-a-map"></a>マップを読み込む

マップを読み込むには、[Map クラス](/javascript/api/azure-maps-control/atlas.map)の新しいインスタンスを作成します。 マップを初期化するときに、マップをレンダリングするための DIV 要素 ID を渡し、マップの読み込み時に使用するオプションのセットを渡します。 `atlas` 名前空間で既定の認証情報が指定されていない場合は、マップの読み込み時にマップ オプションでこの情報を指定する必要があります。 マップでは、パフォーマンス向上のために複数のリソースが非同期的に読み込まれます。 そのため、マップ インスタンス作成後に、`ready` または `load` イベントをマップにアタッチし、マップと対話する追加のコードをイベント ハンドラーに追加します。 `ready` イベントは、プログラムで対話するのに十分なリソースがマップに読み込まれるとすぐに発生します。 `load` イベントは、初期マップ ビューの読み込みが完全に完了した後で発生します。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本的なマップの読み込み" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Basic map load</a>」Pen を表示します。
</iframe>

> [!TIP]
> 同じページに複数のマップを読み込むことができます。 同じページにある複数のマップでは、認証と言語の設定は同じでも異なっていてもかまいません。

## <a name="show-a-single-copy-of-the-world"></a>世界の 1 つのコピーを表示する

ワイド画面でマップをズームアウトすると、世界の複数のコピーが水平方向に表示されます。 このオプションは一部のシナリオには適していますが、他のアプリケーションでは、世界の 1 つのコピーを表示する方が適しています。 この動作は、マップの `renderWorldCopies` オプションを `false` に設定することで実装されます。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a>」Pen を表示します。
</iframe>


## <a name="map-options"></a>マップ オプション

マップを作成するときに、マップの機能をカスタマイズするために渡すことができる、以下のような各種オプションがあります。

- [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) と [CameraBoundOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions) は、マップに表示する領域を指定するために使用します。
- [ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions) は、マップを機能強化するサービスとマップとの対話方法を指定するために使用します。
- [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions) は、マップのスタイル設定とレンダリング方法を指定するために使用します。
- [UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) は、ユーザーによるマップの操作時にマップがどのようになるかを指定するために使用します。 

これらのオプションは、`setCamera`、`setServiceOptions`、`setStyle`、および `setUserInteraction` の各関数を使用してマップを読み込んだ後に更新することもできます。 

## <a name="controlling-the-map-camera"></a>マップ カメラの制御

マップのカメラを使用してマップの表示領域を設定する方法が 2 つあります。 マップの読み込み時にカメラのオプションを設定できます。 または、マップが読み込まれた後の任意の時点で `setCamera` オプションを呼び出して、プログラムによってマップ ビューを更新できます。  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>カメラの設定

マップ カメラは、マップ キャンバスのビューポートに表示される内容を制御します。 カメラ オプションは、初期化時にマップ オプションに渡すか、マップの `setCamera` 関数に渡すことができます。

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

次のコードでは、[Map オブジェクト](/javascript/api/azure-maps-control/atlas.map)が作成され、中心とズームのオプションが設定されます。 中心やズーム レベルなどのマップのプロパティは、[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) の一部です。

<br/>

<iframe height='500' scrolling='no' title='CameraOptions を介してマップを作成する' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって、Pen の <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via `CameraOptions` </a> を表示します。
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>カメラ境界の設定

境界ボックスを使用して、マップ カメラを更新できます。 境界ボックスがポイント データから計算された場合、アイコンのサイズを考慮するために、カメラ オプションでピクセル パディング値も指定すると役立つ場合がよくあります。 これにより、ポイントがマップ ビューポートに収まらなくなるのを防ぐことができます。

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

次のコードでは、`new atlas.Map()` によって [Map オブジェクト](/javascript/api/azure-maps-control/atlas.map)が構築されます。 `CameraBoundsOptions` などの Map プロパティは、Map クラスの [setCamera](/javascript/api/azure-maps-control/atlas.map) 関数を介して定義できます。 境界とパディングのプロパティは、`setCamera` を使用して設定します。

<br/>

<iframe height='500' scrolling='no' title='CameraBoundsOptions を介してマップを作成する' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって Pen の <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via `CameraBoundsOptions` </a> を表示します。
</iframe>

### <a name="animate-map-view"></a>マップ ビューをアニメーション化する

マップのカメラ オプションを設定するときに、[アニメーション オプション](/javascript/api/azure-maps-control/atlas.animationoptions)を設定することもできます。 これらのオプションでは、カメラの移動に必要なアニメーションの種類と継続時間を指定します。

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

次のコードでは、最初のコード ブロックでマップが作成され、中心とズームのマップ スタイルが設定されます。 2 番目のコード ブロックでは、アニメーション ボタンのクリック イベント ハンドラーが作成されます。 このボタンがクリックされると、`setCamera` 関数が呼び出され、[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) と [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) にはランダムな値が指定されます。

<br/>

<iframe height='500' scrolling='no' title='マップ ビューをアニメーション化する' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a>」Pen を表示します。
</iframe>

## <a name="request-transforms"></a>要求の変換

マップ コントロールによって行われた HTTP 要求を変更できると便利な場合があります。 次に例を示します。

- タイル要求にヘッダーを追加する。 これは多くの場合、パスワードで保護されたサービスに対して行われます。
- プロキシ サービスを介して要求を実行するように URL を変更する。

マップの[サービス オプション](/javascript/api/azure-maps-control/atlas.serviceoptions)には `transformRequest` があります。これを使用すると、マップによって行われるすべての要求を、それらが行われる前に変更することができます。 `transformRequest` オプションは、2 つのパラメーター (文字列の URL と、要求の使用目的を示すリソースの種類の文字列) を受け取る関数です。 この関数は、[RequestParameters](/javascript/api/azure-maps-control/atlas.requestparameters) の結果を返す必要があります。

```JavaScript
transformRequest: (url: string, resourceType: string) => RequestParameters
```

次の例では、これを使用して、ユーザー名とパスワードをヘッダーとして要求に追加することにより、`https://example.com` サイトへのすべての要求を変更する方法を示します。

```JavaScript
var map = new atlas.Map('myMap', {
    transformRequest: function (url, resourceType) {
        //Check to see if the request is to the specified endpoint.
        if (url.indexOf('https://examples.com') > -1) {
            //Add custom headers to the request.
            return {
                url: url,
                header: {
                    username: 'myUsername',
                    password: 'myPassword'
                }
            };
        }

        //Return the URL unchanged by default.
        return { url: url };
    },

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

## <a name="try-out-the-code"></a>コードを実行する

コード サンプルをご覧ください。 **[JS] タブ** 内で JavaScript コードを編集し、 **[結果] タブ** でマップ ビューの変更を確認することができます。また、右上隅にある **[Edit on CodePen]\(CodePen で編集\)** をクリックして、CodePen でコードを変更することもできます。

<a id="relatedReference"></a>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

ご利用のアプリに機能を追加するには、次のコード例を参照してください。

> [!div class="nextstepaction"]
> [マップのスタイルを変更する](choose-map-style.md)

> [!div class="nextstepaction"]
> [マップにコントロールを追加する](map-add-controls.md)

> [!div class="nextstepaction"]
> [コード サンプル](/samples/browse/?products=azure-maps)