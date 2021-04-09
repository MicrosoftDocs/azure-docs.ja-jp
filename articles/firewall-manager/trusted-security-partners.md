---
title: Azure Firewall Manager セキュリティ パートナー プロバイダーとは
description: Azure Firewall Manager セキュリティ パートナー プロバイダーについての説明
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 923c6b685d20ff68788e7d9cfcb45ebaecb535e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490110"
---
# <a name="what-are-security-partner-providers"></a>セキュリティ パートナー プロバイダーとは

Azure Firewall Manager の "*セキュリティ パートナー プロバイダー*" を使用すると、使い慣れた、最適なサードパーティのサービスとしてのセキュリティ (SECaaS) オファリングを使用して、ユーザーのインターネット アクセスを保護することができます。

クイック構成を使用すると、サポートされているセキュリティ パートナーを使用してハブをセキュリティで保護し、Virtual Network (VNet) またはリージョン内のブランチの場所からのインターネット トラフィックをルーティングしてフィルター処理することができます。 これは、ユーザー定義ルート (UDR) を設定したり、管理したりすることなく、自動ルート管理を使用して行われます。

複数の Azure リージョンで任意のセキュリティ パートナーを使用して構成されたセキュリティで保護されているハブをデプロイし、世界中のリージョンのあらゆる場所でユーザーの接続性とセキュリティを確保できます。 インターネット/SaaS アプリケーションのトラフィックではセキュリティ パートナーのオファリングを、セキュリティで保護されたハブのプライベート トラフィックでは Azure Firewall を使用でき、グローバルに分散されたユーザーとアプリケーションに近い Azure でのセキュリティ エッジの構築を開始できるようになりました。

サポートされているセキュリティ パートナーは、**Zscaler**、 **[Check Point](check-point-overview.md)** 、**iboss** です。

![セキュリティ パートナー プロバイダー](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>主なシナリオ

次のシナリオでは、セキュリティ パートナーを使用してインターネット トラフィックをフィルター処理できます。

- Virtual Network (VNet) からインターネットに

   Azure 上で実行されているクラウド ワークロードに対して、高度なユーザー対応のインターネット保護を利用します。

- ブランチからインターネットに

   Azure の接続とグローバル分散を利用して、ブランチからインターネットへのシナリオにサードパーティの NSaaS フィルター処理を簡単に追加できます。 Azure Virtual WAN を使用して、グローバルな転送ネットワークとセキュリティ エッジを構築できます。

次のシナリオがサポートされます。
- セキュリティ パートナー プロバイダー経由で VNet/ブランチからインターネット、Azure Firewall 経由のその他のトラフィック (スポークからスポーク、スポークからブランチ、ブランチからスポーク)。
- セキュリティ パートナー プロバイダー経由で VNet/ブランチからインターネット

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>セキュリティで保護された仮想ハブでのインターネット トラフィック フィルター処理に関するベストプラクティス

通常、インターネット トラフィックには Web トラフィックが含まれます。 ただし、Microsoft 365 などの SaaS アプリケーションや、Azure Storage、Azure Sql などの Azure パブリック PaaS サービス宛てのトラフィックも含まれます。 これらのサービスへのトラフィックを処理するためのベスト プラクティスの推奨事項を以下に示します。

### <a name="handling-azure-paas-traffic"></a>Azure PaaS トラフィックの処理
 
- トラフィックが Azure PaaS の大部分で構成されており、アプリケーションのリソース アクセスを IP アドレス、FQDN、サービス タグ、または FQDN タグを使用してフィルター処理できる場合は、保護に Azure Firewall を使用します。

- トラフィックが SaaS アプリケーション アクセスで構成されている場合、またはユーザー対応のフィルター処理が必要な場合 (たとえば、仮想デスクトップ インフラストラクチャ (VDI) ワークロードの場合) あるいは高度なインターネット フィルター処理機能が必要な場合は、ハブでサードパーティのパートナー ソリューションを使用します。

![Azure Firewall Manager のすべてのシナリオ](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>Microsoft 365 のトラフィックの処理

グローバル分散ブランチの場所のシナリオでは、Azure のセキュリティで保護されたハブに残りのインターネット トラフィックを送信する前に、Microsoft 365 のトラフィックをブランチで直接リダイレクトする必要があります。

Microsoft 365 では、良好なユーザー エクスペリエンスのために、ネットワークの待機時間とパフォーマンスが重要になります。 最適なパフォーマンスとユーザー エクスペリエンスに関するこれらの目標を達成するには、お客様は、Azure を介した残りのインターネット トラフィックのルーティングを検討する前に、Microsoft 365 のダイレクトおよびローカル エスケープを実装する必要があります。

[Microsoft 365 ネットワーク接続の原則](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles)では、主な Microsoft 365 ネットワーク接続をユーザー ブランチまたはモバイル デバイスからローカルに、また、インターネットを介して最寄りの Microsoft ネットワーク POP (point of presence) に直接ルーティングすることが求められます。

さらに、Microsoft 365 接続はプライバシーを確保するために暗号化されており、パフォーマンス上の理由から、効率的で専用のプロトコルが使用されます。 これにより、これらの接続を従来のネットワーク レベルのセキュリティ ソリューションの対象にすることが非現実的で影響力の強いものとなります。 これらの理由から、お客様には、Azure を介して残りのトラフィックを送信する前に、ブランチから直接 Microsoft 365 トラフィックを送信することを強くお勧めします。 Microsoft では、Azure と Microsoft 365 を統合し、お客様が Microsoft 365 ダイレクトおよびローカルのインターネット ブレークアウトを簡単に有効にできるようにする、複数の SD-WAN ソリューション プロバイダーと提携しています。 詳細については、「[Azure Virtual WAN とは](../virtual-wan/virtual-wan-about.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Firewall Manager を使用して、セキュリティで保護されたハブにセキュリティ パートナー オファリングをデプロイする](deploy-trusted-security-partner.md)。
