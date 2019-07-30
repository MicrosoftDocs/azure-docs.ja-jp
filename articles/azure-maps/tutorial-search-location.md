---
title: Azure Maps での検索 | Microsoft Docs
description: Azure Maps を使用して近くの目的地を検索する
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a75f3f606129d370457816507537f2cb4491adf8
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478824"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Azure Maps を使用して近くの目的地を検索する

このチュートリアルでは、Azure Maps でアカウントを設定してから、Maps API を使って目的地を検索する方法を示します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Maps アカウントを作成する
> * Maps アカウントの主キーを取得する
> * マップ コントロール API を使って新しい Web ページを作成する
> * Maps 検索サービスを使って近くの目的地を検索する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com) にサインインします。

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Azure Maps でアカウントを作成する

次の手順で新しい Maps アカウントを作成します。

1. [Azure Portal](https://portal.azure.com) の左上隅にある **[リソースの作成]** をクリックします。
2. *[Marketplace を検索]* ボックスに「**Maps**」と入力します。
3. *[結果]* から **[Maps]** を選択します。 マップの下に表示される **[作成]** ボタンをクリックします。
4. **[Azure Maps アカウントの作成]** ページで、次の値を入力します。
    * このアカウントで使う "*サブスクリプション*"。
    * このアカウントの "*リソース グループ*" の名前。 *[新規作成]* を選んで新しく作成することも、 *[既存のものを使用]* を選んで既存のリソース グループを使うこともできます。
    * 新しいアカウントの "*名前*"。
    * このアカウントの "*価格レベル*"。
    * *[ライセンス]* と *[プライバシーに関する声明]* の内容を読み、チェック ボックスをオンにして条件に同意します。
    * **[作成]** ボタンをクリックします。

![ポータルでの Azure Maps アカウントの作成](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>アカウントの主キーを取得する

Maps アカウントが正常に作成されたら、Maps API のクエリを実行できるキーを取得します。

1. ポータルで、Maps アカウントを開きます。
2. [設定] セクションで **[認証]** を選択します。
3. **[主キー]** をクリップボードにコピーします。 このチュートリアルで後ほど使用するためにローカルに保存します。

![ポータルで主キーを取得する](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>新しいマップの作成

マップ コントロール API は、Maps を Web アプリケーションに簡単に統合できる便利なクライアント ライブラリです。 ベア REST サービスの呼び出しの複雑さを隠ぺいし、スタイルの設定とカスタマイズが可能なコンポーネントにより生産性を高めます。 次の手順は、マップ コントロール API を使用して埋め込まれた静的 HTML ページの作成方法を示したものです。

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

4. 変更をファイルに保存し、ブラウザーで HTML ページを開きます。 これは、アカウント キーを使用して `atlas.Map` を呼び出すことで作成できる、最も基本的なマップです。

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

   このコード セグメントで、`ready` イベントがマップに追加されます。このイベントは、マップ リソースが読み込まれ、マップへのアクセスの準備ができたときに発生します。 マップの `ready` イベントのハンドラーに、結果データを格納するためのデータ ソースが作成されます。 記号レイヤーを作成し、データ ソースにアタッチします。 データ ソースに格納された結果データのレンダリング方法は、このレイヤーで指定します。ここでは、濃い青色の円形のピン アイコンが、結果座標上に中央揃えで、結果データと共に表示されます。また、このピン アイコン上には他のアイコンを重ねて表示することができます。 その結果のレイヤーがマップ レイヤーに追加されます。

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>検索機能の追加

ここでは、Maps [Search API](https://docs.microsoft.com/rest/api/maps/search) を使って、マップ上で目的地を検索する方法を示します。 これは、開発者向けに設計された、住所、目的地、その他の地理的な情報を検索するための RESTful API です。 Search サービスは、指定された住所に緯度と経度の情報を割り当てます。 後述する**サービス モジュール**は、Maps Search API を使用して場所を検索する場合に使用できます。

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

   サブスクリプション キーを使用して Azure Maps に対する HTTP 要求を認証するために、`SubscriptionKeyCredential` によって `SubscriptionKeyCredentialPolicy` が作成されます。 `atlas.service.MapsURL.newPipeline()` は、`SubscriptionKeyCredential` ポリシーを取り込んで、[パイプライン](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) インスタンスを作成します。 `searchURL` は、Azure Maps の [Search](https://docs.microsoft.com/rest/api/maps/search) 操作の URL を表します。

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

3. **MapSearch.html** ファイルを保存し、ブラウザーを更新します。 シアトルを中心としたマップが表示され、領域内のガソリン スタンドの位置が青色の円形のピンでマークされます。

   ![検索結果でのマップの表示](./media/tutorial-search-location/pins-map.png)

4. ブラウザーに次の HTTPRequest を入力すると、マップがレンダリングしている生のデータを確認することができます。 \<Your Azure Maps Key\> は、実際の主キーに置き換えてください。

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=2&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

この時点で、MapSearch ページに、あいまい検索クエリから返される目的地の場所を表示できます。 いくつかの対話型機能と場所の詳細情報を追加しましょう。

## <a name="add-interactive-data"></a>対話型のデータの追加

作成したマップでは、これまで検索結果の経度/緯度データだけを参照してきました。 しかし、Maps 検索サービスが返す生の JSON を参照すると、名前や番地など、各ガソリン スタンドに関する追加情報が含まれていることがわかります。 対話型のポップアップ ボックスで、そのデータをマップに組み込むことができます。

1. マップの `ready` イベントのハンドラーに次のコード行を追加します。ファジー検索サービスを照会するコードの後に追加してください。 これにより、Popup のインスタンスが作成され、mouseover イベントが記号レイヤーに追加されます。

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
        //Get the properties and coordinates of the first shape that the event occured on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. ファイルを保存し、ブラウザーを更新します。 いずれかの検索ピンをポイントすると、ブラウザーのマップに情報ポップアップが表示されます。

    ![Azure マップ コントロールと Search Service](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure Maps でアカウントを作成する
> * アカウントの主キーを取得する
> * マップ コントロール API を使って新しい Web ページを作成する
> * Search Service を使って近くの目的地を検索する

> [!div class="nextstepaction"]
> [ソース コード全体を見る](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [ライブ サンプルを見る](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

次のチュートリアルでは、2 つの場所間のルートを表示する方法を示します。

> [!div class="nextstepaction"]
> [行き先へのルートの決定](./tutorial-route-location.md)
