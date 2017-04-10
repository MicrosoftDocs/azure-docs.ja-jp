---
title: "Azure マイクロサービスで状態を定義および管理する | Microsoft Docs"
description: "Service Fabric でサービスの状態を定義し管理する方法"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: d03bd6a4c317da67a4e6d0e8cdb0cbd3f07d5a1f


---
# <a name="service-state"></a>サービスの状態
**サービスの状態**とは、サービスが機能するために必要なデータのことです。 これには、サービスが機能するために読み取りや書き込みをする、データ構造および変数が含まれます。

例として、電卓サービスを考えてみす。 このサービスは、2 つの数値を受け取り、その合計を返します。 これは、関連付けられているデータを持たない純粋なステートレス サービスです。

ここで、同じ電卓が、合計を返すだけでなく、計算した最終合計を返すメソッドも持っているとしましょう。 このサービスは、ステートフルなサービスです - このサービスには、書き込みをする状態 (新しい合計を計算する場合) と読み取りをする状態 (計算の最終合計を返す場合) とを含むからです。

Azure Service Fabric の場合、前者のサービスはステートレス サービスと呼ばれます。 後者のサービスは、ステートフル サービスと呼ばれます。

## <a name="storing-service-state"></a>サービスの状態の格納
状態は、外部化するか、状態を操作するコードと同じ場所に配置できます。 状態の外部化は、通常、外部データベースまたはストアを使用してなされます。 電卓の例では、現在の結果がテーブルに格納される SQL データベースなどが該当します。 合計値の計算要求ごとに、この行の更新が実行されます。

状態は、このコードを操作するコードと同じ場所に配置することもできます。 Service Fabric のステートフル サービスは、このモデルを使って構築できます。 Service Fabric は、可用性が高く、障害発生時のフォールト トレランスの高い状態を実現するインフラストラクチャを提供します。

## <a name="next-steps"></a>次のステップ
Service Fabric の概念について詳しくは、次の記事をご覧ください。

* [Service Fabric サービスの可用性](service-fabric-availability-services.md)
* [Service Fabric サービスの拡張性](service-fabric-concepts-scalability.md)
* [Service Fabric サービスのパーティション分割](service-fabric-concepts-partitioning.md)
* [Service Fabric の Reliable Services](service-fabric-reliable-services-introduction.md)



<!--HONumber=Jan17_HO4-->


