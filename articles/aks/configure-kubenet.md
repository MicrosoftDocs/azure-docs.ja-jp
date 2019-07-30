---
title: Azure Kubernetes Service (AKS) で kubenet ネットワークを構成する
description: Azure Kubernetes Service (AKS) で kubenet (基本) ネットワークを構成して、既存の仮想ネットワークおよびサブネットに AKS クラスターをデプロイする方法について説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/26/2019
ms.author: mlearned
ms.reviewer: nieberts, jomore
ms.openlocfilehash: e1279261de8e26b9e11f55100ce01277650e251b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615752"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) の独自の IP アドレス範囲で kubenet ネットワークを使用する

既定では、AKS クラスターでは [kubenet][kubenet] が使用されて、Azure 仮想ネットワークとサブネットが自動的に作成されます。 *kubenet* を使用すると、ノードでは Azure 仮想ネットワーク サブネットから IP アドレスが取得されます。 ポッドでは、ノードの Azure 仮想ネットワーク サブネットとは論理的に異なるアドレス空間から IP アドレスを受け取ります。 その後、ポッドで Azure 仮想ネットワークのリソースに到達できるように、ネットワーク アドレス変換 (NAT) が構成されます。 トラフィックの発信元 IP アドレスは、ノードのプライマリ IP アドレスに NAT 変換されます。 この方法では、ポッド用にネットワーク空間で予約する必要がある IP アドレスの数が大幅に減ります。

[Azure Container Networking Interface (CNI)][cni-networking] では、すべてのポッドにサブネットから IP アドレスが割り当てられ、すべてのポッドに直接アクセスできます。 これらの IP アドレスは、ネットワーク空間全体で一意である必要があり、事前に計画する必要があります。 各ノードには、サポートされるポッドの最大数に対する構成パラメーターがあります。 ノードごとにそれと同じ数の IP アドレスが、そのノードに対して事前に予約されます。 この方法では詳細な計画が必要であり、多くの場合、IP アドレスが不足するか、アプリケーション需要の拡大に伴い、より大きなサブネットでのクラスターの再構築が必要になります。

この記事では、 *kubenet* ネットワークを使用して、AKS クラスター用の仮想ネットワーク サブネットを作成して使用する方法を示します。 ネットワークのオプションと考慮事項について詳しくは、[Kubernetes および AKS のネットワークの概念][aks-network-concepts]に関する記事をご覧ください。

> [!WARNING]
> Windows Server ノード プール (現在は AKS でプレビュー段階) を使用するには、Azure CNI を使用する必要があります。 Windows Server コンテナーには、ネットワーク モデルとして kubenet を使用できません。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.65 以降がインストールされて構成されている必要があります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>独自のサブネットでの kubenet ネットワークの概要

多くの環境では、割り当て済みの IP アドレス範囲を使用して仮想ネットワークとサブネットを定義します。 これらの仮想ネットワーク リソースは、複数のサービスとアプリケーションをサポートするために使用されます。 ネットワーク接続を提供するため、AKS クラスターでは *kubenet* (基本的なネットワーク) または Azure CNI ("*高度なネットワーク*) を使用できます。

*kubenet* では、ノードだけが仮想ネットワーク サブネットの IP アドレスを受け取ります。 ポッドが相互に直接通信することはできません。 代わりに、複数のノードにまたがるポッド間の接続には、ユーザー定義ルーティング (UDR) と IP 転送が使用されます。 割り当て済み IP アドレスを受け取ってアプリケーションに対するトラフィックを負荷分散するサービスの背後に、ポッドをデプロイすることもできます。 次の図では、ポッドではなく AKS ノードが仮想ネットワーク サブネットの IP アドレスを受け取る方法を示します。

![AKS クラスターでの kubenet ネットワーク モデル](media/use-kubenet/kubenet-overview.png)

Azure でサポートされる UDR のルート数は最大 400 なので、AKS クラスターに 400 個より多くのノードを作成することはできません。 [仮想ノード][virtual-nodes]やネットワーク ポリシーなどの AKS の機能は、*kubenet* ではサポートされていません。

*Azure CNI* では、各ポッドは IP サブネット内の IP アドレスを受け取り、他のポッドやサービスと直接通信できます。 クラスターは、ユーザーが指定する IP アドレスの範囲まで拡大できます。 ただし、IP アドレスの範囲を事前に計画する必要があり、すべての IP アドレスは、ノードでサポートできるポッドの最大数に基づいて AKS ノードによって消費されます。 *Azure CNI* では、[仮想ノード][virtual-nodes]やネットワーク ポリシーなどの高度なネットワーク機能とシナリオがサポートされます。

### <a name="ip-address-availability-and-exhaustion"></a>IP アドレスの使用可能性と不足

*Azure CNI* でよくある問題は、割り当てられている IP アドレス範囲が小さすぎて、クラスターをスケーリングまたはアップグレードするときにノードを追加できないことです。 ネットワーク チームが、予想されるアプリケーションの需要をサポートするのに十分な大きさの IP アドレス範囲を発行できないこともあります。

妥協案として、*kubenet* を使用する AKS クラスターを作成し、既存の仮想ネットワーク サブネットに接続することができます。 この方法では、ノードは定義済みの IP アドレスを受け取ることができ、クラスター内で実行される可能性のあるすべてのポッド用に多数の IP アドレスを事前に予約する必要はありません。

*kubenet* では、はるかに小さい IP アドレス範囲を使用して、大きなクラスターやアプリケーションの需要をサポートできます。 たとえばを、 */27* の IP アドレス範囲でも、20 ～ 25 ノードのクラスターを実行し、スケーリングやアップグレードのための十分な余裕を確保できます。 このクラスター サイズでは、最大で *2,200 ～ 2,750* 個のポッドがサポートされます (既定では、ノードあたり最大 110 ポッド)。 AKS において *kubenet* で構成できるノードあたりの最大ポッド数は 110 です。

次の基本的な計算では、ネットワーク モデルの違いを比較します。

- **kubenet** - 単純な */24* の IP アドレス範囲で、クラスター内の最大 *251* ノードをサポートできます (各 Azure 仮想ネットワーク サブネットでは、最初の 3 つの IP アドレスが管理操作用に予約されます)
  - このノード数では、最大で *27,610* 個のポッドをサポートできます (*kubenet* の既定では、ノードあたり最大 110 ポッド)
- **Azure CNI** - 同じ基本的な */24* のサブネット範囲では、クラスター内の最大 *8* ノードしかサポートできません
  - このノード数では、最大で *240* 個のポッドしかサポートできません (*Azure CNI* の既定では、ノードあたり最大 30 ポッド)

> [!NOTE]
> これらの最大値では、アップグレードまたはスケーリング操作は考慮されていません。 実際には、サブネットの IP アドレス範囲でサポートされる最大数のノードを実行することはできません。 スケーリングまたはアップグレード操作の間に使用できるように、一部の IP アドレスを残しておく必要があります。

### <a name="virtual-network-peering-and-expressroute-connections"></a>仮想ネットワーク ピアリングと ExpressRoute 接続

オンプレミスの接続を提供するため、*kubenet* および *Azure CNI* のどちらのネットワーク アプローチでも、[Azure 仮想ネットワーク ピアリング][vnet-peering]or [ExpressRoute connections][express-route]を使用できます。 重複および正しくないトラフィック ルーティングが発生しないように、慎重に IP アドレス範囲を計画する必要があります。 たとえば、多くのオンプレミス ネットワークでは、ExpressRoute 接続経由でアドバタイズされる *10.0.0.0/8* のアドレス範囲が使用されます。 AKS クラスターをこのアドレス範囲外の Azure 仮想ネットワーク サブネット (*172.16.0.0/16* など) に作成することをお勧めします。

### <a name="choose-a-network-model-to-use"></a>使用するネットワーク モデルを選択する

通常、AKS クラスターに使用するネットワーク プラグインは、柔軟性と高度な構成のニーズのバランスを考慮して選択します。 以下の考慮事項は、それぞれのネットワーク モデルが最も適切である可能性がある状況の概要を理解するのに役立ちます。

*kubenet* を使用する場合:

- IP アドレス空間が限られている。
- ポッドのほとんどの通信がクラスター内で行われる。
- 仮想ノードやネットワーク ポリシーなどの高度な機能を使用する必要がない。

*Azure CNI* を使用する場合:

- 使用可能な IP アドレス空間が十分にある。
- ポッドの通信のほとんどが、クラスターの外部にあるリソースに対するものである。
- UDR を管理したくない。
- 仮想ノードやネットワーク ポリシーなどの高度な機能を使用する必要がある。

どのネットワーク モデルを使用するかの決定に役立つ詳細については、[ネットワーク モデルとそのサポート範囲の比較][network-comparisons]に関するページを参照してください。

> [!NOTE]
> Kuberouter により、kubenet の使用時にネットワーク ポリシーを有効にして AKS クラスターにデーモンセットとしてインストールできます。 Kube ルーターはまだベータ版であり、プロジェクトに対する Microsoft のサポートは提供されていないことに注意してください。

## <a name="create-a-virtual-network-and-subnet"></a>仮想ネットワークとサブネットの作成

*kubenet* と独自の仮想ネットワーク サブネットを使って作業を始めるには、最初に [az group create][az-group-create] コマンドを使用してリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用できる既存の仮想ネットワークとサブネットがない場合は、[az network vnet create][az-network-vnet-create] コマンドを使用してこれらのネットワーク リソースを作成します。 次の例では、*192.168.0.0/16* のアドレス プレフィックスを持つ仮想ネットワークに *myVnet* という名前が付けられています。 *192.168.1.0/24* のアドレス プレフィックスを持つ *myAKSSubnet* という名前のサブネットが作成されています。

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>サービス プリンシパルを作成してアクセス許可を割り当てる

AKS クラスターが他の Azure リソースと対話できるようにするために、Azure Active Directory のサービス プリンシパルを使用します。 サービス プリンシパルには、AKS ノードで使用される仮想ネットワークとサブネットを管理するためのアクセス許可が必要です。 サービス プリンシパルを作成するには、[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] コマンドを使用します。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

次の出力例では、サービス プリンシパルのアプリケーション ID とパスワードが示されています。 これらの値を後の手順で使用して、サービス プリンシパルにロールを割り当てた後、AKS クラスターを作成します。

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

後の手順で正しい委任を割り当てるため、[az network vnet show][az-network-vnet-show]and [az network vnet subnet show][az-network-vnet-subnet-show] コマンドを使用して、必要なリソース ID を取得します。 これらのリソース ID を変数に格納し、後の手順で参照します。

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

次に、[az role assignment create][az-role-assignment-create] コマンドを使用して、AKS クラスターのサービス プリンシパルに、仮想ネットワークでの "*共同作成者*" アクセス許可を割り当てます。 前のサービス プリンシパル作成コマンドの出力で示されている独自の *\<appId>* を指定します。

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>仮想ネットワークに AKS クラスターを作成する

ここまで、仮想ネットワークとサブネットを作成し、サービス プリンシパルを作成して、それらのネットワーク リソースを使用するためのアクセス許可を割り当てました。 次に、[az aks create][az-aks-create] コマンドを使用して、仮想ネットワークとサブネット内に AKS クラスターを作成します。 前のサービス プリンシパル作成コマンドの出力で示されているように、独自のサービス プリンシパルの *\<appId>* と *\<password>* を定義します。

クラスター作成プロセスの一部として、次の IP アドレス範囲も定義します。

* *--service-cidr* は、AKS クラスター内の内部サービスに IP アドレスを割り当てるために使用します。 この IP アドレス範囲は、ネットワーク環境の他の場所で使われていないアドレス空間でなければなりません。 ExpressRoute またはサイト間 VPN 接続を使用して、お使いの Azure 仮想ネットワークを接続している場合、または接続する予定である場合は、すべてのオンプレミス ネットワーク範囲がこの範囲に含まれます。

* *--dns-service-ip* アドレスは、サービス IP アドレス範囲の *.10* アドレスにする必要があります。

* *--pod-cidr* は、ネットワーク環境の他の場所で使われていない大きいアドレス空間にする必要があります。 ExpressRoute またはサイト間 VPN 接続を使用して、お使いの Azure 仮想ネットワークを接続している場合、または接続する予定である場合は、すべてのオンプレミス ネットワーク範囲がこの範囲に含まれます。
    * このアドレス範囲は、スケールアップ後に予想されるノードの数を格納するのに十分な大きさである必要があります。 追加ノード用により多くのアドレスが必要になった場合でも、クラスターをデプロイした後では、このアドレス範囲を変更できません。
    * ポッドの IP アドレス範囲は、クラスター内の各ノードに */24* アドレス空間を割り当てるために使用されます。 次の例では、*10.244.0.0/16* の *--pod-cidr* によって、最初のノードに *10.244.0.0/24*、2 番目のノードに *10.244.1.0/24*、3 番目のノードに *10.244.2.0/24* が割り当てられます。
    * クラスターをスケーリングまたはアップグレードすると、Azure プラットフォームによって引き続き新しい各ノードにポッドの IP アドレス範囲が割り当てられます。
    
* *--docker-bridge-address* を使用すると、AKS ノードは基になる管理プラットフォームと通信できます。 この IP アドレスは、クラスターの仮想ネットワーク IP アドレス範囲に含まれていてはならず、ネットワークで使用されている他のアドレス範囲と重複していてもなりません。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

AKS クラスターを作成すると、ネットワーク セキュリティ グループとルート テーブルが作成されます。 これらのネットワーク リソースは、AKS コントロール プレーンによって管理されます。 ネットワーク セキュリティ グループは、ノードの仮想 NIC と自動的に関連付けられます。 ルート テーブルは、仮想ネットワーク サブネットと自動的に関連付けられます。 サービスを作成して公開すると、ネットワーク セキュリティ グループ規則とルート テーブルが自動的に更新されます。

## <a name="next-steps"></a>次の手順

既存の仮想ネットワーク サブネットに AKS クラスターをデプロイしたので、通常どおりクラスターを使用できます。 [Azure Dev Spaces を使用してアプリの構築][dev-spaces]or [using Draft][use-draft]に着手するか、[Helm][use-helm] を使用してアプリをデプロイします。

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
