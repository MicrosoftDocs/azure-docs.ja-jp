---
title: CloudSimple サービスのセキュリティ
description: CloudSimple サービスのセキュリティの共同責任モデルについて説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/27/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 325915aae43c905236910382f650730a6daa127a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595336"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple のセキュリティの概要

この記事では、CloudSimple サービス、インフラストラクチャ、およびデータセンターによって Azure VMware ソリューションにセキュリティがどのように実装されるかの概要について説明します。 データ保護とセキュリティ、ネットワーク セキュリティ、および脆弱性と修正プログラムの管理方法について説明します。

## <a name="shared-responsibility"></a>Shared responsibility

CloudSimple による Azure VMware ソリューションでは、セキュリティに関する共同責任モデルを使用します。 クラウドでは、サービス プロバイダーである Microsoft とお客様の共同責任を通じて、信頼の置けるセキュリティが実現されます。 この責任を基盤とすることで、より強固なセキュリティが生まれ、単一障害点が排除されています。

## <a name="azure-infrastructure"></a>Azure インフラストラクチャ 

Azure のインフラストラクチャ セキュリティでは、たとえばデータセンターと機器の場所が慎重に検討されます。

### <a name="datacenter-security"></a>データセンターのセキュリティ 

Microsoft には、Azure をサポートしている物理的な施設の設計、建設、および運営を一手に引き受けている部門があります。 このチームは、最先端の物理セキュリティの維持に注力しています。 物理的なセキュリティの詳細については、「[Azure の施設、建物、および物理上のセキュリティ](https://docs.microsoft.com/azure/security/azure-physical-security)」を参照してください。

### <a name="equipment-location"></a>機器の場所

プライベート クラウドを実行するベア メタル ハードウェア機器は、Azure データセンターの場所でホストされます。 その機器が存在するケージにアクセスするには、生体認証ベースの 2 要素認証が必要です。

## <a name="dedicated-hardware"></a>専用のハードウェア

CloudSimple サービスの一部として、CloudSimple のすべてのお客様には、他のテナント ハードウェアから物理的に分離されたローカル接続ディスクを備える専用のベア メタル ホストが割り当てられます。 すべてのノードで、ESXi ハイパーバイザーと vSAN が実行されます。 これらのノードは、お客様専用の VMware、vCenter、および NSX 経由で管理されます。 複数のテナントによってハードウェアが共有されないため、いっそう強固な分離性とセキュリティ保護が実現されています。

## <a name="data-security"></a>データのセキュリティ

データを管理し、所有するのは常にお客様です。 お客様のデータを管理する責任はお客様にあります。

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>内部ネットワークにおける保存データと移動中のデータの保護

プライベート クラウド環境内の保存データの場合は、vSAN 暗号化を使用できます。 vSAN 暗号化は、お客様独自の仮想ネットワークまたはオンプレミスにある VMware で認定された外部のキー管理サーバー (KMS) と連携して動作します。 データ暗号化キーは、お客様がご自身で管理することになります。 プライベート クラウド内の移動中のデータの場合、vSphere では、すべての VMkernel トラフィック (vMotion トラフィックを含む) に対してネットワーク上のデータの暗号化がサポートされています。

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>パブリック ネットワーク経由で移動する必要のあるデータに対するデータ保護

パブリック ネットワーク経由で移動するデータを保護するには、プライベート クラウド用の IPsec および SSL VPN トンネルを作成できます。 128 バイト AES や 256 バイト AES を含む一般的な暗号化方法がサポートされています。 転送中のデータ (認証、管理アクセス権、および顧客データを含む) は、SSH、TLS 1.2、安全な RDP などの標準の暗号化メカニズムを使用して暗号化されます。 機密情報を転送する通信では、標準の暗号化メカニズムが使用されます。

### <a name="secure-disposal"></a>安全な廃棄 

CloudSimple サービスが期限切れになるか、または終了した場合、データを削除する責任はお客様にあります。 適用される法律によって CloudSimple が個人データの一部またはすべてを保持することが求められる場合を除き、CloudSimple は、顧客契約の規定に従い、お客様と協力してすべての顧客データを削除または返却します。 いずれかの個人データを保持する必要がある場合、CloudSimple はそのデータをアーカイブし、顧客データがそれ以上処理されないようにするための妥当な手段を実装します。

### <a name="data-location"></a>データの場所

プライベート クラウドを設定する場合は、それがデプロイされる Azure リージョンを選択します。 VMware 仮想マシンのデータは、お客様がデータ移行またはオフサイトのデータ バックアップを実行しない限り、その物理データセンターから移動されることはありません。 また必要に応じて、複数の Azure リージョン内でワークロードをホストしたり、データを格納したりすることもできます。

プライベート クラウドのハイパーコンバージド ノードに存在する顧客データは、テナント管理者の明示的なアクションなくして場所が移動されることはありません。 可用性の高い方法でワークロードを実装する責任はお客様にあります。

### <a name="data-backups"></a>データのバックアップ
お客様のデータを CloudSimple がバックアップしたりアーカイブしたりすることはありません。 管理サーバーの高可用性を確保するために、vCenter と NSX のデータについては、CloudSimple によって定期的なバックアップが実行されます。 バックアップの前に、vCenter ソースにあるすべてのデータが VMware API を使用して暗号化されます。 暗号化されたデータは転送され、Azure BLOB に格納されます。 バックアップのための暗号化キーは、Azure の CloudSimple 仮想ネットワーク内で実行される高度にセキュリティ保護された CloudSimple マネージド コンテナーに格納されます。

## <a name="network-security"></a>ネットワークのセキュリティ

CloudSimple ソリューションのネットワーク セキュリティは階層になっています。

### <a name="azure-edge-security"></a>Azure エッジ セキュリティ

CloudSimple サービスは、Azure によって提供されるベース ネットワーク セキュリティを基盤として構築されています。 異常なイングレス/エグレス トラフィック パターンや分散型サービス拒否 (DDoS) 攻撃に関連したネットワークベースの攻撃を検出して適時に対応するため、Azure には多層防御技術が利用されています。 このセキュリティ制御は、プライベート クラウド環境や、CloudSimple によって開発されたコントロール プレーン ソフトウェアに適用されます。

### <a name="segmentation"></a>セグメント化

CloudSimple サービスには、アクセスをプライベート クラウド環境内のお客様独自のプライベート ネットワークに制限する、論理的に分離されたレイヤー 2 ネットワークが用意されています。 ファイアウォールを使用して、プライベート クラウド ネットワークをさらに保護することができます。 CloudSimple ポータルでは、すべてのネットワーク トラフィックに対して東西および南北のネットワーク トラフィック制御の規則を定義します。これには、プライベート クラウド内のトラフィック、プライベート クラウド間のトラフィック、インターネットへの一般的なトラフィック、および IPsec VPN または Azure ExpressRoute 接続経由のオンプレミスへのネットワーク トラフィックが含まれます。

## <a name="vulnerability-and-patch-management"></a>脆弱性と修正プログラムの管理 

CloudSimple は、ESXi、vCenter、NSX などのマネージド VMware ソフトウェアの定期的なセキュリティ修正プログラム適用に責任を負います。

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

お客様は、推奨される方法として多要素認証または SSO を使用して (Azure Active Directory で) ご自身の Azure アカウントを認証できます。 Azure portal から、資格情報を再入力することなく CloudSimple ポータルを起動できます。

CloudSimple では、プライベート クラウド vCenter の ID ソースのオプション構成がサポートされています。 [オンプレミスの ID ソース](https://docs.azure.cloudsimple.com/set-vcenter-identity)、プライベート クラウドの新しい ID ソース、または [Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad) を使用できます。

既定では、お客様には、プライベート クラウド内での vCenter の日常の操作に必要な特権が割り当てられます。 このアクセス許可レベルに、vCenter への管理アクセス権は含まれません。 一時的に管理アクセスが必要になった場合は、その管理タスクを行っている間のみ、[権限をエスカレート](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges)することができます。

## <a name="next-steps"></a>次の手順

* [Azure で CloudSimple サービスを作成する](quickstart-create-cloudsimple-service.md)方法を学習します。
* [プライベート クラウドを作成する](https://docs.azure.cloudsimple.com/create-private-cloud/)方法を学習します。
* [プライベート クラウド環境を構成する](quickstart-create-private-cloud.md)方法を学習します。
