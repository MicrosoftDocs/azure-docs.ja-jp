---
title: Azure Maps の空間 IO モジュールの使用方法 | Microsoft Azure Maps
description: Azure Maps Web SDK で提供される、空間 IO モジュールの使用方法について説明します。 このモジュールには、開発者が Azure Maps Web SDK に空間データを簡単に統合するための堅牢な機能があります。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: cd64c80acceb1542c080fc45efbce59f287d448a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680695"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Azure Maps の空間 IO モジュールの使用方法

Azure Maps Web SDK には、JavaScript または TypeScript を使用して、空間データを Azure Maps Web SDK に統合する、**空間 IO モジュール** が用意されています。 開発者は、このモジュールの堅牢な機能により、次のことを行うことができます。

- [一般的な空間データ ファイルに対する読み取りと書き込み](spatial-io-read-write-spatial-data.md)。 サポートされているファイル形式は以下の通りです｡空間情報を含む列がある、KML、KMZ、GPX、GeoRSS、GML、CSV ファイル。 また、Well-Known Text (WKT) もサポートされています。
- [Open Geospatial Consortium (OGC) サービスに接続し、Azure Maps Web SDK と統合する。Web Map Service (WMS) と Web Map Tile Service (WMTS) をマップにレイヤーとしてオーバーレイする。](spatial-io-add-ogc-map-layer.md)
- [Web Feature Service (WFS) でデータを照会する。](spatial-io-connect-wfs-service.md)
- [スタイル情報を含む複雑なデータ セットをオーバーレイし、最小コードを使用してそれらが自動的にレンダリングされるようにする。](spatial-io-add-simple-data-layer.md)
- [高速 XML および区切りファイル リーダーとライター クラスを利用する。](spatial-io-core-operations.md)

このガイドでは、Web アプリケーションに空間 IO モジュールを統合して使用する方法について説明します。

この動画では、Azure Maps Web SDK での空間 IO モジュールの概要を示します。

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player?format=ny]

> [!WARNING]
> 特に別のドメインから参照する場合は、信頼できるソースからのデータとサービスのみを使用します。 空間 IO モジュールではリスクを最小限に抑えるための手順を行いますが、最初にアプリケーションに危険なデータを許可しないことが最も安全な方法です。 

## <a name="prerequisites"></a>前提条件

空間 IO モジュールを使用する前に、[Azure Maps アカウントを作成](./quick-demo-map-app.md#create-an-azure-maps-account)し、[お使いのアカウントのプライマリ サブスクリプション キーを取得](./quick-demo-map-app.md#get-the-primary-key-for-your-account)する必要があります。

## <a name="installing-the-spatial-io-module"></a>空間 IO モジュールのインストール

Azure Maps 空間 IO モジュールは、次の 2 つのオプションのいずれかを使用して読み込むことができます。

* グローバルにホストされている Azure Maps 空間 IO モジュール用の Azure CDN。 このオプションでは、HTML ファイルの `<head>` 要素に JavaScript への参照を追加します。

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) のソース コードはローカルに読み込んで、お使いのアプリでホストすることができます。 このパッケージには TypeScript 定義も含まれています。 このオプションでは、次のコマンドを実行して、パッケージをインストールします。

    ```sh
    npm install azure-maps-spatial-io
    ```

    次いで、HTML ドキュメントの `<head>` 要素に JavaScript への参照を追加します。

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>空間 IO モジュールの使用

1. 新しい HTML ファイルを作成します。

2. Azure Maps Web SDK を読み込んで、マップ コントロールを初期化します。 詳細については、[Azure Maps のマップ コントロール](./how-to-use-map-control.md) ガイドに関するページを参照してください。 この手順を完了すると、お使いの HTML ファイルは次のようになります。

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
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
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Azure Maps の空間 IO モジュールを読み込みます。 この演習では、Azure Maps 空間 IO モジュール用の CDN を使用します。 次の参照を HTML ファイルの `<head>` 要素に追加します。

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. `datasource` を初期化し、データ ソースをマップに追加します。 `layer` を初期化し、データ ソースをマップ レイヤーに追加します。 次いで、データ ソースとレイヤーの両方をレンダリングします。 次の手順で下にスクロールしてコードをすべて表示する前に、データ ソースとレイヤー コードのスニペットをどこに配置するのが最適か考えます。 マップをプログラムで操作する前に、マップのリソースの準備が整うまで待つ必要があったことを思い出してください。

    ```javascript
    var datasource, layer;
    ```

    and

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. これをすべてまとめると、お使いの HTML コードは次のようなコードになります。 このサンプルでは、URL から XML ファイルを読み込む方法を示します。 次いで、ファイルの機能データをマップに読み込んで表示します。 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
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
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. `<Your Azure Maps Key>` は必ずご自分の主キーに置き換えてください。 お使いの HTML ファイルを開くと、次の図のような結果が表示されます。

    <center>

    ![空間データの例](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>次のステップ

ここで説明した機能は、空間 IO モジュールで使用できる多数の機能のうちの 1 つにすぎません。 下記のガイドを参照して、空間 IO モジュールのその他の機能の使用方法を学習してください。

> [!div class="nextstepaction"]
> [シンプルなデータ レイヤーの追加](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [空間データの読み取りと書き込み](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [OGC マップ レイヤーの追加](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [WFS サービスへの接続](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [コア操作の活用](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [サポートされるデータ形式の詳細](spatial-io-supported-data-format-details.md)

以下の Azure Maps 空間 IO のドキュメントを参照してください。

> [!div class="nextstepaction"]
> [Azure Maps 空間 IO パッケージ](/javascript/api/azure-maps-spatial-io/)