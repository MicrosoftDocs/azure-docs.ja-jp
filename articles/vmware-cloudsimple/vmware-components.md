---
title: CloudSimple による Azure VMware ソリューション - プライベート クラウド VMware コンポーネント
description: VMware コンポーネントをプライベート クラウド上にインストールする方法について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 62511118edb4f8b5061f90138bac2aa2b5d3cfe3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165153"
---
# <a name="private-cloud-vmware-components"></a>プライベート クラウド VMware コンポーネント

プライベート クラウドとは、管理ドメイン内の vCenter サーバーによって管理される、分離された VMware スタック (ESXi ホスト、vCenter、vSAN、および NSX) 環境のことです。  CloudSimple サービスを使用すると、Azure の場所にある Azure ベア メタル インフラストラクチャ上に VMware をネイティブにデプロイできます。  プライベート クラウドは、Azure クラウドの残りの部分と統合されます。  プライベート クラウドをデプロイするときに使用する VMware スタック コンポーネントを次に示します。

* **VMware ESXi -** Azure 専用ノード上のハイパーバイザー
* **VMware vCenter -** プライベート クラウド vSphere 環境を一元管理するためのアプライアンス
* **VMware vSAN -** ハイパー コンバージド インフラストラクチャ ソリューション
* **VMware NSX Data Center -** ネットワーク仮想化およびセキュリティ ソフトウェア  

## <a name="vmware-component-versions"></a>VMware コンポーネントのバージョン

プライベート クラウド VMware スタックをデプロイするときに使用するソフトウェア バージョンを次に示します。

| コンポーネント | バージョン | ライセンス付きのバージョン |
|-----------|---------|------------------|
| ESXi | 6.7U1 | Enterprise Plus |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.3 | 詳細 |

## <a name="esxi"></a>ESXi

VMware ESXi は、プライベート クラウドを作成するときに、プロビジョニングされた CloudSimple ノード上にインストールされます。  ESXi は、ワークロード仮想マシン (VM) をデプロイするためのハイパーバイザーです。  ノードは、プライベート クラウド上のハイパー コンバージド インフラストラクチャ (コンピューティングおよびストレージ) です。  これらのノードは、プライベート クラウド上で vSphere クラスターを構成する一部となります。  各ノードでは、4 つの物理ネットワーク インターフェイスがアンダーレイ ネットワークに接続されています。  2 つの物理ネットワーク インターフェイスを使用して、vCenter 上の **vSphere 分散スイッチ (VDS)** と、**NSX マネージド仮想分散スイッチ (N-VDS)** が作成されます。  高可用性を実現するため、ネットワーク インターフェイスは、アクティブ/アクティブ モードで構成されます。

VMware ESXi の詳細を確認してください。

## <a name="vcenter-server-appliance"></a>vCenter サーバー アプライアンス

vCenter サーバー アプライアンス (VCSA) は、CloudSimple による VMware ソリューションで、認証、管理、およびオーケストレーションの各機能を提供します。 プライベート クラウドを作成するときは、Platform Services Controller (PSC) が組み込まれた VCSA をデプロイします。  VCSA は、プライベート クラウドをデプロイするときに作成した vSphere クラスター上にデプロイします。  各プライベート クラウドには、専用の VCSA があります。  プライベート クラウドを拡張すると、プライベート クラウド上の VCSA にノードが追加されます。

### <a name="vcenter-single-sign-on"></a>vCenter シングル サインオン

VCSA に組み込まれた Platform Services Controller は、**vCenter シングル サインオン ドメイン**に関連付けられています。  ドメイン名は、**cloudsimple.local** です。  vCenter にアクセスするために既定のユーザー **CloudOwner@cloudsimple.com** が作成されます。  vCenter 用のオンプレミス/Azure Active Directory [ID ソース](https://docs.azure.cloudsimple.com/set-vcenter-identity/)を追加できます。

## <a name="vsan-storage"></a>vSAN ストレージ

プライベート クラウドは、完全に構成されたオール フラッシュ vSAN ストレージを使用して、クラスター ローカルで作成されます。  vSAN データストアを含む vSphere クラスターを作成するには、同じ SKU のノードが 3 つ以上必要です。  vSAN データストア上では、重複除去と圧縮が既定で有効になっています。  vSphere クラスターの各ノード上には、2 つのディスク グループが作成されます。 各ディスク グループには、1 つのキャッシュ ディスクと 3 つのキャパシティ ディスクが含まれています。

既定の vSAN ストレージ ポリシーが vSphere クラスター上に作成され、vSAN データストアに適用されます。  必要なサービス レベルを保証するため、このポリシーでは、VM ストレージ オブジェクトをデータ ストア内でプロビジョニングおよび割り当てする方法が決定されます。  ストレージ ポリシーでは、**許容エラー (FTT)** と **エラー許容メソッド**が定義されます。  新規のストレージ ポリシーを作成して VM に適用することが可能です。 SLA を維持するため、vSAN データストア上では、25% のキャパシティを予備として確保しておく必要があります。  

### <a name="default-vsan-storage-policy"></a>既定の vSAN ストレージ ポリシー

次の表は、vSAN ストレージ ポリシー パラメーターの既定値を示しています。

| Number of nodes in vSphere Cluster (vSphere クラスター内のノード数) | FTT | Failure tolerance method (エラー許容メソッド) |
|------------------------------------|-----|--------------------------|
| 3 ～ 4 個のノード | 1 | RAID 1 (ミラーリング) - 2 つのコピーを作成 |
| 5 ～ 16 個のノード | 2 | RAID 1 (ミラーリング) - 3 つのコピーを作成 |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center は、プライベート クラウド上でネットワーク仮想化、マイクロ セグメント化、およびネットワーク セキュリティの各機能を提供します。  プライベート クラウド上の NSX Data Center でサポートされるすべてのサービスは、NSX を通じて構成できます。  プライベート クラウドを作成するときは、次の NSX コンポーネントをインストールして構成します。

* NSXT Manager
* トランスポート ゾーン
* ホストおよびエッジのアップリンク プロファイル
* エッジ トランスポート用の論理スイッチ (Ext1 および Ext2)
* ESXi トランスポート ノード用の IP プール
* エッジ トランスポート ノード用の IP プール
* エッジ ノード
* コントローラーおよびエッジ VM の DRS アンチ アフィニティ ルール
* 階層 0 ルーター
* 階層 0 ルーター上 BGP を有効化

## <a name="vsphere-cluster"></a>vSphere クラスター

ESXi ホストは、プライベート クラウドの高可用性を保証するため、クラスターとして構成されます。  プライベート クラウドを作成するときは、最初のクラスターに vSphere の管理コンポーネントがデプロイされます。  管理コンポーネント用のリソース プールが作成され、このリソース プール内にすべての管理 VM がデプロイされます。 最初のクラスターを削除して、プライベート クラウドを縮小することはできません。  vSphere クラスターでは、**vSphere HA** を使用して、VM の高可用性が実現されます。  許容エラーは、クラスター内の使用可能なノード数に基づきます。  ```Number of nodes = 2N+1``` という式が使用されます。ここで ```N``` は、許容エラーの数です。

### <a name="vsphere-cluster-limits"></a>vSphere クラスターの制限

| Resource | 制限 |
|----------|-------|
| プライベート クラウドを作成するためのノードの最小数 (最初の vSphere クラスター) | 3 |
| プライベート クラウド上の vSphere クラスター内のノードの最大数 | 16 |
| プライベート クラウド内の最大ノード数 | 64 |
| プライベート クラウド内の vSphere クラスターの最大数 | 21 |
| 新しい vSphere クラスター上のノードの最小数 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware インフラストラクチャのメンテナンス

VMware インフラストラクチャの構成変更が必要になることがあります。 現在のところ、この間隔は 1-2 か月に 1 回ですが、頻度は時間の経過と共に下がると予想されます。 この種類のメンテナンスは通常、CloudSimple サービスの通常利用を中断することなく実行できます。 VMware メンテナンスの期間中、次のサービスは影響なしで作動し続けます。

* VMware 管理プレーンとアプリケーション
* vCenter アクセス
* すべてのネットワークとストレージ
* すべての Azure トラフィック

## <a name="updates-and-upgrades"></a>更新とアップグレード

CloudSimple は、プライベート クラウド内の VMware ソフトウェア (ESXi、vCenter、PSC、NSX) のライフサイクル管理を担います。

ソフトウェア更新の内容:

* **パッチ**。 VMware によってリリースされたセキュリティ パッチまたはバグ修正プログラム。
* **[Updates]** (更新)。 VMware スタック コンポーネントのマイナー バージョン変更。
* **アップグレード**。 VMware スタック コンポーネントのメジャー バージョン変更。

CloudSimple は、VMware から重要なセキュリティ パッチが利用できるようになると、直後にそれをテストします。 SLA に基づき、CloudSimple は 1 週間以内にプライベート クラウド環境にセキュリティ パッチをロールアウトします。

CloudSimple は、VMware ソフトウェア コンポーネントに四半期ごとのメンテナンス更新を提供します。 新しいメジャー バージョンの VMware ソフトウェアが利用できるようになると、CloudSimple は顧客と連携し、アップグレードに適したメンテナンス期間を調整します。  

## <a name="next-steps"></a>次の手順

* [CloudSimple のメンテナンスと更新](cloudsimple-maintenance-updates.md)