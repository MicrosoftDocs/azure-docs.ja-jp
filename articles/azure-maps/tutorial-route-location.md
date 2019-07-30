---
title: Azure Maps を使ってルートを検索する | Microsoft Docs
description: Azure Maps を使って目的地へのルートを検索する
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fd75ca1fbad358e80a2c040b5ead8c50611489e2
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478876"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Azure Maps を使って目的地へのルートを検索する

このチュートリアルでは、Azure Maps アカウントと Route Service SDK を使って、目的地までのルートを検索する方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * マップ コントロール API を使って新しい Web ページを作成する
> * 住所の座標を設定する
> * Route Service で目的地までの指示を照会する

## <a name="prerequisites"></a>前提条件

先に進む前に、前のチュートリアルの手順に従って [Azure Maps アカウントを作成](./tutorial-search-location.md#createaccount)し、[アカウントのサブスクリプション キーを取得](./tutorial-search-location.md#getkey)してください。

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>新しいマップの作成

次の手順は、マップ コントロール API を使用して埋め込まれた静的 HTML ページの作成方法を示したものです。

1. ローカル コンピューターに新しいファイルを作成し、名前を **MapRoute.html** にします。
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

    `atlas.Map` は、ビジュアルと対話型 Web マップのためのコントロールを提供し、Azure マップ コントロール API のコンポーネントです。

4. ファイルを保存し、ブラウザーで開きます。 この時点でのマップは基本的なものです。ここからさらに開発を加えることができます。

   ![基本的なマップの表示](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>ルートのレンダリング方法を定義する

このチュートリアルでは、ルートの起点と終点の記号アイコンおよびルート経路の線を使用して、単純なルートをレンダリングします。

1. マップを初期化した後、次の JavaScript コードを追加します。

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
    
    マップの `ready` イベントのハンドラーで、ルートの線および起点と終点を格納するためのデータ ソースが作成されます。 線レイヤーを作成してデータ ソースにアタッチすることで、ルートの線のレンダリング方法を定義します。 ルートの線は、青色でレンダリングされます。線幅は 5 ピクセルで、線の接合箇所と線端には丸みを与えています。 このレイヤーをマップに追加する際に、`'labels'` という値の第 2 パラメーターを渡します。これにより、このレイヤーをマップ ラベルの下にレンダリングするよう指定します。 この結果、ルートの線で道路のラベルが覆い隠されないようになります。 記号レイヤーを作成し、データ ソースにアタッチします。 起点と終点のレンダリング方法は、このレイヤーで指定します。ここでは、各ポイント オブジェクトのプロパティからアイコン画像とテキスト ラベル情報を取得するための式を追加しています。 
    
2. このチュートリアルでは、Microsoft を起点として設定し、シアトルにあるガソリン スタンドを終点として設定します。 マップの `ready` イベントのハンドラーに、次のコードを追加します。

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

    このコードは、ルートの起点と終点を表す 2 つの [GeoJSON ポイント オブジェクト](https://en.wikipedia.org/wiki/GeoJSON)を作成し、これらの点をデータソースに追加します。 それぞれの点に、`title` プロパティと `icon` プロパティを追加します。 最後のブロックでは、マップの [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) プロパティを使用して、始点と終点の緯度と経度の情報を使用するカメラ ビューを設定します。

3. **MapRoute.html** ファイルを保存し、ブラウザーを更新します。 マップの中心がシアトルに設定され、起点を示す青色のピンと、終点を示す丸い青色のピンが表示されます。

   ![起点と終点が表示されたマップ](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>道順の取得

ここでは、Azure Maps のルート サービス API を使って、指定した起点から終点までのルートを検索する方法について説明します。 ルート サービスの API では、2 つの場所の間の、*最速*、*最短*、*エコ*、または*スリリング*なルートを計画できます。 また、Azure の広範な履歴トラフィック データベースを使い、任意の日時のルート所要時間を予測することによって、将来のルートを計画することもできます。 詳しくは、「[Get route directions (ルートの道順を取得する)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)」をご覧ください。 **map ready eventListener 内に**以下の機能をすべて追加し、マップ リソースへのアクセスの準備ができた後で、機能が読み込まれるようにする必要があります。

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

2. 資格情報と URL を設定した後、基点から終点までのルートを構築するための次の JavaScript コードを追加します。 `routeURL` は、Azure Maps のルート サービスに対して、道順を計算するように要求します。 `geojson.getFeatures()` メソッドを使用して応答から GeoJSON フィーチャー コレクションが抽出され、データ ソースに追加されます。

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

3. **MapRoute.html** ファイルを保存し、Web ブラウザーを更新します。 Maps API と正常に接続されると、次のようなマップが表示されます。

    ![Azure マップ コントロールと Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * マップ コントロール API を使って新しい Web ページを作成する
> * 住所の座標を設定する
> * ルート サービスで目的地までの道順を照会する

> [!div class="nextstepaction"]
> [ソース コード全体を見る](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [ライブ サンプルを見る](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

次のチュートリアルでは、移動のモードや積み荷の種類など、制限を設定したルート クエリを作成し、同じマップ上に複数のルートを表示する方法について説明します。

> [!div class="nextstepaction"]
> [異なる移動モードでルートを検索する](./tutorial-prioritized-routes.md)
