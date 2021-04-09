---
title: チュートリアル:マップ上の近くの場所を検索する | Microsoft Azure Maps
description: マップ上で目的地を検索する方法に関するチュートリアルです。 Azure Maps Web SDK を使用して、検索機能と対話型のポップアップ ボックスをマップに追加する方法をご覧ください。
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 31dd1c06b0f17b469454593131ccdc93b45b2446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624969"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>チュートリアル:Azure Maps を使用して近くの目的地を検索する

このチュートリアルでは、Azure Maps でアカウントを設定してから、Maps API を使って目的地を検索する方法を示します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Maps アカウントを作成する
> * Maps アカウントの主キーを取得する
> * マップ コントロール API を使って新しい Web ページを作成する
> * Maps 検索サービスを使って近くの目的地を検索する

## <a name="prerequisites"></a>前提条件

<a id="createaccount"></a>
<a id="getkey"></a>

1. [Azure portal](https://portal.azure.com) にサインインします。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
2. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
3. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。

<a id="createmap"></a>

## <a name="create-a-new-map"></a>新しいマップの作成

マップ コントロール API は、便利なクライアント ライブラリです。 この API を使用すると、Maps を簡単に Web アプリケーションに統合できます。 ベア REST サービスの呼び出しの複雑さを隠され、カスタマイズが可能なコンポーネントにより生産性が高まります。 次の手順は、マップ コントロール API を使用して埋め込まれた静的 HTML ページの作成方法を示したものです。

1. ローカル コンピューターに新しいファイルを作成し、名前を **MapSearch.html** にします。
2. 次の HTML コンポーネントをファイルに追加します。

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   HTML ヘッダーに、Azure マップ コントロール ライブラリによってホストされる CSS および JavaScript のリソース ファイルが含まれることに注意してください。 ページ本体の `onload` イベントに注目してください。ページの本体が読み込まれると、このイベントによって `GetMap` 関数が呼び出されます。 `GetMap` 関数には、Azure Maps API にアクセスするためのインライン JavaScript コードが含まれます。

3. 次の JavaScript コードを、HTML ファイルの `GetMap` 関数に追加します。 `<Your Azure Maps Key>` という文字列は、Maps アカウントからコピーした主キーに置き換えてください。

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

   このセグメントは、Azure Maps アカウント キーに対するマップ コントロール API を開始します。 `atlas` は、API および関連するビジュアル コンポーネントを含む名前空間です。 `atlas.Map` は、ビジュアルと対話型 Web マップに対するコントロールを提供します。

4. 変更をファイルに保存し、ブラウザーで HTML ページを開きます。 表示されるマップは、アカウント キーを使用して `atlas.Map` を呼び出すことで作成できる、最も基本的なマップです。

   ![マップの表示](./media/tutorial-search-location/basic-map.png)

5. `GetMap` 関数で、マップを初期化した後、次の JavaScript コードを追加します。

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   このコード セグメントで、`ready` イベントがマップに追加されます。このイベントは、マップ リソースが読み込まれ、マップへのアクセスの準備ができたときに発生します。 マップの `ready` イベントのハンドラーに、結果データを格納するためのデータ ソースが作成されます。 記号レイヤーを作成し、データ ソースにアタッチします。 このレイヤーでは、データ ソース内の結果データをどのようにレンダリングするかを指定します。 この場合は、結果が結果座標の中央に濃い青色の丸いピン アイコンでレンダリングされます。また、このピンのアイコン上には他のアイコンを重ねて表示することができます。 その結果のレイヤーがマップ レイヤーに追加されます。

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>検索機能の追加

ここでは、Maps [Search API](/rest/api/maps/search) を使って、マップ上で目的地を検索する方法を示します。 これは、開発者向けに設計された、住所、目的地、その他の地理的な情報を検索するための RESTful API です。 Search サービスは、指定された住所に緯度と経度の情報を割り当てます。 後述する **サービス モジュール** は、Maps Search API を使用して場所を検索する場合に使用できます。

### <a name="service-module"></a>サービス モジュール

1. マップの `ready` イベント ハンドラーに次の JavaScript コードを追加して、検索サービスの URL を構築します。

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   サブスクリプション キーを使用して Azure Maps に対する HTTP 要求を認証するために、`SubscriptionKeyCredential` によって `SubscriptionKeyCredentialPolicy` が作成されます。 `atlas.service.MapsURL.newPipeline()` は、`SubscriptionKeyCredential` ポリシーを取り込んで、[パイプライン](/javascript/api/azure-maps-rest/atlas.service.pipeline) インスタンスを作成します。 `searchURL` は、Azure Maps の [Search](/rest/api/maps/search) 操作の URL を表します。

2. 続けて次のスクリプト ブロックを追加して検索クエリを作成します。 Search Service の基本的な検索 API であるファジー検索サービスを使用します。 ファジー検索サービスは、住所、場所、目的地 (POI) など、ほとんどのファジー入力を処理します。 このコードは、指定された緯度と経度の指定された半径内で、近くにあるガソリン スタンドを検索します。 次に、`geojson.getFeatures()` メソッドを使用して応答から GeoJSON のフィーチャー コレクションが抽出されて、データ ソースに追加されます。それにより、シンボル レイヤーを介してマップ上でデータが自動的にレンダリングされます。 スクリプトの最後の部分では、結果の境界ボックスとマップの [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) プロパティを使用して、マップのカメラ ビューを設定しています。

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. **MapSearch.html** ファイルを保存し、ブラウザーを更新します。 シアトルを中心としたマップに、領域内のガソリン スタンドの位置を示す青色の丸いピンが表示されます。

   ![検索結果でのマップの表示](./media/tutorial-search-location/pins-map.png)

4. ブラウザーに次の HTTPRequest を入力すると、マップがレンダリングしている生のデータを確認することができます。 \<Your Azure Maps Key\> をお使い主キーに置き換えます。

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

この時点で、MapSearch ページに、あいまい検索クエリから返される目的地の場所を表示できます。 いくつかの対話型機能と場所の詳細情報を追加しましょう。

## <a name="add-interactive-data"></a>対話型のデータの追加

作成したマップでは、これまで検索結果の経度/緯度データだけを参照してきました。 ただし、Maps Search Service によって返される未加工の JSON には、各ガソリン スタンドに関する追加情報が含まれています。 たとえば、名前や住所などです。 対話型のポップアップ ボックスで、そのデータをマップに組み込むことができます。

1. マップの `ready` イベントのハンドラーに次のコード行を追加します。ファジー検索サービスを照会するコードの後に追加してください。 このコードにより、Popup のインスタンスが作成され、mouseover イベントが記号レイヤーに追加されます。

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    API `*atlas.Popup` は、マップ上の目的の位置に固定された情報ウィンドウを提供します。 

2. `GetMap` 関数内に次のコードを追加します。これは、マウス オーバーした結果の情報をポップアップに表示するものです。

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. ファイルを保存し、ブラウザーを更新します。 いずれかの検索ピンをポイントすると、ブラウザーのマップに情報ポップアップが表示されます。

    ![Azure マップ コントロールと Search Service](./media/tutorial-search-location/popup-map.png)

このチュートリアルの完全なコードを表示するには、[こちら](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)をクリックしてください。 ライブ サンプルを表示するには、[こちら](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)をクリックしてください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップが必要なリソースはありません。

## <a name="next-steps"></a>次の手順

次のチュートリアルでは、2 つの場所間のルートを表示する方法を示します。

> [!div class="nextstepaction"]
> [行き先へのルートの決定](./tutorial-route-location.md)