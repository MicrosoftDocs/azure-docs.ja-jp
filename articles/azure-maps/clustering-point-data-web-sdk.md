---
title: Azure Maps でのポイント データのクラスタリング | Microsoft Docs
description: Web SDK でポイント データをクラスター化する方法
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 5f51c1166364a3470a1cc943e66d429c32cdc49b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839481"
---
# <a name="clustering-point-data"></a>ポイント データのクラスタリング

多数のデータ ポイントをマップ上に視覚化すると、ポイントが互いに重なり合い、マップの見た目がごちゃごちゃして、見づらく使いづらいものになります。 ポイント データのクラスタリングは、このユーザー エクスペリエンスを向上させるために使用できます。 ポイント データのクラスタリングは、互いに近いポイント データを結合し、単一のクラスター化されたデータ ポイントとしてマップ上に表現するプロセスです。 ユーザーがマップにズーム インすると、クラスターは個々のデータ ポイントに分解します。

## <a name="enabling-clustering-on-a-data-source"></a>データ ソースでのクラスタリングの有効化

`cluster` オプションを true に設定することにより、`DataSource` クラスでクラスタリングを簡単に有効化できます。 また、クラスターに結合する近隣の点を選択するためのピクセル半径は `clusterRadius` を使用して設定でき、クラスタリング ロジックを無効化するズーム レベルは `clusterMaxZoom` オプションを使用して指定できます。 次に示すのは、データ ソースでクラスタリングを有効化する方法の例です。

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> 2 つのデータ ポイントがきわめて近い場合、ユーザーがどれだけ近くまでズーム インしてもクラスターが分解しない可能性があります。 これに対処するには、データ ソースの `clusterMaxZoom` オプションを設定し、クラスタリング ロジックを無効化してすべてを表示するズーム レベルを指定することができます。

`DataSource` クラスには、クラスタリングに関連する以下のメソッドもあります。

| Method | 戻り値の型 | 説明 |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 次のズーム レベルで指定されたクラスターの子を取得します。 これらの子はシェイプとサブクラスターの組み合わせの場合があります。 サブクラスターは ClusteredProperties と一致するプロパティを持つフィーチャーになります。 |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | クラスターが拡大し始めるか、または分解するズーム レベルを計算します。 |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | クラスター内のすべてのポイントを取得します。 ポイントのサブセットを返すには `limit` を設定し、ポイントをページ送りするには `offset` を使用します。 |

## <a name="display-clusters-using-a-bubble-layer"></a>バブル レイヤーを使用してクラスターを表示する

バブル レイヤーは、クラスター化したポイントをレンダリングしながら半径を簡単に変更したり、式を使用してクラスター内のポイント数に基づいてクラスター化したポイントに色を付けたりするための優れた方法です。 バブル レイヤーを使用してクラスターを表示するときは、クラスター化されていないデータ ポイントをレンダリングするための別のレイヤーも使用することをお勧めします。 バブルの上にクラスターのサイズも表示できると便利なことがよくあります。 この動作を実現するために、テキスト付きでアイコンがないシンボル レイヤーを使用できます。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本的なバブル レイヤー クラスタリング" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps による<a href='https://codepen.io/azuremaps/pen/qvzRZY/'>基本的なバブル レイヤー クラスタリング</a>の Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>シンボル レイヤーを使用してクラスターを表示する

シンボル レイヤーを使用してポイント データを視覚化するとき、互いに重なるシンボルは既定で自動的に非表示になるため見た目はすっきりしますが、マップ上のデータ ポイントの密度を確認したい場合、これは望ましい動作ではない可能性があります。 シンボル レイヤーの `iconOptions` プロパティの `allowOverlap` オプションを `true` に設定すると、この動作が無効になり、すべてのシンボルが表示されます。 クラスタリングを使用すれば、すべてのデータの密度確認と、快適ですっきりしたユーザー エクスペリエンスを両立できます。 このサンプルでは、カスタム シンボルを使用してクラスターと個々のデータ ポイントを表現します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="クラスター化されたシンボル レイヤー" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps による<a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>クラスター化されたシンボル レイヤー</a>の Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>クラスタリングとヒート マップ レイヤー

ヒート マップは、データの密度をマップ上に表示するための優れた方法です。 この視覚化は、それだけでも多数のデータ ポイントを処理できますが、データ ポイントをクラスター化し、クラスター サイズをヒート マップの重みとして使用すれば、さらに多くのデータを処理できます。 これを実現するには、ヒート マップ レイヤーの `weight` オプションを `['get', 'point_count']` に設定します。 クラスターの半径が小さい場合、クラスター化しないデータ ポイントを使用したヒート マップと比べてヒート マップの見た目はほぼ同じですが、パフォーマンスは大きく上回ります。 ただし、クラスターの半径が小さいほどヒート マップは正確になりますが、パフォーマンスの利点は少なくなります。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="クラスター加重ヒート マップ" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps による<a href='https://codepen.io/azuremaps/pen/VRJrgO/'>クラスター加重ヒート マップ</a> の Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>クラスター化されたデータ ポイントでのマウス イベント

クラスター化されたデータ ポイントを含むレイヤーでマウス イベントが発生すると、クラスター化されたデータ ポイントが GeoJSON ポイント フィーチャー オブジェクトとしてイベントに返されます。 このポイント フィーチャーには次のプロパティがあります。

| プロパティ名 | Type | 説明 |
|---------------|------|-------------|
| cluster | ブール値 | フィーチャーがクラスターを表すかどうかを示します。 |
| cluster_id | string | DataSource の `getClusterExpansionZoom`、`getClusterChildren`、および `getClusterLeaves` メソッドで使用できるクラスターの一意な ID。 |
| point_count | number | クラスターに含まれているポイントの数。 |
| point_count_abbreviated | string | `point_count` の値が長い場合にその値を省略形にした文字列。 (たとえば、4,000 が 4K になります) |

この例では、クラスター ポイントをレンダリングするバブル レイヤーを受け取り、クリック イベントを追加します。このイベントは、トリガーされると、クラスターが分解する次のズーム レベルを計算してそのレベルにマップをズームします。このとき、`DataSource` クラスの `getClusterExpansionZoom` メソッドと、クリックされたクラスター化データ ポイントの `cluster_id` プロパティが使用されます。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps による <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> の Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="display-cluster-area"></a>クラスター領域を表示する 

クラスターが表すポイント データは領域に分散しています。 このサンプルでは、クラスターにマウス カーソルを合わせると、それに含まれる個々のデータ ポイント (リーフ) を使用して凸包が計算され、領域を示すためにマップ上に表示されます。 クラスターに含まれるすべてのポイントは、`getClusterLeaves` メソッドを使用してデータソースから取得できます。 凸包は輪ゴムのように一連のポイントを囲む多角形であり、`atlas.math.getConvexHull` メソッドを使用して計算できます。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="クラスター領域の凸包" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps による<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>クラスター領域の凸包</a>の Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="aggregating-data-in-clusters"></a>クラスター内のデータの集計

多くの場合、クラスターはクラスター内のいくつかのポイントと共にシンボルを使用して表されますが、クラスター内のすべてのポイントの合計収益など、いくつかのメトリックに基づいてクラスターのスタイルをさらにカスタマイズすることが望ましい場合があります。 クラスター集計では、[集計式](data-driven-style-expressions-web-sdk.md#aggregate-expression)の計算を使用してカスタム プロパティを作成および設定できます。  クラスター集計は、`DataSource` の `clusterProperties` オプションで定義できます。

次のサンプルでは、集計式を使用して、クラスター内の各データ ポイントのエンティティ型プロパティに基づいてカウントを計算します。

<iframe height="500" style="width: 100%;" scrolling="no" title="クラスター集計" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/jgYyRL/'>クラスター集計</a>の Pen を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [DataSource クラス](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math 名前空間](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

ご利用のアプリに機能を追加するには、次のコード例を参照してください。

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](map-add-pin.md)

> [!div class="nextstepaction"]
> [ヒート マップ レイヤーを追加する](map-add-heat-map-layer.md)
