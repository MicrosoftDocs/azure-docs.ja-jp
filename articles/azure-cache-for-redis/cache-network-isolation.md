---
title: Azure Cache for Redis のネットワークの分離オプション
description: この記事では、ニーズに合わせて最適なネットワークの分離ソリューションを決定する方法について説明します。 Azure Private Link、Azure Virtual Network (VNet) インジェクション、Azure Firewall 規則の基本を、その利点と制限事項とともに説明します。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621520"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Azure Cache for Redis のネットワークの分離オプション 
この記事では、ニーズに合わせて最適なネットワークの分離ソリューションを決定する方法について説明します。 Azure Private Link、Azure Virtual Network (VNet) インジェクション、Azure Firewall 規則の基本を、その利点と制限事項とともに説明します。  

## <a name="azure-private-link-public-preview"></a>Azure Private Link (パブリック プレビュー) 
Azure Private Link を使用すると、仮想ネットワークから Azure PaaS サービスへのプライベート接続が可能になります。 ネットワーク アーキテクチャが簡素化され、パブリック インターネットへのデータの公開をなくすことで Azure でのエンドポイント間の接続がセキュリティで保護されます。 

### <a name="advantages"></a>長所
* Basic、Standard、および Premium の Azure Cache for Redis インスタンスに対してサポートされています。 
* [Azure Private Link](../private-link/private-link-overview.md) を使用すると、仮想ネットワーク内のサブネットでプライベート IP アドレスが割り当てられているプライベート エンドポイント経由で、仮想ネットワークから Azure Cache インスタンスに接続できます。 これにより、キャッシュ インスタンスは VNet 内とパブリックの両方から使用できるようになります。  
* プライベート エンドポイントが作成されたら、`publicNetworkAccess` フラグを使用してパブリック ネットワークへのアクセスを制限できます。 このフラグは既定で、プライベート リンク アクセスのみを許可する `Disabled` に設定されています。 この値は、PATCH 要求を使用して `Enabled` または `Disabled` に設定できます。 詳細については、「[Azure Private Link を使用した Azure Cache for Redis (パブリック プレビュー)](cache-private-link.md)」を参照してください。 
* 外部キャッシュの依存関係はすべて、VNet の NSG ルールには影響しません。

### <a name="limitations"></a>制限事項 
* ネットワーク セキュリティ グループ (NSG) はプライベート エンドポイントに対しては無効になっています。 ただし、サブネットに他のリソースがある場合は、NSG の強制がこれらのリソースに適用されます。
* geo レプリケーション、ファイアウォール規則、ポータル コンソールのサポート、クラスター化されたキャッシュごとの複数のエンドポイント、ファイアウォールと VNet インジェクションされたキャッシュの永続化はサポートされていません。 
* クラスター化されたキャッシュに接続するには、`publicNetworkAccess` を `Disabled` に設定する必要があり、プライベート エンドポイント接続は 1 つしか保持できません。

> [!NOTE]
> キャッシュ インスタンスにプライベート エンドポイントを追加すると、DNS が原因で、すべての Redis トラフィックがプライベート エンドポイントに移動されます。
> 以前のファイアウォール規則が事前に調整されているようにします。  
>
>

## <a name="azure-virtual-network-injection"></a>Azure Virtual Network インジェクション 
VNet は、Azure 内のプライベート ネットワークの基本的な構成要素です。 VNet によって、多くの Azure リソースがお互い同士や、インターネット、オンプレミス ネットワークと安全に通信できるようになります。 VNet は、自社のデータ センターで運用する従来のネットワークと似ていますが、Azure インフラストラクチャ、スケール、可用性、分離性の利点が提供されます。 

### <a name="advantages"></a>長所
* VNet を使用して Azure Cache for Redis インスタンスを構成する場合、パブリックにアドレスを指定することはできないため、VNet 内の仮想マシンとアプリケーションからしかアクセスできません。  
* VNet を制限付き NSG ポリシーと結合することで、データ流出のリスクを軽減することができます。 
* VNet のデプロイによって、Azure Cache for Redis のための強化されたセキュリティと分離、サブネット、アクセス制御ポリシーなど、アクセスをさらに制限するための機能が提供されます。 
* geo レプリケーションがサポートされています。 

### <a name="limitations"></a>制限事項
* VNet インジェクションされたキャッシュは、Premium の Azure Cache for Redis でのみ使用できます。 
* VNet インジェクションされたキャッシュを使用する場合は、VNet を開き、CRL や PKI、AKV、Azure Storage、Azure Monitor などの依存関係をキャッシュする必要があります。  


## <a name="azure-firewall-rules"></a>Azure Firewall 規則
[Azure Firewall](../firewall/overview.md) は、Azure VNet リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 組み込みの高可用性とクラウドの無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。 サブスクリプションと仮想ネットワークをまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。  

### <a name="advantages"></a>長所
* ファイアウォール ルールを構成すると、指定した IP アドレス範囲からのクライアント接続のみがキャッシュに接続できます。 ファイアウォール ルールが構成されている場合でも、Azure Cache for Redis 監視システムからの接続は常に許可されます。 自分が定義した NSG ルールも許可されます。  

### <a name="limitations"></a>制限事項
* ファイアウォール規則は、VNet インジェクションされたキャッシュと組み合わせて使用できますが、現在のところプライベート エンドポイントでは使用できません。 


## <a name="next-steps"></a>次の手順
* [Premium の Azure Cache for Redis インスタンス用の VNet インジェクションされたキャッシュ](cache-how-to-premium-vnet.md)を構成する方法について学習します。  
* [すべてのレベルの Azure Cache for Redis に対するファイアウォール規則](cache-configure.md#firewall)を構成する方法について学習します。 
* [すべてのレベルの Azure Cache for Redis に対するプライベート エンドポイントを構成する](cache-private-link.md)方法について学習します。