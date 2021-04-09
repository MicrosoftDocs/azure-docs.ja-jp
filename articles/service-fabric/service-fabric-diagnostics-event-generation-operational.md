---
title: Azure Service Fabric イベント一覧
description: クラスターの監視に役立つ、Azure Service Fabric によって提供されるイベントの包括的な一覧。
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "85846651"
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

| EventId | 名前 | カテゴリ | 説明 |ソース (タスク) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | アップグレード | クラスターのアップグレードが開始されました | CM | Informational |
| 29628 | ClusterUpgradeCompleted | アップグレード | クラスターのアップグレードが完了しました | CM | Informational | 
| 29629 | ClusterUpgradeRollbackStarted | アップグレード | クラスターのアップグレードのロールバックが開始されました  | CM | 警告 | 
| 29630 | ClusterUpgradeRollbackCompleted | アップグレード | クラスターのアップグレードのロールバックが完了しました | CM | 警告 | 
| 29631 | ClusterUpgradeDomainCompleted | アップグレード | アップグレード ドメインはクラスターのアップグレード中にアップグレードを完了しました | CM | Informational | 

## <a name="node-events"></a>ノード イベント

**ノード ライフサイクル イベント** 

| EventId | 名前 | カテゴリ | 説明 |ソース (タスク) | Level |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | ノードの非アクティブ化が完了しました | FM | Informational | 
| 18603 | NodeUp | StateTransition | クラスターでノードが起動したことが検出されました | FM | Informational | 
| 18604 | NodeDown | StateTransition | クラスターでノードがシャットダウンしたことが検出されました。 ノードの再起動中に、NodeUp イベント、続いて NodeDown イベントが発生します |  FM | エラー | 
| 18605 | NodeAddedToCluster | StateTransition |  新しいノードがクラスターに追加されており、Service Fabric はこのノードにアプリケーションをデプロイできます | FM | Informational | 
| 18606 | NodeRemovedFromCluster | StateTransition |  クラスターからノードが削除されました。 Service Fabric ではこのノードにそれ以上アプリケーションはデプロイされません | FM | Informational | 
| 18607 | NodeDeactivateStarted | StateTransition |  ノードの非アクティブ化が開始されました | FM | Informational | 
| 25621 | NodeOpenSucceeded | StateTransition |  ノードが正常に起動しました | FabricNode | Informational | 
| 25622 | NodeOpenFailed | StateTransition |  ノードは起動してリングに参加できませんでした | FabricNode | エラー | 
| 25624 | NodeClosed | StateTransition |  ノードが正常にシャットダウンしました | FabricNode | Informational | 
| 25626 | NodeAborted | StateTransition |  正常ではない方法でノードがシャットダウンしました | FabricNode | エラー | 

## <a name="application-events"></a>アプリケーション イベント

**アプリケーション ライフサイクル イベント**

| EventId | 名前 | カテゴリ | 説明 |ソース (タスク) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | 新しいアプリケーションが作成されました | CM | Informational | 
| 29625 | ApplicationDeleted | LifeCycle | 既存のアプリケーションが削除されました | CM | Informational | 
| 23083 | ApplicationProcessExited | LifeCycle | アプリケーション内のプロセスが終了しました | Hosting | Informational | 

**アプリケーション アップグレード イベント**

アプリケーションのアップグレードについて詳しくは、[こちら](service-fabric-application-upgrade.md)をご覧ください。

| EventId | 名前 | カテゴリ | 説明 |ソース (タスク) | Level | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | アップグレード | アプリケーションのアップグレードが開始されました | CM | Informational | 
| 29622 | ApplicationUpgradeCompleted | アップグレード | アプリケーションのアップグレードが完了しました | CM | Informational | 
| 29623 | ApplicationUpgradeRollbackStarted | アップグレード | アプリケーションのアップグレードのロールバックが開始されました |CM | 警告 | 
| 29624 | ApplicationUpgradeRollbackCompleted | アップグレード | アプリケーションのアップグレードのロールバックが完了しました | CM | 警告 | 
| 29626 | ApplicationUpgradeDomainCompleted | アップグレード | アップグレード ドメインはアプリケーションのアップグレード中にアップグレードを完了しました | CM | Informational | 

## <a name="service-events"></a>サービス イベント

**サービス ライフサイクル イベント**

| EventId | 名前 | カテゴリ | 説明 |ソース (タスク) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | 新しいサービスが作成されました | FM | Informational | 
| 18658 | ServiceDeleted | LifeCycle | 既存のサービスが削除されました | FM | Informational | 

## <a name="partition-events"></a>パーティション イベント

**パーティション移動イベント**

| EventId | 名前 | カテゴリ | 説明 |ソース (タスク) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | パーティションの再構成が完了しました | RA | Informational | 

## <a name="replica-events"></a>レプリカ イベント

**レプリカのライフサイクル イベント**

| EventId | 名前 | カテゴリ | 説明 |ソース (タスク) | Level |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | LifeCycle | リライアブル ディクショナリが開きました | DistributedDictionary | Informational |
| 61702 | ReliableDictionaryClosed | LifeCycle | リライアブル ディクショナリが終了しました | DistributedDictionary | Informational |
| 61703 | ReliableDictionaryCheckpointRecovered | LifeCycle | リライアブル ディクショナリがそのチェックポイントを回復しました | DistributedDictionary | Informational |
| 61704 | ReliableDictionaryCheckpointFilesSent | LifeCycle | レプリカがリライアブル ディクショナリのチェックポイント ファイルを送信しました | DistributedDictionary | Informational |
| 61705 | ReliableDictionaryCheckpointFilesReceived | LifeCycle | レプリカがリライアブル ディクショナリのチェックポイント ファイルを受信しました | DistributedDictionary | Informational |
| 61963 | ReliableQueueOpened | LifeCycle | リライアブル キューが開きました | DistributedQueue | Informational |
| 61964 | ReliableQueueClosed | LifeCycle | リライアブル キューが終了しました | DistributedQueue | Informational |
| 61965 | ReliableQueueCheckpointRecovered | LifeCycle | リライアブル キューがそのチェックポイントを回復しました | DistributedQueue | Informational |
| 61966 | ReliableQueueCheckpointFilesSent | LifeCycle | レプリカがリライアブル キューのチェックポイント ファイルを送信しました | DistributedQueue | Informational |
| 63647 | ReliableQueueCheckpointFilesReceived | LifeCycle | レプリカがリライアブル キューのチェックポイント ファイルを受信しました | DistributedQueue | Informational |
| 63648 | ReliableConcurrentQueueOpened | LifeCycle | 同時実行のリライアブル キューが開きました | ReliableConcurrentQueue | Informational |
| 63649 | ReliableConcurrentQueueClosed | LifeCycle | 同時実行のリライアブル キューが終了しました | ReliableConcurrentQueue | Informational |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | LifeCycle | 同時実行のリライアブル キューがそのチェックポイントを回復しました | ReliableConcurrentQueue | Informational |
| 61687 | TStoreError | 障害 | リライアブル コレクションで予期しないエラーが発生しました | TStore | エラー |
| 63831 | PrimaryFullCopyInitiated | LifeCycle | プライマリ レプリカが完全なコピーを開始しました | TReplicator | Informational |
| 63832 | PrimaryPartialCopyInitiated | LifeCycle | プライマリ レプリカが部分的コピーを開始しました | TReplicator | Informational |
| 16831 | BuildIdleReplicaStarted | LifeCycle | プライマリ レプリカがアイドル状態のレプリカのビルドを開始しました | レプリケーション | Informational |
| 16832 | BuildIdleReplicaCompleted | LifeCycle | プライマリ レプリカがアイドル状態のレプリカのビルドを完了しました | レプリケーション | Informational |
| 16833 | BuildIdleReplicaFailed | LifeCycle | プライマリ レプリカがアイドル状態のレプリカのビルドに失敗しました | レプリケーション | 警告 |
| 16834 | PrimaryReplicationQueueFull | Health | プライマリ レプリカのレプリケーション キューがいっぱいです | レプリケーション | 警告 |
| 16835 | PrimaryReplicationQueueWarning | Health | プライマリ レプリカのレプリケーション キューがほぼいっぱいです | レプリケーション | 警告 |
| 16836 | PrimaryReplicationQueueWarningMitigated | Health | プライマリ レプリカのレプリケーション キューは問題ありません | レプリケーション | Informational |
| 16837 | SecondaryReplicationQueueFull | Health | セカンダリ レプリカのレプリケーション キューがいっぱいです | レプリケーション | 警告 |
| 16838 | SecondaryReplicationQueueWarning | Health | セカンダリ レプリカのレプリケーション キューがほぼいっぱいです | レプリケーション | 警告 |
| 16839 | SecondaryReplicationQueueWarningMitigated | Health | セカンダリ レプリカのレプリケーション キューは問題ありません | レプリケーション | Informational |
| 16840 | PrimaryFaultedSlowSecondary | Health | プライマリ レプリカが、低速のセカンダリ レプリカでエラーが発生しました | レプリケーション | 警告 |
| 16841 | ReplicatorFaulted | Health | レプリカでエラーが発生しました | レプリケーション | 警告 |

## <a name="container-events"></a>コンテナー イベント

**コンテナー ライフサイクル イベント** 

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | コンテナーが起動しました | Hosting | Informational | 1 |
| 23075 | ContainerDeactivated | コンテナーが停止しました | Hosting | Informational | 1 |
| 23082 | ContainerExited | コンテナーが終了しました。UnexpectedTermination フラグを確認してください | Hosting | Informational | 1 |

## <a name="health-reports"></a>正常性レポート

[Service Fabric 正常性モデル](service-fabric-health-introduction.md)では、機能が豊富で、柔軟性と拡張可能性を備えた正常性評価とレポートが提供されます。 Service Fabric バージョン 6.2 以降では、正常性の履歴レコードを提供するため、正常性データはプラットフォーム イベントとして書き込まれます。 正常性イベントの量を低く抑えるため、以下のもののみが Service Fabric イベントとして書き込まれます。

* すべての `Error` または `Warning` の正常性レポート
* 移行中の `Ok` 正常性レポート
* `Error` または `Warning` の正常性イベントの有効期限が切れたとき。 これを使用すると、エンティティが正常な状態ではなかった時間がわかります

**クラスター正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | クラスターの新しい正常性レポートが使用可能です | HM | Informational | 1 |
| 54437 | ClusterHealthReportExpired | クラスターの既存の正常性レポートの有効期限が切れました | HM | Informational | 1 |

**ノード正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | ノードの新しい正常性レポートが使用可能です | HM | Informational | 1 |
| 54432 | NodeHealthReportExpired | ノードの既存の正常性レポートの有効期限が切れました | HM | Informational | 1 |

**アプリケーション正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | アプリケーションの新しい正常性レポートが作成されました。 これは展開解除されたアプリケーションのレポートです。 | HM | Informational | 1 |
| 54426 | DeployedApplicationNewHealthReport | 展開済みアプリケーションの新しい正常性レポートが作成されました | HM | Informational | 1 |
| 54427 | DeployedServicePackageNewHealthReport | 展開済みサービスの新しい正常性レポートが作成されました | HM | Informational | 1 |
| 54434 | ApplicationHealthReportExpired | アプリケーションの既存の正常性レポートの有効期限が切れました | HM | Informational | 1 |
| 54435 | DeployedApplicationHealthReportExpired | 展開済みアプリケーションの既存の正常性レポートの有効期限が切れました | HM | Informational | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | 展開済みサービスの既存の正常性レポートの有効期限が切れました | HM | Informational | 1 |

**サービス正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | サービスの新しい正常性レポートが作成されました | HM | Informational | 1 |
| 54433 | ServiceHealthReportExpired | サービスの既存の正常性レポートの有効期限が切れました | HM | Informational | 1 |

**パーティション正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | パーティションの新しい正常性レポートが作成されました | HM | Informational | 1 |
| 54431 | PartitionHealthReportExpired | パーティションの既存の正常性レポートの有効期限が切れました | HM | Informational | 1 |

**レプリカ正常性レポート イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | ステートフル レプリカの正常性レポートが作成されました | HM | Informational | 1 |
| 54430 | StatelessInstanceNewHealthReport | ステートレス インスタンスの新しい正常性レポートが作成されました | HM | Informational | 1 |
| 54438 | StatefulReplicaHealthReportExpired | ステートフル レプリカの既存の正常性レポートの有効期限が切れました | HM | Informational | 1 |
| 54439 | StatelessInstanceHealthReportExpired | ステートレス インスタンスの既存の正常性レポートの有効期限が切れました | HM | Informational | 1 |

## <a name="chaos-testing-events"></a>カオス テスト イベント 

**カオス セッション イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | カオス テスト セッションが開始されました | Testability | Informational | 1 |
| 50023 | ChaosStopped | カオス テスト セッションが中止されました | Testability | Informational | 1 |

**Chaos ノード イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | カオス テスト セッションの一環として、ノードの再起動がスケジュールされました | Testability | Informational | 1 |
| 50087 | ChaosNodeRestartCompleted | カオス テスト セッションの一環として、ノードの再起動が完了しました | Testability | Informational | 1 |

**Chaos アプリケーション イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | カオス テスト セッション中に、コード パッケージの再起動がスケジュールされました | Testability | Informational | 1 |
| 50101 | ChaosCodePackageRestartCompleted | カオス テスト セッション中に、コード パッケージの再起動が完了しました | Testability | Informational | 1 |

**Chaos パーティション イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | カオス テスト セッションの一環として、プライマリ パーティションの移動がスケジュールされました | Testability | Informational | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | カオス テスト セッションの一環として、セカンダリ パーティションの移動がスケジュールされました | Testability | Informational | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | プライマリ パーティションの移動の詳細な分析が使用可能です | Testability | Informational | 1 |

**Chaos レプリカ イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | カオス テスト セッションの一環として、レプリカの再起動がスケジュールされました | Testability | Informational | 1 |
| 50051 | ChaosReplicaRemovalScheduled | カオス テスト セッションの一環として、レプリカの削除がスケジュールされました | Testability | Informational | 1 |
| 50093 | ChaosReplicaRemovalCompleted | カオス テスト セッションの一環として、レプリカの削除が完了しました | Testability | Informational | 1 |

## <a name="other-events"></a>その他のイベント

**相関イベント**

| EventId | 名前 | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | 相関関係が検出されました | Testability | Informational | 1 |

## <a name="events-prior-to-version-62"></a>バージョン 6.2 より前のイベント

バージョン 6.2 より前の Service Fabric で提供されるイベントの包括的な一覧を次に示します。

| EventId | 名前 | ソース (タスク) | Level |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informational |
| 25621 | NodeOpenedSuccess | FabricNode | Informational |
| 25622 | NodeOpenedFailed | FabricNode | Informational |
| 25623 | NodeClosing | FabricNode | Informational |
| 25624 | NodeClosed | FabricNode | Informational |
| 25625 | NodeAborting | FabricNode | Informational |
| 25626 | NodeAborted | FabricNode | Informational |
| 29627 | ClusterUpgradeStart | CM | Informational |
| 29628 | ClusterUpgradeComplete | CM | Informational |
| 29629 | ClusterUpgradeRollback | CM | Informational |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informational |
| 29631 | ClusterUpgradeDomainComplete | CM | Informational |
| 23074 | ContainerActivated | Hosting | Informational |
| 23075 | ContainerDeactivated | Hosting | Informational |
| 29620 | ApplicationCreated | CM | Informational |
| 29621 | ApplicationUpgradeStart | CM | Informational |
| 29622 | ApplicationUpgradeComplete | CM | Informational |
| 29623 | ApplicationUpgradeRollback | CM | Informational |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informational |
| 29625 | ApplicationDeleted | CM | Informational |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informational |
| 18566 | ServiceCreated | FM | Informational |
| 18567 | ServiceDeleted | FM | Informational |

## <a name="next-steps"></a>次のステップ

* [Service Fabric での診断](service-fabric-diagnostics-overview.md)の概要を把握する
* [Service Fabric Eventstore の概要](service-fabric-diagnostics-eventstore.md)に関する記事で EventStore についてさらに学習する
* より多くのログを収集するための [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) の構成の変更
* 稼動チャネルのログを参照するための [Application Insights の設定](service-fabric-diagnostics-event-analysis-appinsights.md)
