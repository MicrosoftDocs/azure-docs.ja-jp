---
title: Azure Kubernetes Service (AKS) での API サーバーの許可された IP 範囲
description: Azure Kubernetes Service (AKS) で API サーバーへのアクセスのための IP アドレス範囲を使用してクラスターをセキュリティで保護する方法について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 6516bbcb4ea879279812d61d9fe31f1ea4268280
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616252"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>プレビュー - Azure Kubernetes Service (AKS) で許可された IP アドレス範囲を使用して API サーバーへのアクセスをセキュリティで保護する

Kubernetes では、API サーバーは、リソースの作成やノードの数のスケーリングなどのクラスター内のアクションを実行するための要求を受信します。 API サーバーは、クラスターを操作したり管理したりするための中心的な方法です。 クラスターのセキュリティを向上させ、攻撃を最小限に抑えるには、API サーバーを限られた一連の IP アドレス範囲からのみアクセス可能にする必要があります。

この記事では、API サーバーの許可された IP アドレス範囲を使用して、要求をコントロール プレーンに制限する方法について説明します。 現在、この機能はプレビュー段階にあります。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトインです。 これらは、コミュニティからフィードバックやバグを収集するために提供されています。 これらの機能はプレビュー段階であり、運用環境での使用を意図していません。 パブリック プレビュー段階の機能は、"ベスト エフォート" のサポートに該当します。 AKS テクニカル サポート チームによるサポートは、太平洋タイム ゾーン (PST) での営業時間内のみで利用できます。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー][aks-support-policies]
> * [Azure サポートに関する FAQ][aks-faq]

## <a name="before-you-begin"></a>開始する前に

API サーバーの許可された IP 範囲は、作成する新しい AKS クラスターに対してのみ機能します。 この記事では、Azure CLI を使用して AKS クラスターを作成する方法について説明します。

Azure CLI バージョン 2.0.61 以降がインストールされて構成されている必要があります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

API サーバーの許可された IP 範囲を構成するには、*aks-preview* CLI 拡張機能バージョン 0.4.1 以降が必要です。 [az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールしてから、az extension update コマンドを使用して使用可能な更新プログラムを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>サブスクリプションの機能フラグを登録する

API サーバーの許可された IP 範囲を使用するには、まずサブスクリプションで機能フラグを有効にします。 *APIServerSecurityPreview* 機能フラグを登録するには、次の例に示すように [az feature register][az-feature-register] コマンドを使用します。

> [!CAUTION]
> サブスクリプションで機能を登録する場合、現時点ではその機能の登録解除を行うことができません。 一部のプレビュー機能を有効にした後、すべての AKS クラスターに対して既定値が使用され、サブスクリプション内に作成されます。 運用サブスクリプションではプレビュー機能を有効にしないでください。 プレビュー機能をテストし、フィードバックを集めるには、別のサブスクリプションを使用してください。

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録状態を確認するには、[az feature list][az-feature-list] コマンドを使用します。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>制限事項

API サーバーの許可された IP 範囲を構成する場合は、次の制限が適用されます。

* API サーバーとの通信もブロックされるため、現在は Azure Dev Spaces を使用できません。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API サーバーの許可された IP 範囲の概要

Kubernetes API サーバーは、基になる Kubernetes API が公開される方法です。 このコンポーネントは、`kubectl` や Kubernetes ダッシュボードなど、管理ツールに対する操作を提供します。 AKS は、専用の API サーバーを含むシングルテナント クラスター マスターを提供します。 既定では、API サーバーにはパブリック IP アドレスが割り当てられるため、ロールベースのアクセス制御 (RBAC) を使用してアクセスを制御する必要があります。

通常はパブリックにアクセス可能な AKS コントロール プレーン/API サーバーへのアクセスをセキュリティで保護するには、許可された IP 範囲を有効にして使用することができます。 これらの許可された IP 範囲では、API サーバーと通信するための定義された IP アドレス範囲のみが許可されます。 これらの許可された IP 範囲に含まれていない IP アドレスから API サーバーへの要求はブロックされます。 その後、ユーザーやそのユーザーが要求するアクションを承認するには、引き続き RBAC を使用する必要があります。

API サーバーやその他のクラスター コンポーネントの詳細については、[AKS における Kubernetes の中心概念][concepts-clusters-workloads]に関するページを参照してください。

## <a name="create-an-aks-cluster"></a>AKS クラスターの作成

API サーバーの許可された IP 範囲は、新しい AKS クラスターに対してのみ機能します。 許可された IP 範囲をクラスター作成操作の一部として有効にすることはできません。 許可された IP 範囲をクラスター作成プロセスの一部として有効にしようとすると、その時点ではエグレス IP アドレスが定義されていないため、クラスター ノードはデプロイ中に API サーバーにアクセスできません。

最初に、[az aks create][az-aks-create] コマンドを使用してクラスターを作成します。 次の例では、*myResourceGroup* という名前のリソース グループ内に *myAKSCluster* という名前の単一ノード クラスターを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>ファイアウォール ルールのための送信ゲートウェイを作成する

次のセクションで許可された IP 範囲を有効にしたときに、クラスター内のノードが確実に API サーバーと通信できるようにするために、送信ゲートウェイとして使用するための Azure Firewall を作成します。 その後、次のセクションで、API サーバーの許可された IP アドレスの一覧に Azure Firewall の IP アドレスが追加されます。

> [!WARNING]
> Azure Firewall を使用すると、毎月の請求サイクルで大きなコストが発生する場合があります。 Azure Firewall を使用するという要件は、この初期のプレビュー期間でのみ必要になります。 詳細およびコスト計画については、「[Azure Firewall の価格][azure-firewall-costs]」を参照してください。

まず、AKS クラスターと仮想ネットワークのための *MC_* リソース グループ名を取得します。 次に、[az network vnet subnet create][az-network-vnet-subnet-create] コマンドを使用してサブネットを作成します。 次の例では、*10.200.0.0/16* の CIDR 範囲を持つ *AzureFirewallSubnet* という名前のサブネットを作成します。

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Azure Firewall を作成するには、[az extension add][az-extension-add] コマンドを使用して *azure-firewall* CLI 拡張機能をインストールします。次に、az network firewall create コマンドを使用してファイアウォールを作成します。 次の例では、*myAzureFirewall* という名前の Azure Firewall を作成します。

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Azure Firewall には、エグレス トラフィックに使用されるパブリック IP アドレスが割り当てられます。 [az network public-ip create][az-network-public-ip-create] コマンドを使用してパブリック アドレスを作成した後、az network firewall ip-config create を使用して、パブリック IP を適用するファイアウォール上の IP 構成を作成します。

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

ここで、[az network firewall network-rule create][az-network-firewall-network-rule-create] コマンドを使用して、すべての *TCP* トラフィックを*許可する* Azure Firewall ネットワーク ルールを作成します。 次の例では、任意のソースまたは宛先アドレスを持つトラフィックのための *AllowTCPOutbound* という名前のネットワーク ルールを作成します。

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Azure Firewall をネットワーク ルートに関連付けるには、既存のルート テーブル情報、Azure Firewall の内部 IP アドレス、および API サーバーの IP アドレスを取得します。 これらの IP アドレスは、トラフィックがクラスター通信のためにルーティングされる方法を制御するために、次のセクションで指定されます。

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

最後に、[az network route-table route create][az-network-route-table-route-create] コマンドを使用して、トラフィックが API サーバーとの通信のために Azure Firewall アプライアンスを使用できるようにするルートを既存の AKS ネットワーク ルート テーブル内に作成します。

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Azure Firewall アプライアンスのパブリック IP アドレスを書き留めておきます。 このアドレスは、次のセクションで、API サーバーの許可された IP 範囲の一覧に追加されます。

## <a name="enable-authorized-ip-ranges"></a>許可された IP 範囲を有効にする

API サーバーの許可された IP 範囲を有効にするには、許可された IP アドレス範囲の一覧を指定します。 CIDR 範囲を指定する場合は、その範囲内の最初の IP アドレスから始めます。 たとえば、*137.117.106.90/29* は有効な範囲ですが、範囲内の最初の IP アドレス (*137.117.106.88/29* など) を指定するようにしてください。

[az aks update][az-aks-update] コマンドを使用し、 *--api-server-authorized-ip-ranges* を指定して許可します。 これらの IP アドレス範囲は通常、オンプレミス ネットワークによって使用されるアドレス範囲です。 前の手順で取得された独自の Azure Firewall のパブリック IP アドレス (*20.42.25.196/32* など) を追加します。

次の例では、*myResourceGroup* という名前のリソース グループ内の *myAKSCluster* という名前のクラスターで API サーバーの許可された IP 範囲を有効にします。 許可する IP アドレス範囲は、*20.42.25.196/32* (Azure Firewall のパブリック IP アドレス)、次に *172.0.0.10/16* と *168.10.0.10/18* です。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>許可された IP 範囲を更新するか、または無効にする

許可された IP 範囲を更新するか、または無効にするには、再度 [az aks update][az-aks-update] コマンドを使用します。 許可したい更新された CIDR 範囲を指定するか、または次の例に示すように、空の範囲を指定して API サーバーの許可された IP 範囲を無効にします。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>次の手順

この記事では、API サーバーの許可された IP 範囲を有効にしました。 このアプローチは、セキュリティで保護された AKS クラスターをどのように実行できるかの一部です。

詳細については、[AKS でのアプリケーションとクラスターのセキュリティの概念][concepts-security]に関するページおよび AKS でのクラスターのセキュリティとアップグレードのベスト プラクティスに関するページを参照してください。

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
