---
title: マップでのポイント データのクラスタリング | Microsoft Azure Maps
description: マップでポイント データをクラスター化する方法について説明します。 Azure Maps Web SDK を使用してデータをクラスター化し、クラスターのマウス イベントに応答して、クラスター集計を表示する方法を確認してください。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 0af327f624d0fed648012ce1d12bacabca688cec
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044189"
---
# <a name="clustering-point-data"></a>ポイント データのクラスタリング

多数のデータ ポイントをマップ上に視覚化すると、データ ポイントが互いに重なり合うことがあります。 重なり合うと、マップが読み取れなくなり、使用が困難になる可能性があります。 ポイント データのクラスタリングは、互いに近いポイント データを結合し、単一のクラスター化されたデータ ポイントとしてマップ上に表現するプロセスです。 ユーザーがマップにズーム インすると、クラスターは個々のデータ ポイントに分解します。 大量のデータ ポイントを操作する場合は、クラスタリング プロセスを使用して、ユーザー エクスペリエンスを向上させます。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player?format=ny]

## <a name="enabling-clustering-on-a-data-source"></a>データ ソースでのクラスタリングの有効化

`cluster` オプションを true に設定することにより、`DataSource` クラスでクラスタリングを簡単に有効化できます。 近くのポイントを選択し、それらをクラスターに結合するように、`clusterRadius` を設定します。 `clusterRadius` の値はピクセル単位です。 `clusterMaxZoom` を使用して、クラスタリング ロジックを無効にするズーム レベルを指定します。 次に示すのは、データ ソースでクラスタリングを有効化する方法の例です。

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
> 2 つのデータ ポイントがきわめて近い場合、ユーザーがどれだけ近くまでズーム インしてもクラスターが分解しない可能性があります。 これに対処するには、`clusterMaxZoom` オプションを設定し、クラスタリング ロジックを無効化して単純にすべてを表示することができます。

`DataSource` クラスには、クラスタリングに関連する以下のメソッドも用意されています。

| Method | の戻り値の型 : | 説明 |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | 次のズーム レベルで指定されたクラスターの子を取得します。 これらの子はシェイプとサブクラスターの組み合わせの場合があります。 サブクラスターは ClusteredProperties と一致するプロパティを持つフィーチャーになります。 |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | クラスターが拡大し始めるか、または分解するズーム レベルを計算します。 |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | クラスター内のすべてのポイントを取得します。 ポイントのサブセットを返すには `limit` を設定し、ポイントをページ送りするには `offset` を使用します。 |

## <a name="display-clusters-using-a-bubble-layer"></a>バブル レイヤーを使用してクラスターを表示する

バブルレイヤーは、クラスター化されたポイントをレンダリングするための優れた方法です。 クラスター内のポイントの数に基づき、式を使用して半径を拡大し、色を変更します。 バブル レイヤーを使用してクラスターを表示する場合、クラスター化されていないデータ ポイントをレンダリングするための別のレイヤーを使用することをお勧めします。

バブルの上にクラスターのサイズを表示するには、テキスト付きのシンボル レイヤーを使用し、アイコンを使用しないようにします。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本的なバブル レイヤー クラスタリング" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps による<a href='https://codepen.io/azuremaps/pen/qvzRZY/'>基本的なバブル レイヤー クラスタリング</a>の Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>シンボル レイヤーを使用してクラスターを表示する

データ ポイントを視覚化すると、シンボル レイヤーは互いに重なり合うシンボルを自動的に非表示にして、ユーザー インターフェイスをすっきりさせることができます。 マップにデータ ポイントの密度を表示する場合、この既定の動作は望ましくないことがあります。 ただし、これらの設定は変更できます。 すべてのシンボルを表示するには、シンボル レイヤー `iconOptions` プロパティの `allowOverlap` オプションを `true` に設定します。 

クラスタリングを使用して、ユーザー インターフェイスをすっきりとした状態に維持しながらデータ ポイントの密度を表示します。 次のサンプルは、カスタム シンボルを追加し、シンボル レイヤーを使用して、クラスターと個々のデータ ポイントを表示する方法を示しています。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="クラスター化されたシンボル レイヤー" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps による<a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>クラスター化されたシンボル レイヤー</a>の Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>クラスタリングとヒート マップ レイヤー

ヒート マップは、データの密度をマップ上に表示するための優れた方法です。 この視覚化方法では、多数のデータ ポイントを自然に処理できます。 データ ポイントがクラスター化されており、クラスター サイズがヒート マップの重みとして使用されている場合、ヒート マップはさらに多くのデータを処理できます。 このオプションを実現するには、ヒート マップ レイヤーの `weight` オプションを `['get', 'point_count']` に設定します。 クラスターの半径が小さい場合、クラスター化しないデータ ポイントを使用したヒート マップと比べてヒート マップの見た目はほぼ同じですが、パフォーマンスは大きく上回ります。 ただし、クラスターの半径が小さいほどヒート マップは正確になりますが、パフォーマンスの利点は少なくなります。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="クラスター加重ヒート マップ" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps による<a href='https://codepen.io/azuremaps/pen/VRJrgO/'>クラスター加重ヒート マップ</a> の Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>クラスター化されたデータ ポイントでのマウス イベント

クラスター化されたデータ ポイントを含むレイヤーでマウス イベントが発生すると、クラスター化されたデータ ポイントは GeoJSON ポイント フィーチャー オブジェクトとしてイベントに返されます。 このポイント フィーチャーには次のプロパティがあります。

| プロパティ名             | Type    | 説明   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | フィーチャーがクラスターを表すかどうかを示します。 |
| `cluster_id`              | string  | DataSource の `getClusterExpansionZoom`、`getClusterChildren`、および `getClusterLeaves` メソッドで使用できるクラスターの一意な ID。 |
| `point_count`             | number  | クラスターに含まれているポイントの数。  |
| `point_count_abbreviated` | string  | `point_count` の値が長い場合にその値を省略形にした文字列。 (たとえば、4,000 が 4K になります)  |

この例では、クラスター ポイントをレンダリングし、クリック イベントを追加したバブル レイヤーを使用します。 クリック イベントがトリガーされると、コードによってマップが計算され、クラスターが分割されている次のズーム レベルにズームされます。 この機能は、`DataSource` クラスの `getClusterExpansionZoom` メソッドと、クリックされたクラスター化されたデータ ポイントの `cluster_id` プロパティを使用して実装されます。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps による <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> の Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="display-cluster-area"></a>クラスター領域を表示する 

クラスターが表すポイント データは領域に分散しています。 このサンプルでは、マウスがクラスター上に置かれたとき、主に 2 つの動作が行われます。 まず、クラスターに含まれている個々のデータ ポイントを使用して、凸包を計算します。 次に、領域を表示するために凸包がマップに表示されます。  凸包は輪ゴムのように一連のポイントを囲む多角形であり、`atlas.math.getConvexHull` メソッドを使用して計算できます。 クラスターに含まれるすべてのポイントは、`getClusterLeaves` メソッドを使用してデータソースから取得できます。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="クラスター領域の凸包" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps による<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>クラスター領域の凸包</a>の Pen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="aggregating-data-in-clusters"></a>クラスター内のデータの集計

クラスターは多くの場合、クラスター内のポイントの数を示すシンボルを使用して表現されます。 ただし、追加のメトリックを使用して、クラスターのスタイルをカスタマイズすることが望ましい場合もあります。 クラスター集計では、[集計式](data-driven-style-expressions-web-sdk.md#aggregate-expression)の計算を使用してカスタム プロパティを作成および設定できます。  クラスター集計は、`DataSource` の `clusterProperties` オプションで定義できます。

次の例では集計式が使用されています。 このコードでは、クラスター内の各データ ポイントのエンティティ型プロパティに基づいて、カウントを計算します。 ユーザーがクラスターをクリックすると、クラスターに関する追加情報を示すポップアップが表示されます。

<iframe height="500" style="width: 100%;" scrolling="no" title="クラスター集計" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/jgYyRL/'>クラスター集計</a>の Pen を <a href='https://codepen.io'>CodePen</a> で表示する。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [DataSource クラス](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions オブジェクト](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [atlas.math 名前空間](/javascript/api/azure-maps-control/atlas.math)

ご利用のアプリに機能を追加するには、次のコード例を参照してください。

> [!div class="nextstepaction"]
> [バブル レイヤーを追加する](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](map-add-pin.md)

> [!div class="nextstepaction"]
> [ヒート マップ レイヤーを追加する](map-add-heat-map-layer.md)