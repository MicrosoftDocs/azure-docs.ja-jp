---
title: チュートリアル:移動モード別に複数のルートを検索する |Microsoft Azure Maps
description: Azure Maps を使用して特定の移動手段による目的地へのルートを検索する方法に関するチュートリアルです。 マップ上に複数のルートを表示する方法をご覧ください。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 5e7f38e4bbc95e04bf54f8f7a2dd80e21891e8df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215495"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>チュートリアル:Azure Maps を使用してさまざまな移動モードのルートを検索して表示する

このチュートリアルでは、Azure Maps の [Route Service](/rest/api/maps/route) および[マップ コントロール](./how-to-use-map-control.md)を使用して、自家用車と、積み荷の種類が `USHazmatClass2` である営業車 (トラック) の両方の道順を表示する方法について説明します。 また、マップ上でリアルタイムのトラフィック データを視覚化する方法についても説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * マップ コントロールを作成して Web ページに表示する
> * マップにリアルタイムのトラフィック データをレンダリングする
> * 自家用車および営業車のルートを要求してマップに表示する

## <a name="prerequisites"></a>前提条件

1. [Azure portal](https://portal.azure.com) にサインインします。

2. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)。

3. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。

サンプルの完全なソース コードは、[こちら](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)から取得できます。 ライブ サンプルは、[こちら](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)でご覧いただけます。

## <a name="create-a-new-web-page-using-the-map-control-api"></a>マップ コントロール API を使って新しい Web ページを作成する

次の手順では、マップ コントロールを作成して Web ページに表示する方法について説明します。

1. ローカル コンピューターに新しいファイルを作成し、名前を **MapTruckRoute.html** にします。
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

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="マップ コントロールの基本的なマップ レンダリング":::

## <a name="render-real-time-traffic-data-on-a-map"></a>マップにリアルタイムのトラフィック データをレンダリングする

1. `GetMap` 関数に、次の JavaScript コードを追加します。 このコードは、マップ コントロールの `ready` イベント ハンドラーを実装します。 このチュートリアルの残りのコードは、`ready` イベント ハンドラー内に配置されます。

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    マップの `ready` イベントのハンドラーで、マップのトラフィック フロー設定を `relative` (フリーフローを基準とした道路の速度) に設定します。 トラフィック オプションの詳細については、「[TrafficOptions インターフェイス](/javascript/api/azure-maps-control/atlas.trafficoptions)」を参照してください。

2. **MapTruckRoute.html** ファイルを保存し、ブラウザーでページを更新します。 ロサンゼルスなどの任意の都市にズームインすると、現在のトラフィック フロー データと共に街路が表示されます。

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="マップ上にトラフィックを表示する":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>ルート表示レンダリングを定義する

このチュートリアルでは、2 つのルートが計算されてマップ上にレンダリングされます。 1 つ目のルートは、自家用車に対して計算されます。 2 つ目のルートは、営業車 (トラック) に対して計算され、結果の違いが示されます。 レンダリングの際、マップには、ルートの起点と終点を表す記号アイコンが表示され、ルートの経路ごとに異なる色でルートの線の配置が表示されます。 線レイヤーの追加の詳細については、「[マップに線レイヤーを追加する](map-add-line-layer.md)」を参照してください。 シンボル レイヤーの詳細については、「[マップにシンボル レイヤーを追加する](map-add-pin.md)」を参照してください。

1. マップ コントロールの `ready` イベント ハンドラーに次のコードを追加します。

    ```JavaScript

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

    ```


    マップ コントロールの `ready` イベント ハンドラーで、起点から終点までのルートを格納するためのデータ ソースが作成されます。 [式](data-driven-style-expressions-web-sdk.md)を使って、線の幅と色をルートの線のフィーチャーに関するプロパティから取得します。 ルートの線で道路のラベルが覆い隠されないようにするために、`'labels'` の値を持つ 2 番目のパラメーターを渡しました。

    次に、シンボル レイヤーが作成され、データ ソースにアタッチされます。 起点と終点のレンダリング方法は、このレイヤーで指定します。各ポイント オブジェクトのプロパティからアイコン画像とテキスト ラベル情報を取得するための式が追加されています。 式の詳細については、[データ ドリブンのスタイルの式](data-driven-style-expressions-web-sdk.md)に関するページをご覧ください。

2. シアトルにある架空の会社 (Fabrikam) を起点とし、Microsoft のオフィスを終点に設定します。  マップ コントロールの `ready` イベント ハンドラーに次のコードを追加します。


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

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    このコードにより、ルートの始点と終点を表す 2 つの [GeoJSON ポイント オブジェクト](https://en.wikipedia.org/wiki/GeoJSON)が作成され、データ ソースに追加されます。

    最後のコード ブロックでは、始点と終点の緯度と経度を使用してカメラ ビューを設定します。 起点と終点をデータ ソースに追加します。 起点と終点の境界ボックスは、`atlas.data.BoundingBox.fromData` 関数を使用して計算されます。 この境界ボックスと `map.setCamera` 関数を使用して、ルート全体にマップのカメラ ビューが設定されます。 記号アイコンのピクセル寸法を補正するためにパディングを追加しています。 マップ コントロールの setCamera プロパティの詳細については、[setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) プロパティを参照してください。

3. **TruckRoute.html** を保存し、ブラウザーを更新します。 これで、マップの中心がシアトルに設定されました。 涙形の青いピンは、始点をマークしています。 円形の青いピンは、終点をマークしています。

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="起点と終点が表示されたマップ":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>自家用車および営業車のルートを要求してマップに表示する

このセクションでは、Azure Maps Route Service を使用して、輸送モードに基づいて、ある地点から別の地点への道順を取得する方法について説明します。 2 つの輸送モード (トラックと車) を使用します。

>[!TIP]
>Route Service には、距離、渋滞状況、および使用する輸送モードに基づいて、"*最速*"、"*最短*"、"*エコ*"、または "*スリリング*" なルートを計画できる API シリーズが用意されています。 また、このサービスを使用すると、ユーザーは過去の渋滞状況に基づいて今後のルートを計画することもできます。 ユーザーは、指定された任意の時刻におけるルート所要時間の予測を確認できます。 詳しくは、[Get Route Directions API](/rest/api/maps/route/getroutedirections) に関する記事をご覧ください。

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

2. 資格情報と URL を設定したら、次の JavaScript コードを追加して、起点から終点までのトラックのルートを作成します。 このルートは、`USHazmatClass2` に分類された積み荷を運搬するトラックに対して作成および表示されます。

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

    上記のコードでは、[Azure Maps Route Directions API](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) を介して Azure Maps Route Service に対してクエリを実行します。 応答から `geojson.getFeatures()` メソッドを使用して抽出される GeoJSON フィーチャー コレクションから、ルートの線が抽出されます。 最後に、ルートの線がデータ ソースに追加されます。 トラックのルート計算は自家用車のルート計算よりも時間がかかることが多いため、これをインデックス 0 で追加して、トラックのルートがデータ ソース内の他の線よりも先にレンダリングされるようにします。 トラックのルートの線が乗用車のルートよりも遅れてデータ ソースに追加された場合、その上にレンダリングされてしまいます。 トラックのルートの線には、線の色 (青) と幅 (9 ピクセル) という 2 つのプロパティを追加します。

    >[!TIP]
    > Azure Maps Route Directions API で使用可能なすべてのオプションと値を確認するには、[郵送ルートの道順の URI パラメーター](/rest/api/maps/route/postroutedirections#uri-parameters)を参照してください。

3. 今度は、次の JavaScript コードを追加して、自家用車のルートを作成します。

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    上記のコードでは、[Azure Maps Route Directions API](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) メソッドを介して Azure Maps ルーティング サービスに対してクエリを実行します。 応答から `geojson.getFeatures()` メソッドを使用して抽出される GeoJSON フィーチャー コレクションから、ルートの線が抽出されます。 最後に、ルートの線がデータ ソースに追加されます。 トラックのルートの線には、線の色 (紫) と幅 (5 ピクセル) という 2 つのプロパティを追加します。

4. **TruckRoute.html** ファイルを保存し、Web ブラウザーを更新します。 これで、マップにトラックと自家用車のルートが表示されます。

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="Azure Route Service を使用したマップ上の自家用車と営業車のルート":::

    トラックのルートは太い青の線を使用して表示されます。 自家用車のルートは細い紫の線を使用して表示されます。 自家用車のルートは、I-90 経由でワシントン湖を渡っています。この道路は住宅地の下のトンネルを通過します。 このトンネルは住宅地に近いため、危険廃棄物の積み荷が制限されています。 トラックのルートは、積み荷の種類を `USHazmatClass2` に指定したので、別の高速道路を使うようにルート指定されています。

サンプルの完全なソース コードは、[こちら](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)から取得できます。 ライブ サンプルは、[こちら](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)でご覧いただけます。

[データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)こともできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップが必要なリソースはありません。

## <a name="next-steps"></a>次の手順

次のチュートリアルでは、Azure Maps を使用して簡単なストア ロケーターを作成する手順を紹介します。

> [!div class="nextstepaction"]
> [Azure Maps を使用してストア ロケーターを作成する](./tutorial-create-store-locator.md)