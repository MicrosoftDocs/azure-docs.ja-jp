---
title: プライベート Azure Kubernetes Service クラスターを作成する
description: プライベート Azure Kubernetes Service (AKS) クラスターの作成方法について説明します
services: container-service
ms.topic: article
ms.date: 7/17/2020
ms.openlocfilehash: 10cbd58807c213418a88b42887cdb76868eac34e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87015651"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>プライベート Azure Kubernetes Service クラスターを作成する

プライベートクラスターにおいては、コントロールプレーンまたは API サーバーは「[プライベートインターネット向けの RFC1918 アドレス割り当て](https://tools.ietf.org/html/rfc1918)」で定義される内部 IP アドレスを持っています。 プライベート クラスターを使用することにより、API サーバーとノード プールの間のネットワーク トラフィックがプライベート ネットワークにのみ保持されるようにすることができます。

コントロールプレーンまたは API サーバーは、Azure Kubernetes Service (AKS) マネージドの Azure サブスクリプションの中にあります。 カスタマーのクラスターまたはノードプールは、カスタマーのサブスクリプションの中にあります。 サーバーとクラスターまたはノードプールは、API サーバー仮想ネットワーク内の [Azure Private Link サービス][private-link-service] と、カスタマーの AKS クラスターのサブネットで公開されているプライベート エンドポイントを介して相互に通信できます。

## <a name="region-availability"></a>利用可能なリージョン

プライベート クラスターは、[AKS がサポートされている](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)パブリック リージョンで利用できます。

* Azure China 21Vianet は現在サポートされていません。
* Private Link のサポートがないため、US Gov Texas は現在サポートされていません。

## <a name="prerequisites"></a>前提条件

* Azure CLI バージョン 2.2.0 以降

## <a name="create-a-private-aks-cluster"></a>プライベート AKS クラスターを作成する

### <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループを作成するか、AKS クラスターの既存のリソース グループを使用します。

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>既定の基本ネットワーク 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
*--enable-private-cluster*は、プライベート クラスターに必須のフラグです 

### <a name="advanced-networking"></a>[高度] ネットワーク  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
*--enable-private-cluster*は、プライベート クラスターに必須のフラグです 

> [!NOTE]
> Docker ブリッジ アドレス CIDR (172.17.0.1/16) がサブネット CIDR と競合する場合は、Docker ブリッジ アドレスを適切に変更します。

## <a name="options-for-connecting-to-the-private-cluster"></a>プライベート クラスターに接続するための選択肢

API サーバー エンドポイントには、パブリック IP アドレスがありません。 API サーバーを管理するには、AKS クラスターの Azure Virtual Network (VNet) にアクセスできる VM を使用する必要があります。 プライベート クラスターへのネットワーク接続を確立するには、いくつかの選択肢があります。

* AKS クラスターと同じ Azure Virtual Network (VNet) に VM を作成します。
* 別のネットワーク内の VM を使用し、[仮想ネットワーク ピアリング][virtual-network-peering]を設定します。  このオプションの詳細については、以下のセクションを参照してください。
* [Express Route または VPN][express-route-or-VPN] 接続を使用します。

AKS クラスターと同じ VNET に VM を作成するのが最も簡単な方法です。  Express Route と VPN にはコストがかかり、ネットワークがさらに複雑になります。  仮想ネットワーク ピアリングを利用する場合、重複する範囲がないようにネットワーク CIDR 範囲を計画する必要があります。

## <a name="virtual-network-peering"></a>仮想ネットワーク ピアリング

前述のように、仮想ネットワーク ピアリングはプライベート クラスターにアクセスする方法の 1 つです。 仮想ネットワーク ピアリングを使用するには、仮想ネットワークとプライベート DNS ゾーンの間にリンクを設定する必要があります。
    
1. Azure portal でノード リソース グループに移動します。  
2. プライベート DNS ゾーンを選択します。   
3. 左側のウィンドウで、 **[仮想ネットワーク]** リンクを選択します。  
4. 新しいリンクを作成して、VM の仮想ネットワークをプライベート DNS ゾーンに追加します。 DNS ゾーンリンクが利用可能になるまで数分かかります。  
5. Azure portal で、クラスターの仮想ネットワークが含まれるリソース グループに移動します。  
6. 右側のウィンドウで、仮想ネットワークを選択します。 仮想ネットワーク名は *aks-vnet-\** のフォームになっています。  
7. 左側のウィンドウで、 **[ピアリング]** を選択します。  
8. **[追加]** を選択して VM の仮想ネットワークを追加し、次にピアリングを作成します。  
9. VM のある仮想ネットワークに移動して **[ピアリング]** を選択、AKS 仮想ネットワークを選択し、次にピアリングを作成します。 AKS 仮想ネットワークのアドレス範囲と VM の仮想ネットワークが競合している場合、ピアリングは失敗します。 詳細については、「[仮想ネットワーク ピアリング][virtual-network-peering]」を参照してください。

## <a name="hub-and-spoke-with-custom-dns"></a>カスタム DNS を使用したハブとスポーク

[ハブとスポークのアーキテクチャ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)は、Azure でネットワークをデプロイするためによく使用されます。 これらのデプロイの多くでは、オンプレミスと Azure ベースの DNS 解決が可能となるよう、スポーク VNet の DNS 設定は中央の DNS フォワーダーを参照するように構成されます。 このようなネットワーク環境に AKS クラスターをデプロイする場合、いくつかの特別な考慮事項を考慮する必要があります。

![プライベート クラスターのハブとスポーク](media/private-clusters/aks-private-hub-spoke.png)

1. 既定では、プライベート クラスターがプロビジョニングされると、プライベート エンドポイント (1) とプライベート DNS ゾーン (2) がクラスター マネージド リソース グループに作成されます。 クラスターによりプライベート ゾーンの A レコードが使用され、API サーバーとの通信のためにプライベート エンドポイントの IP が解決されます。

2. プライベート DNS ゾーンは、クラスター ノードが接続されている VNet にのみリンクされます (3)。 つまり、プライベート エンドポイントは、そのリンクされた VNet 内のホストによってのみ解決できます。 VNet にカスタム DNS が一切構成されていないシナリオ (既定) では、これは問題なく機能します。リンクされているためにプライベート DNS ゾーン内のレコードを解決できる DNS の 168.63.129.16 が、ホストにより指定されるためです。

3. クラスターを含む VNet にカスタム DNS 設定があるシナリオでは (4)、プライベート DNS ゾーンがカスタム DNS リゾルバーを含む VNet にリンクされていない限り (5)、クラスターのデプロイは失敗します。 このリンクは、クラスターのプロビジョニング中にプライベート ゾーンを作成した後に手動で、またはイベントベースのデプロイ メカニズム (Azure Event Grid や Azure Functions など) を使用したゾーンの作成の検出時に自動で、作成することができます。

## <a name="dependencies"></a>依存関係  

* Private Link サービスは、Standard Azure Load Balancer でのみサポートされています。 Basic Azure Load Balancer はサポートされていません。  
* カスタム DNS サーバーを使用するには、カスタム DNS サーバーにアップストリーム DNS サーバーとして Azure DNS IP 168.63.129.16 を追加します。

## <a name="limitations"></a>制限事項 
* 承認済み IP 範囲は、プライベート API サーバー エンドポイントには適用できません。パブリック API サーバーにのみ適用されます
* [Availability Zones][availability-zones] は現在、特定のリージョンでサポートされています。 
* [Azure Private Link サービスの制限事項][private-link-service]は、プライベート クラスターに適用されます。
* Azure DevOps Microsoft でホストするエージェントとプライベート クラスターの組み合わせはサポートされていません。 [セルフホステッド エージェント][devops-agents]を使用することを検討してください。 
* Azure Container Registry をプライベート AKS で使用できるようにする必要があるカスタマーは、Container Registry 仮想ネットワークをエージェントクラスターの仮想ネットワークとピアリングしてください。
* Azure Dev Spaces は現在サポートされていません
* 既存の AKS クラスターからプライベートクラスターへの変換はサポートされていません
* カスタマーのサブネット内でプライベート エンドポイントを削除または変更すると、クラスターが機能しなくなります。 
* コンテナー用 Azure Monitor の Live Data は現在サポートされていません。
* アップタイム SLA は現在サポートされていません。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md
