---
title: Azure VMware Solution by CloudSimple - CloudSimple サービスのセキュリティ
description: CloudSimple サービスのセキュリティの共同責任モデルについて説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d62eec72a884470ade03897a0533e62c6f1b0f71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898795"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple のセキュリティの概要

この記事では、Azure VMware Solution by CloudSimple サービス、インフラストラクチャ、およびデータセンターにセキュリティがどのように実装されるかの概要について説明します。 データ保護とセキュリティ、ネットワーク セキュリティ、および脆弱性と修正プログラムの管理方法について説明します。

## <a name="shared-responsibility"></a>共同責任

Azure VMware Solution by CloudSimple では、セキュリティに関する共同責任モデルを使用します。 クラウドでは、サービス プロバイダーである Microsoft とお客様の共同責任を通じて、信頼の置けるセキュリティが実現されます。 この責任を基盤とすることで、より強固なセキュリティが生まれ、単一障害点が排除されています。

## <a name="azure-infrastructure"></a>Azure インフラストラクチャ

Azure のインフラストラクチャ セキュリティでは、たとえばデータセンターと機器の場所が慎重に検討されます。

### <a name="datacenter-security"></a>データセンターのセキュリティ

Microsoft には、Azure をサポートしている物理的な施設の設計、建設、および運営を一手に引き受けている部門があります。 このチームは、最先端の物理セキュリティの維持に注力しています。 物理セキュリティの詳細については、「[Azure の施設、建物、および物理上のセキュリティ](../security/fundamentals/physical-security.md)」を参照してください。

### <a name="equipment-location"></a>機器の場所

お客様のプライベート クラウドを実行するベア メタル ハードウェア機器は、Azure データ センターでホストされます。  機器が置かれているケージにアクセスするためには、生体認証ベースの 2 要素認証が必要となります。

## <a name="dedicated-hardware"></a>専用のハードウェア

CloudSimple サービスの一環として、CloudSimple を利用するすべてのお客様には、他のテナントのハードウェアから物理的に隔離された専用のベア メタル ホストとローカル接続ディスクが割り当てられます。 すべてのノードで、ESXi ハイパーバイザーと vSAN が実行されます。 ノードは、お客様専用の VMware vCenter と NSX を通じて管理されます。 複数のテナントによってハードウェアが共有されないため、いっそう強固な分離性とセキュリティ保護が実現されています。

## <a name="data-security"></a>データのセキュリティ

データを管理し、所有するのは常にお客様です。 お客様のデータを管理する責任はお客様にあります。

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>内部ネットワークにおける保存データと移動中のデータの保護

プライベート クラウド環境の保存データには、vSAN 暗号化を使用できます。 vSAN 暗号化は、お客様独自の仮想ネットワークまたはオンプレミスに置かれた VMware 認定の外部キー マネージメント サーバー (KMS) と連動します。  データ暗号化キーは、お客様がご自身で管理することになります。 プライベート クラウド内の移動中のデータについては、すべての vmkernel トラフィック (vMotion トラフィックを含む) を対象に、ネットワーク上のデータの暗号化が vSphere によりサポートされます。

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>公衆ネットワークを経由する必要のあるデータの保護

公衆ネットワークを経由するデータを保護するには、プライベート クラウド用の IPsec および TLS VPN トンネルを作成できます。 128 バイト AES や 256 バイト AES を含む一般的な暗号化方法がサポートされています。 転送中のデータ (認証、管理アクセス、顧客データを含む) は、標準の暗号化メカニズム (SSH、TLS 1.2、Secure RDP) で暗号化されます。 秘匿性の高い情報を転送する通信には、標準の暗号化メカニズムが使用されます。

### <a name="secure-disposal"></a>安全な廃棄

CloudSimple サービスの有効期限が切れるか、終了した場合、お客様がご自身の責任でそのデータを移動または削除する必要があります。 適用される法令によって個人データの一部または全部を保持することが CloudSimple に義務付けられている場合を除き、CloudSimple は、顧客契約の規定に従い、お客様と連携してすべての顧客データを削除または返還します。 個人データを保持する必要がある場合、CloudSimple はデータをアーカイブし、しかるべき手段を講じて、顧客データにそれ以上の処理が適用されることを防止します。

### <a name="data-location"></a>データの場所

プライベート クラウドをセットアップする際、そのデプロイ先となる Azure リージョンをお客様が選択します。 お客様がデータ移行またはオフサイト データ バックアップを実行しない限り、その物理データセンターから VMware 仮想マシンのデータが移動されることはありません。 必要であれば、複数の Azure リージョンでワークロードをホストしたりデータを保存したりすることもできます。

テナント管理者が明示的に操作しない限り、プライベート クラウドのハイパーコンバージド ノードに存在するお客様のデータがその場所から外に出ることはありません。 ワークロードの高可用性は、お客様がご自身の責任で確保する必要があります。

### <a name="data-backups"></a>データのバックアップ

お客様のデータを CloudSimple がバックアップしたりアーカイブしたりすることはありません。 管理サーバーの高可用性を確保するために、vCenter と NSX のデータについては、CloudSimple によって定期的なバックアップが実行されます。 vCenter ソースのすべてのデータは、バックアップの前に、VMware API を使用して暗号化されます。 暗号化されたデータは Azure BLOB に転送されて保存されます。 バックアップの暗号化キーは、Azure の CloudSimple 仮想ネットワーク内で実行される、高度なセキュリティが確保された CloudSimple のマネージド コンテナーに格納されます。

## <a name="network-security"></a>ネットワークのセキュリティ

CloudSimple ソリューションのネットワーク セキュリティは階層になっています。

### <a name="azure-edge-security"></a>Azure エッジ セキュリティ

CloudSimple サービスは、Azure によって提供されるベース ネットワーク セキュリティを基盤として構築されています。 異常なイングレス/エグレス トラフィック パターンや分散型サービス拒否 (DDoS) 攻撃に関連したネットワークベースの攻撃を検出して適時に対応するため、Azure には多層防御技術が利用されています。 そのセキュリティ コントロールが、CloudSimple によって開発されたコントロール プレーン ソフトウェアとプライベート クラウド環境に適用されます。

### <a name="segmentation"></a>セグメント化

CloudSimple サービスには、お客様が所有するプライベート クラウド環境内のプライベート ネットワークへのアクセスを制限する論理的に分離されたレイヤー 2 ネットワークが使用されています。 ファイアウォールを使用することで、プライベート クラウド ネットワークの保護をさらに強固なものにすることができます。 CloudSimple ポータルでは、プライベート クラウド内のトラフィック、プライベート クラウド間のトラフィック、インターネットへの一般的なトラフィック、IPsec VPN または ExpressRoute 接続を介したオンプレミスへのネットワーク トラフィックを含め、すべてのネットワーク トラフィックを対象に、EW および NS のネットワーク トラフィック制御ルールを定義できます。

## <a name="vulnerability-and-patch-management"></a>脆弱性と修正プログラムの管理

マネージド VMware ソフトウェア (ESXi、vCenter、NSX) の定期的なセキュリティ パッチは、CloudSimple が担います。

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

お客様の (Azure AD 内の) Azure アカウントに対する認証には適宜、多要素認証または SSO を使用できます。 Azure portal から、資格情報を再入力することなく CloudSimple ポータルを起動できます。

CloudSimple では、プライベート クラウド vCenter に関して、ID ソースの構成を選択できるようになっています。 [オンプレミスの ID ソース](set-vcenter-identity.md)、プライベート クラウド用の新しい ID ソース、[Azure AD](azure-ad.md) のいずれかを使用できます。

お客様には、プライベート クラウドにおける日常的な vCenter 運用に必要な権限が既定で与えられます。 このアクセス許可レベルに、vCenter への管理アクセス権は含まれません。 一時的に管理アクセスが必要になった場合は、その管理タスクを行っている間のみ、[権限をエスカレート](escalate-private-cloud-privileges.md)することができます。
