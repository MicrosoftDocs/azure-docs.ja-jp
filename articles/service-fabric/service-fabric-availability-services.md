---
title: "Service Fabric サービスの可用性 | Microsoft Docs"
description: "障害の検出、フェールオーバー、サービスの回復について説明します"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: b0d4615a9b8ab566f69b27e4879b6e2d597b4990
ms.contentlocale: ja-jp
ms.lasthandoff: 08/19/2017

---

# <a name="availability-of-service-fabric-services"></a>Service Fabric サービスの可用性
この記事では、Service Fabric がサービスの可用性を維持する方法の概要を示します。

## <a name="availability-of-service-fabric-stateless-services"></a>Service Fabric ステートレス サービスの可用性
Azure Service Fabric サービスには、ステートレスなものとステートフルなものがあります。 ステートレス サービスとは、高い可用性または信頼性が求められる[ローカル状態](service-fabric-concepts-state.md)のないアプリケーション サービスです。

ステートレス サービスを作成するには、`InstanceCount` を定義する必要があります。 このインスタンス数により、クラスター内で実行されるステートレス サービスのアプリケーション ロジックのインスタンス数を定義します。 ステートレス サービスをスケール アウトする場合は、インスタンス数を増やす方法が推奨されています。

ステートレスの名前付きサービスのインスタンスでエラーが発生すると、クラスター内の適当なノードに新しいインスタンスが作成されます。 たとえば、ステートレス サービス インスタンスが Node1 で失敗すると、Node5 で再作成されます。

## <a name="availability-of-service-fabric-stateful-services"></a>Service Fabric ステートフル サービスの可用性
ステートフル サービスには、関連付けられている状態があります。 Service Fabric の場合、ステートフル サービスはレプリカのセットとしてモデル化されます。 各レプリカは、サービスのコードの実行中のインスタンスであり、そのサービスの状態のコピーも持ちます。 読み取りおよび書き込み操作は、1 つのレプリカ (プライマリと呼ばれます) で実行されます。 書き込み操作からの状態の変更は、そのレプリカ セット内のその他のレプリカ (アクティブ セカンダリと呼ばれます) に "*レプリケート*" されて適用されます。 

プライマリ レプリカは 1 つのみですが、アクティブ セカンダリ レプリカは複数ある場合があります。 アクティブ セカンダリ レプリカの数は構成可能で、レプリカの数を多くすると、ソフトウェア障害やハードウェア障害が多数同時発生した場合への耐性が得られます。

プライマリ レプリカがダウンした場合、Service Fabric はアクティブ セカンダリ レプリカの 1 つを新しいプライマリ レプリカにします。 このアクティブ セカンダリ レプリカは、更新されたバージョンの状態を (*レプリケーション*経由で) 既に取得しているため、その先の読み取り処理と書き込み処理を続行できます。

レプリカがプライマリであったりアクティブ セカンダリであったりするこの概念は、レプリカ ロールと呼ばれます。

### <a name="replica-roles"></a>レプリカ ロール
レプリカのロールは、そのレプリカによって管理されている状態のライフサイクルを管理するために使用されます。 ロールがプライマリになっているレプリカは、読み取り要求を処理します。 またプライマリは、状態を更新し、変更内容をレプリケートすることで、すべての書き込み要求を処理します。 これらの変更は、レプリカ セット内のアクティブ セカンダリに適用されます。 アクティブ セカンダリのジョブは、プライマリ レプリカがレプリケートした状態の変更を受信し、その状態のビューを更新することです。

> [!NOTE]
> [Reliable Actors](service-fabric-reliable-actors-introduction.md) や [Reliable Services](service-fabric-reliable-services-introduction.md) など、抽象度の高いプログラミング モデルの場合、レプリカ ロールの概念は開発者には隠されています。 Reliable Actors では、ロールの概念は不要です。Reliable Services では、ほとんどの場合に大幅に簡略化されます。
>

## <a name="next-steps"></a>次のステップ
Service Fabric の概念について詳しくは、次の記事をご覧ください。

- [Service Fabric サービスのスケーリング](service-fabric-concepts-scalability.md)
- [Service Fabric サービスのパーティション分割](service-fabric-concepts-partitioning.md)
- [状態の定義と管理](service-fabric-concepts-state.md)
- [Reliable Service](service-fabric-reliable-services-introduction.md)

