---
title: 'Service Fabric クラスターのセキュリティ: クライアント ロール'
description: この記事では、2 つのクライアント ロールと、ロールに提供されるアクセス許可について説明します。
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75451894"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>ロール ベースのアクセス制御 (Service Fabric クライアント用)
Azure Service Fabric では、Service Fabric クラスターに接続されるクライアントのために、管理者用とユーザー用の 2 つの異なるアクセス コントロールの種類がサポートされています。 アクセス制御を使用すると、クラスター管理者は、ユーザーのグループごとに特定のクラスター操作へのアクセスを制限して、クラスターのセキュリティを強化できます。  

**管理者** には、管理機能へのフル アクセス権 (読み取り/書き込み機能など) が付与されています。 **ユーザー** には、管理機能 (クエリ機能など) と、アプリケーションとサービスを解決する機能への読み取りアクセス権のみが既定で付与されています。

クラスターの作成時に、2 つのクライアント ロール (管理者とクライアント) を指定し、それぞれに個別の証明書を設定します。 セキュリティで保護された Service Fabric クラスターのセットアップの詳細については、 [Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md) に関するページをご覧ください。

## <a name="default-access-control-settings"></a>既定のアクセス制御の設定
管理者のアクセス制御の種類は、すべての FabricClient API へのフル アクセスを持ちます。 Service Fabric クラスターに対して、次のような任意の読み取りおよび書き込み操作を実行できます。

### <a name="application-and-service-operations"></a>アプリケーションおよびサービスの操作
* **CreateService**: サービスの作成                             
* **CreateServiceFromTemplate**: テンプレートからのサービスの作成                             
* **UpdateService**: サービスの更新                             
* **DeleteService**: サービスの削除                             
* **ProvisionApplicationType**: アプリケーションの種類のプロビジョニング                             
* **CreateApplication**: アプリケーションの作成                               
* **DeleteApplication**: アプリケーションの削除                             
* **UpgradeApplication**: アプリケーションのアップグレードの開始または中断                             
* **UnprovisionApplicationType**: アプリケーションの種類のプロビジョニング解除                             
* **MoveNextUpgradeDomain**: 明示的な更新ドメインでのアプリケーションのアップグレードの再開                             
* **ReportUpgradeHealth**: 現在のアップグレードの進行状況でのアプリケーションのアップグレードの再開                             
* **ReportHealth**: 正常性のレポート                             
* **PredeployPackageToNode**: デプロイメント前の API                            
* **CodePackageControl**: コード パッケージの再開                             
* **RecoverPartition**: パーティションの復旧                             
* **RecoverPartitions**: 複数のパーティションの復旧                             
* **RecoverServicePartitions**: サービス パーティションの復旧                             
* **RecoverSystemPartitions**: システム サービス パーティションの復旧                             

### <a name="cluster-operations"></a>クラスターの操作
* **ProvisionFabric**: MSI やクラスター マニフェストのプロビジョニング                             
* **UpgradeFabric**: クラスターのアップグレードの開始                             
* **UnprovisionFabric**: MSI やクラスター マニフェストのプロビジョニング解除                         
* **MoveNextFabricUpgradeDomain**: 明示的な更新ドメインを使用したクラスターのアップグレードの再開                             
* **ReportFabricUpgradeHealth**: 現在のアップグレードの進行状況でのクラスターのアップグレードの再開                             
* **StartInfrastructureTask**: インフラストラクチャのタスクの開始                             
* **FinishInfrastructureTask**: インフラストラクチャのタスクの終了                             
* **InvokeInfrastructureCommand**: インフラストラクチャのタスクの管理コマンド                              
* **ActivateNode**: ノードのアクティブ化                             
* **DeactivateNode**: ノードの非アクティブ化                             
* **DeactivateNodesBatch**: 複数のノードの非アクティブ化                             
* **RemoveNodeDeactivations**: 複数のノードでの非アクティブ化の取り消し                             
* **GetNodeDeactivationStatus**: 非アクティブ化の状態の確認                             
* **NodeStateRemoved**: 削除済みノード状態のレポート                             
* **ReportFault**: 障害のレポート                             
* **FileContent**: イメージ ストア クライアント ファイルの転送 (クラスターの外部へ)                             
* **FileDownload**: イメージ ストア クライアント ファイルのダウンロードの開始 (クラスターの外部へ)                             
* **InternalList**: イメージ ストア クライアント ファイルの一覧操作 (内部)                             
* **Delete**: イメージ ストア クライアントの削除操作                              
* **Upload**: イメージ ストア クライアントのアップロード操作                             
* **NodeControl**: ノードの開始、停止、および再開                             
* **MoveReplicaControl**: ノードから別のノードへのレプリカの移動                             

### <a name="miscellaneous-operations"></a>その他の演算子
* **Ping**: クライアントの ping                             
* **Query**: 許可されるすべてのクエリ
* **NameExists**: 名前付け URI の存在確認                             

ユーザー アクセス制御の種類は既定で次の操作に制限されています。 

* **EnumerateSubnames**: 名前付け URI の列挙                             
* **EnumerateProperties**: 名前付けプロパティの列挙                             
* **PropertyReadBatch**: 名前付けプロパティの読み取り操作                             
* **GetServiceDescription**: 長いポーリングのサービス通知と読み取りサービスの説明                             
* **ResolveService**: クレーム ベースのサービス解決                             
* **ResolveNameOwner**: 名前付け URI の所有者の解決                             
* **ResolvePartition**: システム サービスの解決                             
* **ServiceNotifications**: イベント ベースのサービスの通知                             
* **GetUpgradeStatus**: アプリケーションのアップグレード状態のポーリング                             
* **GetFabricUpgradeStatus**: クラスターのアップグレード状態のポーリング                             
* **InvokeInfrastructureQuery**: インフラストラクチャ タスクのクエリ                             
* **List**: イメージ ストア クライアント ファイルのリスト操作                             
* **ResetPartitionLoad**: フェールオーバー ユニットの負荷の再設定                             
* **ToggleVerboseServicePlacementHealthReporting**: 詳細なサービス配置正常性レポートの切り替え                             

管理者アクセス制御も、前述の操作にアクセスできます。

## <a name="changing-default-settings-for-client-roles"></a>クライアント ロールの既定の設定の変更
クラスター マニフェスト ファイルでは、必要であれば、クライアントに管理者機能を提供できます。 既定の設定を変更するには、[クラスターの作成](service-fabric-cluster-creation-via-portal.md)中に **[Fabric の設定]** オプションに移動し、**name**、**admin**、**user**、**value** の各フィールドに前述の設定を入力します。

## <a name="next-steps"></a>次のステップ
[Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)

[Service Fabric クラスターの作成](service-fabric-cluster-creation-via-portal.md)

