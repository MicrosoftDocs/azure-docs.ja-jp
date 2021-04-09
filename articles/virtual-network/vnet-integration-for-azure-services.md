---
title: ネットワーク分離のための Azure サービスの仮想ネットワーク統合
titlesuffix: Azure Virtual Network
description: この記事では、Azure サービスを仮想ネットワークに統合するさまざまな方法について説明します。これにより、Azure サービスに安全にアクセスできるようになります。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 2c0c4bec93b8fa61275c376fbae2a3a063e72a6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785534"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>ネットワーク分離のために Azure サービスと仮想ネットワークを統合する

Azure サービスの Virtual Network (VNet) 統合を使用すると、サービスへのアクセスを、仮想ネットワーク インフラストラクチャのみに制限できます。 VNet インフラストラクチャには、ピアリングされた仮想ネットワークとオンプレミス ネットワークも含まれています。

VNet 統合は、Azure サービスにネットワーク分離の利点を提供します。これは、次の 1 つ以上の方法で実現できます。
- [サービスの専用インスタンスを仮想ネットワークにデプロイする](virtual-network-for-azure-services.md)。 サービスは、仮想ネットワーク内で、また、オンプレミス ネットワークからプライベート アクセスできます。
- [Azure Private Link](../private-link/private-link-overview.md) を使用するサービスにプライベートかつ安全に接続する[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用する。 プライベート エンドポイントでは、自分の VNet からのプライベート IP アドレスを使用して、サービスを効果的に仮想ネットワークに取り込みます。
- [サービス エンドポイント](virtual-network-service-endpoints-overview.md)を介して仮想ネットワークをサービスに拡張することによって、パブリック エンドポイントを使用してサービスにアクセスする。 サービス エンドポイントを使用することで、仮想ネットワークに対してサービス リソースをセキュリティで保護することができます。
- [サービス タグ](service-tags-overview.md)を使用して、パブリック IP エンドポイントとの間で Azure リソースへのトラフィックを許可または拒否する。

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>仮想ネットワークに専用の Azure サービスをデプロイする

仮想ネットワークに専用の Azure サービスをデプロイすると、プライベート IP アドレスを利用してサービス リソースに非公開で通信できます。

![仮想ネットワークに専用の Azure サービスをデプロイする](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

仮想ネットワークに専用の Azure サービスをデプロイすると、次のことができるようになります。
- 仮想ネットワーク内のリソースは、プライベート IP アドレスを利用し、非公開で互いと通信できます。 たとえば、仮想ネットワーク内で、HDInsight と仮想マシンで実行されている SQL Server の間でデータを直接転送できます。
- オンプレミスのリソースは、サイト間 VPN (VPN Gateway) または ExpressRoute とプライベート IP アドレスを利用し、仮想ネットワーク内のリソースにアクセスできます。
- 仮想ネットワーク内のリソースがプライベート IP アドレスで互いに通信できるように仮想ネットワークをピアリングできます。
- 仮想ネットワーク内のサービス インスタンスは通常、Azure サービスによって完全に管理されます。 リソースの正常性の監視、負荷に応じたスケーリングなどが行われます。
- サービス インスタンスは、仮想ネットワークのサブネットにデプロイされます。 サブネットのインバウンド ネットワーク アクセスとアウトバウンド ネットワーク アクセスは、サービスで提供されるガイダンスに基づき、ネットワーク セキュリティ グループ経由で開放する必要があります。
- また、いくつかのサービスには、デプロイ先のサブネットに関して制限が課され、ポリシーの適用、ルート、同じサブネット内の VM とサービス リソースの組み合わせに制限があります。 具体的な制限は、時間の経過と共に変わる可能性があるため、サービスごとにそれらをチェックしてください。 そのようなサービスの例として、Azure NetApp Files、Dedicated HSM、Azure Container Instances、App Service があります。
- 場合によっては、サブネットが特定のサービスをホストできる明示的な識別子として、委任されたサブネットがサービスで必要になることがあります。 委任されたサブネットにサービス固有のリソースを作成するための明示的なアクセス許可を、サービスは委任により取得します。
- 委任されたサブネットを含む仮想ネットワークでの REST API 応答の例を参照してください。 委任サブネット モデルを使用するサービスを網羅した一覧は、Available Delegations API で取得できます。

仮想ネットワークにデプロイできるサービスの一覧については、「[仮想ネットワークに専用の Azure サービスをデプロイする](virtual-network-for-azure-services.md)」を参照してください。

## <a name="private-link-and-private-endpoints"></a>Private Link とプライベート エンドポイント

プライベート エンドポイントを使用すると、パブリック インターネットを経由せずにプライベート リンク上で安全に仮想ネットワークから Azure リソースへ直接、イベントのイングレスを行えるようになります。 プライベート エンドポイントは、仮想ネットワーク内の Azure サービス用の特別なネットワーク インターフェイスです。 Azure リソースのプライベート エンドポイントを作成すると、対象の仮想ネットワーク上のクライアントと対象の Azure リソース間にセキュリティで保護された接続が提供されます。 プライベート エンドポイントには、仮想ネットワークの IP アドレス範囲から IP アドレスが割り当てられます。 プライベート エンドポイントと Azure サービス間の接続には、セキュリティで保護されたプライベート リンクが使用されます。

次の例では、仮想ネットワーク上のクライアントと Event Grid リソースの間にセキュリティで保護された接続を提供する Event Grid リソース プライベート エンドポイントのプライベート アクセスを示します。

![プライベート エンドポイントを使用した SQL DB リソースのプライベート アクセス](./media/network-isolation/architecture-diagram.png)

Private Link と、サポートされている Azure サービスの一覧については、「[Private Link とは](../private-link/private-link-overview.md)」を参照してください。

## <a name="service-endpoints"></a>サービス エンドポイント
VNet サービス エンドポイントでは、Azure のバックボーン ネットワーク上で最適化されたルートを介して、Azure サービスに安全に直接接続できます。 エンドポイントを使用することで、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。 サービス エンドポイントを使用すると、VNet 内のプライベート IP アドレスは、VNet 上のパブリック IP アドレスを必要とせずに、Azure サービスのエンドポイントに接続できます。

![Azure サービスへのアクセスを仮想ネットワークに限定する](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

詳細については、「[仮想ネットワーク サービス エンドポイント](virtual-network-service-endpoints-overview.md)」を参照してください。

## <a name="service-tags"></a>サービス タグ

サービス タグは、指定された Azure サービスからの IP アドレス プレフィックスのグループを表します。 サービス タグを使用して、[ネットワーク セキュリティ グループ](./network-security-groups-overview.md#security-rules)または [Azure Firewall](../firewall/service-tags.md) でのネットワーク アクセス制御を定義できます。 規則の適切なソース フィールドまたはターゲット フィールドにサービス タグ名 (たとえば AzureEventGrid) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。

![サービス タグを使用したトラフィックの許可または拒否](./media/network-isolation/service-tags.png)

サービス タグを使用すると、ネットワーク分離を実現し、パブリック エンドポイントを持つ Azure サービスへのアクセス時に一般のインターネットから Azure リソースを保護できます。 受信/送信ネットワーク セキュリティ グループ規則を作成して、**インターネット** との間のトラフィックを拒否し、**AzureCloud** または他の特定の Azure サービスの [利用可能なサービス タグ](service-tags-overview.md#available-service-tags)との間のトラフィックを許可します。

サービス タグおよびこれをサポートする Azure サービスの詳細については、[サービス タグの概要](service-tags-overview.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure ネットワークとアプリを統合する](../app-service/web-sites-integrate-with-vnet.md)方法について説明します。
- [サービス タグを使用してリソースへのアクセスを制限する](tutorial-restrict-network-access-to-resources.md)方法について説明します。
- [Azure Private Link を使用して Azure Cosmos アカウントに非公開で接続する](../private-link/tutorial-private-endpoint-cosmosdb-portal.md)方法について説明します。