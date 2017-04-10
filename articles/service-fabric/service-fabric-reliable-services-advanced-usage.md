---
title: "Reliable Services の詳細な使用方法 | Microsoft Docs"
description: "サービスの柔軟性を高めるために、Service Fabric の Reliable Services の高度な使用方法について説明します。"
services: Service-Fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: masnider
ms.assetid: f2942871-863d-47c3-b14a-7cdad9a742c7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a0180074df84b1e8eba27066787559d8d2c80c50


---
# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Reliable Services プログラミング モデルの詳細な使用方法
Azure Service Fabric により、信頼性の高いステートレス サービスとステートフル サービスの作成と管理が簡素化されます。 このガイドでは、サービスを細かく制御し、柔軟性を高めるために、Reliable Services の高度な使用方法について説明します。 このガイドをお読みになる前に、 [Reliable Services プログラミング モデル](service-fabric-reliable-services-introduction.md)について理解しておいてください。

ステートフル サービスとステートレス サービスには、どちらもユーザー コードの 2 つのプライマリ エントリ ポイントがあります。

* `RunAsync` は、サービス コードの汎用エントリ ポイントです。
* `CreateServiceReplicaListeners` と `CreateServiceInstanceListeners` は、クライアント要求のために通信リスナーを開くためのものです。

ほとんどのサービスでは、この 2 つのエントリ ポイントで十分です。 まれですが、サービスのライフサイクルを細かく制御する必要がある場合は、追加のライフサイクル イベントを使用できます。

## <a name="stateless-service-instance-lifecycle"></a>ステートレス サービス インスタンスのライフサイクル
ステートレス サービスのライフサイクルは、非常にシンプルです。 ステートレス サービスの操作は、開く、終了、および中止だけです。 `RunAsync` は、サービス インスタンスを開くと実行され、サービス インスタンスを終了するか中止するとキャンセルされます。 

ほとんどの場合、 `RunAsync` で十分ですが、ステートレス サービスの開く、終了、中止の各イベントも使用できます。

* `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
   OnOpenAsync はステートレス サービス インスタンスが使用されるときに呼び出されます。 この時点で、拡張サービス初期化タスクを開始できます。
* `Task OnCloseAsync(CancellationToken)`
   OnCloseAsync は、ステートレス サービス インスタンスが正常にシャットダウンされるときに呼び出されます。 これは、サービスのコードがアップグレードされるか、サービス インスタンスが負荷分散のために移動されるか、または一時的なエラーが検出されたときに行われる可能性があります。 OnCloseAsync を使用して、安全にすべてのリソースを閉じたり、バック グラウンド処理を停止したり、外部の状態の保存を完了したり、既存の接続を終了したりすることができます。
* `void OnAbort()`
   OnAbort は、ステートレス サービス インスタンスが強制的にシャットダウンされているときに呼び出されます。 これは一般に、ノードで永続的なエラーが検出されたときや Service Fabric が内部エラーのために、サービス インスタンスのライフ サイクルを確実に管理できないときに呼び出されます。

## <a name="stateful-service-replica-lifecycle"></a>ステートフル サービス レプリカのライフサイクル
ステートフル サービス レプリカのライフサイクルは、ステートレス サービス インスタンスよりもかなり複雑です。 開く、終了、中止の各イベントに加え、ステートフル サービス レプリカでは、有効期間中にロールの変更が発生します。 ステートフル サービス レプリカがロールを変更すると、 `OnChangeRoleAsync` イベントがトリガーされます。

* `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
   OnChangeRoleAsync は、ステートフル サービス レプリカのロールが、プライマリやセカンダリなどに変更されるときに呼び出されます。 プライマリ レプリカには書き込み状態が与えられます (Reliable Collection の作成と Reliable Collection への書き込みが可能)。 セカンダリ レプリカには読み取り状態が与えられます (既存の Reliable Collection からの読み取りのみが可能)。 ステートフル サービスの作業のほとんどは、プライマリ レプリカで実行されます。 セカンダリ レプリカでは、読み取り専用の検証、レポートの生成、データ マイニングなど、読み取り専用のジョブを実行できます。

ステートフル サービスでは、状態に書き込みアクセスできるのはプライマリ レプリカだけであるため、サービスが実際の作業を実行するときは、一般にプライマリ レプリカが実行します。 ステートフル サービスの `RunAsync` メソッドは、ステートフル サービス レプリカがプライマリの場合にのみ実行されます。 プライマリ レプリカのロールがプライマリから変更されたときや、終了イベントまたは中止イベントが発生したときには、 `RunAsync` メソッドはキャンセルされます。 

`OnChangeRoleAsync` イベントを使用すると、レプリカのロールやロールの変更に応じて作業を実行できます。

また、ステートフル サービスでは、ステートレス サービスと同じ 4 つのライフサイクル イベントを同じセマンティクスとユース ケースで提供します。

* `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
* `Task OnCloseAsync(CancellationToken)`
* `void OnAbort()`

## <a name="next-steps"></a>次のステップ
Service Fabric に関するさらに高度なトピックについては、次の記事をご覧ください。

* [ステートフル Reliable Services の構成](service-fabric-reliable-services-configuration.md)
* [Service Fabric の正常性の概要](service-fabric-health-introduction.md)
* [トラブルシューティングのためのシステム正常性レポートの使用](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Service Fabric クラスター リソース マネージャーでサービスを構成する](service-fabric-cluster-resource-manager-configure-services.md)




<!--HONumber=Nov16_HO3-->


