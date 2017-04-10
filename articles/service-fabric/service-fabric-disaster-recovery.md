---
title: "Azure Service Fabric の障害復旧 | Microsoft Docs"
description: "Azure Service Fabric では、あらゆる種類の障害に対処するために必要な機能が提供されています。 この記事では、発生する可能性がある障害の種類とそれらに対処する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/01/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 6d8f489ac053db4898741671df73b6abfabeb0dd
ms.openlocfilehash: 73f5413fb7dd0ca179bf5012478a453963e996a1
ms.lasthandoff: 12/14/2016


---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Azure Service Fabric での障害復旧
高可用性クラウド アプリケーションの提供において重要な部分は、管理者がどうすることもできないものも含めて、あらゆる種類の障害を切り抜けられるようにすることです。 この記事では、潜在的障害のコンテキストでの Azure Service Fabric クラスターの物理レイアウト、およびダウンタイムやデータ損失のリスクを制限または排除するためにそのような障害に対処する方法のガイダンスについて説明します。

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Azure での Service Fabric クラスターの物理的レイアウト
さまざまな種類の障害によってもたらされるリスクを理解するには、Azure でのクラスターの物理的レイアウトを知っておくと役に立ちます。

Azure で Service Fabric クラスターを作成するときは、クラスターがホストされるリージョンを選択する必要があります。 Azure インフラストラクチャはそのリージョン内にクラスターのリソースをプロビジョニングします。特に重要なのは、要求された数の仮想マシン (VM) です。 これらの VM がプロビジョニングされる方法と場所を詳しく見ていくことにしましょう。

### <a name="fault-domains"></a>障害ドメイン
既定では、クラスター内の VM は障害ドメイン (FD) と呼ばれる複数の論理グループに均等に分散されます。FD は、ホスト ハードウェアにおける障害の可能性に基づいてマシンを分割します。 具体的には、2 つの VM が異なる&2; つの FD に存在する場合、それらが同じ電源またはネットワーク スイッチを共有しないようにすることができます。 その結果、一方の VM に影響するローカル ネットワークまたは電源の障害が発生しても他方に影響はなく、Service Fabric はクラスター内の応答しないコンピューターの作業負荷を再調整できます。

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)で提供されるクラスター マップを使用して、障害ドメイン間のクラスターのレイアウトを視覚化できます。

![Service Fabric Explorer での障害ドメインに分散されたノードの表示][sfx-cluster-map]

> [!NOTE]
> クラスター マップにもう&1; つの軸には、アップグレード ドメインが表示されます。これは、計画的なメンテナンス アクティビティに基づいてノードを論理的にグループ化するものです。 Azure の Service Fabric クラスターは常に、5 つのアップグレード ドメインにレイアウトされます。
> 
> 

### <a name="geographic-distribution"></a>地理的な分散
現在、[世界中に&30; の Azure リージョン][azure-regions]があり、さらに追加のリージョンが発表されています。 個々のリージョンは、さまざまな要因の中でも特に需要と適切な場所の可用性に応じて、1 つまたは複数の物理的データ センターを持つことができます。 ただし、複数の物理データ センターを持つリージョンであっても、クラスターの VM がこれらの物理的な場所に均等に分散されるという保証はありません。 実際、現時点では、特定のクラスターのすべての VM は&1; つの物理サイトにプロビジョニングされます。

## <a name="dealing-with-failures"></a>障害への対処
クラスターに影響を与える可能性のある障害には複数の種類があり、それぞれに固有の対応策があります。 それらを発生する可能性の高い順に見ていきます。

### <a name="individual-machine-failures"></a>個々のマシンの障害
前述のように、個々のマシンの障害は、それが VM、ハードウェア、または障害ドメインでそれをホストするソフトウェアのどこで発生したものであっても、それ自体ではリスクはありません。 通常、Service Fabric は数秒で障害を検出し、クラスターの状態に基づいて適切に対応します。 たとえば、ノードがパーティションのプライマリ レプリカをホストしていた場合は、パーティションのセカンダリ レプリカから新しいプライマリが選択されます。 障害が発生したマシンは、Azure によって回復されると、自動的にクラスターに再び参加し、ワークロードを分担します。

### <a name="multiple-concurrent-machine-failures"></a>複数のマシンの同時障害
障害ドメインによりマシンの同時障害のリスクは大幅に低下しますが、複数のランダムな障害によってクラスター内の複数のマシンが同時にダウンする可能性は常にあります。

一般に、ノードの過半数が利用可能な限り、ステートフルなレプリカが小さいマシン セットにパックされ、負荷分散に使用できるステートレス インスタンスが少なくなるため能力は低下するものの、クラスターは動作し続けます。

#### <a name="quorum-loss"></a>クォーラムの損失
ステートフル サービスのパーティションのレプリカの過半数がダウンした場合、そのパーティションは "クォーラム損失" と呼ばれる状態になります。 この時点で、Service Fabric はそのパーティションへの書き込み許可を停止し、パーティションの一貫性と信頼性が保たれるようにします。 実際には、クライアントのデータが本当は保存されていないのに保存されたものとしてクライアントに通知されないよう、使用不可能な期間を受け入れるようになっています。 管理者がそのステートフル サービスのセカンダリ レプリカからの読み取りを許可している場合、この状態の間も読み取り操作の実行を続けられることに注意してください。 十分な数のレプリカが復旧するまで、またはクラスター管理者が [Repair-ServiceFabricPartition API][repair-partition-ps].を使って強制的にシステムを使用できるようにするまで、パーティションはクォーラム損失状態のままになります。

> [!WARNING]
> プライマリ レプリカがダウンしたときに修復アクションを実行すると、データの損失が発生します。
> 
> 

システム サービスでもクォーラム損失が発生する可能性があり、その影響は問題のあるサービスに固有です。 たとえば、ネーム サービスでクォーラム損失が発生すると名前の解決に影響があり、フェールオーバー マネージャー サービスの場合は新しいサービスの作成とフェールオーバーがブロックされます。 ユーザー独自のサービスとは異なり、システム サービスは " *修復しない* " ことをお勧めします。 代わりに、単にダウンしたレプリカが復旧するまで待つことをお勧めします。

#### <a name="minimizing-the-risk-of-quorum-loss"></a>クォーラム損失のリスクを最小限に抑える
サービスのターゲット レプリカ セットのサイズを大きくすることで、クォーラム損失のリスクを最小限に抑えることができます。 他のノードは書き込みに使用できるものとして、同時にいくつのノードが使用できなくなっても許容できるかという観点から、必要なレプリカの数を考えることをお勧めします。アプリケーションまたはクラスターのアップグレードを行うと、ハードウェア障害に加えて、ノードが一時的に使用できなくなることに注意してください。

運用サービスに推奨されるレプリカの最小数である MinReplicaSetSize を&3; にしてサービスが構成されているものとして、次の例を検討してください。 TargetReplicaSetSize が&3; では (プライマリが&1;、セカンダリが&2;)、アップグレードの間にハードウェア障害が発生すると (2 つのレプリカがダウン)、クォーラム損失状態になり、サービスは読み取り専用になります。 レプリカを&5; つにすると、アップグレードの間に&2; つのレプリカで障害が発生しても (3 つのレプリカがダウン)、残りの&2; つのレプリカが最小のレプリカ セット内のクォーラムを形成するので耐えられます。

### <a name="data-center-outages-or-destruction"></a>データ センターの停止または破壊
まれに、停電やネットワーク切断のために、物理的なデータ センターが一時的に使用できなくなることがあります。 このような場合は、Service Fabric クラスターとアプリケーションも同様に使用できなくなりますが、データは保存されます。 Azure で実行されているクラスターの場合、[Azure ステータス ページ][azure-status-dashboard]で停止時の更新を確認できます。

極めてまれなことですが、データ センター全体が物理的に破棄された場合、そこでホストされている Service Fabric クラスターはその状態と共に失われます。

このような可能性を防ぐため、地理的に異なる場所のストアに定期的に [状態をバックアップ](service-fabric-reliable-services-backup-restore.md) し、復元できるようにしておくことが非常に重要です。 バックアップを実行する頻度は、目標復旧時点 (RPO) によって決まります。 バックアップと復元をまだ完全に実装していない場合でも、次のようにして `OnDataLoss` イベント用のハンドラーを実装し、イベントが発生したときにログを取得できるようにしておく必要があります。

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(false);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>ソフトウェア障害およびデータ損失の他の原因
データ損失の原因としては、サービスのコードの欠陥、人為的な操作エラー、およびセキュリティ侵害の方が、広範囲にわたるデータ センターの障害より一般的です。 ただし、どのような場合でも、復旧方法は同じです。すべてのステートフル サービスの定期的なバックアップを実行し、その状態を復元する方法を練習しておきます。

## <a name="next-steps"></a>次のステップ
* [Testability フレームワーク](service-fabric-testability-overview.md)
* 障害復旧と高可用性に関する他のリソースを読みます。 Microsoft は、これらのトピックに関して多数のガイダンスを公開しています。 これらのドキュメントの一部は他の製品で使用するための具体的な方法に関するものですが、Service Fabric にも適用できる多くの一般的なベスト プラクティスが含まれます。
  * [可用性のチェックリスト](../best-practices-availability-checklist.md)
  * [障害復旧訓練の実行](../sql-database/sql-database-disaster-recovery-drills.md)
  * [Azure アプリケーションの障害復旧と高可用性][dr-ha-guide]
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png

