---
title: Azure Maps を使った複数のルート | Microsoft Docs
description: Azure Maps を使用してさまざまな移動モードのルートを検索します
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ad67b17d76e811d5977955c40f444c4b7c0a01e3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478849"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Azure Maps を使用してさまざまな移動モードのルートを検索します

このチュートリアルでは、Azure Maps のアカウントとルート サービスを使って、目的地までのルートを移動モードの優先順に検索する方法について説明します。 マップには 2 つのルートを表示します (乗用車用とトラック用)。これらのルートは、高さ、重量、積み荷の危険性を理由に制限される場合があります。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * マップ コントロール API を使って新しい Web ページを作成する
> * マップ上にトラフィック フローを表示する
> * ルート クエリを作成し、移動モードを指定する
> * マップ上に複数のルートを表示する

## <a name="prerequisites"></a>前提条件

先に進む前に、最初のチュートリアルの手順に従って [Azure Maps アカウントを作成](./tutorial-search-location.md#createaccount)し、[アカウントのサブスクリプション キーを取得](./tutorial-search-location.md#getkey)してください。

## <a name="create-a-new-map"></a>新しいマップの作成

次の手順は、マップ コントロール API を使用して埋め込まれた静的 HTML ページの作成方法を示したものです。

1. ローカル コンピューターに新しいファイルを作成し、名前を **MapTruckRoute.html** にします。
2. 次の HTML コンポーネントをファイルに追加します。

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

    HTML ヘッダーに、Azure マップ コントロール ライブラリによってホストされる CSS および JavaScript のリソース ファイルが含まれることに注意してください。 ページ本体の `onload` イベントに注目してください。ページの本体が読み込まれると、このイベントによって `GetMap` 関数が呼び出されます。 この関数には、Azure Maps API にアクセスするためのインライン JavaScript コードが含まれます。

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

    `atlas.Map` クラスは、ビジュアルと対話型 Web マップのためのコントロールを提供し、Azure マップ コントロール API のコンポーネントです。

4. ファイルを保存し、ブラウザーで開きます。 この時点でのマップは基本的なものです。ここからさらに開発を加えることができます。

   ![基本的なマップの表示](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>トラフィック フローを表示する

1. マップにトラフィック フローの表示を追加します。 マップの `ready` イベントは、マップのリソースが読み込まれ、安全に対話する準備ができるまで待機します。

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    マップの `ready` イベントのハンドラーで、マップのトラフィック フロー設定を `relative` (フリーフローを基準とした道路の速度) に設定します。 道路の速度を `absolute` に設定することもできますし、`relative-delay` に設定して、フリーフローとの差を示す相対的速度を表示することもできます。

2. **MapTruckRoute.html** ファイルを保存し、ブラウザーでページを更新します。 マップと対話してロサンゼルスにズームインすると、道路が現在の交通データ付きで表示されます。

   ![トラフィック マップの表示](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>ルートのレンダリング方法を定義する

このチュートリアルでは、2 つのルートが計算されてマップ上にレンダリングされます。 1 つは乗用車が通行可能な道路を使ったルートで、もう 1 つはトラックが通行可能な道路を使ったルートです。 レンダリングの際、ルートの起点と終点を示す記号アイコンを表示し、ルートの経路ごとに異なる色で線を表示します。

1. マップを初期化した後、マップの `ready` イベントのハンドラー内に次の JavaScript コードを追加します。

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
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
    
    マップの `ready` イベントのハンドラーに、ルートの線および起点と終点を格納するためのデータ ソースが作成されます。 線レイヤーを作成してデータ ソースにアタッチすることで、ルートの線のレンダリング方法を定義します。 式を使って、線の幅と色をルートの線の特徴に関するプロパティから取得します。 このレイヤーをマップに追加する際に、`'labels'` という値の第 2 パラメーターを渡します。これにより、このレイヤーをマップ ラベルの下にレンダリングするよう指定します。 この結果、ルートの線で道路のラベルが覆い隠されないようになります。 記号レイヤーを作成し、データ ソースにアタッチします。 起点と終点のレンダリング方法は、このレイヤーで指定します。ここでは、各ポイント オブジェクトのプロパティからアイコン画像とテキスト ラベル情報を取得するための式を追加しています。 
    
2. このチュートリアルでは、シアトルにある架空の会社 (Fabrikam) を起点とし、Microsoft のオフィスを終点に設定します。 マップの `ready` イベントのハンドラーに、次のコードを追加します。

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    このコードは、ルートの起点と終点を表す 2 つの [GeoJSON オブジェクト](https://en.wikipedia.org/wiki/GeoJSON)を作成します。 それぞれの点に、`title` プロパティと `icon` プロパティを追加します。

3. 続けて、次の JavaScript コードを追加して、起点と終点のピンをマップに追加します。

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    起点と終点をデータ ソースに追加します。 起点と終点の境界ボックスは、`atlas.data.BoundingBox.fromData` 関数を使用して計算されます。 この境界ボックスと `map.setCamera` 関数を使用して、ルート全体にマップのカメラ ビューが設定されます。 記号アイコンの画像サイズ (ピクセル) を相殺するためにパディングを追加しています。

4. ファイルを保存し、ブラウザーを更新してマップ上にピンを表示します。 マップの中心がシアトルに設定され、起点を示す丸い青色のピンと、終点を示す青色のピンが表示されます。

   ![起点と終点が表示されたマップ](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>トラベルのモードの優先順にルートを表示する

このセクションでは、Maps のルート サービス API を使って、指定した起点から終点までの複数のルートを、移動のモードに基づいて検索する方法について説明します。 ルーティング サービスの API では、現在のトラフィック状況を考慮したうえで、2 つの場所の間の、*最速*、*最短*、*エコ*、または*スリリング*なルートを計画できます。 また、Azure の広範な履歴トラフィック データベースを使い、任意の日時のルート所要時間を予測することによって、将来のルートを計画することもできます。 詳細については、[ルートの道順の取得](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)に関するページを参照してください。 **マップを読み込む eventListener 内に**以下のコード ブロックをすべて追加し、マップが完全に読み込まれた後に、それらが読み込まれるようにする必要があります。

1. GetMap 関数内に、次の JavaScript コードを追加します。

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   サブスクリプション キーを使用して Azure Maps に対する HTTP 要求を認証するために、`SubscriptionKeyCredential` によって `SubscriptionKeyCredentialPolicy` が作成されます。 `atlas.service.MapsURL.newPipeline()` は、`SubscriptionKeyCredential` ポリシーを取り込んで、[パイプライン](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) インスタンスを作成します。 `routeURL` は、Azure Maps の [Route](https://docs.microsoft.com/rest/api/maps/route) 操作の URL を表します。

2. 資格情報と URL を設定した後、USHazmatClass2 クラスの積み荷を運ぶトラックの基点から終点までのルートを構築し、その結果を表示するための次の JavaScript コードを追加します。

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    上記のコード スニペットでは、[getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) メソッドを介して Azure Maps ルーティング サービスにクエリを行います。 応答から `geojson.getFeatures()` メソッドを使用して抽出される GeoJSON フィーチャー コレクションから、ルートの線が抽出されます。 その後、ルートの線がデータ ソースに追加されます。 さらに、データ ソース内の他の行の前に確実にレンダリングされるように、インデックス 0 が追加されます。 トラックのルート計算は、えてして乗用車のルート計算よりも時間がかかるものであり、トラックのルートの線が乗用車のルートよりも遅れてデータ ソースに追加された場合、その上にレンダリングされてしまうためです。 トラックのルートの線には、2 つのプロパティを追加します。ストローク カラー (繊細な青色) とストロークの幅 (9 ピクセル) です。

3. 次の JavaScript コードを追加して、乗用車のルートを構築し、結果を表示します。

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    上記のコード スニペットでは、[getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) メソッドを介して Azure Maps ルーティング サービスにクエリを行います。 応答から `geojson.getFeatures()` メソッドを使用して抽出される GeoJSON フィーチャー コレクションから、ルートの線が抽出されます。 その後、ルートの線がデータ ソースに追加されます。 乗用車のルートの線には、2 つのプロパティを追加します。ストローク カラー (紫色) とストロークの幅 (5 ピクセル) です。  

4. **MapTruckRoute.html** ファイルを保存し、ブラウザーを更新して結果を表示します。 Maps の API と正常に接続されると、次のようなマップが表示されます。

    ![Azure Route Service を使用した優先順のルート](./media/tutorial-prioritized-routes/prioritized-routes.png)

    トラックのルートは青色の太い線で、乗用車のルートは紫色の細い線で表示されます。 乗用車のルートは、I-90 経由でワシントン湖を渡っています。この道路は住宅地の下のトンネルを通過するため、危険廃棄物の積み荷が制限されています。 トラックのルートは、積み荷の種類を USHazmatClass2 に指定したので、別の高速道路を使って適切にルート指定されています。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * マップ コントロール API を使って新しい Web ページを作成する
> * マップ上にトラフィック フローを表示する
> * ルート クエリを作成し、移動モードを指定する
> * マップ上に複数のルートを表示する

> [!div class="nextstepaction"]
> [ソース コード全体を見る](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [ライブ サンプルを見る](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

次のチュートリアルでは、Azure Maps を使用して簡単なストア ロケーターを作成する手順を紹介します。

> [!div class="nextstepaction"]
> [Azure Maps を使用してストア ロケーターを作成する](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)