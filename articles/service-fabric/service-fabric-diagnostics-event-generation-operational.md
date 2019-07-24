---
title: Azure Service Fabric のイベントの一覧 | Microsoft Docs
description: クラスターの監視に役立つ、Azure Service Fabric によって提供されるイベントの包括的な一覧。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: 7a4cccf774d89229810c1668f38e4e2ef99fa79d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663946"
---
# <a name="list-of-service-fabric-events"></a>Service Fabric イベントの一覧 

Service Fabric は、クラスター イベントのプライマリ セットを公開して、クラスターの状態を [Service Fabric イベント](service-fabric-diagnostics-events.md)として通知します。 これらは、ノードとクラスター上の Service Fabric によって実行されるアクション、またはクラスターの所有者/オペレーターによって行われる管理上の決定事項に基づきます。 これらのイベントは、[クラスターでの Azure Monitor ログ](service-fabric-diagnostics-oms-setup.md)の構成や、[EventStore](service-fabric-diagnostics-eventstore.md) のクエリなど、さまざまな方法で構成することによりアクセスできます。 Windows マシンでは、これらのイベントは EventLog にフィードされるので、イベント ビューアーで Service Fabric イベントを確認できます。 

これらのイベントの特徴をいくつか次に示します
* 各イベントは、クラスター内の特定のエンティティ (アプリケーション、サービス、ノード、レプリカなど) に関連付けられています。
* 各イベントには、一連の共通フィールドが含まれています:EventInstanceId、EventName、Category。
* 各イベントには、それが関連付けられているエンティティに逆に結び付けるフィールドが含まれています。 たとえば、ApplicationCreated イベントには、作成されたアプリケーションの名前を示すフィールドがあります。
* イベントは、さらに分析するためのさまざまなツールで使用できるように構造化されています。 さらに、イベントに関する詳細は、長い文字列ではなく個別のプロパティとして定義されています。 
* イベントは Service Fabric のさまざまなサブシステムによって書き込まれ、以下のソース (タスク) によって識別されます。 これらのサブシステムについて詳しくは、「[Service Fabric のアーキテクチャ](service-fabric-architecture.md)」および [Service Fabric の技術概要](service-fabric-technical-overview.md)に関する記事をご覧ください。

これらの Service Fabric イベントの一覧をエンティティ別に示します。

## <a name="cluster-events"></a>クラスター イベント

**クラスター アップグレード イベント**

クラスターのアップグレードについて詳しくは、[こちら](service-fabric-cluster-upgrade-windows-server.md)をご覧ください。

| EventId | 名前 | Category | 説明 |ソース (タスク) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | アップグレード | クラスターのアップグレードが開始されました | CM | 情報 |
| 29628 | ClusterUpgradeCompleted | アップグレード | クラスターのアップグレードが完了しました | CM | 情報 | 
| 29629 | ClusterUpgradeRollbackStarted | アップグレード | クラスターのアップグレードのロールバックが開始されました  | CM | 警告 | 
| 29630 | ClusterUpgradeRollbackCompleted | アップグレード | クラスターのアップグレードのロールバックが完了しました | CM | 警告 | 
| 29631 | ClusterUpgradeDomainCompleted | アップグレード | アップグレード ドメインはクラスターのアップグレード中にアップグレードを完了しました | CM | 情報 | 

## <a name="node-events"></a>ノード イベント

**ノード ライフサイクル イベント** 

| EventId | 名前 | Category | 説明 |ソース (タスク) | Level |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | ノードの非アクティブ化が完了しました | FM | 情報 | 
| 18603 | NodeUp | StateTransition | クラスターでノードが起動したことが検出されました | FM | 情報 | 
| 18604 | NodeDown | StateTransition | クラスターでノードがシャットダウンしたことが検出されました。 ノードの再起動中に、NodeUp イベント、続いて NodeDown イベントが発生します |  FM | Error | 
| 18605 | NodeAddedToCluster | StateTransition |  新しいノードがクラスターに追加されており、Service Fabric はこのノードにアプリケーションをデプロイできます | FM | 情報 | 
| 18606 | NodeRemovedFromCluster | StateTransition |  クラスターからノードが削除されました。 Service Fabric ではこのノードにそれ以上アプリケーションはデプロイされません | FM | 情報 | 
| 18607 | NodeDeactivateStarted | StateTransition |  ノードの非アクティブ化が開始されました | FM | 情報 | 
| 25621 | NodeOpenSucceeded | StateTransition |  ノードが正常に起動しました | FabricNode | 情報 | 
| 25622 | NodeOpenFailed | StateTransition |  ノードは起動してリングに参加できませんでした | FabricNode | Error | 
| 25624 | NodeClosed | StateTransition |  ノードが正常にシャットダウンしました | FabricNode | 情報 | 
| 25626 | NodeAborted | StateTransition |  正常ではない方法でノードがシャットダウンしました | FabricNode | Error | 

## <a name="application-events"></a>アプリケーション イベント

**アプリケーション ライフサイクル イベント**

| EventId | 名前 | Category | 説明 |ソース (タスク) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | 新しいアプリケーションが作成されました | CM | 情報 | 
| 29625 | ApplicationDeleted | LifeCycle | 既存のアプリケーションが削除されました | CM | 情報 | 
| 23083 | ApplicationProcessExited | LifeCycle | アプリケーション内のプロセスが終了しました | Hosting | 情報 | 

**アプリケーション アップグレード イベント**

アプリケーションのアップグレードについて詳しくは、[こちら](service-fabric-application-upgrade.md)をご覧ください。

| EventId | 名前 | Category | 説明 |ソース (タスク) | Level | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | アップグレード | アプリケーションのアップグレードが開始されました | CM | 情報 | 
| 29622 | ApplicationUpgradeCompleted | アップグレード | アプリケーションのアップグレードが完了しました | CM | 情報 | 
| 29623 | ApplicationUpgradeRollbackStarted | アップグレード | アプリケーションのアップグレードのロールバックが開始されました |CM | 警告 | 
| 29624 | ApplicationUpgradeRollbackCompleted | アップグレード | アプリケーションのアップグレードのロールバックが完了しました | CM | 警告 | 
| 29626 | ApplicationUpgradeDomainCompleted | アップグレード | アップグレード ドメインはアプリケーションのアップグレード中にアップグレードを完了しました | CM | 情報 | 

## <a name="service-events"></a>サービス イベント

**サービス ライフサイクル イベント**

| EventId | 名前 | Category | 説明 |ソース (タスク) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | 新しいサービスが作成されました | FM | 情報 | 
| 18658 | ServiceDeleted | LifeCycle | 既存のサービスが削除されました | FM | 情報 | 

## <a name="partition-events"></a>パーティション イベント

**パーティション移動イベント**

| EventId | 名前 | Category | 説明 |ソース (タスク) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | パーティションの再構成が完了しました | RA | 情報 | 

## <a name="container-events"></a>コンテナー イベント

**コンテナー ライフサイクル イベント** 

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | コンテナーが起動しました | Hosting | 情報 | 1 |
| 23075 | ContainerDeactivated | コンテナーが停止しました | Hosting | 情報 | 1 |
| 23082 | ContainerExited | コンテナーが終了しました。UnexpectedTermination フラグを確認してください | Hosting | 情報 | 1 |

## <a name="health-reports"></a>正常性レポート

[Service Fabric 正常性モデル](service-fabric-health-introduction.md)では、機能が豊富で、柔軟性と拡張可能性を備えた正常性評価とレポートが提供されます。 Service Fabric バージョン 6.2 以降では、正常性の履歴レコードを提供するため、正常性データはプラットフォーム イベントとして書き込まれます。 正常性イベントの量を低く抑えるため、以下のもののみが Service Fabric イベントとして書き込まれます。

* すべての `Error` または `Warning` の正常性レポート
* 移行中の `Ok` 正常性レポート
* `Error` または `Warning` の正常性イベントの有効期限が切れたとき。 これを使用すると、エンティティが正常な状態ではなかった時間がわかります

**クラスター正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | クラスターの新しい正常性レポートが使用可能です | HM | 情報 | 1 |
| 54437 | ClusterHealthReportExpired | クラスターの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

**ノード正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | ノードの新しい正常性レポートが使用可能です | HM | 情報 | 1 |
| 54432 | NodeHealthReportExpired | ノードの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

**アプリケーション正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | アプリケーションの新しい正常性レポートが作成されました。 これは展開解除されたアプリケーションのレポートです。 | HM | 情報 | 1 |
| 54426 | DeployedApplicationNewHealthReport | 展開済みアプリケーションの新しい正常性レポートが作成されました | HM | 情報 | 1 |
| 54427 | DeployedServicePackageNewHealthReport | 展開済みサービスの新しい正常性レポートが作成されました | HM | 情報 | 1 |
| 54434 | ApplicationHealthReportExpired | アプリケーションの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |
| 54435 | DeployedApplicationHealthReportExpired | 展開済みアプリケーションの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | 展開済みサービスの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

**サービス正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | サービスの新しい正常性レポートが作成されました | HM | 情報 | 1 |
| 54433 | ServiceHealthReportExpired | サービスの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

**パーティション正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | パーティションの新しい正常性レポートが作成されました | HM | 情報 | 1 |
| 54431 | PartitionHealthReportExpired | パーティションの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

**レプリカ正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | ステートフル レプリカの正常性レポートが作成されました | HM | 情報 | 1 |
| 54430 | StatelessInstanceNewHealthReport | ステートレス インスタンスの新しい正常性レポートが作成されました | HM | 情報 | 1 |
| 54438 | StatefulReplicaHealthReportExpired | ステートフル レプリカの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |
| 54439 | StatelessInstanceHealthReportExpired | ステートレス インスタンスの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

## <a name="chaos-testing-events"></a>カオス テスト イベント 

**カオス セッション イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | カオス テスト セッションが開始されました | Testability | 情報 | 1 |
| 50023 | ChaosStopped | カオス テスト セッションが中止されました | Testability | 情報 | 1 |

**Chaos ノード イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | カオス テスト セッションの一環として、ノードの再起動がスケジュールされました | Testability | 情報 | 1 |
| 50087 | ChaosNodeRestartCompleted | カオス テスト セッションの一環として、ノードの再起動が完了しました | Testability | 情報 | 1 |

**Chaos アプリケーション イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | カオス テスト セッション中に、コード パッケージの再起動がスケジュールされました | Testability | 情報 | 1 |
| 50101 | ChaosCodePackageRestartCompleted | カオス テスト セッション中に、コード パッケージの再起動が完了しました | Testability | 情報 | 1 |

**Chaos パーティション イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | カオス テスト セッションの一環として、プライマリ パーティションの移動がスケジュールされました | Testability | 情報 | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | カオス テスト セッションの一環として、セカンダリ パーティションの移動がスケジュールされました | Testability | 情報 | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | プライマリ パーティションの移動の詳細な分析が使用可能です | Testability | 情報 | 1 |

**Chaos レプリカ イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | カオス テスト セッションの一環として、レプリカの再起動がスケジュールされました | Testability | 情報 | 1 |
| 50051 | ChaosReplicaRemovalScheduled | カオス テスト セッションの一環として、レプリカの削除がスケジュールされました | Testability | 情報 | 1 |
| 50093 | ChaosReplicaRemovalCompleted | カオス テスト セッションの一環として、レプリカの削除が完了しました | Testability | 情報 | 1 |

## <a name="other-events"></a>その他のイベント

**相関イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | バージョン |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | 相関関係が検出されました | Testability | 情報 | 1 |

## <a name="events-prior-to-version-62"></a>バージョン 6.2 より前のイベント

バージョン 6.2 より前の Service Fabric で提供されるイベントの包括的な一覧を次に示します。

| EventId | 名前 | ソース (タスク) | Level |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | 情報 |
| 25621 | NodeOpenedSuccess | FabricNode | 情報 |
| 25622 | NodeOpenedFailed | FabricNode | 情報 |
| 25623 | NodeClosing | FabricNode | 情報 |
| 25624 | NodeClosed | FabricNode | 情報 |
| 25625 | NodeAborting | FabricNode | 情報 |
| 25626 | NodeAborted | FabricNode | 情報 |
| 29627 | ClusterUpgradeStart | CM | 情報 |
| 29628 | ClusterUpgradeComplete | CM | 情報 |
| 29629 | ClusterUpgradeRollback | CM | 情報 |
| 29630 | ClusterUpgradeRollbackComplete | CM | 情報 |
| 29631 | ClusterUpgradeDomainComplete | CM | 情報 |
| 23074 | ContainerActivated | ホスティング | 情報 |
| 23075 | ContainerDeactivated | Hosting | 情報 |
| 29620 | ApplicationCreated | CM | 情報 |
| 29621 | ApplicationUpgradeStart | CM | 情報 |
| 29622 | ApplicationUpgradeComplete | CM | 情報 |
| 29623 | ApplicationUpgradeRollback | CM | 情報 |
| 29624 | ApplicationUpgradeRollbackComplete | CM | 情報 |
| 29625 | ApplicationDeleted | CM | 情報 |
| 29626 | ApplicationUpgradeDomainComplete | CM | 情報 |
| 18566 | ServiceCreated | FM | 情報 |
| 18567 | ServiceDeleted | FM | 情報 |

## <a name="next-steps"></a>次の手順

* [Service Fabric での診断](service-fabric-diagnostics-overview.md)の概要を把握する
* [Service Fabric Eventstore の概要](service-fabric-diagnostics-eventstore.md)に関する記事で EventStore についてさらに学習する
* より多くのログを収集するための [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) の構成の変更
* 稼動チャネルのログを参照するための [Application Insights の設定](service-fabric-diagnostics-event-analysis-appinsights.md)
