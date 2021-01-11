---
title: Azure Kubernetes Service (AKS) での API サーバーの許可された IP 範囲
description: Azure Kubernetes Service (AKS) で API サーバーへのアクセスのための IP アドレス範囲を使用してクラスターをセキュリティで保護する方法について説明します
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 404bd600f825a5da334811744132c6aa9b751566
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88006895"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で許可された IP アドレス範囲を使用して API サーバーへのアクセスをセキュリティで保護する

Kubernetes では、API サーバーは、リソースの作成やノードの数のスケーリングなどのクラスター内のアクションを実行するための要求を受信します。 API サーバーは、クラスターを操作したり管理したりするための中心的な方法です。 クラスターのセキュリティを向上させ、攻撃を最小限に抑えるには、API サーバーを限られた一連の IP アドレス範囲からのみアクセス可能にする必要があります。

この記事では、API サーバーで許可された IP アドレス範囲を使用して、コントロール プレーンにアクセスできる IP アドレスと CIDR を制限する方法を示します。

> [!IMPORTANT]
> API サーバーで許可される IP アドレス範囲が、2019 年 10 月のプレビューの範囲外に移動され後に作成されたクラスターでは、API サーバーで許可される IP アドレス範囲は *Standard* SKU のロード バランサーでのみサポートされます。 *Basic* SKU ロード バランサーと API サーバーで許可された IP アドレス範囲が構成された既存のクラスターは、引き続き機能しますが、*Standard* SKU ロード バランサーに移行することはできません。 これらの既存のクラスターは、Kubernetes のバージョンまたはコントロール プレーンがアップグレードされた場合も引き続き機能します。 API サーバーで許可される IP アドレス範囲は、プライベート クラスターではサポートされません。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI を使用して AKS クラスターを作成する方法について説明します。

Azure CLI バージョン 2.0.76 以降がインストールされて構成されている必要があります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API サーバーの許可された IP 範囲の概要

Kubernetes API サーバーは、基になる Kubernetes API が公開される方法です。 このコンポーネントは、`kubectl` や Kubernetes ダッシュボードなど、管理ツールに対する操作を提供します。 AKS は、専用の API サーバーを含むシングルテナント クラスター マスターを提供します。 既定では、API サーバーにはパブリック IP アドレスが割り当てられるため、ロールベースのアクセス制御 (RBAC) を使用してアクセスを制御する必要があります。

通常はパブリックにアクセス可能な AKS コントロール プレーン/API サーバーへのアクセスをセキュリティで保護するには、許可された IP 範囲を有効にして使用することができます。 これらの許可された IP 範囲では、API サーバーと通信するための定義された IP アドレス範囲のみが許可されます。 これらの許可された IP 範囲に含まれていない IP アドレスから API サーバーへの要求はブロックされます。 ユーザーやそのユーザーが要求するアクションを承認するには、引き続き RBAC を使用します。

API サーバーやその他のクラスター コンポーネントの詳細については、[AKS における Kubernetes の中心概念][concepts-clusters-workloads]に関するページを参照してください。

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>API サーバーの許可された IP 範囲を有効にした AKS クラスターを作成する

[az aks create][az-aks-create] を使用してクラスターを作成し、 *`--api-server-authorized-ip-ranges`* パラメーターを使用して、許可された IP アドレス範囲のリストを指定します。 これらの IP アドレス範囲は通常、オンプレミス ネットワークまたはパブリック IP によって使用されるアドレス範囲です。 CIDR 範囲を指定する場合は、その範囲内の最初の IP アドレスから始めます。 たとえば、*137.117.106.90/29* は有効な範囲ですが、範囲内の最初の IP アドレス (*137.117.106.88/29* など) を指定するようにしてください。

> [!IMPORTANT]
> 既定では、クラスターは [Standard SKU ロード バランサー][standard-sku-lb]を使用します。これを使用して、アウトバウンド ゲートウェイを構成できます。 クラスターの作成時に API サーバーの許可された IP 範囲を有効にすると、指定した範囲の他に、クラスターのパブリック IP も既定で許可されます。 *`--api-server-authorized-ip-ranges`* に *""* を指定するか、値を指定しなかった場合、API サーバーの許可された IP 範囲は無効になります。 PowerShell を使用している場合は、解析の問題を回避するために、 *`--api-server-authorized-ip-ranges=""`* (等号付き) を使用することに注意してください。

次の例では、*myResourceGroup* という名前のリソース グループに *myAKSCluster* という名前の単一ノードのクラスターを作成し、API サーバーの許可された IP 範囲を有効にします。 許可される IP アドレス範囲は、*73.140.245.0/24* です。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> 次の範囲を許可リストに追加してください。
> - ファイアウォール パブリック IP アドレス
> - クラスターを管理するネットワークを表すあらゆる範囲
> - AKS クラスターで Azure Dev Spaces を使用している場合は、[リージョンに基づく追加の範囲][dev-spaces-ranges]を許可する必要があります。
>
> 指定できる IP 範囲の数の上限は 200 です。
>
> ルールが反映されるまで、最大 2 分かかる場合があります。 接続をテストするときは、その時間に至るまで待ってください。

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Standard SKU ロード バランサーのアウトバウンド IP を指定する

AKS クラスターを作成するときに、クラスターのアウトバウンド IP アドレスまたはプレフィックスを指定すると、それらのアドレスまたはプレフィックスも許可されます。 次に例を示します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

上の例では、パラメーター *`--load-balancer-outbound-ip-prefixes`* に指定されているすべての IP が、 *`--api-server-authorized-ip-ranges`* パラメーターの IP と共に使用できます。

また、 *`--load-balancer-outbound-ip-prefixes`* パラメーターを指定して、アウトバウンド ロード バランサー IP プレフィックスを許可することもできます。

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Standard SKU ロード バランサーのアウトバウンド パブリック IP のみを許可する

クラスターの作成時に API サーバーの許可された IP 範囲を有効にすると、指定した範囲の他に、クラスターの Standard SKU ロード バランサーのアウトバウンド パブリック IP も既定で許可されます。 Standard SKU ロード バランサーのアウトバウンド パブリック IP のみを許可するには、 *`--api-server-authorized-ip-ranges`* パラメーターを指定するときに、*0.0.0.0/32* を使用します。

次の例では、Standard SKU ロード バランサーのアウトバウンド パブリック IP のみが許可されており、クラスター内のノードからのみ API サーバーにアクセスできます。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>クラスターの、API サーバーの許可された IP 範囲を更新する

既存のクラスター上で、API サーバーの許可された IP 範囲を更新するには、[az aks update][az-aks-update] コマンドを使用し、 *`--api-server-authorized-ip-ranges`* 、--load-balancer-outbound-ip-prefixes *、 *`--load-balancer-outbound-ips`* 、または --load-balancer-outbound-ip-prefixes* パラメーターを使用します。

次の例では、*myResourceGroup* という名前のリソース グループ内の *myAKSCluster* という名前のクラスターで API サーバーの許可された IP 範囲を更新します。 許可する IP アドレス範囲は、*73.140.245.0/24* です。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Standard SKU ロード バランサーのパブリック IP のみを許可するために、 *`--api-server-authorized-ip-ranges`* パラメーターを指定するときに、*0.0.0.0/32* を使用することもできます。

## <a name="disable-authorized-ip-ranges"></a>許可された IP 範囲を無効にする

許可された IP 範囲を無効にするには、[az aks update][az-aks-update] を使用し、空の範囲を指定して、API サーバーの許可された IP 範囲を無効にします。 次に例を示します。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>次のステップ

この記事では、API サーバーの許可された IP 範囲を有効にしました。 このアプローチは、セキュリティで保護された AKS クラスターをどのように実行できるかの一部です。

詳細については、[AKS でのアプリケーションとクラスターのセキュリティの概念][concepts-security]に関するページおよび [AKS でのクラスターのセキュリティとアップグレードのベスト プラクティス][operator-best-practices-cluster-security]に関するページを参照してください。

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
