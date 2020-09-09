---
title: Azure Maps の Mobility Service のデータ構造 | Microsoft Azure Maps
description: Azure Maps Mobility Service で、データがどのように都市圏にまとめられているかについて説明します。 どのフィールドに、公共交通機関の停留所や路線に関する情報が格納されているかを確認します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 02d89226bd2df4bfe5d11897199c50c702e7bc1c
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88033225"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure Maps Mobility Service のデータ構造

この記事では、[Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService) における都市圏の概念について説明します。 ここでは、このサービスで公共輸送機関の停留所や路線についてクエリを実行したときに返されるいくつかの一般的なフィールドについて説明します。 Mobility Service API を使って開発をする前に、この記事を読むことをお勧めします。

## <a name="metro-area"></a>都市圏

Mobility Service データは、サポートされている都市圏によってグループ化されています。 都市圏は都市の境界に沿っていません。 1 つの都市圏に複数の都市、人口密集都市、周辺都市が含まれる場合があります。 実際、国/地域が 1 つの都市圏になる可能性があります。 

`metroID` は都市圏の ID であり、[Get Metro Area Info API](https://aka.ms/AzureMapsMobilityMetroAreaInfo) を呼び出すために使用できます。 Azure Maps の "Get Metro" API を使用して、輸送の種類、輸送機関、アクティブなアラート、選択した都市圏の追加情報を要求します。 サポートされている都市圏と metroID を要求することもできます。 都市圏 ID は変更されることがあります。

**metroID:** 522   **名前:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停留所 ID

輸送機関の停留所は、[General Transit Feed Specification (GTFS)](http://gtfs.org/) ID と Azure Maps の停留所 ID の 2 種類の ID で参照できます。 GTFS ID は stopKey と呼ばれ、Azure Maps の停留所 ID は stopID と呼ばれます。 輸送機関の停留所を頻繁に参照する場合は、Azure Maps の停留所 ID を使用することをお勧めします。 stopID はより静的なものであり、物理的な停留所が存在する限り同じままです。 GTFS の停留所 ID は、より頻繁に更新されます。 たとえば、GTFS の停留所 ID は、GTFS プロバイダーの要求ごとに更新されることも、新しい GTFS のバージョンがリリースされたときに更新されることもあります。 物理的な停留所が変更されていなくても、GTFS の停留所 ID は変更される可能性があります。

始めに、[Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit) を使用して近くの輸送機関の停留所を要求できます。

## <a name="line-groups-and-lines"></a>路線グループと路線

Mobility Service では、路線と路線グループに並列データ モデルを使用しています。 このモデルは、[GTFS](http://gtfs.org/) ルートから継承された変更と、乗車データをより適切に処理するために使用されます。


### <a name="line-groups"></a>路線グループ

路線グループはエンティティであり、論理的に同じグループの一部であるすべての路線をグループ化します。 通常、路線グループには 2 つの路線が含まれます。1 つはポイント A から B へ、もう 1 つはポイント B から A への路線です。両方の路線が同じ公共輸送機関に属し、同じ路線番号を持ちます。 ただし、1 つの路線グループに 3 つ以上の路線が含まれている場合や、1 つの路線しか含まれていない場合があります。


### <a name="lines"></a>路線

前述のように、各路線グループは一連の路線から構成されます。 各路線グループは 2 つの路線で構成され、各路線は方向を表します。  ただし、1 つの路線グループがより多くの路線で構成される場合があります。 たとえば、特定の近隣を迂回する場合としない場合がある路線があります。 どちらの場合も同じ路線番号で運行されます。 また、1 つの路線グループが 1 つの路線で構成される場合があります。 一方向の循環路線は、1 つの路線を持つ路線グループです。

始めに、[Get Transit Line API](https://aka.ms/AzureMapsMobilityTransitLine) を使用して路線グループを要求することができます。


## <a name="next-steps"></a>次のステップ

Mobility Service を使用して輸送データを要求する方法について説明します。

> [!div class="nextstepaction"]
> [輸送データを要求する方法](how-to-request-transit-data.md)

Mobility Service を使用してリアルタイム データを要求する方法について説明します。

> [!div class="nextstepaction"]
> [リアルタイム データを要求する方法](how-to-request-real-time-data.md)

Azure Maps Mobility Service API のドキュメントを確認します

> [!div class="nextstepaction"]
> [Mobility Service API ドキュメント](https://aka.ms/AzureMapsMobilityService)
