---
title: チュートリアル:Bing 地図から Web アプリを移行する | Microsoft Azure Maps
description: Bing 地図から Microsoft Azure Maps に Web アプリを移行する方法についてのチュートリアルです。
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 2b072107275fba1ff83ab3ddac63ed8bf7766356
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389112"
---
# <a name="tutorial-migrate-a-web-app-from-bing-maps"></a>チュートリアル:Bing 地図から Web アプリを移行する

Bing 地図が使用される Web アプリでは、多くの場合、Bing 地図 V8 JavaScript SDK が使用されます。 Azure Maps Web SDK は、移行に適した Azure ベースの SDK です。 Azure Maps Web SDK を使用すると、インタラクティブ マップをカスタマイズして、Web アプリケーションまたはモバイル アプリケーションに独自のコンテンツや映像を表示することができます。 このコントロールには、WebGL が利用されているため、大きなデータ セットを高いパフォーマンスでレンダリングすることができます。 JavaScript または TypeScript を使用して、この SDK での開発を行います。 このチュートリアルでは、次の内容を学習します。

> [!div class="checklist"]
> * マップを読み込む
> * マップをローカライズする
> * 画鋲、ポリライン、およびポリゴンを追加する。
> * ポップアップまたは情報ボックスに情報を表示する
> * KML と GeoJSON のデータを読み込んで表示する
> * 画鋲をクラスタリングする
> * タイル レイヤーをオーバーレイする
> * トラフィック データを表示する
> * グラウンド オーバーレイを追加する

既存の Web アプリケーションを移行する場合は、Cesium、Leaflet、OpenLayers などのオープンソースのマップ コントロール ライブラリが使用されているかどうかを確認します。 そういったライブラリが使用されていて、使用を継続したい場合は、それを Azure Maps タイル サービス ([道路タイル](/rest/api/maps/render/getmaptile) \| [衛星タイル](/rest/api/maps/render/getmapimagerytile)) に接続することができます。 よく使用されるいくつかのオープンソースのマップ コントロール ライブラリで Azure Maps を使用する方法については、以下のリンクから詳しい情報をご覧いただけます。

* [Cesium](https://cesiumjs.org/) - Web 用の 3D マップ コントロール。 [コード サンプル](https://azuremapscodesamples.azurewebsites.net/?search=Cesium) \| [プラグイン リポジトリ]()
* [Leaflet](https://leafletjs.com/) – Web 用の軽量な 2D マップ コントロール。 [コード サンプル](https://azuremapscodesamples.azurewebsites.net/?search=leaflet) \| [プラグイン リポジトリ]()
* [OpenLayers](https://openlayers.org/) - プロジェクションをサポートする Web 用の 2D マップ コントロール。 [コード サンプル](https://azuremapscodesamples.azurewebsites.net/?search=openlayers) \| [プラグイン リポジトリ]()

JavaScript フレームワークを開発に使用している場合は、次のいずれかのオープンソース プロジェクトを利用できる可能性があります。

* [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - Azure Maps の Angular 10 ラッパー。
* [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Azure Maps Blazor コンポーネント。
* [Azure Maps React Component](https://github.com/WiredSolutions/react-azure-maps) - Azure Maps コントロール用の React ラッパー。
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) - Vue アプリケーション用の Azure Maps コンポーネント。

## <a name="prerequisites"></a>前提条件

1. [Azure portal](https://portal.azure.com) にサインインします。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
2. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
3. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。

## <a name="key-features-support"></a>主なフィーチャーのサポート

次の表には、Bing 地図 V8 JavaScript SDK の主な API 機能と、Azure Maps Web SDK での同様の API のサポートが示されています。

| Bing 地図の機能        | Azure Maps Web SDK のサポート                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| 画鋲                 | ✓                                                                                      |
| 画鋲クラスタリング       | ✓                                                                                      |
| ポリラインと多角形     | ✓                                                                                      |
| グラウンド オーバーレイ          | ✓                                                                                      |
| ヒート マップ                | ✓                                                                                      |
| タイル レイヤー              | ✓                                                                                      |
| KML レイヤー                | ✓                                                                                      |
| 等高線レイヤー            | [サンプル](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| データのビン分割レイヤー       | オープンソースの Azure Maps [Gridded Data Source モジュール](https://github.com/Azure-Samples/azure-maps-gridded-data-source)に含まれています。       |
| アニメーション タイル レイヤー      | オープンソースの Azure Maps [アニメーション モジュール](https://github.com/Azure-Samples/azure-maps-animations)に含まれています。 |
| 描画ツール            | ✓                                                                                      |
| ジオコーダー サービス         | ✓                                                                                      |
| 方向サービス       | ✓                                                                                      |
| 距離マトリックス サービス  | ✓                                                                                      |
| 空間データ サービス     | 該当なし                                                                                    |
| 衛星または航空映像 | ✓                                                                                      |
| 鳥瞰図         | N/A                                                                                |
| Streetside 映像       | N/A                                                                                |
| GeoJSON サポート          | ✓                                                                                      |
| GeoXML サポート           | ✓ [空間 IO モジュール](how-to-use-spatial-io-module.md)                                                                                     |
| Well-Known Text サポート  | ✓                                                                                      |
| カスタム マップ スタイル        | Partial                                                                                |

Azure Maps には、その機能を拡張する [Web SDK 用のオープンソース モジュール](open-source-projects.md#open-web-sdk-modules)が他にも数多く存在します。

## <a name="notable-differences-in-the-web-sdks"></a>Web SDK の注目すべき相違点

Bing 地図と Azure Maps の Web SDK の注意すべき主な相違点をいくつか以下に示します。

* Azure Maps Web SDK にアクセスするためのホスト エンドポイントが提供されるだけでなく、必要に応じて、Web SDK をアプリに埋め込むための NPM パッケージも使用可能です。 詳細については、こちらの[ドキュメント](./how-to-use-map-control.md)を参照してください。 このパッケージには TypeScript 定義も含まれています。
* Bing 地図には、SDK のホスト ブランチとして、リリース用と実験用の 2 つがあります。 実験ブランチでは、新規の開発が進行していると、1 日に複数回、更新が行われる場合があります。 Azure Maps でホストされるのはリリース ブランチのみですが、オープンソースの Azure Maps コード サンプル プロジェクトに試験的な機能がカスタム モジュールとして作成されます。 Bing 地図にはかつて、更新頻度を低くすることによってリリースによる破壊的変更のリスクを軽減する凍結ブランチもありました。 Azure Maps では、NPM モジュールを使用して、過去にリリースされたあらゆるマイナー バージョンを参照できるようになっています。

> [!TIP]
> Azure Maps では、縮小されたバージョンと縮小されていないバージョン両方の SDK が公開されています。 ファイル名から `.min` を削除するだけでかまいません。 縮小されていないバージョンは問題をデバッグする際に役立ちますが、運用環境では必ず、縮小されたバージョンを使用してください。その方がファイル サイズが小さくて済みます。

* Azure Maps で Map クラスのインスタンスを作成した後、マップを操作する前にマップの `ready` または `load` イベントが発生するのをコードで待機する必要があります。 これらのイベントにより、マップ リソースがすべて読み込まれ、アクセスできる状態であることが保証されます。
* 両方のプラットフォームで基本マップに同様のタイリング システムを使用しますが、Bing 地図のタイルのディメンションは 256 ピクセルで、Azure Maps のタイルのディメンションは 512 ピクセルとなります。 そのため、Azure Maps で Bing 地図と同じマップ ビューを取得するには、Bing 地図で使用されるズーム レベルを、Azure Maps で 1 ずつ減算する必要があります。
* Bing 地図では座標は `latitude, longitude` と呼ばれますが、Azure Maps では `longitude, latitude` が使用されます。 この形式は、ほとんどの GIS プラットフォームが準拠する標準の `[x, y]` と合致します。

* Azure Maps Web SDK の図形は、GeoJSON スキーマに基づいています。 ヘルパー クラスは、"[atlas.data" 名前空間](/javascript/api/azure-maps-control/atlas.data)を通じて公開されます。 また、[atlas.Shape](/javascript/api/azure-maps-control/atlas.shape) というクラスがあります。これを使用して、GeoJSON オブジェクトをラップし、データ バインド可能な方法で簡単に更新および保守できるようにします。
* Azure Maps の座標は、`[longitude, latitude]` または `new atlas.data.Position(longitude, latitude)` 形式のシンプルな数値配列として指定できる位置オブジェクトとして定義されます。

> [!TIP]
> Position クラスには、`latitude, longitude` 形式の座標をインポートするための静的ヘルパー関数があります。 [atlas.data.Position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) 関数は、多くの場合、Bing 地図コードの `new Microsoft.Maps.Location` 関数と置き換えることができます。

* マップに追加される各図形にスタイル設定情報を指定するのではなく、Azure Maps によってデータからスタイルが分離されます。 データはデータ ソースに格納され、Azure Maps コードでデータをレンダリングするために使用される、レンダリング レイヤーに接続されます。 この手法では、パフォーマンスが向上します。 さらに、多くのレイヤーでは、データドリブンのスタイル設定がサポートされます。これにより、ビジネス ロジックをレイヤー スタイル オプションに追加して、図形で定義されているプロパティに基づいてレイヤー内で個々の図形をレンダリングする方法を変更することができます。
* Azure Maps の `atlas.math` 名前空間には、便利な空間演算関数が数多く用意されていますが、これらは Bing 地図の空間演算モジュールのものとは異なります。 主な違いとして、Azure Maps には、二項演算 (和集合、積集合など) 用の組み込みの関数がありません。ただし Azure Maps は、オープン スタンダードである GeoJSON をベースとしているため、オープンソースのライブラリが数多く提供されています。 広く使われているオプションの 1 つとして、[turf js](http://turfjs.org/) があります。これは Azure Maps との相性がよく、多数の空間演算機能を利用できます。

Azure Maps 関連の用語を網羅した一覧については、[Azure Maps の用語集](./glossary.md)も参照してください。

## <a name="web-sdk-side-by-side-examples"></a>Web SDK のサイドバイサイドの例

Web アプリケーションを Bing 地図 V8 JavaScript SDK から Azure Maps Web SDK に移行する際に役立つ一般的なユース ケースについて説明する、各プラットフォームのコード サンプルのコレクションを以下に示します。 Web アプリケーションに関連するコード サンプルは JavaScript で提供されています。しかし、Azure Maps では、[NPM モジュール](./how-to-use-map-control.md)を通じて、追加オプションとして TypeScript 定義も提供されます。

**トピック**

* [マップを読み込む](#load-a-map)
* [マップのローカライズ](#localizing-the-map)
* [マップ ビューの設定](#setting-the-map-view)
* [画鋲の追加](#adding-a-pushpin)
* [カスタム画鋲の追加](#adding-a-custom-pushpin)
* [ポリラインの追加](#adding-a-polyline)
* [多角形の追加](#adding-a-polygon)
* [情報ボックスの表示](#display-an-infobox)
* [画鋲クラスタリング](#pushpin-clustering)
* [ヒート マップを追加する](#add-a-heat-map)
* [タイル レイヤーをオーバーレイする](#overlay-a-tile-layer)
* [トラフィック データを表示する](#show-traffic-data)
* [グラウンド オーバーレイを追加する](#add-a-ground-overlay)
* [マップへの KML データの追加](#add-kml-data-to-the-map)
* [描画ツールの追加](#add-drawing-tools)

### <a name="load-a-map"></a>マップを読み込む

どちらの SDK も、マップの読み込みは、同じ一連の手順に従って行います。

* Map SDK への参照を追加します。
* マップのプレースホルダーとして機能する `div` タグをページの本文に追加します。
* ページが読み込まれたときに呼び出される JavaScript 関数を作成します。
* それぞれのマップ クラスのインスタンスを作成します。

**いくつかの主な相違点**

* Bing 地図では、API のスクリプト参照で、またはマップ オプションとして、アカウント キーを指定する必要があります。 Azure Maps の認証資格情報は、マップ クラスのオプションとして指定されます。 これは、サブスクリプション キーまたは Azure Active Directory 情報にすることができます。
* Bing 地図では、API のスクリプト参照でコールバック関数を受け取ります。これは、マップを読み込むために初期化関数を呼び出す際に使用されます。 Azure Maps では、ページの onload イベントを使用する必要があります。
* マップがレンダリングされる `div` 要素を ID で参照するとき、Bing 地図では HTML セレクター (`#myMap`) が使用されるのに対し、Azure Maps では ID 値のみ (`myMap`) が使用されます。
* Azure Maps の座標は、`[longitude, latitude]` 形式のシンプルな数値配列として指定できる位置オブジェクトとして定義されます。
* Azure Maps のズーム レベルは、Bing 地図の例よりも 1 つ下のレベルとなります。これは、プラットフォーム間のタイリング システム サイズが異なるためです。
* 既定では、Azure Maps で、ズーム ボタンやマップ スタイル ボタンなどのナビゲーション コントロールはマップ キャンバスに追加されません。 しかし、マップ スタイル ピッカー、ズーム ボタン、コンパスまたは回転コントロール、およびピッチ コントロールを追加するためのコントロールがあります。
* イベント ハンドラーは、マップ インスタンスの `ready` イベントを監視するために Azure Maps に追加されます。 これは、マップで WebGL コンテキストおよび必要なすべてのリソースの読み込みが完了したときに発生します。 このイベント ハンドラーでは、任意のポスト ロード コードを追加できます。

以下の例では、ニューヨークが中心の基本マップの読み込み方法が示されています。これは、座標の経度が -73.985、緯度が 40.747 で、ズーム レベルが 12 の Bing 地図の場合です。

**前: Bing 地図**

次のコードは、ある場所を中心にズームされた Bing 地図を表示する方法の例です。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

ブラウザーでこのコードを実行すると、次のイメージのようなマップが表示されます。

![Bing 地図のマップ](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)

**後: Azure Maps**

次のコードは、Azure Maps で同じビューのマップを、マップ スタイル コントロールおよびズーム ボタンと共に読み込む方法を示しています。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                        new atlas.control.StyleControl(),
                        new atlas.control.ZoomControl()
                    ], {
                        position: 'top-right'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

ブラウザーでこのコードを実行すると、次のイメージのようなマップが表示されます。

![Azure Maps のマップ](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)

Web アプリで Azure Maps マップ コントロールを設定して使用する方法に関する詳細なドキュメントについては、[こちら](how-to-use-map-control.md)を参照してください。

> [!TIP]
> Azure Maps では、縮小されたバージョンと縮小されていないバージョン両方の SDK が公開されています。 ファイル名から `.min` を削除してください。 縮小されていないバージョンは問題をデバッグする際に役立ちますが、運用環境では必ず、縮小されたバージョンを使用してください。その方がファイル サイズが小さくて済みます。

**その他のリソース**

* Azure Maps では、マップ ビューの回転とピッチを行うためのナビゲーション コントロールも提供されます ([こちら](map-add-controls.md)を参照してください)。

### <a name="localizing-the-map"></a>マップのローカライズ

対象ユーザーが複数の国に分散している場合や、使用されている言語が異なる場合は、ローカライズが重要になります。

**前: Bing 地図**

Bing 地図をローカライズするために、API の `<script>` タグ参照には `UR` パラメーターが追加され、`setLang` を使用して言語と地域が指定されています。 Bing 地図の一部の機能は、特定の市場でのみ利用できす。たとえば、ユーザーの市場は、`setMkt` パラメーターを使用して指定します。

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

以下に、言語が "fr-FR" に設定されている Bing 地図の例を示します。

![ローカライズされた Bing 地図のマップ](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

**後: Azure Maps**

Azure Maps には、マップの言語と地域ビューを設定するためのオプションのみが用意されています。 市場パラメーターは、機能を制限するためには使用されません。 マップの言語と地域ビューを設定するための 2 つの異なる方法が用意されています。 最初のオプションでは、この情報をグローバルな `atlas` 名前空間に追加します。これにより、アプリ内のすべてのマップ コントロール インスタンスにより、これらの設定が既定で使用されます。 以下では、言語をフランス語 ("fr-FR") に、地域ビューを `"Auto"` に設定します。

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

2 つ目のオプションでは、次のようにマップを読み込むときに、この情報をマップ オプションに渡します。

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Azure Maps を使用すると、言語と地域の設定が異なる複数のマップ インスタンスを同じページに読み込むことができます。 また、マップでは読み込み後に、これらの設定を更新することもできます。 Azure Maps でサポートされている言語の詳細な一覧については、[こちら](./supported-languages.md)を参照してください。

言語が "fr" に設定され、ユーザーの地域が "fr-FR" に設定されている Azure Maps の例を以下に示します。

![ローカライズされた Azure Maps のマップ](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

### <a name="setting-the-map-view"></a>マップ ビューの設定

Bing 地図と Azure Maps の動的マップはどちらも、JavaScript で適切な関数を呼び出すことによって、プログラムで新しい地理的な場所に移動できます。 以下の例では、マップに衛星航空画像を表示し、マップの中心をある場所に指定する方法を示しています。これは、座標の経度が -111.0225、緯度が 35.0272 で、ズーム レベルを 15 に変更する Bing 地図の場合です。

> [!NOTE]
> Bing 地図では、ディメンションが 256 ピクセルのタイルが使用されますが、Azure Maps では、より大きな 512 ピクセルのタイルが使用されます。 これにより、Azure Maps で Bing 地図と同じマップ領域を読み込むために必要なネットワーク要求の数が減ります。 しかし、マップ コントロールでのタイル ピラミッドの動作方法により、Azure Maps のより大きなタイルは、Bing 地図のマップと同じ可視領域を得るために、Azure Maps の使用時に Bing 地図で利用されるズーム レベルを 1 ずつ減算する必要があることを意味します。

**前: Bing 地図**

Bing 地図のマップ コントロールは、マップの中心とズーム レベルを指定できる `setView` 関数を使用してプログラムで移動できます。

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

![Bing 地図のマップ ビュー設定](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)

**後: Azure Maps**

Azure Maps では、マップの `setCamera` 関数を使用して、マップの位置をプログラムで変更し、`setStyle` 関数を使用して、マップ スタイルを変更することができます。 Azure Maps 内の座標は "経度, 緯度" の形式であり、ズーム レベルの値は 1 ずつ減算されることに注意してください。

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

![Azure Maps のマップ ビュー設定](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)

**その他のリソース**

-   [マップ スタイルを選択する](./choose-map-style.md)
-   [サポートされているマップ スタイル](./supported-map-styles.md)

### <a name="adding-a-pushpin"></a>画鋲の追加

Azure Maps には、ポイント データをマップ上にレンダリングできる方法が複数あります。

* HTML マーカー – 従来の DOM 要素を使用してポイントをレンダリングします。 HTML マーカーではドラッグがサポートされます。
* シンボル レイヤー – WebGL コンテキスト内でアイコンやテキストを使用してポイントをレンダリングします。
* バブル レイヤー - ポイントをマップ上で円としてレンダリングします。 円の半径は、データのプロパティに基づいてスケーリングできます。

シンボルおよびバブル レイヤーの両方は、WebGL コンテキスト内にレンダリングされ、マップ上で非常に大きなポイント セットをレンダリングできます。 これらのレイヤーでは、データをデータ ソースに格納する必要があります。 `ready` イベントが発生した後、データ ソースとレンダリング レイヤーをマップに追加する必要があります。 HTML マーカーは、ページ内に DOM 要素としてレンダリングされ、データ ソースは使用されません。 ページに含まれる DOM 要素が多いほど、そのページの速度は遅くなります。 マップ上で数百を超えるポイントをレンダリングする場合は、代わりにレンダリング レイヤーのいずれかを使用することをお勧めします。

以下の例では、マップにマーカーを追加します。ここでは、経度が -0.2、緯度が 51.5 で、オーバーレイ ラベルの数値は 10 になっています。

**前: Bing 地図**

Bing 地図では、`Microsoft.Maps.Pushpin` クラス*を使用して、マップにマーカーを追加します。 そのうえで、2 つの関数のどちらかを使用してマップに画鋲を追加します。

1 つ目の関数では、レイヤーを作成し、そこに画鋲を挿入して、マップの `layers` プロパティにレイヤーを追加します。

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

2 つ目は、マップの `entities` プロパティを使用して追加するものです。 この関数は、Bing 地図 V8 のドキュメントでは非推奨化のマークが付いていますが、基本的なシナリオであれば、ある程度機能します。

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

![画鋲を追加する (Bing 地図)](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)

**後: HTML マーカーを使用した Azure Maps**

Azure Maps では、HTML マーカーを使用してマップ上のポイントを簡単に表示することができます。これは、マップ上に表示するポイントが少数のみであるシンプルなアプリにおすすめです。 HTML マーカーを使用するには、`atlas.HtmlMarker` クラスのインスタンスを作成して、テキストおよび位置のオプションを設定し、`map.markers.add` 関数を使用してマップにマーカーを追加します。

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![マーカーを追加する (Azure Maps)](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**後: シンボル レイヤーを使用した Azure Maps**

シンボル レイヤーを使用する場合は、データをデータ ソースに追加し、データ ソースをレイヤーにアタッチする必要があります。 さらに、`ready` イベントが発生した後、データ ソースとレイヤーをマップに追加する必要があります。 一意のテキスト値をシンボルの上にレンダリングするには、テキスト情報をデータ ポイントのプロパティとして格納し、そのプロパティをレイヤーの `textField` オプションで参照する必要があります。 これは HTML マーカーを使用するよりも少し手間がかかりますが、多くのパフォーマンスの利点が得られます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![シンボル レイヤーを追加する (Azure Maps)](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**その他のリソース**

-   [データ ソースを作成する](./create-data-source-web-sdk.md)
-   [シンボル レイヤーを追加する](./map-add-pin.md)
-   [バブル レイヤーを追加する](./map-add-bubble-layer.md)
-   [ポイント データをクラスタリングする](./clustering-point-data-web-sdk.md)
-   [HTML マーカーを追加する](./map-add-custom-html.md)
-   [データドリブンのスタイルの式を使用する](./data-driven-style-expressions-web-sdk.md)
-   [シンボル レイヤーのアイコン オプション](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [シンボル レイヤーのテキスト オプション](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML マーカーのクラス](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML マーカーのオプション](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>カスタム画鋲の追加

カスタム イメージを使用すると、マップ上のポイントを表すことができます。 以下のイメージ例では、カスタム イメージを使用してマップ上のポイントを表示します。ここでは、緯度が 51.5、経度が -0.2 で、マーカーの位置をオフセットして、画鋲アイコンのポイントがマップ上の正しい位置に揃うようにします。

| ![画鋲を追加する (Azure Maps)](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |

**前: Bing 地図**

Bing 地図では、画鋲の `icon` オプションに画像の URL を渡すことでカスタム マーカーを作成します。 画鋲の画像のポイントは、`anchor` オプションを使用して、マップ上の座標に合わせます。 Bing 地図のアンカー値は、画像の左上隅を基準とします。

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

![カスタム画鋲を追加する (Bing 地図)](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)

**後: HTML マーカーを使用した Azure Maps**

Azure Maps で HTML マーカーをカスタマイズする場合は、HTML `string` または `HTMLElement` をマーカーの `htmlContent` オプションに渡すことができます。 Azure Maps では、`anchor` オプションを使用して、9 つの定義済みの参照ポイントである "center"、"top"、"bottom"、"left"、"right"、"top-left"、"top-right"、"bottom-left"、"bottom-right" のいずれかを使用して、位置座標を基準とするマーカーの相対位置を指定します。 コンテンツは固定され、既定では "bottom" (html コンテンツの下中央) に設定されます。 Bing 地図からコードをより簡単に移行できるようにするには、アンカーを "top-left" に設定してから、Bing 地図で使用したものと同じオフセットで `offset` オプションを使用します。 Azure Maps のオフセットは、Bing 地図の反対方向に移動するため、-1 を乗算します。

> [!TIP]
> HTML コンテンツにスタイルとして `pointer-events:none` を追加して、不要なアイコンを表示する、MS Edge での既定のドラッグ動作を無効にします。

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![カスタム マーカーを追加する (Azure Maps)](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)

**後: シンボル レイヤーを使用した Azure Maps**

Azure Maps のシンボル レイヤーでもカスタム イメージがサポートされますが、最初にイメージをマップ リソースに読み込んで、一意の ID を割り当てる必要があります。 その後、シンボル レイヤーでこの ID を参照できます。 アイコンの `offset` オプションを使用して、イメージ上の正しいポイントに合わせるために、シンボルをオフセットできます。 Azure Maps では、`anchor` オプションを使用して、9 つの定義済みの参照ポイントである "center"、"top"、"bottom"、"left"、"right"、"top-left"、"top-right"、"bottom-left"、"bottom-right" のいずれかを使用して、位置座標を基準とするシンボルの相対位置を指定します。 コンテンツは固定され、既定では "bottom" (HTML コンテンツの下中央) に設定されます。 Bing 地図からコードをより簡単に移行できるようにするには、アンカーを "top-left" に設定してから、Bing 地図で使用したものと同じオフセットで `offset` オプションを使用します。 Azure Maps のオフセットは、Bing 地図の反対方向に移動するため、-1 を乗算します。

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![カスタム シンボル レイヤーを追加する (Bing 地図)](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)

> [!TIP]
> ポイントの高度なカスタム レンダリングを作成するには、複数のレンダリング レイヤーを一緒に使用します。 たとえば、異なる色付きの円で同じアイコンの画鋲を複数使用する場合は、色ごとに多くのイメージを作成するのではなく、バブル レイヤーの上にシンボル レイヤーをオーバーレイし、同じデータ ソースを参照するようにします。 これは作成するよりもはるかに効率的であり、マップに多くのさまざまなイメージが保持されます。

**その他のリソース**

-   [データ ソースを作成する](./create-data-source-web-sdk.md)
-   [シンボル レイヤーを追加する](./map-add-pin.md)
-   [HTML マーカーを追加する](./map-add-custom-html.md)
-   [データドリブンのスタイルの式を使用する](./data-driven-style-expressions-web-sdk.md)
-   [シンボル レイヤーのアイコン オプション](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [シンボル レイヤーのテキスト オプション](/javascript/api/azure-maps-control/atlas.textoptions)
-   [HTML マーカーのクラス](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [HTML マーカーのオプション](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>ポリラインの追加

ポリラインは、マップ上の線またはパスを表すために使用されます。 以下の例では、マップ上に破線のポリラインを作成する方法を示します。

**前: Bing 地図**

Bing 地図では、Polyline クラスに場所の配列と一連のオプションを渡します。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

![Bing 地図のポリライン](media/migrate-bing-maps-web-app/bing-maps-line.jpg)

**後: Azure Maps**

Azure Maps におけるポリラインは、`LineString` オブジェクトまたは `MultiLineString` オブジェクトという、地理空間に関するより一般的な用語で呼ばれています。 これらのオブジェクトは、データ ソースに追加し、線レイヤーを使用してレンダリングできます。 ストロークの色と幅、そして破線の配列オプションは、どのプラットフォームもほぼ同じです。

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

![Azure Maps の線](media/migrate-bing-maps-web-app/azure-maps-line.jpg)

**その他のリソース**

-   [マップに線を追加する](./map-add-line-layer.md)
-   [線レイヤーのオプション](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [データドリブンのスタイルの式を使用する](./data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>多角形の追加

多角形は、マップ上の領域を表すために使用されます。 Azure Maps と Bing 地図では、多角形について、よく似たサポートが提供されます。 以下の例では、マップの中心座標に基づいて、三角形を形成する多角形を作成する方法を示しています。

**前: Bing 地図**

Bing 地図では、Polygon クラスに、座標の配列 (座標環) と一連のオプションを渡します。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

![Bing 地図の多角形](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**後: Azure Maps**

Azure Maps では、Polygon および MultiPolygon オブジェクトをデータ ソースに追加し、レイヤーを使用してマップ上にレンダリングすることができます。 多角形の領域は、多角形レイヤーでレンダリングできます。 多角形の枠線は、線レイヤーを使用してレンダリングできます。

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

![Azure Maps の多角形](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**その他のリソース**

-   [マップに多角形を追加する](./map-add-shape.md#use-a-polygon-layer)
-   [マップに円を追加する](./map-add-shape.md#add-a-circle-to-the-map)
-   [多角形レイヤーのオプション](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [線レイヤーのオプション](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [データドリブンのスタイルの式を使用する](./data-driven-style-expressions-web-sdk.md)

### <a name="display-an-infobox"></a>情報ボックスの表示

エンティティの追加情報は、Bing 地図では `Microsoft.Maps.Infobox` クラスとしてマップに表示できます。Azure Maps では、`atlas.Popup` クラスを使用して、これを行うことができます。 以下の例は、マップに画鋲またはマーカーを追加し、それがクリックされたときに情報ボックス (ポップアップ) を表示するものです。

**前: Bing 地図**

Bing 地図では、`Microsoft.Maps.Infobox` コンストラクターを使用して、情報ボックスが作成されます。

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

![Bing 地図の情報ボックス](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)

**後: Azure Maps**

Azure Maps では、ポップアップを使用して、場所の追加情報を表示できます。 HTML の `string` または `HTMLElement` オブジェクトを、ポップアップの `content` オプションに渡すことができます。 ポップアップは、必要に応じて、図形とは別に表示できるため、`position` 値を指定する必要があります。 ポップアップを表示するには、`open` 関数を呼び出し、ポップアップの表示先となるマップを渡します。

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

![Azure Maps のポップアップ](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)

> [!NOTE]
> シンボル、バブル、線または多角形のレイヤーで同じことを行うには、マーカーではなく、マップのイベント コードにレイヤーを渡します。

**その他のリソース**

-   [ポップアップを追加する](./map-add-popup.md)
-   [メディア コンテンツを含むポップアップ](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [図形のポップアップ](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [複数のピンでのポップアップの再利用](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [ポップアップのクラス](/javascript/api/azure-maps-control/atlas.popup)
-   [ポップアップのオプション](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>画鋲クラスタリング

多数のデータ ポイントをマップ上に視覚化すると、ポイントが互いに重なり合い、マップの見た目がごちゃごちゃして、見づらく使いづらいものになります。 ポイント データのクラスタリングは、このユーザー エクスペリエンスを向上させ、パフォーマンスを向上させるためにも使用できます。 ポイント データのクラスタリングは、互いに近いポイント データを結合し、単一のクラスター化されたデータ ポイントとしてマップ上に表現するプロセスです。 ユーザーがマップにズーム インすると、クラスターは個々のデータ ポイントに分解します。

以下の例では、過去 1 週間の地震データの GeoJSON フィードを読み込み、それをマップに追加します。 クラスターは、含まれているポイントの数に応じて、スケーリングされた色付きの円としてレンダリングされます。

> [!NOTE]
> 画鋲クラスタリングには、いくつかの異なるアルゴリズムが使用されています。 Bing 地図では単純なグリッドベースの関数が使用されるのに対し、Azure Maps では、より高度で見栄えの良いポイントベースのクラスタリング メソッドが使用されます。

**前: Bing 地図**

Bing 地図では、GeoJSON モジュールを使用して GeoJSON データを読み込むことができます。 画鋲をクラスタリングするには、クラスタリング モジュールを読み込み、そこに含まれるクラスタリング レイヤーを使用します。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing 地図のクラスタリング](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)

**後: Azure Maps**

Azure Maps では、データはデータ ソースによって追加および管理されます。 レイヤーはデータ ソースに接続され、そこでデータがレンダリングされます。 Azure Maps の `DataSource` クラスでは、いくつかのクラスタリング オプションが提供されます。

* `cluster` – データ ソースにポイント データをクラスタリングするように指示します。 
* `clusterRadius` -ポイントをまとめてクラスタリングする場合の半径 (ピクセル単位)。
* `clusterMaxZoom` - クラスタリングが行われる最大ズーム レベル。 これを超えてズームインすると、すべてのポイントがシンボルとしてレンダリングされます。
* `clusterProperties` - 各クラスター内のすべてのポイントに対して式を使用して計算され、各クラスター ポイントのプロパティに追加されるカスタム プロパティを定義します。

クラスタリングが有効になっている場合、データ ソースでは、クラスタリングおよびクラスタリング解除されたデータ ポイントをレンダリングするためにレイヤーに送信します。 データ ソースでは、数十万のデータ ポイントをクラスタリングすることができます。 クラスタリングされたデータ ポイントには、次のプロパティがあります。

| プロパティ名               | Type    | 説明                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | フィーチャーがクラスターを表すかどうかを示します。     |
| `cluster_id`                | string  | `DataSource` クラスの関数である `getClusterExpansionZoom`、`getClusterChildren`、`getClusterLeaves` で使用できるクラスターの一意の ID。 |
| `point_count`               | number  | クラスターに含まれているポイントの数。     |
| `point_count_abbreviated`   | string  | `point_count` の値が長い場合にその値を省略形にした文字列。 (たとえば、4,000 が 4K になります) |

`DataSource` クラスには、`cluster_id` を使用してクラスターに関する追加情報にアクセスするための次のヘルパー関数があります。

| 機能       | の戻り値の型 :        | 説明     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | 次のズーム レベルで指定されたクラスターの子を取得します。 これらの子はシェイプとサブクラスターの組み合わせの場合があります。 サブクラスターはクラスターのプロパティと一致するプロパティを備えた機能になります。 |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | クラスターが拡大し始めるか、分解するズーム レベルを計算します。    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | クラスター内のすべてのポイントを取得します。 ポイントのサブセットを返すには `limit` を設定し、ポイントをページ送りするには `offset` を使用します。    |

クラスター化されたデータをマップ上にレンダリングする際、多くの場合は、2 つ以上のレイヤーを使用するのが最も簡単です。 以下の例では、3 つのレイヤーを使用します。つまり、クラスターのサイズに基づいてスケーリングされた色付き円を描画するためのバブル レイヤー、クラスター サイズをテキストとしてレンダリングするためのシンボル レイヤー、およびクラスタリング解除されたポイントをレンダリングするための 2 番目のシンボル レイヤーです。 Azure Maps でクラスタリングされたデータをレンダリングする方法は他にも多数あります (詳しくは、[ポイント データのクラスタリング](./clustering-point-data-web-sdk.md)に関するドキュメントを参照)。

GeoJSON データは、`DataSource` クラスの `importDataFromUrl` 関数を使用して、Azure Maps に直接インポートできます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps のクラスタリング](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)

**その他のリソース**

-   [シンボル レイヤーを追加する](./map-add-pin.md)
-   [バブル レイヤーを追加する](./map-add-bubble-layer.md)
-   [ポイント データをクラスタリングする](./clustering-point-data-web-sdk.md)
-   [データドリブンのスタイルの式を使用する](./data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>ヒート マップを追加する

ヒート マップは、点密度マップとも呼ばれ、一定の範囲の色を使用して、データの密度を表すために使用されるデータ視覚化の一種です。 多くの場合、マップ上のデータの "ホットスポット" を示すために使用され、大規模なポイント データ セットをレンダリングする場合に適しています。

以下の例では、USGS から過去 1 か月間のすべての地震の GeoJSON フィードを読み込み、それらをヒート マップとしてレンダリングします。

**前: Bing 地図**

Bing 地図でヒート マップを作成するには、ヒート マップ モジュールを読み込みます。 同様に、GeoJSON データに対応するために GeoJSON モジュールを読み込みます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing 地図のヒートマップ](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)

**後: Azure Maps**

Azure Maps で、GeoJSON データをデータ ソースに読み込み、そのデータ ソースをヒート マップ レイヤーに接続します。 GeoJSON データは、`DataSource` クラスの `importDataFromUrl` 関数を使用して、Azure Maps に直接インポートできます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps のヒートマップ](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)

**その他のリソース**

-   [ヒート マップ レイヤーを追加する](./map-add-heat-map-layer.md)
-   [ヒート マップ レイヤーのクラス](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [ヒート マップ レイヤーのオプション](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [データドリブンのスタイルの式を使用する](./data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>タイル レイヤーをオーバーレイする

タイル レイヤーを使用すると、マップ タイル システムに合わせて、より小さなタイル表示されたイメージに分割されたより大きなイメージをオーバーレイすることができます。 これは、大きな画像や非常に大きなデータ セットをオーバーレイする一般的な方法です。

以下の例では、"X、Y、ズーム" のタイル名前付けスキーマを使用するアイオワ州立大学の Iowa Environmental Mesonet の気象レーダー タイル レイヤーをオーバーレイします。

**前: Bing 地図**

Bing 地図では、`Microsoft.Maps.TileLayer` クラスを使用して、タイル レイヤーを作成できます。

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

![Bing 地図の加重ヒートマップ](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)

**後: Azure Maps**

Azure Maps では、他のレイヤーとほぼ同じように、タイル レイヤーをマップに追加できます。 x、y、ズーム プレースホルダー(`{x}`、`{y}`、`{z}`) の書式設定された URL はそれぞれ、タイルにアクセスする場所をレイヤーに指示するために使用されます。 Azure Maps のタイル レイヤーでは、`{quadkey}`、`{bbox-epsg-3857}` および `{subdomain}` プレースホルダーもサポートされます。

> [!TIP]
> Azure Maps では、レイヤーは、基本マップ レイヤーを含む、他のレイヤーの下に簡単にレンダリングできます。 多くの場合、読みやすいようにタイル レイヤーをマップ ラベルの下にレンダリングすることをお勧めします。 `map.layers.add` 関数には第 2 パラメーターがあります。これは、2 つ目のレイヤーの ID です。新しいレイヤーはこの下に挿入されます。 マップ ラベルの下にタイル レイヤーを挿入する場合は、次のコードを使用できます。
>
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

![Azure Maps の加重ヒートマップ](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)

> [!TIP]
> タイル要求は、マップの `transformRequest` オプションを使用してキャプチャできます。 これにより、必要に応じて、要求に対してヘッダーの変更や追加を行うことができます。

**その他のリソース**

-   [タイル レイヤーを追加する](./map-add-tile-layer.md)
-   [タイル レイヤーのクラス](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [タイル レイヤーのオプション](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>トラフィック データを表示する

交通データは、Bing と Azure のどちらのマップにもオーバーレイできます。

**前: Bing 地図**

Bing 地図では、交通モジュールを使用して、交通データをマップにオーバーレイすることができます。

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

![Bing 地図の交通情報](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)

**後: Azure Maps**

Azure Maps では、交通情報を表示するためのさまざまなオプションが提供されます。 道路の閉鎖や事故などの交通事故は、マップ上にアイコンとして表示できます。 交通流量 (色分けされた道路) をマップ上にオーバーレイでき、掲示された速度制限、通常の予想される遅延、または絶対遅延を基準として、色を変更することができます。 Azure Maps の事故データは 1 分ごとに、流量データは 2 分ごとに更新されます。

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

![Azure Maps の交通情報](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)

Azure Maps の交通アイコンのいずれかをクリックすると、ポップアップに追加情報が表示されます。

![Azure Maps の交通情報のポップアップ](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)

**その他のリソース**

-   [マップ上にトラフィックを表示する](./map-show-traffic.md)
-   [交通情報オーバーレイのオプション](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [トラフィック コントロール](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>グラウンド オーバーレイを追加する

Bing と Azure のどちらのマップも、マップのパンとズームを行うときに移動および拡大縮小できるように、マップ上のジオリファレンスされた画像をオーバーレイすることがサポートされています。 Bing 地図では、これらはグラウンド オーバーレイとして知られており、Azure Maps では、画像レイヤーと呼ばれます。 これらは、ビルのフロア プラン、古いマップのオーバーレイ、またはドローンからのイメージに適しています。

**前: Bing 地図**

Bing 地図でグラウンド オーバーレイを作成する場合は、オーバーレイする画像の URL と、マップ上の画像をバインドするための境界ボックスを指定する必要があります。 この例では、マップ上に [1922 年のニュージャージー州のニューアーク](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)のマップ イメージをオーバーレイします。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

ブラウザーでこのコードを実行すると、次のイメージのようなマップが表示されます。

![Bing 地図のグラウンド オーバーレイ](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)

**後: Azure Maps**

Azure Maps では、`atlas.layer.ImageLayer` クラスを使用して、ジオリファレンスされたイメージをオーバーレイできます。 このクラスでは、イメージへの URL と、イメージの四隅の座標のセットが必要です。 イメージは、同じドメインでホストされているか、CORS が有効になっている必要があります。

> [!TIP]
> 画像の各隅の座標ではなく、北、南、東、西および回転の情報のみがある場合は、静的な [atlas.layer.ImageLayer.getCoordinatesFromEdges](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) 関数を使用できます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps のグラウンド オーバーレイ](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)

**その他のリソース**

-   [イメージをオーバーレイする](./map-add-image-layer.md)
-   [イメージ レイヤーのクラス](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>マップへの KML データの追加

Azure と Bing のどちらのマップも、KML、KMZ、GeoRSS、GeoJSON、Well-Known Text (WKT) のデータをインポートしてマップ上にレンダリングすることができます。 Azure Maps ではさらに、GPX、GML、空間 CSV ファイル、Web Mapping Services (WMS)、Web Mapping Tile Services (WMTS)、Web Feature Services (WFS) がサポートされます。

**前: Bing 地図**

ブラウザーでこのコードを実行すると、次のイメージのようなマップが表示されます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
                
            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Bing 地図の KML](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)

**後: Azure Maps**

Azure Maps では、GeoJSON が Web SDK で使用される主なデータ形式となりますが、[空間 IO モジュール](/javascript/api/azure-maps-spatial-io/)を使用すれば、その他の空間データ形式も容易に統合することができます。 このモジュールには、空間データの読み取りと書き込みの両方に対応した関数のほか、これらの空間データ形式から簡単にデータをレンダリングできるシンプルなデータ レイヤーが備わっています。 空間データ ファイルのデータを読み取るには、URL を (あるいは生データを文字列または BLOB として) `atlas.io.read` 関数に渡します。 ファイルから解析済みのデータがすべて返されるので、そのデータをマップに追加することができます。 KML は、多くのスタイル情報を含んでいるため、ほとんどの空間データ形式と比べてやや複雑です。 そうしたスタイルの大半のレンダリングは、`SpatialDataLayer` クラスでサポートされますが、地物データを読み込む前にアイコン画像をマップに読み込む必要があり、また、マップには別途レイヤーとしてグラウンド オーバーレイを追加する必要があります。 URL でデータを読み込む場合は、CORS が有効なエンドポイントでデータがホストされているか、または、読み取り関数にオプションとしてプロキシ サービスを渡す必要があります。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('myKMLFile.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps の KML](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)

**その他のリソース**

-   [atlas.io.read 関数](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>描画ツールの追加

Bing 地図と Azure Maps のどちらにも、ユーザーがマウスや他の入力デバイスでマップ上に図形を描画したりそれらを編集したりする機能を追加するモジュールが用意されています。 どちらも、画鋲、線、多角形の描画がサポートされます。 Azure Maps にはさらに、円や四角形を描画するためのオプションがあります。

**前: Bing 地図**

Bing 地図では、`Microsoft.Maps.loadModule` 関数を使用して `DrawingTools` モジュールを読み込みます。 読み込み後、DrawingTools クラスのインスタンスを作成できるので、その `showDrawingManager` 関数を呼び出してマップにツール バーを追加します。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

![Bing 地図の描画ツール](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)

**後: Azure Maps**

Azure Maps では、アプリで参照する必要のある JavaScript ファイルと CSS ファイルを読み込むことによって、描画ツール モジュールを読み込む必要があります。 マップの読み込み後、`DrawingManager` クラスのインスタンスを作成し、`DrawingToolbar` インスタンスをアタッチすることができます。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

![Azure Maps の描画ツール](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)

> [!TIP]
> Azure Maps のレイヤーでは、描画ツールに備わっているさまざまな方法を利用してユーザーが図形を描画できます。 たとえば多角形を描画する場合、ユーザーは、個々のポイントをクリックして追加するか、マウスの左ボタンを押し下げたままマウスをドラッグしてパスを描画することができます。 これは、`DrawingManager` の `interactionType` オプションを使用して変更できます。

**その他のリソース**

-   [ドキュメント](./set-drawing-options.md)
-   [コード サンプル](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="additional-resources"></a>その他のリソース

[オープンソースの Azure Maps Web SDK モジュール](open-source-projects.md#open-web-sdk-modules)をご覧ください。 これらのモジュールは、他にもさまざまな機能を備えており、自在にカスタマイズすることができます。

その他の Bing 地図機能の移行に関連したコード サンプルをご確認ください。

**データ可視化**

> [!div class="nextstepaction"]
> [等高線レイヤー](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [データのビン分割](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**サービス**

> [!div class="nextstepaction"]
> [Azure Maps サービス モジュールの使用](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [観光名所の検索](./map-search-location.md)

> [!div class="nextstepaction"]
> [座標から情報を取得する (逆ジオコード)](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [A から B までのルートを表示する ](./map-route.md)

> [!div class="nextstepaction"]
> [JQuery UI を使用する自動提案検索](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Azure Maps Web SDK の詳細について学習します。

> [!div class="nextstepaction"]
> [マップ コントロールの使用方法](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [サービス モジュールの使用方法](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [描画ツール モジュールの使用方法](set-drawing-options.md)

> [!div class="nextstepaction"]
> [コード サンプル](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Azure Maps Web SDK サービス API のリファレンス ドキュメント](/javascript/api/azure-maps-control/)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップすべきリソースはありません。

## <a name="next-steps"></a>次のステップ

Bing 地図から Azure Maps への移行について理解を深めます。

> [!div class="nextstepaction"]
> [Web サービスを移行する](migrate-from-bing-maps-web-services.md)