---
title: Azure Maps を使用してストア ロケーターを作成する | Microsoft Docs
description: Azure Maps を使用してストア ロケーターを作成します。
author: walsehgal
ms.author: v-musehg
ms.date: 11/15/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 4cc21a4dbab7d5114eed8414c6530eab5f42bb00
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478851"
---
# <a name="create-a-store-locator-by-using-azure-maps"></a>Azure Maps を使用してストア ロケーターを作成する

このチュートリアルでは、Azure Maps を使用してシンプルなストア ロケーターを作成する手順を紹介します。 ストア ロケーターは広く一般的に使用されています。 この種のアプリケーションで使われている概念の多くは、他のさまざまな種類のアプリケーションに当てはまります。 コンシューマーに商品を直接販売するほとんどの企業にとって、ストア ロケーターを顧客に提供することは必要不可欠になっています。 このチュートリアルでは、以下の内容を学習します。
    
> [!div class="checklist"]
> * Azure マップ コントロール API を使用して新しい Web ページを作成する。
> * ファイルからカスタム データを読み込んで地図上に表示する。
> * Azure Maps Search サービスを使用して住所を検索したりクエリを入力したりする。
> * ユーザーの場所をブラウザーから取得して地図上に表示する。
> * 複数のレイヤーを組み合わせて独自の記号を地図上で作成する。  
> * データ ポイントをクラスター化する。  
> * 地図にズーム コントロールを追加する。

<a id="Intro"></a>

まずは、[ストア ロケーターのライブ サンプル](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator)または[ソース コード](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)をご覧ください。 

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するためには、まず [Azure Maps アカウントを作成](./tutorial-search-location.md#createaccount)し、[アカウントのサブスクリプション キーを取得](./tutorial-search-location.md#getkey)する必要があります。

## <a name="design"></a>設計

すぐにコードを作成し始めるよりも、まず設計から取り組むことをお勧めします。 単純なものから複雑なものまで、必要に応じてさまざまなストア ロケーターを作成できます。 このチュートリアルでは、シンプルなストア ロケーターを作成します。 読者が必要に応じて機能を拡張しやすいよう、途中でいくつかのヒントを記載しています。 ここでは、Contoso Coffee という架空の企業向けにストア ロケーターを作成します。 次の図は、このチュートリアルで作成するストア ロケーターの一般的なレイアウトのワイヤーフレームを示したものです。

<br/>
<center>

![Contoso Coffee コーヒー ショップの所在地を示すストア ロケーターのワイヤーフレーム](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

このストア ロケーターには、その実用性を最大化するために、ユーザーの画面幅が 700 ピクセルよりも狭いときに調整されるレスポンシブ レイアウトを採用しています。 レスポンシブ レイアウトにより、モバイル デバイスなどの小さな画面でも使いやすいストア ロケーターとなっています。 小さな画面レイアウトのワイヤーフレームを次に示します。  

<br/>
<center>

![モバイル デバイスにおける Contoso Coffee ストア ロケーターのワイヤーフレーム](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

ワイヤーフレームを見ると、きわめてシンプルなアプリケーションであることがわかります。 このアプリケーションには、検索ボックス、近隣の店舗の一覧、いくつかのマーカー (記号) が表示された地図に加え、ユーザーがマーカーを選択したときに詳しい情報を表示するポップアップ ウィンドウがあります。 さらに、このチュートリアルでこのストア ロケーターに組み込む機能は次のとおりです。

* インポートしたタブ区切りデータ ファイル内のすべての所在地が地図上に読み込まれます。
* ユーザーは地図に対するパン操作やズーム操作のほか、検索を実行したり、[My Location]\(現在位置\) という GPS ボタンを選択したりすることができます。
* デバイスの画面の幅に応じてページ レイアウトが調整されます。  
* ヘッダーには店舗のロゴが表示されます。  
* ユーザーは、検索ボックスと検索ボタンを使用して所在地 (住所、郵便番号、都市など) を検索することができます。 
* ユーザーが Enter キーを押すと、検索ボックスに追加された `keypress` イベントによって検索がトリガーされます。 これは見過ごされることも多いですが、ユーザー エクスペリエンスの向上につながる機能です。
* 地図を動かすと、地図の中心から個々の所在地までの距離が計算されます。 結果リストが更新され、最も近い所在地が地図の一番上に表示されます。  
* 結果リストでいずれかの結果を選択すると、選択した場所が地図の中央に移動し、その場所についての情報がポップアップ ウィンドウに表示されます。  
* また、地図上で特定の場所を選択した場合も、ポップアップ ウィンドウがトリガーされます。
* ユーザーが地図を縮小すると、一連の所在地がクラスターとしてグループ化されます。 クラスターは、内側に番号が付いた円で表されます。 クラスターは、ユーザーがズーム レベルを変更すると、それに従ってグループを形成したり分離したりします。
* クラスターを選択すると、地図上で 2 レベル拡大表示され、クラスターの位置が中心に来ます。

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>店舗所在地のデータセットを作成する

ストア ロケーター アプリケーションを開発する前に、地図上に表示する店舗のデータセットを作成する必要があります。 このチュートリアルでは、Contoso Coffee という架空のコーヒー ショップのデータセットを使用します。 このシンプルなストア ロケーターのデータセットは、Excel ブックで管理します。 データセットには、米国、カナダ、イギリス、フランス、ドイツ、イタリア、オランダ、デンマーク、スペインの 9 つの国/地域に点在する Contoso Coffee コーヒー ショップ 10,213 店舗の所在地が含まれています。 実際のデータのスクリーンショットを次に示します。

<br/>
<center>

![Excel ブック形式のストア ロケーター データのスクリーンショット](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

[この Excel ブックはダウンロード](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)できます。 

このデータのスクリーンショットを見ると、次のことが確認できます。
    
* 所在地情報は、**AddressLine**、**City**、**Municipality** (郡)、**AdminDivision** (州)、**PostCode** (郵便番号)、**Country** の各列を使用して格納されています。  
* **Latitude** 列と **Longitude** 列には、Contoso Coffee コーヒー ショップの各所在地に対応する座標が格納されています。 座標情報がない場合は、Azure Maps の Search サービスを使用して所在地の座標を特定できます。
* その他いくつかの列に、コーヒー ショップに関連したメタデータが格納されています。電話番号、Wi-Fi スポットと車椅子の利用の可否 (ブール型列)、店舗の開店時間と閉店時間 (24 時間形式) です。 実際の場所データとの関係がより強いメタデータを格納する列を独自に作成してもかまいません。

> [!Note]
> Azure Maps は、球面メルカトル図法 "EPSG:3857" でデータをレンダリングしますが、WGS84 測地系を使用する "EPSG:4325" のデータを読み取ります。 

データセットをアプリケーションに公開する方法は数多く存在します。 その 1 つが、データベースにデータを読み込んだうえで、そのデータを照会してユーザーのブラウザーに結果を返す Web サービスを公開する方法です。 この方法は、データセットが大きい場合や更新頻度が高い場合に最適です。 ただし、この方法は開発作業が著しく増え、コストも大きくなります。 

さらに別の方法として、ブラウザーで簡単に解析できるフラット テキスト ファイルにこのデータセットを変換する方法もあります。 ファイルそのものは、アプリケーションの他の要素と共にホストすることができます。 この方法ならシンプルな状態を保つことができますが、選択肢として適しているのはデータセットが比較的小さい場合に限られます。ユーザーがすべてのデータをダウンロードすることになるためです。 ここでは、データ ファイルのサイズが 1 MB 未満であるため、このデータセットにフラット テキスト ファイルを使用します。  

このブックをフラット テキスト ファイルに変換するには、タブ区切りファイルとしてブックを保存します。 それぞれの列はタブ文字で区切られているため、コード内で容易に列を解析することができます。 コンマ区切り値 (CSV) 形式を使用することもできますが、その場合、より多くの解析ロジックが必要となります。 コンマを含んでいるフィールドはすべて、前後に引用符を付けてラップされます。 このデータを Excel でタブ区切りファイルとしてエクスポートするには、 **[名前を付けて保存]** を選択します。 **[ファイルの種類]** ボックスの一覧から **[テキスト (タブ区切り)(*.txt)]** を選択します。 このファイルに *ContosoCoffee.txt* という名前を付けます。 

<br/>
<center>

![[ファイルの種類] ダイアログ ボックスのスクリーンショット](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

テキスト ファイルをメモ帳で開いた場合、次の図のようになります。

<br/>
<center>

![タブ区切りデータセットが表示されたメモ帳ファイルのスクリーンショット](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>プロジェクトのセットアップ

プロジェクトの作成には、[Visual Studio](https://visualstudio.microsoft.com) または任意のコード エディターを使用できます。 プロジェクト フォルダーに、*index.html*、*index.css*、*index.js* の 3 つのファイルを作成します。 これらのファイルによって、アプリケーションのレイアウト、スタイル、ロジックが定義されます。 *data* という名前のフォルダーを作成し、そのフォルダーに *ContosoCoffee.txt* を追加します。 *images* という名前の別のフォルダーを作成します。 このアプリケーションでは、地図上のアイコン、ボタン、マーカー用に 10 個の画像を使用します。 [これらの画像をダウンロード](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)することができます。 これでプロジェクト フォルダーは、次の図のようになります。

<br/>
<center>

![Simple Store Locator プロジェクト フォルダーのスクリーンショット](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>ユーザー インターフェイスを作成する

ユーザー インターフェイスを作成するには、次のコードを *index.html* に追加します。

1. *index.html* の `head` に次の `meta` タグを追加します。 これらのタグで文字セット (UTF-8) を定義し、Internet Explorer と Microsoft Edge に最新のブラウザー バージョンを使用するよう伝え、レスポンシブ レイアウトに適したビューポートを指定しています。

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Azure Maps Web コントロールの JavaScript ファイルと CSS ファイルへの参照を追加します。

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Azure Maps Services モジュールへの参照を追加します。 このモジュールは、Azure Maps の REST サービスをラップして JavaScript で使いやすいようにしている JavaScript ライブラリです。 検索機能を強化するうえで、このモジュールは役立ちます。

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. *index.js* と *index.css* への参照を追加します。

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. ドキュメントの本文に、`header` タグを追加します。 `header` タグ内にロゴと会社名を追加します。

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. `main` タグを追加し、テキスト ボックスと検索ボタンを備えた検索パネルを作成します。 さらに、地図、リスト パネル、[My Location]\(現在位置\) GPS ボタンに対応する `div` 参照を追加します。

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

完成した *index.html* は、[こちらの例の index.html ファイルのようになります](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html)。

次の手順で、CSS スタイルを定義します。 アプリケーション コンポーネントのレイアウトとアプリケーションの外観は、CSS スタイルによって定義されます。 *index.css* を開いて、次のコードを追加します。 `@media` スタイルは、画面幅が 700 ピクセルよりも狭いときに使用される代替スタイルのオプションを定義します。  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

ここでアプリケーションを実行した場合、ヘッダー、検索ボックス、検索ボタンは表示されますが、地図はまだ読み込まれていないので表示されません。 検索を実行しようとしても何も起こりません。 ストア ロケーターのすべての機能を利用するには、次のセクションで説明する JavaScript ロジックを設定する必要があります。

## <a name="wire-the-application-with-javascript"></a>JavaScript を使用してアプリケーションを接続する

この時点で、ユーザー インターフェイスの設定はすべて整っています。 次に、データを読み込んで解析した後、そのデータを地図上にレンダリングする JavaScript を追加する必要があります。 最初に、以下の手順で示すとおり、*index.js* を開いてコードを追加します。

1. 設定を更新しやすいようグローバル オプションを追加します。 さらに、地図、ポップアップ ウィンドウ、データ ソース、アイコン レイヤー、検索エリアの中心に表示される HTML マーカー、Azure Maps Search サービス クライアントのインスタンス用の各変数を定義します。

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. *index.js* にコードを追加します。 以下のコードでは、地図の初期化、ページの読み込みが完了するまで待機する[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)の追加、地図の読み込みを監視するイベントの接続、検索ボタンと [My Location]\(現在位置\) ボタンの追加を行います。

   ユーザーが検索ボタンを選択するか、検索ボックスに場所を入力した後で Enter キーを押すと、ユーザーのクエリに対するあいまい検索が開始されます。 検索結果を特定の国/地域に限定するために、それらの国の ISO 2 値の配列を `countrySet` オプションに渡します。 検索の国/地域を限定することで、返される結果の精度向上につながります。 
  
   検索が完了したら、最初の結果を取得し、その地域上に地図のカメラを設定します。 [My Location]\(現在位置\) ボタンがユーザーによって選択されたら、ブラウザーに組み込まれている HTML5 Geolocation API を使用して、ユーザーの位置情報を取得し、その位置が中心に来るように地図を配置します。  

   > [!Tip]
   > ポップアップ ウィンドウを使用するときは、単一の `Popup` インスタンスを作成し、その内容と位置を更新することによって再利用することをお勧めします。 コードに追加する `Popup` インスタンスごとに、ページに複数の DOM 要素が追加されます。 ページ上の DOM 要素が増えるほど、ブラウザーで追跡する必要のあるものが増えます。 項目が多すぎると、ブラウザーが低速化する可能性があります。

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. 地図の `ready` イベント リスナーに、検索エリアの中心を表示する HTML マーカーとズーム コントロールを追加します。

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. 地図の `ready` イベント リスナーで、データ ソースを追加します。 そのうえで、データセットの読み込みと解析のための呼び出しを行います。 データ ソースでは、クラスタリングを有効にしてください。 データ ソースでのクラスタリングにより、重なり合うポイントがまとめて 1 つのクラスターにグループ化されます。 ユーザーが拡大操作を実行すると、クラスターは個々のポイントに分かれます。 これによって、より流動的なユーザー エクスペリエンスとなり、パフォーマンスも向上します。

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. 地図の `ready` イベント リスナーでデータセットを読み込んだ後、データをレンダリングするための一連のレイヤーを定義します。 クラスター化されたデータ ポイントのレンダリングには、バブル レイヤーが使用されます。 シンボル レイヤーは、バブル レイヤー上の各クラスターに含まれるポイントの数をレンダリングする目的で使用されます。 2 つ目のシンボル レイヤーでは、地図上の個々の場所のカスタム アイコンをレンダリングします。

   ユーザーが地図上のクラスターまたはアイコンにマウス ポインターを重ねたときにマウス カーソルを変化させるために、バブル レイヤーとアイコン レイヤーに `mouseover` イベントと `mouseout` イベントを追加します。 クラスターのバブル レイヤーには、`click` イベントを追加します。 いずれかのクラスターをユーザーが選択すると、この `click` イベントによって地図が 2 レベル拡大され、クラスターが中心に来るように地図が配置されます。 アイコン レイヤーには `click` イベントを追加します。 ユーザーが個々の所在地アイコンを選択すると、コーヒー ショップの詳細を表示するポップアップ ウィンドウがこの `click` イベントによって表示されます。 地図には、その移動が完了するタイミングを監視するためのイベントを追加します。 このイベントが発生したら、リスト パネル内の項目を更新します。  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. コーヒー ショップのデータセットを読み込む場合は、まずそれをダウンロードする必要があります。 さらに、テキスト ファイルを行単位に分割する必要があります。 先頭行には、ヘッダー情報が格納されています。 ここでは、コードをたどりやすいように、ヘッダーを解析してオブジェクトに格納しています。それを使用することで、各プロパティのセル インデックスを検索することができます。 先頭行の後、残りの行をループで処理しながらポイント フィーチャーを作成します。 そのポイント フィーチャーをデータ ソースに追加します。 最後に、リスト パネルを更新します。

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. リスト パネルが更新されたら、地図の中心から現在のマップ ビュー内のすべてのポイント フィーチャーまでの距離が計算されます。 その後、距離でフィーチャーが並べ替えられます。 HTML が生成され、個々の場所がリスト パネルに表示されます。

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. リスト パネル内の項目がユーザーによって選択されたら、その項目に関連した図形がデータ ソースから取得されます。 図形に格納されているプロパティ情報に基づいてポップアップ ウィンドウが生成されます。 地図は、その図形が中心に来るように配置されます。 地図の幅が 700 ピクセル未満である場合は、ポップアップ ウィンドウが隠れないよう、マップ ビューがオフセットされます。

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

これで、完全に機能するストア ロケーターができあがりました。 ストア ロケーターの *index.html* ファイルを Web ブラウザーで開いてください。 地図上にクラスターが表示されたら、検索ボックスの使用、[My Location]\(現在位置\) ボタンの選択、クラスターの選択、地図の拡大表示によって場所を検索し、個々の場所を表示することができます。

ユーザーが初めて [My Location]\(現在位置\) ボタンを選択するときは、ユーザーの位置情報へのアクセス許可を求めるセキュリティ警告がブラウザーに表示されます。 ユーザーが位置情報の共有に同意した場合、その場所が地図で拡大表示され、付近のコーヒー ショップが表示されます。 

<br/>
<center>

![ユーザーの位置情報へのアクセスを求めるブラウザーの要求のスクリーンショット](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

コーヒー ショップの所在地を含んだエリアを十分拡大表示すると、クラスターが個々の所在地に分かれます。 地図上のいずれかのアイコンを選択するか、サイド パネル内の項目を選択すると、その所在地の情報を示すポップアップ ウィンドウが表示されます。

<br/>
<center>

![完成したストア ロケーターのスクリーンショット](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

ブラウザー ウィンドウのサイズを幅 700 ピクセル未満に変更するか、アプリケーションをモバイル デバイスで開いた場合、小さい画面用に合わせてレイアウトが変化します。 

<br/>
<center>

![小画面バージョンのストア ロケーターのスクリーンショット](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Maps を使用して基本的なストア ロケーターを作成する方法を紹介しています。 このチュートリアルで作成するストア ロケーターには、必要な機能がすべて備わっているものと思われます。 次のように、ストア ロケーターに機能を追加したり、より高度な機能を使用したりすれば、ユーザー エクスペリエンスの独自性を高めることができます。 

> [!div class="checklist"]
> * 検索ボックスの[入力補完](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI)を有効にする。  
> * [多言語のサポート](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization)を追加する。 
> * ユーザーが[ルートに沿って場所をフィルター処理](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route)できるようにする。 
> * [フィルターを設定](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property)する機能を追加する。 
> * クエリ文字列を使用して検索の初期値を指定できるようにする。 このオプションをストア ロケーターに実装すれば、ユーザーが検索をブックマークに登録したり共有したりできます。 このページに対し、別のページから検索を渡す簡単な方法としても利用できます。  
> * ストア ロケーターを [Azure App Service Web アプリ](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html)としてデプロイする。 
> * データベースにデータを格納して、付近の所在地を検索する。 詳細については、[SQL Server の空間データ型の概要](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017)と[空間データに対するニアレストネイバーのクエリ](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017)に関するページを参照してください。

> [!div class="nextstepaction"]
> [ソース コード全体を見る](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [ライブ サンプルを見る](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Azure Maps の対象範囲と機能について詳しくは、以下を参照してください。

> [!div class="nextstepaction"]
> [ズーム レベルとタイル グリッド](zoom-levels-and-tile-grid.md)

他のコード例や対話型のコーディング エクスペリエンスについては、以下を参照してください。

> [!div class="nextstepaction"]
> [マップ コントロールの使用方法](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)