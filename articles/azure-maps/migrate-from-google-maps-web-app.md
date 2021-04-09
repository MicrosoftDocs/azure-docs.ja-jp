---
title: チュートリアル - Google マップから Web アプリを移行する | Microsoft Azure Maps
description: Google Maps から Microsoft Azure Maps に Web アプリを移行する方法についてのチュートリアルです。
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: fcb8090427530271600a6699fafa5c488c426784
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680877"
---
# <a name="tutorial-migrate-a-web-app-from-google-maps"></a>チュートリアル:Google Maps から Web アプリを移行する

Google マップを使用するほとんどの Web アプリでは、Google Maps V3 JavaScript SDK が使用されています。 Azure Maps Web SDK は、移行に適した Azure ベースの SDK です。 Azure Maps Web SDK では、独自のコンテンツや映像を使用して、インタラクティブ マップをカスタマイズすることができます。 Web アプリケーションとモバイル アプリケーションの両方でアプリを実行することができます。 このコントロールには、WebGL が利用されているため、大きなデータ セットを高いパフォーマンスでレンダリングすることができます。 JavaScript または TypeScript を使用して、この SDK での開発を行います。 このチュートリアルでは、次の内容を学習します。

> [!div class="checklist"]
> * マップを読み込む
> * マップをローカライズする
> * マーカー、ポリライン、およびポリゴンを追加する。
> * ポップアップ ウィンドウまたは情報ウィンドウに情報を表示する
> * KML と GeoJSON のデータを読み込んで表示する
> * クラスター マーカー
> * タイル レイヤーをオーバーレイする
> * トラフィック データを表示する
> * グラウンド オーバーレイを追加する

また、次についても学習します。

> [!div class="checklist"]
> * Azure Maps Web SDK を使用して一般的なマッピング タスクを実行する方法。
> * パフォーマンスとユーザー エクスペリエンスを向上させるためのベスト プラクティス。
> * Azure Maps で利用可能なより高度な機能を使用したアプリケーションの作成方法に関するヒント。

既存の Web アプリケーションを移行する場合は、オープンソースのマップ コントロール ライブラリが使用されているかどうかを確認します。 オープンソースのマップ コントロール ライブラリの例としては、Cesium、Leaflet、OpenLayers があります。 オープンソースのマップ コントロール ライブラリがご自分のアプリケーションで使用されていて、Azure Maps Web SDK を使用したくない場合でも、移行することはできます。 その場合は、ご自分のアプリケーションを Azure Maps タイル サービス ([道路タイル](/rest/api/maps/render/getmaptile) \| [衛星タイル](/rest/api/maps/render/getmapimagerytile)) に接続してください。 一般的に使用されるいくつかのオープンソースのマップ コントロール ライブラリで Azure Maps を使用する方法の詳細を以下に示します。

* Cesium - Web 用の 3D マップ コントロール。 [コード サンプル](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [ドキュメント](https://cesiumjs.org/)
* Leaflet – Web 用の軽量な 2D マップ コントロール。 [コード サンプル](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [ドキュメント](https://leafletjs.com/)
* OpenLayers - プロジェクションをサポートする Web 用の 2D マップ コントロール。 [コード サンプル](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [ドキュメント](https://openlayers.org/)

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

次の表は、Google Maps V3 JavaScript SDK の主な API 機能と、Azure Maps Web SDK でサポートされる API 機能の一覧です。

| Google Maps 機能     | Azure Maps Web SDK のサポート |
|-------------------------|:--------------------------:|
| マーカー                 | ✓                          |
| マーカー クラスタリング       | ✓                          |
| ポリラインと多角形    | ✓                          |
| データ レイヤー             | ✓                          |
| グラウンド オーバーレイ         | ✓                          |
| ヒート マップ               | ✓                          |
| タイル レイヤー             | ✓                          |
| KML レイヤー               | ✓                          |
| 描画ツール           | ✓                          |
| ジオコーダー サービス        | ✓                          |
| 方向サービス      | ✓                          |
| 距離マトリックス サービス | ✓                          |
| 標高サービス       | ✓                          |

## <a name="notable-differences-in-the-web-sdks"></a>Web SDK の注目すべき相違点

注意すべき Google マップと Azure Maps の Web SDK の主な相違点をいくつか以下に示します。

- Azure Maps Web SDK にアクセスするためのホステッド エンドポイントが提供されるだけでなく、NPM パッケージも使用可能です。 Web SDK パッケージをアプリに埋め込むことができます。 詳しくは、こちらの[ドキュメント](how-to-use-map-control.md)をご覧ください。 このパッケージには TypeScript 定義も含まれています。
- まず、Azure Maps で Map クラスのインスタンスを作成する必要があります。 マップの `ready` または `load` イベントが発生するのを待ってから、プログラムでマップを操作してください。 この順序により、確実にマップ リソースがすべて読み込まれ、アクセスできる状態になります。
- どちらのプラットフォームも、基本マップには同様のタイリング システムが使用されています。 Google マップのタイルのディメンションは 256 ピクセルですが、Azure Maps のタイルのディメンションは 512 ピクセルとなります。 Google マップと同じマップ ビューを Azure Maps で取得するには、Azure Maps では、Google マップのズーム レベルから 1 減らします。
- Google マップでは座標は `latitude,longitude` と呼ばれますが、Azure Maps では `longitude,latitude` が使用されます。 Azure Maps の形式は、ほとんどの GIS プラットフォームが準拠している標準の `[x, y]` に合わせてあります。
- Azure Maps Web SDK の図形は、GeoJSON スキーマに基づいています。 ヘルパー クラスは、["*atlas.data*" 名前空間](/javascript/api/azure-maps-control/atlas.data)を通じて公開されます。 また、[*atlas.Shape*](/javascript/api/azure-maps-control/atlas.shape) というクラスもあります。 このクラスを使用すると、GeoJSON オブジェクトをラップし、データ バインド可能な方法で簡単に更新、保守できます。
- Azure Maps の座標は、位置オブジェクトとして定義されます。 座標は、`[longitude,latitude]` 形式の数値配列として指定されます。 または、新しい atlas.data.Position(経度, 緯度) を使用して指定されます。
    > [!TIP]
    > Position クラスには、"緯度, 経度" 形式の座標をインポートするための静的ヘルパー メソッドがあります。 [atlas.data.Position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) メソッドは、多くの場合、Google Maps のコード内の `new google.maps.LatLng` メソッドと置き換えることができます。
- マップに追加される各図形にスタイル設定情報を指定するのではなく、Azure Maps によってデータからスタイルが分離されます。 データはデータ ソースに格納され、レンダリング レイヤーに接続されます。 Azure Maps のコードはデータ ソースを使用してそのデータをレンダリングします。 この手法では、パフォーマンスが向上します。 さらに、多くのレイヤーでは、データドリブンのスタイル設定がサポートされ、ビジネス ロジックをレイヤー スタイル オプションに追加することができます。 このサポートにより、図形で定義されているプロパティに基づいてレイヤー内で個々の図形をレンダリングする方法が変更されます。

## <a name="web-sdk-side-by-side-examples"></a>Web SDK のサイドバイサイドの例

このコレクションには各プラットフォーム用のコード サンプルが用意されており、それぞれが一般的なユース ケースを対象としています。 これは、Google Maps V3 JavaScript SDK から Azure Maps Web SDK への Web アプリケーションの移行をサポートすることを目的としています。 Web アプリケーションに関連するコード サンプルは JavaScript で提供されています。 ただし、Azure Maps では、[NPM モジュール](how-to-use-map-control.md)を通じて、追加オプションとして TypeScript 定義も提供しています。

**トピック**

* [マップを読み込む](#load-a-map)
* [マップのローカライズ](#localizing-the-map)
* [マップ ビューの設定](#setting-the-map-view)
* [マーカーの追加](#adding-a-marker)
* [カスタム マーカーの追加](#adding-a-custom-marker)
* [ポリラインの追加](#adding-a-polyline)
* [多角形の追加](#adding-a-polygon)
* [情報ウィンドウを表示する](#display-an-info-window)
* [GeoJSON ファイルをインポートする](#import-a-geojson-file)* 
* [マーカー クラスタリング](#marker-clustering)
* [ヒート マップを追加する](#add-a-heat-map)
* [タイル レイヤーをオーバーレイする](#overlay-a-tile-layer)
* [トラフィック データを表示する](#show-traffic-data)
* [グラウンド オーバーレイを追加する](#add-a-ground-overlay)
* [マップに KML データを追加する](#add-kml-data-to-the-map)

### <a name="load-a-map"></a>マップを読み込む

どちらの SDK も、マップを読み込む手順は同じです。

* Map SDK への参照を追加します。
* マップのプレースホルダーとして機能する `div` タグをページの本文に追加します。
* ページが読み込まれたときに呼び出される JavaScript 関数を作成します。
* それぞれのマップ クラスのインスタンスを作成します。

**いくつかの主な相違点**

* Google Maps では、API のスクリプト参照でアカウント キーを指定する必要があります。 Azure Maps の認証資格情報は、マップ クラスのオプションとして指定されます。 この資格情報には、サブスクリプション キーまたは Azure Active Directory 情報を指定できます。
* Google マップでは、API のスクリプト参照でコールバック関数を受け取ります。これは、マップを読み込むために初期化関数を呼び出すために使用されます。 Azure Maps では、ページの onload イベントを使用する必要があります。
* マップがレンダリングされる `div` 要素を参照する場合、Azure Maps の `Map` クラスには `id` 値のみが必要ですが、Google Maps では `HTMLElement` オブジェクトが必要です。
* Azure Maps の座標は、`[longitude, latitude]` 形式のシンプルな数値配列として指定できる、位置オブジェクトとして定義されます。
* Azure Maps のズーム レベルは、Google Maps のズーム レベルよりも 1 つ下のレベルとなります。 この違いは、この 2 つのプラットフォームのタイル システムのサイズが異なることが原因です。
* Azure Maps では、ナビゲーション コントロールがマップ キャンバスに追加されません。 そのため、既定ではマップにズーム ボタンやマップ スタイル ボタンがありません。 しかし、マップ スタイル ピッカー、ズーム ボタン、コンパスまたは回転コントロール、およびピッチ コントロールを追加するためのコントロール オプションがあります。
* イベント ハンドラーは、マップ インスタンスの `ready` イベントを監視するために Azure Maps に追加されます。 このイベントは、マップで WebGL コンテキストと必要なすべてのリソースの読み込みが完了したときに発生します。 マップの読み込みが完了した後に実行したいコードがあれば、このイベント ハンドラーに追加してください。

以下の基本的な例では、Google マップを使用して、ニューヨークが座標の中心に来るようにマップを読み込みます。 経度は -73.985、緯度は 40.747、マップのズーム レベルは 12 です。

#### <a name="before-google-maps"></a>前: Google Maps

ある場所を中心にズームされた Google Map を表示します。

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

ブラウザーでこのコードを実行すると、次のイメージのようなマップが表示されます。

![シンプルな Google Maps](media/migrate-google-maps-web-app/simple-google-map.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps で、同じビューのマップをマップ スタイル コントロールおよびズーム ボタンと共に読み込みます。

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

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
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

![シンプルな Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)

Web アプリで Azure Maps マップ コントロールを設定して使用する方法に関する詳細なドキュメントについては、[こちら](how-to-use-map-control.md)をクリックしてください。

> [!NOTE]
> Google マップとは異なり、Azure Maps では、マップの読み込み時に最初の中心とズーム レベルは必要ありません。 マップの読み込み時にこの情報が指定されていない場合は、ユーザーが存在する都市の特定が Azure Maps によって試行されます。 そこでマップの中心とズームの設定が行われます。

**その他のリソース:**

* Azure Maps では、マップ ビューの回転とピッチを行うためのナビゲーション コントロールも提供されます ([こちら](map-add-controls.md)を参照してください)。

### <a name="localizing-the-map"></a>マップのローカライズ

対象ユーザーが複数の国または地域に分散している場合や、使用されている言語が異なる場合は、ローカライズが重要になります。

#### <a name="before-google-maps"></a>前: Google Maps

Google マップをローカライズするには、言語と地域のパラメーターを追加します。

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

以下に、言語が "fr-fr" に設定されている Google Maps の例を示します。

![Google Maps のローカライズ](media/migrate-google-maps-web-app/google-maps-localization.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps には、マップの言語と地域ビューを設定するための 2 つの異なる方法が用意されています。 最初のオプションでは、この情報をグローバル *atlas* 名前空間に追加します。 これにより、アプリ内のすべてのマップ コントロール インスタンスが、既定でこれらの設定になります。 以下では、言語をフランス語 ("fr-FR") に、地域ビューを "auto" に設定します。

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

2 つ目のオプションでは、マップを読み込むときに、この情報をマップ オプションに渡します。 例:

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
> Azure Maps を使用すると、言語と地域の設定が異なる複数のマップ インスタンスを同じページに読み込むことができます。 また、マップでは読み込み後に、これらの設定を更新することもできます。

Azure Maps で[サポートされている言語](supported-languages.md)の詳細な一覧をご覧ください。

言語が "fr" に設定され、ユーザーの地域が "fr-FR" に設定されている Azure Maps の例を以下に示します。

![Azure Maps のローカライズ](media/migrate-google-maps-web-app/azure-maps-localization.png)

### <a name="setting-the-map-view"></a>マップ ビューの設定

Azure Maps と Google Maps の両方の動的マップは、プログラムで新しい地理的な場所に移動できます。 そのためには、JavaScript で適切な関数を呼び出します。 この例では、Google Maps でマップに衛星航空映像を表示し、マップの中心をある場所に指定して、ズーム レベルを 15 に変更する方法を紹介します。 使用されている位置座標は、経度 -111.0225 で、緯度 35.0272 です。

> [!NOTE]
> Google マップでは、ディメンションが 256 ピクセルのタイルが使用されますが、Azure Maps では、より大きな 512 ピクセルのタイルが使用されます。 これにより、Azure Maps で Google マップと同じマップ領域を読み込むために必要なネットワーク要求の数が減ります。 マップ コントロールでのタイル ピラミッドの動作方法により、Azure Maps の使用時には、Google マップで使用されるズーム レベルを 1 ずつ減らす必要があります。 この算術演算により、Azure Maps のより大きなタイルで、Google マップと同じマップ領域がレンダリングされます。

#### <a name="before-google-maps"></a>前: Google Maps

Google マップのマップ コントロールは、`setOptions` メソッドを使用して移動します。 このメソッドを使用すると、マップの中心とズーム レベルを指定できます。

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

![Google Maps の設定ビュー](media/migrate-google-maps-web-app/google-maps-set-view.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps では、`setCamera` メソッドを使用してマップの位置を変更し、`setStyle` メソッドを使用してマップのスタイルを変更します。 Azure Maps 内の座標は "経度, 緯度" 形式になっていて、ズーム レベルの値は 1 ずつ減算されます。

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

![Azure Maps の設定ビュー](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)

**その他のリソース:**

* [マップ スタイルを選択する](choose-map-style.md)
* [サポートされているマップ スタイル](supported-map-styles.md)

### <a name="adding-a-marker"></a>マーカーの追加

Azure Maps には、ポイント データをマップ上にレンダリングできる方法が複数あります。

* **HTML マーカー** – 従来の DOM 要素を使用してポイントをレンダリングします。 HTML マーカーではドラッグがサポートされます。
* **シンボル レイヤー** – WebGL コンテキスト内でアイコンやテキストを使用してポイントをレンダリングします。
* **バブル レイヤー** - ポイントをマップ上で円としてレンダリングします。 円の半径は、データのプロパティに基づいてスケーリングできます。

シンボル レイヤーとバブル レイヤーは、WebGL コンテキスト内でレンダリングします。 どちらのレイヤーも、マップ上で大きなポイント セットをレンダリングすることができます。 これらのレイヤーでは、データをデータ ソースに格納する必要があります。 `ready` イベントが発生した後、データ ソースとレンダリング レイヤーをマップに追加する必要があります。 HTML マーカーは、ページ内に DOM 要素としてレンダリングされ、データ ソースは使用されません。 ページに含まれる DOM 要素が多いほど、そのページの速度は遅くなります。 マップ上で数百を超えるポイントをレンダリングする場合は、代わりにレンダリング レイヤーのいずれかを使用することをお勧めします。

オーバーレイ ラベルの数値を 10 として、マップにマーカーを追加しましょう。 経度は -0.2、緯度は 51.5 を使用します。

#### <a name="before-google-maps"></a>前: Google Maps

Google マップでは、`google.maps.Marker` クラスを使用してマーカーをマップに追加し、マップをオプションの 1 つとして指定します。

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

![Google Maps のマーカー](media/migrate-google-maps-web-app/google-maps-marker.png)

**後: HTML マーカーを使用した Azure Maps**

Azure Maps では、HTML マーカーを使用してマップ上にポイントを表示します。 マップ上に表示する必要のあるポイントの数が少ないアプリには、HTML マーカーをお勧めします。 HTML マーカーを使用するには、`atlas.HtmlMarker` クラスのインスタンスを作成します。 テキストおよび位置のオプションを設定し、`map.markers.add` メソッドを使用してマップにマーカーを追加します。

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Azure Maps の HTML マーカー](media/migrate-google-maps-web-app/azure-maps-html-marker.png)

**後: シンボル レイヤーを使用した Azure Maps**

シンボル レイヤーの場合は、データ ソースにデータを追加します。 そのデータ ソースをレイヤーにアタッチしてください。 さらに、`ready` イベントが発生した後、データ ソースとレイヤーをマップに追加する必要があります。 一意のテキスト値をシンボルの上にレンダリングするには、テキスト情報をデータ ポイントのプロパティとして格納する必要があります。 そのプロパティをレイヤーの `textField` オプションで参照する必要があります。 この方法は HTML マーカーを使用するよりも少し手間がかかりますが、パフォーマンスは高くなります。

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

![Azure Maps のシンボル レイヤー](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)

**その他のリソース:**

- [データ ソースを作成する](create-data-source-web-sdk.md)
- [シンボル レイヤーを追加する](map-add-pin.md)
- [バブル レイヤーを追加する](map-add-bubble-layer.md)
- [ポイント データをクラスタリングする](clustering-point-data-web-sdk.md)
- [HTML マーカーを追加する](map-add-custom-html.md)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)
- [シンボル レイヤーのアイコン オプション](/javascript/api/azure-maps-control/atlas.iconoptions)
- [シンボル レイヤーのテキスト オプション](/javascript/api/azure-maps-control/atlas.textoptions)
- [HTML マーカーのクラス](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [HTML マーカーのオプション](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-marker"></a>カスタム マーカーの追加

カスタム画像を使用すると、マップ上のポイントを表すことができます。 以下のマップでは、カスタム画像を使用してマップ上のポイントを表示しています。 このポイントの表示位置は、緯度が 51.5 で、経度は -0.2 です。 アンカーは、マーカーの位置をオフセットして、画鋲アイコンのポイントがマップ上の正しい位置に揃うようにします。

<center>

![黄色の画鋲のイメージ](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>

#### <a name="before-google-maps"></a>前: Google Maps

カスタム マーカーは、画像の `url` を含んだ `Icon` オブジェクトを指定することによって作成します。 `anchor` ポイントを指定して、画鋲の画像のポイントをマップ上の座標に合わせます。 Google Maps のアンカー値は、イメージの左上隅を基準としています。

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

![Google Maps のカスタム マーカー](media/migrate-google-maps-web-app/google-maps-custom-marker.png)

**後: HTML マーカーを使用した Azure Maps**

HTML マーカーをカスタマイズするには、HTML の `string` または `HTMLElement` をマーカーの `htmlContent` オプションに渡します。 `anchor` オプションを使用し、位置座標を基準として、マーカーの相対位置を指定します。 `anchor` オプションには、定義済みの 9 つの基準点のいずれかを割り当てます。 定義されている基準点は "center"、"top"、"bottom"、"left"、"right"、"top-left"、"top-right"、"bottom-left"、"bottom-right" です。 既定では、コンテンツは html コンテンツの下中央に固定されます。 Google Maps からコードをより簡単に移行できるようにするには、`anchor` を "top-left" に設定してから、Google Maps で使用したものと同じオフセットで `pixelOffset` オプションを使用します。 Azure Maps のオフセットは、Google マップのオフセットとは反対方向に移動します。 したがって、オフセットには -1 を乗算してください。

> [!TIP]
> html コンテンツにスタイルとして `pointer-events:none` を追加して、不要なアイコンを表示する、Microsoft Edge での既定のドラッグ動作を無効にします。

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Azure Maps のカスタム HTML マーカー](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)

**後: シンボル レイヤーを使用した Azure Maps**

Azure Maps のシンボル レイヤーでもカスタム画像がサポートされます。 最初に画像をマップ リソースに読み込んで、それに一意の ID を割り当てます。 その画像をシンボル レイヤーで参照します。 マップ上の正しいポイントに画像を合わせるには、`offset` オプションを使用します。 `anchor` オプションを使用し、位置座標を基準として、シンボルの相対位置を指定します。 9 つある定義済みの基準点のいずれかを使用してください。 "center"、"top"、"bottom"、"left"、"right"、"top-left"、"top-right"、"bottom-left"、"bottom-right" がそれらの基準点となります。 既定では、コンテンツは html コンテンツの下中央に固定されます。 Google Maps からコードをより簡単に移行できるようにするには、`anchor` を "top-left" に設定してから、Google Maps で使用したものと同じオフセットで `offset` オプションを使用します。 Azure Maps のオフセットは、Google マップのオフセットとは反対方向に移動します。 したがって、オフセットには -1 を乗算してください。

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

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

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

![Azure Maps のカスタム アイコン シンボル レイヤー](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</

> [!TIP]
> 高度なカスタム ポイントをレンダリングするには、複数のレンダリング レイヤーを一緒に使用します。 たとえば、異なる色の円に同じアイコンを使用した画鋲を複数用意するとします。 カラー オーバーレイごとに多数の画像を作成するのではなく、バブル レイヤーの上にシンボル レイヤーを追加します。 画鋲が同じデータ ソースを参照するようにしてください。 この方法は、多数の異なるイメージを作成して保守するよりも効率的です。

**その他のリソース:**

- [データ ソースを作成する](create-data-source-web-sdk.md)
- [シンボル レイヤーを追加する](map-add-pin.md)
- [HTML マーカーを追加する](map-add-custom-html.md)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)
- [シンボル レイヤーのアイコン オプション](/javascript/api/azure-maps-control/atlas.iconoptions)
- [シンボル レイヤーのテキスト オプション](/javascript/api/azure-maps-control/atlas.textoptions)
- [HTML マーカーのクラス](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [HTML マーカーのオプション](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>ポリラインの追加

ポリラインは、マップ上の線またはパスを表すために使用します。 マップ上に破線のポリラインを作成してみましょう。

#### <a name="before-google-maps"></a>前: Google Maps

Polyline クラスは、一連のオプションを受け取ります。 ポリラインの `path` オプションに座標の配列を渡します。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

![Google Maps のポリライン](media/migrate-google-maps-web-app/google-maps-polyline.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

ポリラインは、`LineString` オブジェクトまたは `MultiLineString` オブジェクトと呼ばれます。 これらのオブジェクトは、データ ソースに追加し、線レイヤーを使用してレンダリングできます。 `LineString` をデータ ソースに追加した後、そのデータ ソースを `LineLayer` に追加することでレンダリングされます。

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

![Azure Maps のポリライン](media/migrate-google-maps-web-app/azure-maps-polyline.png)

**その他のリソース:**

- [マップに線を追加する](map-add-line-layer.md)
- [線レイヤーのオプション](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>多角形の追加

Azure Maps と Google Maps では、多角形を同じようにサポートしています。 多角形は、マップ上の領域を表すために使用されます。 次の例は、マップの中心座標に基づいて、三角形を形成する多角形を作成する方法を示しています。

#### <a name="before-google-maps"></a>前: Google Maps

Polygon クラスは、一連のオプションを受け取ります。 多角形の `paths` オプションに座標の配列を渡します。

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

![Google Maps の多角形](media/migrate-google-maps-web-app/google-maps-polygon.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

`Polygon` または `MultiPolygon` オブジェクトをデータ ソースに追加します。 レイヤーを使用して、マップ上にオブジェクトをレンダリングします。 多角形の領域は、多角形レイヤーを使用してレンダリングします。 また、多角形の枠線は、線レイヤーを使用してレンダリングします。

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

![Azure Maps の多角形](media/migrate-google-maps-web-app/azure-maps-polygon.png)

**その他のリソース:**

- [マップに多角形を追加する](map-add-shape.md)
- [マップに円を追加する](map-add-shape.md#add-a-circle-to-the-map)
- [多角形レイヤーのオプション](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
- [線レイヤーのオプション](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>情報ウィンドウを表示する

エンティティの追加情報は、Google Maps では `google.maps.InfoWindow` クラスとしてマップに表示できます。 Azure Maps では、`atlas.Popup` クラスを使用して、この機能を実現できます。 次の例では、マップにマーカーを追加します。 マーカーをクリックすると、情報ウィンドウまたはポップアップが表示されます。

#### <a name="before-google-maps"></a>前: Google Maps

`google.maps.InfoWindow` コンストラクターを使用して情報ウィンドウをインスタンス化します。

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

![Google Maps のポップアップ](media/migrate-google-maps-web-app/google-maps-popup.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

ポップアップを使用して、場所に関する追加情報を表示してみましょう。 HTML の `string` または `HTMLElement` オブジェクトを、ポップアップの `content` オプションに渡します。 ポップアップは、必要に応じて図形とは別に表示できます。 そのため、ポップアップでは、`position` 値を指定する必要があります。 `position` 値を指定します。 ポップアップを表示するには、`open` メソッドを呼び出し、ポップアップの表示先となる `map` を渡します。

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

![Azure Maps のポップアップ](media/migrate-google-maps-web-app/azure-maps-popup.png)

> [!NOTE]
> 選択したレイヤーを、マーカーではなくマップのイベント コードに渡すことで、シンボル、バブル、線、または多角形のレイヤーで同じことを行えます。

**その他のリソース:**

- [ポップアップを追加する](map-add-popup.md)
- [メディア コンテンツを含むポップアップ](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [図形のポップアップ](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [複数のピンでのポップアップの再利用](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [ポップアップのクラス](/javascript/api/azure-maps-control/atlas.popup)
- [ポップアップのオプション](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="import-a-geojson-file"></a>GeoJSON ファイルをインポートする

Google Maps では、`google.maps.Data` クラスを使用した GeoJSON データの読み込みと動的なスタイル設定がサポートされています。 このクラスの機能は、Azure Maps のデータドリブンのスタイル設定とより多くの点で一致しています。 ただし、大きな違いがあります。 Google マップでは、コールバック関数を指定します。 各フィーチャーのスタイルを設定するためのビジネス ロジックは、UI スレッドで個別に処理されます。 一方、Azure Maps では、レイヤーでデータドリブン式がスタイル設定オプションとして指定することがサポートされます。 これらの式は、個々のスレッドでレンダリング時に処理されます。 Azure Maps のアプローチでは、レンダリングのパフォーマンスが向上します。 この利点が顕著に表れるのは、より大きなデータ セットを短時間でレンダリングしなければならないときです。

次の例では、USGS から過去 7 日間のすべての地震の GeoJSON フィードを読み込みます。 地震のデータは、拡大縮小される円としてマップ上にレンダリングされます。 各円の色と大きさは、データ セット内の各フィーチャーの `"mag"` プロパティに格納されている、各地震のマグニチュードに基づいています。 マグニチュードが 5 以上の場合、円は赤色になります。 3 以上 5 未満の場合、円はオレンジ色になります。 3 未満の場合、円は緑色になります。 各円の半径は、0.1 を乗算したマグニチュードの指数になります。

#### <a name="before-google-maps"></a>前: Google Maps

`map.data.setStyle` メソッドに単一のコールバック関数を指定します。 コールバック関数内で、それぞれのフィーチャーにビジネス ロジックを適用します。 `map.data.loadGeoJson` メソッドで GeoJSON フィードを読み込みます。

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Google Maps の GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

GeoJSON は、Azure Maps の基本データ型です。 `datasource.importFromUrl` メソッドを使用して、それをデータ ソースにインポートします。 バブル レイヤーを使用します。 バブル レイヤーでは、データ ソース内のフィーチャーのプロパティに基づいて、スケーリングされた円をレンダリングするための機能が提供されます。 コールバック関数を使用するのではなく、ビジネス ロジックが式に変換され、スタイル オプションに渡されます。 式は、ビジネス ロジックの動作を定義します。 式を別のスレッドに渡し、フィーチャー データに対して評価することができます。 Azure Maps には、複数のデータ ソースとレイヤーを、それぞれ異なるビジネス ロジックと共に追加できます。 この機能により、複数のデータ セットをさまざまな方法でマップにレンダリングすることができます。

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
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
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

![Azure Maps の GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)

**その他のリソース:**

* [シンボル レイヤーを追加する](map-add-pin.md)
* [バブル レイヤーを追加する](map-add-bubble-layer.md)
* [ポイント データをクラスタリングする](clustering-point-data-web-sdk.md)
* [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>マーカー クラスタリング

多数のデータ ポイントをマップ上に視覚化すると、ポイントが互いに重なり合うことがあります。 それらが重なり合うことで、マップの見た目がごちゃごちゃして、見づらく使いづらいものになります。 ポイント データのクラスタリングは、互いに近いデータ ポイントを結合し、単一のクラスタリングされたデータ ポイントとしてマップ上に表現するプロセスです。 ユーザーがマップにズーム インすると、クラスターは個々のデータ ポイントに分解します。 データ ポイントをクラスタリングすることで、ユーザー エクスペリエンスとマップのパフォーマンスが向上します。

次の例では、コードは過去 1 週間の地震データの GeoJSON フィードを読み込み、それをマップに追加します。 クラスターは、スケーリングされた色付きの円としてレンダリングされます。 そこに含まれているポイントの数に応じて、円のスケールと色が決まります。

> [!NOTE]
> Google マップと Azure Maps とでは、使用されているクラスタリング アルゴリズムが若干異なります。 そのため、クラスター内のポイントの分布が異なる場合があります。

#### <a name="before-google-maps"></a>前: Google Maps

マーカーをクラスタリングするには、MarkerCluster ライブラリを使用します。 クラスター アイコンは、1 から 5 の数字の名前が付いた画像に限定されます。 これらは同じディレクトリでホストされます。

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
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Google Maps のクラスタリング](media/migrate-google-maps-web-app/google-maps-clustering.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

データは、データ ソースに追加して管理します。 データ ソースとレイヤーを接続したうえで、データをレンダリングします。 Azure Maps の `DataSource` クラスでは、いくつかのクラスタリング オプションが提供されます。

* `cluster` – データ ソースにポイント データをクラスタリングするように指示します。
* `clusterRadius` -ポイントをまとめてクラスタリングする場合の半径 (ピクセル単位)。
* `clusterMaxZoom` - クラスタリングが行われる最大ズーム レベル。 このレベルを超えてズームインすると、すべてのポイントがシンボルとしてレンダリングされます。
* `clusterProperties` - 各クラスター内のすべてのポイントに対して式を使用して計算され、各クラスター ポイントのプロパティに追加されるカスタム プロパティを定義します。

クラスタリングが有効になっている場合、データ ソースでは、クラスタリングおよびクラスタリング解除されたデータ ポイントをレンダリングするためにレイヤーに送信します。 データ ソースでは、数十万のデータ ポイントをクラスタリングすることができます。 クラスタリングされたデータ ポイントには、次のプロパティがあります。

| プロパティ名             | Type    | 説明   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | フィーチャーがクラスターを表すかどうかを示します。 |
| `cluster_id`              | string  | DataSource の `getClusterExpansionZoom`、`getClusterChildren`、および `getClusterLeaves` メソッドで使用できるクラスターの一意な ID。 |
| `point_count`             | number  | クラスターに含まれているポイントの数。  |
| `point_count_abbreviated` | string  | `point_count` の値が長い場合にその値を省略形にした文字列。 (たとえば、4,000 が 4K になります)  |

`DataSource` クラスには、`cluster_id` を使用してクラスターに関する追加情報にアクセスするための次のヘルパー関数があります。

| Method | の戻り値の型 : | 説明 |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 次のズーム レベルで指定されたクラスターの子を取得します。 これらの子はシェイプとサブクラスターの組み合わせの場合があります。 サブクラスターは ClusteredProperties と一致するプロパティを持つフィーチャーになります。 |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;number&gt; | クラスターが拡大し始めるか、または分解するズーム レベルを計算します。 |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | クラスター内のすべてのポイントを取得します。 ポイントのサブセットを返すには `limit` を設定し、ポイントをページ送りするには `offset` を使用します。 |

クラスタリングされたデータをマップ上にレンダリングする際、多くの場合は、2 つ以上のレイヤーを使用するのが一番です。 次の例では、3 つのレイヤーを使用します。 クラスターのサイズに基づいて拡大縮小される色付き円を描画するためのバブル レイヤー。 クラスター サイズをテキストとしてレンダリングするためのシンボル レイヤー。 そして、クラスター化されていないポイントをレンダリングするための 2 つ目のシンボル レイヤーを使用します。 クラスター化されたデータをレンダリングする方法は他にも多数あります。 詳細については、「[ポイント データのクラスタリング](clustering-point-data-web-sdk.md)」を参照してください。

GeoJSON データは、`DataSource` クラスの `importDataFromUrl` 関数を使用して直接 Azure Maps マップ内にインポートします。

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

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
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

![Azure Maps のクラスタリング](media/migrate-google-maps-web-app/azure-maps-clustering.png)

**その他のリソース:**

* [シンボル レイヤーを追加する](map-add-pin.md)
* [バブル レイヤーを追加する](map-add-bubble-layer.md)
* [ポイント データをクラスタリングする](clustering-point-data-web-sdk.md)
* [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>ヒート マップを追加する

ヒート マップは、点密度マップとも呼ばれる、データ視覚化の一種です。 一定の範囲の色を使用して、データの密度を表すために使用されます。 また、マップ上のデータの "ホットスポット" を示す目的で使用されることもよくあります。 多数のポイント データ セットをレンダリングするにはヒート マップが適しています。

次の例では、USGS から過去 1 か月間のすべての地震の GeoJSON フィードを読み込み、加重ヒート マップとしてそれらをレンダリングします。 重みとして `"mag"` プロパティが使用されています。

#### <a name="before-google-maps"></a>前: Google Maps

ヒート マップを作成するには、API スクリプトの URL に `&libraries=visualization` を追加して、"視覚エフェクト" ライブラリを読み込みます。 Google Maps のヒート マップ レイヤーでは、GeoJSON データを直接サポートしていません。 最初にデータをダウンロードし、加重データ ポイントの配列に変換します。

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

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Google Maps のヒートマップ](media/migrate-google-maps-web-app/google-maps-heatmap.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

GeoJSON データをデータ ソースに読み込み、そのデータ ソースをヒート マップ レイヤーに接続します。 重みに使用されるプロパティは、式を使用して `weight` オプションに渡すことができます。 GeoJSON データは、`DataSource` クラスの `importDataFromUrl` 関数を使用して直接 Azure Maps マップにインポートします。

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
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                    weight: ['get', 'mag'],
                    intensity: 0.005,
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

![Azure Maps のヒートマップ](media/migrate-google-maps-web-app/azure-maps-heatmap.png)

**その他のリソース:**

- [ヒート マップ レイヤーを追加する](map-add-heat-map-layer.md)
- [ヒート マップ レイヤーのクラス](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
- [ヒート マップ レイヤーのオプション](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
- [データドリブンのスタイルの式を使用する](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>タイル レイヤーをオーバーレイする

Azure Maps のタイル レイヤーは、Google マップでは画像オーバーレイと呼ばれます。 タイル レイヤーを使用すると、マップ タイル システムに合わせて、より小さなタイル表示された画像に分割された大きな画像をオーバーレイすることができます。 この方法は、大きな画像や大きなデータ セットをオーバーレイする際によく使用されます。

次の例では、アイオワ州立大学の Iowa Environmental Mesonet の気象レーダー タイル レイヤーをオーバーレイします。

#### <a name="before-google-maps"></a>前: Google Maps

Google Maps では、`google.maps.ImageMapType` クラスを使用して、タイル レイヤーを作成できます。

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

![Google Maps のタイル レイヤー](media/migrate-google-maps-web-app/google-maps-tile-layer.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

他のレイヤーと同じように、タイル レイヤーをマップに追加します。 x、y、ズーム プレースホルダー (`{x}`、`{y}`、`{z}`) の書式設定された URL は、タイルにアクセスする場所をレイヤーに指示するために使用します。 Azure Maps のタイル レイヤーでは、`{quadkey}`、`{bbox-epsg-3857}`、`{subdomain}` プレースホルダーもサポートされます。

> [!TIP]
> Azure Maps では、レイヤーは、基本マップ レイヤーを含む、他のレイヤーの下に簡単にレンダリングできます。 多くの場合、読みやすいようにタイル レイヤーをマップ ラベルの下にレンダリングすることをお勧めします。 `map.layers.add` メソッドでは、2 番目のパラメーターを受け取ります。これは、下に新しいレイヤーを挿入するレイヤーの id です。 マップ ラベルの下にタイル レイヤーを挿入する場合は、`map.layers.add(myTileLayer, "labels");` というコードを使用します。

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

![Azure Maps のタイル レイヤー](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)

> [!TIP]
> タイル要求は、マップの `transformRequest` オプションを使用してキャプチャできます。 これにより、必要に応じて、要求に対してヘッダーの変更や追加を行うことができます。

**その他のリソース:**

- [タイル レイヤーを追加する](map-add-tile-layer.md)
- [タイル レイヤーのクラス](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
- [タイル レイヤーのオプション](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>トラフィック データを表示する

交通データは、Azure および Google Maps の両方にオーバーレイできます。

#### <a name="before-google-maps"></a>前: Google Maps

交通データは、交通情報レイヤーを使用してマップにオーバーレイします。

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

![Google Maps の交通情報](media/migrate-google-maps-web-app/google-maps-traffic.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps では、交通情報を表示するためのさまざまなオプションが提供されます。 道路の閉鎖や事故などの交通事故は、マップ上にアイコンとして表示します。 交通流量と色分けされた道路は、マップ上にオーバーレイします。 掲示された速度制限、通常の予想される遅延、または絶対遅延を基準として、色を変更することができます。 Azure Maps の事故データは 1 分ごとに、流量データは 2 分ごとに更新されます。

必要な値は、`setTraffic` のオプションで割り当てます。

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

![Azure Maps の交通情報](media/migrate-google-maps-web-app/azure-maps-traffic.png)

Azure Maps の交通アイコンのいずれかをクリックすると、ポップアップに追加情報が表示されます。

![Azure Maps の交通事故](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)

**その他のリソース:**

* [マップ上にトラフィックを表示する](map-show-traffic.md)
* [交通情報オーバーレイのオプション](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>グラウンド オーバーレイを追加する

Azure と Google のどちらのマップも、ジオリファレンスされた画像をマップ上にオーバーレイすることができます。 マップをパンしたりズームしたりすると、ジオリファレンスされた画像が移動およびスケーリングされます。 Google マップでは、ジオリファレンスされた画像がグラウンド オーバーレイとして知られていますが、Azure Maps では画像レイヤーと呼ばれます。 これらは、ビルのフロア プラン、古いマップのオーバーレイ、またはドローンからの映像に適しています。

#### <a name="before-google-maps"></a>前: Google Maps

オーバーレイする画像の URL と、マップ上の画像をバインドするための境界ボックスを指定します。 この例では、マップ上に [1922 年のニュージャージー州のニューアーク](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)のマップ イメージをオーバーレイします。

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

ブラウザーでこのコードを実行すると、次のイメージのようなマップが表示されます。

![Google Maps のイメージ オーバーレイ](media/migrate-google-maps-web-app/google-maps-image-overlay.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

ジオリファレンスされた画像をオーバーレイするには、`atlas.layer.ImageLayer` クラスを使用します。 このクラスでは、イメージへの URL と、イメージの四隅の座標のセットが必要です。 イメージは、同じドメインでホストされているか、CORS が有効になっている必要があります。

> [!TIP]
> 画像の各隅の座標ではなく、北、南、東、西および回転の情報のみがある場合は、静的な [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) メソッドを使用できます。

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

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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

![Azure Maps のイメージ オーバーレイ](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)

**その他のリソース:**

- [イメージをオーバーレイする](map-add-image-layer.md)
- [イメージ レイヤーのクラス](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>マップに KML データを追加する

Azure と Google のどちらのマップも、KML、KMZ、GeoRSS のデータをインポートしてマップ上にレンダリングすることができます。 Azure Maps ではさらに、GPX、GML、空間 CSV ファイル、GeoJSON、Well Known Text (WKT)、Web Mapping Services (WMS)、Web Mapping Tile Services (WMTS)、Web Feature Services (WFS) がサポートされます。 Azure Maps は、ファイルをローカルのメモリに読み取るので、ほとんどの場合、Azure Maps の方が、はるかに大きな KML ファイルを扱うことができます。 

#### <a name="before-google-maps"></a>前: Google Maps

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

ブラウザーでこのコードを実行すると、次のイメージのようなマップが表示されます。

![Google Maps KML](media/migrate-google-maps-web-app/google-maps-kml.png)

#### <a name="after-azure-maps"></a>後: Azure Maps

Azure Maps では、GeoJSON が Web SDK で使用される主なデータ形式となりますが、[空間 IO モジュール](/javascript/api/azure-maps-spatial-io/)を使用すれば、その他の空間データ形式も容易に統合することができます。 このモジュールには、空間データの読み取りと書き込みの両方に対応した関数のほか、これらの空間データ形式から簡単にデータをレンダリングできるシンプルなデータ レイヤーが備わっています。 空間データ ファイルのデータを読み取るには、URL を (あるいは生データを文字列または BLOB として) `atlas.io.read` 関数に渡します。 ファイルから解析済みのデータがすべて返されるので、そのデータをマップに追加することができます。 KML は、多くのスタイル情報を含んでいるため、ほとんどの空間データ形式と比べてやや複雑です。 そうしたスタイルの大半のレンダリングは、`SpatialDataLayer` クラスでサポートされますが、地物データを読み込む前にアイコン画像をマップに読み込む必要があり、また、マップには別途レイヤーとしてグラウンド オーバーレイを追加する必要があります。 URL でデータを読み込む場合は、CORS が有効なエンドポイントでデータがホストされているか、または、読み取り関数にオプションとしてプロキシ サービスを渡す必要があります。 

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

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
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
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
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

![Azure Maps の KML](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>

**その他のリソース:**

- [atlas.io.read 関数](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>その他のコード サンプル

Google Maps の移行に関連するその他のコード サンプルを以下にいくつか示します。

* [描画ツール](map-add-drawing-toolbar.md)
* [マップを 2 本指でパンするように制限する](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
* [スクロール ホイールのズームを制限する](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
* [全画面表示コントロールを作成する](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**サービス**

* [Azure Maps サービス モジュールの使用](how-to-use-services-module.md)
* [観光名所の検索](map-search-location.md)
* [座標から情報を取得する (逆ジオコード)](map-get-information-from-coordinate.md)
* [A から B までのルートを表示する ](map-route.md)
* [JQuery UI を使用する自動提案検索](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Google Maps V3 から Azure Maps Web SDK へのクラスのマッピング

次の付録では、Google Maps V3 でよく使用されるクラスから、それに相当する Azure Maps Web SDK への相互参照を示します。

### <a name="core-classes"></a>コア クラス

| Google Maps   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](/javascript/api/azure-maps-control/atlas.map)  |
| `google.maps.InfoWindow` | [atlas.Popup](/javascript/api/azure-maps-control/atlas.popup)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](/javascript/api/azure-maps-control/atlas.data.position)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)<br/>[atlas.CameraBoundsOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions)<br/>[atlas.ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions)<br/>[atlas.StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)<br/>[atlas.UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) |
| `google.maps.Point`  | [atlas.Pixel](/javascript/api/azure-maps-control/atlas.pixel)   |

## <a name="overlay-classes"></a>オーバーレイ クラス

| Google Maps  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)<br/>[atlas.data.Point](/javascript/api/azure-maps-control/atlas.data.point)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)<br/>[atlas.layer.SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)<br/>[atlas.SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)<br/>[atlas.IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)<br/>[atlas.TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)<br/>[atlas.layer.BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)<br/>[atlas.BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) |
| `google.maps.Polygon`  | [atlas.data.Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)<br/> [atlas.PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)<br/> [atlas.layer.LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/> [atlas.LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)|
| `google.maps.Polyline` | [atlas.data.LineString](/javascript/api/azure-maps-control/atlas.data.linestring)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/>[atlas.LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) |
| `google.maps.Circle`  | 「[マップに円を追加する](map-add-shape.md#add-a-circle-to-the-map)」を参照してください                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)<br/>[atlas.ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions) |

## <a name="service-classes"></a>サービス クラス

Azure Maps Web SDK には、個別に読み込むことができるサービス モジュールが含まれています。 このモジュールでは、Web API で Azure Maps REST サービスをラップし、JavaScript、TypeScript、Node.js アプリケーションで使用できます。

| Google Maps | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions)<br/>[atlas.SearchAddressRevrseOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions)<br/>[atlas.SearchAddressReverseCrossStreetOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions)<br/>[atlas.SearchAddressStructuredOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions)<br/>[atlas.SearchAlongRouteOptions](/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions)<br/>[atlas.SearchFuzzyOptions](/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions)<br/>[atlas.SearchInsideGeometryOptions](/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions)<br/>[atlas.SearchNearbyOptions](/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions)<br/>[atlas.SearchPOIOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoioptions)<br/>[atlas.SearchPOICategoryOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](/javascript/api/azure-maps-rest/atlas.service.routeurl)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |

## <a name="libraries"></a>ライブラリ

ライブラリでは、マップにさらに機能が追加されます。 これらのライブラリの多くは、Azure Maps のコア SDK に含まれています。 これらの Google Maps ライブラリの代わりに使用される、同等のクラスをいくつか以下に示します

| Google Maps           | Azure Maps   |
|-----------------------|--------------|
| 描画ライブラリ       | [描画ツール モジュール](set-drawing-options.md) |
| ジオメトリ ライブラリ      | [atlas.math](/javascript/api/azure-maps-control/atlas.math)   |
| 視覚エフェクト ライブラリ | [ヒート マップ レイヤー](map-add-heat-map-layer.md) |

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップすべきリソースはありません。

## <a name="next-steps"></a>次のステップ

Azure Maps への移行について理解を深めます。

> [!div class="nextstepaction"]
> [Web サービスを移行する](migrate-from-google-maps-web-services.md)
