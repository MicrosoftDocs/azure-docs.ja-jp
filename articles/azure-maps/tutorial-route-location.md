---
title: チュートリアル:場所へのルートを検索する |Microsoft Azure Maps
description: 目的地へのルートを検索する方法に関するチュートリアルです。 住所の座標を設定し、Azure Maps Route Service に目的地への道順を照会する方法をご覧ください。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 1f1647766eeae917f7a2266ebb8f00fdfab3452e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210582"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>チュートリアル:Azure Maps Route Service とマップ コントロールを使用してルートの道順を表示する方法

このチュートリアルでは、Azure Maps [Route Service API](/rest/api/maps/route) と[マップ コントロール](./how-to-use-map-control.md)を使用して、始点から終点までのルートの道順を表示する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * マップ コントロールを作成して Web ページに表示します。 
> * [シンボル レイヤー](map-add-pin.md)と[線レイヤー](map-add-line-layer.md)を定義して、ルートの表示レンダリングを定義します。
> * GeoJSON オブジェクトを作成し、始点と終点を表すようにマップに追加します。
> * [Get Route Directions API](/rest/api/maps/route/getroutedirections) を使用して、始点と終点からのルートの道順を取得します。

サンプルの完全なソース コードは、[こちら](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)から取得できます。 ライブ サンプルは、[こちら](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)でご覧いただけます。

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>マップ コントロールを作成して表示する

次の手順では、マップ コントロールを作成して Web ページに表示する方法について説明します。

1. ローカル コンピューターに新しいファイルを作成し、名前を **MapRoute.html** にします。
2. 次の HTML マークアップをコピーし、ファイルに貼り付けます。

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    HTML ヘッダーには、Azure マップ コントロール ライブラリによってホストされる CSS および JavaScript のリソース ファイルが含まれています。 本文の `onload` イベントにより、`GetMap` 関数が呼び出されます。 次の手順では、マップ コントロールの初期化コードを追加します。

3. `GetMap` 関数に、次の JavaScript コードを追加します。 `<Your Azure Maps Key>` という文字列は、Maps アカウントからコピーした主キーに置き換えてください。

    ```JavaScript
   //Instantiate a map object
   var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
           authType: 'subscriptionKey',
           subscriptionKey: '<Your Azure Maps Key>'
        }
   });
   ```

4. ファイルを保存し、ブラウザーで開きます。 サンプルが表示されます。

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="マップ コントロールの基本的なマップ レンダリング":::

## <a name="define-route-display-rendering"></a>ルート表示レンダリングを定義する

このチュートリアルでは、線レイヤーを使用してルートをレンダリングします。 起点と終点は、シンボル レイヤーを使用してレンダリングされます。 線レイヤーの追加の詳細については、「[マップに線レイヤーを追加する](map-add-line-layer.md)」を参照してください。 シンボル レイヤーの詳細については、「[マップにシンボル レイヤーを追加する](map-add-pin.md)」を参照してください。

1. `GetMap` 関数に、次の JavaScript コードを追加します。 このコードは、マップ コントロールの `ready` イベント ハンドラーを実装します。 このチュートリアルの残りのコードは、`ready` イベント ハンドラー内に配置されます。

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```

    マップ コントロールの `ready` イベント ハンドラーで、始点から終点までのルートを格納するためのデータ ソースが作成されます。 ルートの線のレンダリング方法を定義するために、線レイヤーが作成され、データ ソースにアタッチされます。  ルートの線で道路のラベルが覆い隠されないようにするために、`'labels'` の値を持つ 2 番目のパラメーターを渡しました。

    次に、シンボル レイヤーが作成され、データ ソースにアタッチされます。 起点と終点のレンダリング方法は、このレイヤーで指定します。各ポイント オブジェクトのプロパティからアイコン画像とテキスト ラベル情報を取得するための式が追加されています。 式の詳細については、[データ ドリブンのスタイルの式](data-driven-style-expressions-web-sdk.md)に関するページをご覧ください。

2. Microsoft を始点として、シアトルにあるガソリン スタンドを終点として設定します。  マップ コントロールの `ready` イベント ハンドラーに次のコードを追加します。

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    このコードにより、ルートの始点と終点を表す 2 つの [GeoJSON ポイント オブジェクト](https://en.wikipedia.org/wiki/GeoJSON)が作成され、データ ソースに追加されます。 

    最後のコード ブロックでは、始点と終点の緯度と経度を使用してカメラ ビューを設定します。 起点と終点をデータ ソースに追加します。 起点と終点の境界ボックスは、`atlas.data.BoundingBox.fromData` 関数を使用して計算されます。 この境界ボックスと `map.setCamera` 関数を使用して、ルート全体にマップのカメラ ビューが設定されます。 記号アイコンのピクセル寸法を補正するためにパディングを追加しています。 マップ コントロールの setCamera プロパティの詳細については、[setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) プロパティを参照してください。

3. **MapRoute.html** を保存し、ブラウザーを更新します。 これで、マップの中心がシアトルに設定されました。 涙形の青いピンは、始点をマークしています。 円形の青いピンは、終点をマークしています。

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="マップ上の起点と終点を結ぶルートを表示する":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>ルートの道順を取得する

このセクションでは、Azure Maps Route Directions API を使用して、ルートの道順と、特定ポイント間の推定到着時間を取得する方法について説明します。

>[!TIP]
>Azure Maps Route Service には、距離、渋滞状況、使用する輸送モードに基づく、"*最速*"、"*最短*"、"*エコ*"、"*スリリング*" などのさまざまなルートの種類を基にルートを計画できる API シリーズが用意されています。 また、このサービスを使用すると、ユーザーは過去の渋滞状況に基づいて今後のルートを計画することもできます。 ユーザーは、指定された任意の時刻におけるルート所要時間の予測を確認できます。 詳しくは、[Get Route Directions API](/rest/api/maps/route/getroutedirections) に関する記事をご覧ください。

1. `GetMap` 関数のコントロールの `ready` イベント ハンドラー内で、JavaScript コードに以下を追加します。

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   サブスクリプション キーを使用して Azure Maps に対する HTTP 要求を認証するために、`SubscriptionKeyCredential` によって `SubscriptionKeyCredentialPolicy` が作成されます。 `atlas.service.MapsURL.newPipeline()` は、`SubscriptionKeyCredential` ポリシーを取り込んで、[パイプライン](/javascript/api/azure-maps-rest/atlas.service.pipeline) インスタンスを作成します。 `routeURL` は、Azure Maps の [Route](/rest/api/maps/route) 操作の URL を表します。

2. 資格情報と URL を設定した後、コントロールの `ready` イベント ハンドラーに次のコードを追加します。 このコードにより、始点から終点までのルートが構築されます。 `routeURL` は、Azure Maps Route Service API に対して、ルートの道順を計算するように要求します。 `geojson.getFeatures()` メソッドを使用して応答から GeoJSON フィーチャー コレクションが抽出され、データ ソースに追加されます。

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. **MapRoute.html** ファイルを保存し、Web ブラウザーを更新します。 マップに、始点から終点までのルートが表示されます。

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Azure マップ コントロールと Route Service":::

サンプルの完全なソース コードは、[こちら](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)から取得できます。 ライブ サンプルは、[こちら](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)でご覧いただけます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップが必要なリソースはありません。

## <a name="next-steps"></a>次の手順

次のチュートリアルでは、移動モードや積み荷の種類など、制限付きのルート クエリを作成する方法について説明します。 その後、同じマップに複数のルートを表示できます。

> [!div class="nextstepaction"]
> [異なる移動モードでルートを検索する](./tutorial-prioritized-routes.md)