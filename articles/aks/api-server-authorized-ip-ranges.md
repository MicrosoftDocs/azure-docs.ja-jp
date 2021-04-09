---
title: Azure Kubernetes Service (AKS) での API サーバーの許可された IP 範囲
description: Azure Kubernetes Service (AKS) で API サーバーへのアクセスのための IP アドレス範囲を使用してクラスターをセキュリティで保護する方法について説明します
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 20c3d46e9a19c66c727e5a21bccd6caa0dc5f0ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771925"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で許可された IP アドレス範囲を使用して API サーバーへのアクセスをセキュリティで保護する

Kubernetes では、API サーバーは、リソースの作成やノードの数のスケーリングなどのクラスター内のアクションを実行するための要求を受信します。 API サーバーは、クラスターを操作したり管理したりするための中心的な方法です。 クラスターのセキュリティを向上させ、攻撃を最小限に抑えるには、API サーバーを限られた一連の IP アドレス範囲からのみアクセス可能にする必要があります。

この記事では、API サーバーで許可された IP アドレス範囲を使用して、コントロール プレーンにアクセスできる IP アドレスと CIDR を制限する方法を示します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI を使用して AKS クラスターを作成する方法について説明します。

Azure CLI バージョン 2.0.76 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

### <a name="limitations"></a>制限事項

API サーバーの許可された IP 範囲の機能には、次の制限があります。
- API サーバーで許可される IP アドレス範囲が、2019 年 10 月のプレビューの範囲外に移動され後に作成されたクラスターでは、API サーバーで許可される IP アドレス範囲は *Standard* SKU のロード バランサーでのみサポートされます。 *Basic* SKU ロード バランサーと API サーバーで許可された IP アドレス範囲が構成された既存のクラスターは、引き続き機能しますが、*Standard* SKU ロード バランサーに移行することはできません。 これらの既存のクラスターは、Kubernetes のバージョンまたはコントロール プレーンがアップグレードされた場合も引き続き機能します。 API サーバーで許可される IP アドレス範囲は、プライベート クラスターではサポートされません。
- この機能は、[ノードごとのパブリック IP](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools) を使用するクラスターとは互換性がありません。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API サーバーの許可された IP 範囲の概要

Kubernetes API サーバーは、基になる Kubernetes API が公開される方法です。 このコンポーネントは、`kubectl` や Kubernetes ダッシュボードなど、管理ツールに対する操作を提供します。 AKS により、専用の API サーバーでシングル テナント クラスター コントロール プレーンが提供されます。 既定では、API サーバーにはパブリック IP アドレスが割り当てられるため、Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) または Azure RBAC を使用してアクセスを制御する必要があります。

通常はパブリックにアクセス可能な AKS コントロール プレーン/API サーバーへのアクセスをセキュリティで保護するには、許可された IP 範囲を有効にして使用することができます。 これらの許可された IP 範囲では、API サーバーと通信するための定義された IP アドレス範囲のみが許可されます。 これらの許可された IP 範囲に含まれていない IP アドレスから API サーバーへの要求はブロックされます。 ユーザーや、これらのユーザーが要求するアクションを承認するには、引き続き Kubernetes RBAC または Azure RBAC を使用します。

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

代わりに、 *`--load-balancer-outbound-ip-prefixes`* パラメーターを指定して、アウトバウンド ロード バランサー IP プレフィックスを許可することもできます。

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

## <a name="find-existing-authorized-ip-ranges"></a>既存の許可された IP 範囲を検索する

許可された IP 範囲を検索するには、[az aks show][az-aks-show] を使用し、クラスターの名前とリソース グループを指定します。 例:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Azure portal を使用して、許可された IP 範囲の更新、無効化、検索を行う

前述の許可された IP 範囲の追加、更新、検索、無効化の各操作は、Azure portal でも実行できます。 アクセスするには、クラスター リソースのメニュー ブレードの **[設定]** の下にある **[ネットワーク]** に移動します。

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="ブラウザーに、クラスター リソースのネットワーク設定 Azure portal ページが表示されています。'指定された IP 範囲の設定' と '指定された IP 範囲' のオプションが強調表示されています。":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>`--api-server-authorized-ip-ranges` に含める IP を見つける方法

そこから API サーバーにアクセスするには、許可された IP 範囲の AKS クラスター リストに、開発用コンピューター、ツール、またはオートメーションの IP アドレスを追加する必要があります。 

もう 1 つの方法は、ファイアウォールの仮想ネットワーク内の別のサブネット内に必要なツールを使用してジャンプボックスを構成することです。 この場合、環境にそれぞれのネットワークのファイアウォールがあり、許可された範囲にファイアウォールの IP を追加してあることが想定されています。 同様に、AKS サブネットからファイアウォール サブネットへのトンネリングを強制している場合は、クラスター サブネット内にジャンプボックスがあってもかまいません。

次のコマンドを使用して、許可された範囲にもう 1 つの IP アドレスを追加します。

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> 上の例では、クラスター上の API サーバーの許可された IP 範囲が追加されます。 許可された IP 範囲を無効にするには、az aks update を使用し、空の範囲 "" を指定します。 

もう 1 つのオプションは、Windows システムで次のコマンドを使用して、パブリック IPv4 アドレスを取得することです。または、「[IP アドレスを確認する](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)」の手順を使用することもできます。

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

このアドレスは、インターネット ブラウザーで "what is my IP address" を検索することによって確認することもできます。

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
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
