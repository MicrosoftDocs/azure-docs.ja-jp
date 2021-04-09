---
title: Microsoft Creator サービス (プレビュー) で Azure Maps の Indoor Maps モジュールを使用する
description: Microsoft Azure Maps の Indoor Maps モジュールを使用し、モジュールの JavaScript ライブラリを埋め込むことによってマップをレンダリングする方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: e527cf5fa6a7caaeaf56ea19d684dd0830d5ca8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708681"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Azure Maps の Indoor Maps モジュールを使用する

> [!IMPORTANT]
> Azure Maps Creator サービスは、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Maps Web SDK には、*Azure Maps Indoor* モジュールが含まれています。 *Azure Maps Indoor* モジュールを使用すると、Azure Maps Creator サービス (プレビュー) で作成されたフロア ガイドをレンダリングできます。 

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Creator (プレビュー) リソースを作成します](how-to-manage-creator.md)
3. [プライマリ サブスクリプション キーを取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account) (主キーまたはサブスクリプション キーとも呼ばれます)。
4. [屋内マップを作成するためのチュートリアル](tutorial-creator-indoor-maps.md)を完了して、`tilesetId` および `statesetId` を取得します。
 Azure Maps の Indoor Maps モジュールを使用して屋内マップをレンダリングするには、これらの識別子を使用する必要があります。

## <a name="embed-the-indoor-maps-module"></a>Indoor Maps モジュールを埋め込む

*Azure Maps Indoor* モジュールは、2 つの方法のどちらかでインストールして埋め込むことができます。

グローバルにホストされている Azure Content Delivery Network バージョンの *Azure Maps Indoor* モジュールを使用する場合は、次の HTML ファイルの `<head>` 要素に含まれている JavaScript とスタイル シートの参照を参照してください。

```html
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
```

 または、*Azure Maps Indoor* モジュールをダウンロードすることもできます。 *Azure Maps Indoor* モジュールには、Azure Maps サービスにアクセスするためのクライアント ライブラリが含まれています。 次の手順に従って *Indoor* モジュールをインストールし、ご自分の Web アプリケーションに読み込んでください。  
  
  1. [azure-maps-indoor パッケージ](https://www.npmjs.com/package/azure-maps-indoor)をインストールします。
  
      ```powershell
      >npm install azure-maps-indoor
      ```

  2. HTML ファイルの `<head>` 要素で、*Azure Maps Indoor* モジュールの JavaScript とスタイル シートを参照します。

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.js"></script>
      ```

## <a name="instantiate-the-map-object"></a>Map オブジェクトをインスタンス化する

まず、"*Map オブジェクト*" を作成します。 "*Map オブジェクト*" は、次の手順で *Indoor Manager* オブジェクトをインスタンス化するために使用します。  次のコードは、"*Map オブジェクト*" をインスタンス化する方法を示しています。

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13203, 47.63645],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: {
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Indoor Manager をインスタンス化する

屋内タイルセットとタイルのマップ スタイルを読み込むには、*Indoor Manager* をインスタンス化する必要があります。 "*Map オブジェクト*" と対応する `tilesetId` を指定して、*Indoor Manager* をインスタンス化します。 [マップの動的スタイル設定](indoor-map-dynamic-styling.md)をサポートする場合は、`statesetId` を渡す必要があります。 `statesetId` 変数名では大文字と小文字が区別されます。 コードは次の JavaScript のようになります。

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});
```

指定した状態データのポーリングを有効にするには、`statesetId` を指定して `indoorManager.setDynamicStyling(true)` を呼び出す必要があります。 状態データのポーリングにより、動的プロパティの状態または "*複数の状態*" を動的に更新できます。 たとえば、部屋などの機能には `occupancy` という名前の動的プロパティ ("*状態*") を設定できます。 アプリケーションで "*状態*" の変更をポーリングして、ビジュアル マップ内の変更を反映することができます。 次のコードは、状態のポーリングを有効にする方法を示しています。

```javascript
const tilesetId = "<tilesetId>";
const statesetId = "<statesetId>";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: tilesetId,
    statesetId: statesetId // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>屋内レベル ピッカー コントロール

 "*屋内レベル ピッカー*" コントロールを使用すると、レンダリングされたマップのレベルを変更できます。 必要に応じて、*Indoor Manager* を使用して "*屋内レベル ピッカー*" コントロールを初期化することができます。 レベル コントロール ピッカーを初期化するためのコードを次に示します。

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>屋内イベント

 *Azure Maps Indoor* モジュールでは、"*Map オブジェクト*" のイベントがサポートされています。 "*Map オブジェクト*" のイベント リスナーは、レベルまたは施設が変更されたときに呼び出されます。 レベルまたは施設が変更されたときにコードを実行する場合は、イベント リスナー内にコードを配置します。 次のコードは、イベント リスナーを "*Map オブジェクト*" に追加する方法を示しています。

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

`eventData` 変数には、`levelchanged` または `facilitychanged` イベントをそれぞれ呼び出したレベルまたは施設に関する情報が保持されます。 レベルが変更されると、`eventData` オブジェクトには `facilityId`、新しい `levelNumber`、およびその他のメタデータが追加されます。 施設が変更されると、`eventData` オブジェクトには新しい `facilityId`、新しい `levelNumber`、およびその他のメタデータが追加されます。

## <a name="example-use-the-indoor-maps-module"></a>例:Indoor Maps モジュールを使用する

この例では、Web アプリケーションで *Azure Maps Indoor* モジュールを使用する方法を示します。 この例は範囲が制限されていますが、*Azure Maps Indoor* モジュールの使用を開始するために必要な基本事項が説明されています。 完全な HTML コードは次の手順の下に記載されています。

1. Azure Content Delivery Network [オプション](#embed-the-indoor-maps-module)を使用して、*Azure Maps Indoor* モジュールをインストールします。

2. 新しい HTML ファイルを作成します

3. HTML のヘッダーで、*Azure Maps Indoor* モジュールの JavaScript とスタイル シートのスタイルを参照します。

4. "*Map オブジェクト*" を初期化します。 "*Map オブジェクト*" では、次のオプションがサポートされています。
    - `Subscription key` はご自分の Azure Maps プライマリ サブスクリプション キーです。
    - `center` を使用して屋内マップの中心位置の緯度と経度を定義します。 `bounds` に値を指定しない場合は、`center` に値を指定してください。 形式は `center`: [-122.13315, 47.63637] のようになります。
    - `bounds` は、タイルセット マップ データを囲む最小の四角形です。 `center` に値を設定しない場合は、`bounds` に値を設定してください。 マップの境界を知るには、[タイルセットの一覧 API](/rest/api/maps/tileset/listpreview) を呼び出します。 タイルセットの一覧 API から `bbox` が返されます。これを解析して `bounds` に割り当てることができます。 形式は `bounds`: [# west, # south, # east, # north] のように表示されるはずです。
    - `style` を使用すると背景色を設定できます。 白い背景を表示するには、`style` を "blank" として定義します。
    - `zoom` を使用すると、マップのズーム レベルの最小値と最大値を指定できます。

5. 次に、*Indoor Manager* モジュールを作成します。 *Azure Maps Indoor* の `tilesetId` を割り当て、必要に応じて `statesetId` を追加します。

6. "*屋内レベル ピッカー*" コントロールをインスタンス化します。

7. "*Map オブジェクト*" のイベント リスナーを追加します。  

これで、ファイルは次の HTML のようになります。

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl: levelControl, //level picker
          tilesetId: tilesetId,
          statesetId: statesetId // Optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

ご自分の屋内マップを確認する場合は、Web ブラウザーに読み込みます。 次の画像のように表示されるはずです。 階段の機能をクリックすると、右上隅に "*レベル ピッカー*" が表示されます。

  ![屋内マップの画像](media/how-to-use-indoor-module/indoor-map-graphic.png)

[ライブ デモを見る](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>次のステップ

*Azure Maps Indoor* モジュールに関連する API について参照してください。

> [!div class="nextstepaction"]
> [Drawing パッケージの要件](drawing-requirements.md)

>[!div class="nextstepaction"]
> [フロア ガイド用の Creator (プレビュー)](creator-indoor-maps.md)

マップにさらにデータを追加する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [屋内マップの動的スタイル設定](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [コード サンプル](/samples/browse/?products=azure-maps)