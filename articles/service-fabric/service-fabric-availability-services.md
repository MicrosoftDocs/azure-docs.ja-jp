---
title: Service Fabric サービスの可用性 | Microsoft Docs
description: 障害の検出、フェールオーバー、サービスの回復について説明します
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: dd10af0d3c8a57168a27a039286ea0ec4c1dad02
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662749"
---
# <a name="availability-of-service-fabric-services"></a>Service Fabric サービスの可用性
この記事では、Azure Service Fabric がサービスの可用性を維持する方法の概要を示します。

## <a name="availability-of-service-fabric-stateless-services"></a>Service Fabric ステートレス サービスの可用性
Service Fabric サービスには、ステートレスなものとステートフルなものがあります。 ステートレス サービスとは、高い可用性または信頼性が求められる[ローカル状態](service-fabric-concepts-state.md)のないアプリケーション サービスです。

ステートレス サービスを作成するには、`InstanceCount` を定義する必要があります。 このインスタンス数により、クラスター内で実行されるステートレス サービスのアプリケーション ロジックのインスタンス数を定義します。 ステートレス サービスをスケール アウトする場合は、インスタンス数を増やす方法が推奨されています。

ステートレスの名前付きサービスのインスタンスでエラーが発生すると、クラスター内の適当なノードに新しいインスタンスが作成されます。 たとえば、ステートレス サービス インスタンスが Node1 で失敗すると、Node5 で再度作成されます。

## <a name="availability-of-service-fabric-stateful-services"></a>Service Fabric ステートフル サービスの可用性
ステートフル サービスには、関連付けられている状態があります。 Service Fabric の場合、ステートフル サービスはレプリカのセットとしてモデル化されます。 各レプリカは、サービスのコードの実行中のインスタンスです。 レプリカには、そのサービスの状態のコピーもあります。 読み取りおよび書き込み操作は、1 つのレプリカ (*プライマリ*と呼ばれます) で実行されます。 書き込み操作からの状態の変更は、そのレプリカ セット内のその他のレプリカ (*アクティブ セカンダリ*と呼ばれます) に "*レプリケート*" されて適用されます。 

プライマリ レプリカは 1 つのみですが、アクティブ セカンダリ レプリカは複数ある場合があります。 アクティブ セカンダリ レプリカの数は構成可能で、レプリカの数を多くすると、ソフトウェア障害やハードウェア障害が多数同時発生した場合への耐性が得られます。

プライマリ レプリカがダウンした場合、Service Fabric はアクティブ セカンダリ レプリカの 1 つを新しいプライマリ レプリカにします。 このアクティブ セカンダリ レプリカは、更新されたバージョンの状態を*レプリケーション*経由で既に取得しているため、その先の読み取り処理と書き込み処理を続行できます。 このプロセスは*再構成*と呼ばれます。詳細については、[再構成に関する記事](service-fabric-concepts-reconfiguration.md)を参照してください。

レプリカがプライマリであったりアクティブ セカンダリであったりするこの概念は、*レプリカ ロール*と呼ばれます。 レプリカの詳細については、「[レプリカとインスタンス](service-fabric-concepts-replica-lifecycle.md)」をご覧ください。 

## <a name="next-steps"></a>次の手順
Service Fabric の概念について詳しくは、次の記事をご覧ください。

- [Service Fabric サービスのスケーリング](service-fabric-concepts-scalability.md)
- [Service Fabric サービスのパーティション分割](service-fabric-concepts-partitioning.md)
- [状態の定義と管理](service-fabric-concepts-state.md)
- [Reliable Service](service-fabric-reliable-services-introduction.md)

