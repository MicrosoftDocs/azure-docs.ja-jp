---
title: 仮想ネットワークを使用するシナリオ
description: コンテナー グループを Azure 仮想ネットワークにデプロイするシナリオ、リソース、制限。
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 20c2b4fe2f19402d6647f398a9696b7e16550d8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606890"
---
# <a name="virtual-network-scenarios-and-resources"></a>仮想ネットワークのシナリオとリソース

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) では、Azure リソースやオンプレミス リソースのセキュアなプライベート ネットワーキングが提供されます。 Azure 仮想ネットワークにコンテナー グループをデプロイすれば、それらのコンテナーで、仮想ネットワーク内の他のリソースと安全に通信することができます。 

この記事では、仮想ネットワークのシナリオ、制限、およびリソースに関する背景について説明します。 Azure CLI を使用したデプロイの例については、「[コンテナー インスタンスを Azure 仮想ネットワークにデプロイする](container-instances-vnet.md)」を参照してください。

> [!IMPORTANT]
> 仮想ネットワークへのコンテナー グループのデプロイは、一般に Azure Container Instances が利用可能なほとんどのリージョンでは、Linux コンテナーで使用できます。 詳細については、[リージョンとリソースの可用性](container-instances-region-availability.md)に関するページをご覧ください。 

## <a name="scenarios"></a>シナリオ

Azure 仮想ネットワークにデプロイされたコンテナー グループでは、次のようなシナリオに対応できます。

* 同じサブネット内のコンテナー グループ間での直接的な通信
* コンテナー インスタンスから、仮想ネットワーク内のデータベースに[タスクベース](container-instances-restart-policy.md)のワークロード出力を送信する
* 仮想ネットワーク内の[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)から、コンテナー インスタンス用のコンテンツを取得する
* [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md)または [ExpressRoute](../expressroute/expressroute-introduction.md) を使用してオンプレミス リソースとのコンテナー通信を有効にする
* [Azure Firewall](../firewall/overview.md) と統合して、コンテナーから発信された送信トラフィックを識別する 
* 仮想マシンなどの仮想ネットワーク内の Azure リソースと通信するために、内部 Azure DNS を介して名前を解決する
* NSG 規則を使用して、サブネットまたはその他のネットワーク リソースへのコンテナー アクセスを制御する

## <a name="unsupported-networking-scenarios"></a>サポートされていないネットワーク シナリオ 

* **Azure Load Balancer** - ネットワーク接続されたコンテナー グループ内のコンテナー インスタンスの前に Azure Load Balancer を配置することはサポートされていません
* **グローバル仮想ネットワーク ピアリング** - グローバル ピアリング (Azure リージョンをまたぐ仮想ネットワークの接続) はサポートされていません
* **パブリック IP または DNS ラベル** - 仮想ネットワークにデプロイされたコンテナー グループでは現在、パブリック IP アドレスまたは完全修飾ドメイン名を使用してコンテナーをインターネットに直接公開することはサポートされていません
* **Virtual Network NAT** - 仮想ネットワークにデプロイされたコンテナー グループでは、現在、送信インターネット接続に NAT ゲートウェイ リソースを使用することはサポートされていません。

## <a name="other-limitations"></a>その他の制限事項

* 現時点で、仮想ネットワークにデプロイされたコンテナー グループでは、Linux コンテナーのみがサポートされています。
* コンテナー グループをサブネットにデプロイする場合、サブネットに他の種類のリソースを含めることはできません。 コンテナー グループをデプロイする前に、既存のサブネットから既存のリソースをすべて削除するか、新しいサブネットを作成してください。
* 仮想ネットワークにデプロイされたコンテナー グループ内で[マネージド ID](container-instances-managed-identity.md) を使用することはできません。
* 仮想ネットワークにデプロイされているコンテナー グループ内の [liveness probe](container-instances-liveness-probe.md) または [readiness probe](container-instances-readiness-probe.md) を有効にすることはできません。
* 追加のネットワーク リソースが関係しているため、通常、仮想ネットワークへのデプロイには標準のコンテナー インスタンスをデプロイするよりも時間がかかります。
* コンテナー グループを Azure Storage アカウントに接続する場合は、そのリソースに[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加する必要があります。

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="required-network-resources"></a>必要なネットワーク リソース

仮想ネットワークにコンテナー グループをデプロイするには、3 つの Azure Virtual Network リソースが必要になります。[仮想ネットワーク](#virtual-network)自体と、仮想ネットワーク内で[委任されたサブネット](#subnet-delegated)、そして[ネットワーク プロファイル](#network-profile)です。 

### <a name="virtual-network"></a>仮想ネットワーク

仮想ネットワークでは、1 つ以上のサブネットを作成するためのアドレス空間が定義されます。 これにより、仮想ネットワーク内のサブネットに Azure リソース (コンテナー グループなど) をデプロイできるようになります。

### <a name="subnet-delegated"></a>サブネット (委任して使用)

サブネットは、仮想ネットワークを個別のアドレス空間に分割し、サブネット内に配置された Azure リソースから使用できるようにするものです。 仮想ネットワーク内には、1 つ以上のサブネットを作成できます。

コンテナー グループ用に使用するサブネットには、コンテナー グループしか含めることができません。 サブネットに最初のコンテナー グループをデプロイすると、Azure によって、そのサブネットが Azure Container Instances に委任されます。 委任されると、サブネットはコンテナー グループ用にしか使用できなくなります。 委任されたサブネットにコンテナー グループ以外のリソースをデプロイしようとすると、操作が失敗します。

### <a name="network-profile"></a>ネットワーク プロファイル

ネットワーク プロファイルは、Azure リソース用のネットワーク構成テンプレートです。 このプロファイルでは、リソースのネットワーク プロパティが指定されます (たとえば、リソースのデプロイ先となるサブネットなど)。 最初に [az container create][az-container-create] コマンドを使用してコンテナー グループをサブネット (および仮想ネットワーク) にデプロイすると、Azure により、ネットワーク プロファイルが自動的に作成されます。 その後は、そのネットワーク プロファイルを使ってサブネットにリソースをデプロイしていくことができます。 

Resource Manager テンプレート、YAML ファイル、またはプログラムのメソッドを使用して、コンテナー グループをサブネットにデプロイするには、ネットワーク プロファイルの完全な Resource Manager リソース ID を指定する必要があります。 以前 [az container create][az-container-create] を使用して作成したプロファイルを使用することも、Resource Manager テンプレート ([テンプレートの例](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet)と[リファレンス](/azure/templates/microsoft.network/networkprofiles)を参照) を使用してプロファイルを作成することもできます。 以前作成したプロファイルの ID を取得するには、[az network profile list][az-network-profile-list] コマンドを使用します。 

次の図では、Azure Container Instances へ委任されたサブネットに、複数のコンテナー グループがデプロイされています。 サブネットにコンテナー グループを 1 つデプロイしたら、同じネットワーク プロファイルを指定して追加のコンテナー グループをデプロイしていくことができます。

![仮想ネットワーク内のコンテナー グループ][aci-vnet-01]

## <a name="next-steps"></a>次のステップ

* Azure CLI を使用したデプロイの例については、「[コンテナー インスタンスを Azure 仮想ネットワークにデプロイする](container-instances-vnet.md)」を参照してください。
* 新しい仮想ネットワーク、サブネット、ネットワーク プロファイル、およびコンテナー グループを Resource Manager テンプレートを使用してデプロイするには、「[Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)」(VNet 内に Azure コンテナー グループを作成する) を参照してください。
* [Azure portal](container-instances-quickstart-portal.md) を使用してコンテナー インスタンスを作成する場合は、 **[ネットワーク]** タブで、新しい仮想ネットワークまたは既存の仮想ネットワークの設定を指定することもできます。


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
