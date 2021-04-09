---
title: Azure Maps Creator (プレビュー) の屋内マップに動的スタイル設定を実装する
description: Creator (プレビュー) の屋内マップに動的スタイル設定を実装する方法について説明します
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a23c492d4a81703c0dc6612928a56b5b31d52cae
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726319"
---
# <a name="implement-dynamic-styling-for-creator-preview-indoor-maps"></a>Creator (プレビュー) の屋内マップに動的スタイル設定を実装する

> [!IMPORTANT]
> Azure Maps Creator サービスは、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Maps Creator [Feature State サービス](/rest/api/maps/featurestate)を使用すると、屋内マップ データ地物の動的プロパティに基づいてスタイルを適用できます。  たとえば、施設の会議室を特定の色でレンダリングして、占有状態を反映できます。 この記事では、[Feature State サービス](/rest/api/maps/featurestate)と [Indoor Web モジュール](how-to-use-indoor-module.md)を使用して、屋内マップ地物を動的にレンダリングする方法について説明します。

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。
3. [Creator (プレビュー) リソースを作成します](how-to-manage-creator.md)
4. [サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)をダウンロードします。
5. [屋内マップを作成](tutorial-creator-indoor-maps.md)して、`tilesetId` と `statesetId` を取得します。
6. [Indoor Maps モジュールの使用方法](how-to-use-indoor-module.md)の手順に従って、Web アプリケーションを構築します。

このチュートリアルでは [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境を選択することもできます。

## <a name="implement-dynamic-styling"></a>動的スタイル設定を実装する

前提条件を完了すると、サブスクリプション キー `tilesetId` および `statesetId` を使用して構成された単純な Web アプリケーションが作成されます。

### <a name="select-features"></a>機能を選択する

動的スタイル設定を実装するには、会議室やカンファレンス ルームなどの地物をその地物 `id` から参照する必要があります。 地物 `id` を使用して、その地物の動的プロパティまたは "*状態*" を更新します。 データセットで定義されている地物を表示するには、次のいずれかの方法を使用できます。

* WFS API (Web Feature Service)。 データセットにクエリを実行するには、WFS API を使用します。 WFS は Open Geospatial Consortium API の地物に従います。 WFS API は、データセット内の地物のクエリに役立ちます。 たとえば、WFS を使用して、特定の施設とレベルの中規模の会議室をすべて検索することができます。

* ユーザーが Web アプリケーションを使用してマップ上の地物を選択できるようにするカスタマイズされたコードを実装します。 この記事では、このオプションを使用します。  

次のスクリプトでは、マウス クリック イベントを実装します。 このコードでは、クリックされたポイントに基づいて地物 `id` を取得します。 アプリケーションでは、Indoor Manager コード ブロックの下にコードを挿入できます。 アプリケーションを実行し、コンソールを確認して、クリックしたポイントの地物 `id` を取得します。

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

[屋内マップの作成](tutorial-creator-indoor-maps.md)チュートリアルでは、`occupancy` の状態の更新を受け入れるように地物状態セットを構成しました。

次のセクションでは、オフィス `UNIT26` の占有 "*状態*" を `true` に設定します。 一方、オフィス `UNIT27` は `false` に設定されます。

### <a name="set-occupancy-status"></a>占有状態を設定する

 ここでは、2 つのオフィス `UNIT26` と `UNIT27` の状態を更新します。

1. Postman アプリケーションで、 **[New]\(新規\)** を選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 **[Request name]\(要求名\)** を入力し、コレクションを選択します。 **[保存]**

2. [Feature Update States API](/rest/api/maps/featurestate/updatestatespreview) を使用して状態を更新します。 状態セット ID を渡し、2 つのユニットのいずれかに `UNIT26` を渡します。 Azure Maps サブスクリプション キーを追加します。 状態を更新するための **POST** 要求の URL は次のとおりです。

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. **POST** 要求の **[Headers]\(ヘッダー\)** で、`Content-Type` を `application/json` に設定します。 **POST** 要求の **[BODY]\(本文\)** で、地物の更新を含む次の未加工の JSON を記述します。 POST されたタイム スタンプが、同じ地物 `ID` に対する以前の地物状態の更新要求で使用されたタイム スタンプより後の場合にのみ、更新は保存されます。 "occupied" の `keyName` を渡して値を更新します。

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

4. 次の JSON を使用し、`UNIT27` を使用して手順 2 および 3 を再実行します。

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>マップ上で動的スタイルを視覚化する

以前にブラウザーで開いた Web アプリケーションには、マップ地物の更新された状態が反映されているはずです。 `UNIT27`(142) は緑色で表示され、`UNIT26`(143) は赤色で表示されます。

![緑の空き部屋と赤の空いていない部屋](./media/indoor-map-dynamic-styling/room-state.png)

[ライブ デモを見る](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [屋内マップ用の Creator (プレビュー)](creator-indoor-maps.md)

この記事で触れられている API のリファレンスを参照してください。

> [!div class="nextstepaction"]
> [Data Upload](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [データ変換](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [データセット](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [タイルセット](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [地物状態セット](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS サービス](creator-indoor-maps.md#web-feature-service-api)