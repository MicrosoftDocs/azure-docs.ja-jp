---
title: Azure Service Fabric サービスで状態を管理する
description: Service Fabric サービスでサービスの状態を定義および管理する方法など、Azure Service Fabric の状態について説明します。
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614572"
---
# <a name="service-state"></a>サービスの状態
**サービスの状態** とは、サービスが機能するために必要な、メモリ内またはディスク上のデータのことです。 これには、たとえば、サービスが機能するために読み取りや書き込みをする、データ構造およびメンバー変数が含まれます。 サービスの設計方法によっては、ディスクに保存されているファイルやその他のリソースが含まれる場合もあります。 たとえば、データとトランザクション ログを格納するためにデータベースで使用するファイルが含まれます。

サービスの例として、電卓を考えてみましょう。 基本的な電卓サービスでは、2 つの数値を受け取り、その合計を返します。 この計算を実行するには、メンバー変数やその他の情報は必要ありません。

ここで、同じ電卓が、計算した最終合計を格納して返すメソッドを持っているとしましょう。 このサービスはステートフルなサービスです。 ステートフルとは、このサービスが、新しい合計を計算する場合に書き込みをする状態と、計算の最終合計を返すように求められた場合に読み取りをする状態を含むことを意味します。

Azure Service Fabric の場合、前者のサービスはステートレス サービスと呼ばれます。 後者のサービスは、ステートフル サービスと呼ばれます。

## <a name="storing-service-state"></a>サービスの状態の格納
状態は、外部化するか、状態を操作するコードと同じ場所に配置できます。 状態の外部化は通常、ネットワーク上のコンピューターまたは同じコンピューター上のアウト プロセスで実行されている外部データベースやその他のデータ ストアを使用して行われます。 この電卓の例では、データ ストアには、SQL データベースまたは Azure テーブル ストアのインスタンスを指定できます。 合計値の計算要求ごとに、このデータの更新が実行され、値を返すようにサービスに要求すると、ストアからフェッチされた現在の値が返されます。 

状態は、この状態を操作するコードと同じ場所に配置することもできます。 Service Fabric のステートフル サービスは通常、このモデルを使用して構築されます。 Service Fabric が提供するインフラストラクチャは、その状態の可用性、一貫性、持続性の高さと、この方法で構築されたサービスの拡張の容易さを保証します。

## <a name="next-steps"></a>次のステップ
Service Fabric の概念について詳しくは、次の記事をご覧ください。

* [Service Fabric サービスの可用性](service-fabric-availability-services.md)
* [Service Fabric サービスの拡張性](service-fabric-concepts-scalability.md)
* [Service Fabric サービスのパーティション分割](service-fabric-concepts-partitioning.md)
* [Service Fabric の Reliable Services](service-fabric-reliable-services-introduction.md)
