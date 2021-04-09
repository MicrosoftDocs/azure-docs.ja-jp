---
title: Azure Kubernetes サービス (AKS) で Azure CNI ネットワークを構成する
description: Azure Kubernetes サービス (AKS) で Azure CNI (高度な) ネットワークを構成する方法について説明します (既存の仮想ネットワークおよびサブネットへの AKS クラスターのデプロイなど)。
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions
ms.openlocfilehash: ef9e3689f5846ddfc66c47a15967a18fc6550d35
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504254"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Azure Kubernetes サービス (AKS) で Azure CNI ネットワークを構成する

既定では、AKS クラスターでは [kubenet][kubenet] が使用されて、仮想ネットワークとサブネットが自動的に作成されます。 *kubenet* を使用すると、ノードでは仮想ネットワーク サブネットから IP アドレスが取得されます。 その後、ノードにネットワーク アドレス変換 (NAT) が構成されて、ポッドはノードの IP の背後に "隠ぺいされた" IP アドレスを受け取ります。 この方法では、ポッド用にネットワーク空間で予約する必要がある IP アドレスの数が減ります。

[Azure Container Networking Interface (CNI)][cni-networking] では、すべてのポッドにサブネットから IP アドレスが割り当てられ、すべてのポッドに直接アクセスできます。 これらの IP アドレスは、ネットワーク空間全体で一意である必要があり、事前に計画する必要があります。 各ノードには、サポートされるポッドの最大数に対する構成パラメーターがあります。 ノードごとにそれと同じ数の IP アドレスが、そのノードに対して事前に予約されます。 この方法では詳細な計画が必要であり、多くの場合、IP アドレスが不足するか、アプリケーション需要の拡大に伴い、より大きなサブネットでのクラスターの再構築が必要になります。

この記事では、*Azure CNI* ネットワークを使用して、AKS クラスター用の仮想ネットワーク サブネットを作成して使用する方法を示します。 ネットワークのオプションと考慮事項について詳しくは、[Kubernetes および AKS のネットワークの概念][aks-network-concepts]に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* AKS クラスターの仮想ネットワークでは、送信インターネット接続を許可する必要があります。
* AKS クラスターでは、Kubernetes サービスのアドレス範囲、ポッド アドレス範囲、またはクラスターの仮想ネットワーク アドレス範囲に `169.254.0.0/16`、`172.30.0.0/16`、`172.31.0.0/16`、`192.0.2.0/24` を使用することはできません。
* AKS クラスターで使用されるクラスター ID には、少なくとも、ご利用の仮想ネットワーク内のサブネットに対する[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md#network-contributor)のアクセス許可が必要です。 組み込みのネットワークの共同作成者ロールを使用する代わりに、[カスタム ロール](../role-based-access-control/custom-roles.md)を定義する場合は、次のアクセス許可が必要です。
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* AKS ノード プールに割り当てられたサブネットを[委任されたサブネット](../virtual-network/subnet-delegation-overview.md)にすることはできません。

## <a name="plan-ip-addressing-for-your-cluster"></a>クラスターの IP アドレス指定を計画する

Azure CNI ネットワークを使用して構成されたクラスターには、追加の計画が必要です。 仮想ネットワークとそのサブネットのサイズは、実行する予定のポッドの数とクラスターのノードの数に対応できる必要があります。

ポッドとクラスターのノードの IP アドレスは、仮想ネットワーク内の指定されたサブネットから割り当てられます。 各ノードは、プライマリ IP アドレスで構成されます。 既定では、ノードでスケジュールされたポッドに割り当てられている Azure CNI によって 30 の追加の IP アドレスが事前に構成されています。 クラスターをスケールアウトすると、サブネットの IP アドレスを使用して各ノードが同様に構成されます。 [ノードごとの最大ポッド数](#maximum-pods-per-node)も表示できます。

> [!IMPORTANT]
> 必要な IP アドレス数では、アップグレードとスケーリング操作も考慮する必要があります。 固定数のノードのみをサポートするよう、IP アドレスを範囲設定した場合、ご使用のクラスターをアップグレードしたり、スケーリングすることはできません。
>
> * ご使用の AKS クラスターを **アップグレード** する場合、新しいノードはそのクラスターにデプロイされます。 サービスやワークロードは新しいノードで実行され、古いノードはクラスターから削除されます。 このローリング アップグレードの手順では、IP アドレスが最低で追加で 1 ブロック利用可能になっている必要があります。 その場合、ノードのカウントは `n + 1` になります。
>   * この考慮事項は、Windows Server ノード プールを使用する場合に特に重要です。 AKS の Windows Server ノードでは、Windows の更新プログラムを自動的に適用するのではなく、ノード プール上でアップグレードを実行します。 このアップグレードでは、最新の Window Server 2019 ベース ノード イメージとセキュリティ パッチを使用して新しいノードをデプロイします。 Windows Server ノード プールのアップグレードの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。
>
> * AKS クラスターを **スケーリングする** 場合、新しいノードはそのクラスターにデプロイされます。 サービスとワークロードは新しいノードで実行開始されます。 クラスターでサポートするノードやポッド数をどのようにスケーリングするかを考慮して、使用する IP アドレスの範囲を考慮する必要があります。 アップグレード操作用に、ノードを追加で 1 つ含める必要もあります。 その場合、ノードのカウントは `n + number-of-additional-scaled-nodes-you-anticipate + 1` になります。

ノードで最大数のポッドを実行し、定期的にポッドを破棄およびデプロイする場合、ノードごとに追加の IP アドレスをいくつか用意することも考慮する必要もあります。 新しいサービスをデプロイし、アドレスを取得する場合、サービスを削除して IP アドレスを解放するために数秒かかります。これらの追加の IP アドレスでは、それらが考慮されています。

AKS クラスターの IP アドレス計画は、仮想ネットワーク、ノードとポッドの 1 つ以上のサブネット、および Kubernetes サービスのアドレス範囲で構成されます。

| アドレス範囲/Azure リソース | 制限とサイズ変更 |
| --------- | ------------- |
| 仮想ネットワーク | Azure の仮想ネットワークは、/8 と同じサイズが可能ですが、構成される IP アドレスの個数は 65,536 に制限されています。 アドレス空間を構成する前に、他の仮想ネットワーク内のサービスとの通信を含め、ネットワークのすべてのニーズを考慮に入れてください。 たとえば、大きすぎるアドレス空間を構成すると、ネットワーク内の他のアドレス空間と重複する問題が発生する可能性があります。|
| Subnet | クラスターにプロビジョニングされている可能性のあるノード、ポッド、すべての Kubernetes、および Azure のリソースを収容するのに十分な大きさである必要があります。 たとえば、内部に Azure Load Balancer をデプロイする場合は、そのフロントエンド IP は、パブリック IP ではなく、クラスター サブネットから割り当てられています。 アップグレード操作や今後のスケーリングのニーズも、サブネットのサイズで考慮される必要があります。<p />アップグレード操作用の追加のノードを含む *最小の* サブネットのサイズの計算には、`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)` を使用します。<p/>たとえば、50 のノードから構成されるクラスターは、`(51) + (51  * 30 (default)) = 1,581` (/21 以上) です。<p/>たとえば、追加でノードを 10 増やす用意がされている 50 のノードのクラスターは、`(61) + (61 * 30 (default)) = 1,891` (/21 以上) です。<p>クラスターを作成するときにノードごとの最大ポッド数を指定しないと、ノードごとの最大ポッド数は *30* に設定されます。 必要な最小 IP アドレス数はその値に基づきます。 別の最大値に基づいて最小 IP アドレス要件を計算する場合、[how to configure the maximum number of pods per node (ノードごとの最大ポッド数の構成方法)](#configure-maximum---new-clusters) を参照して、クラスターをデプロイするときにこの値を設定します。 |
| Kubernetes サービスのアドレス範囲 | この範囲は、この仮想ネットワーク上のネットワーク要素、またはこの仮想ネットワークに接続されているネットワーク要素では使用しないでください。 サービスのアドレスの CIDR は、/12 より小さくする必要があります。 この範囲は、さまざまな AKS クラスター間で再利用できます。 |
| Kubernetes DNS サービスの IP アドレス | クラスター サービス検索で使用される、Kubernetes サービスのアドレス範囲内の IP アドレス。 アドレス範囲内の最初の IP アドレス (.1 など) は使用しないでください。 サブネット範囲の最初のアドレスは、*kubernetes.default.svc.cluster.local* アドレスに使用されます。 |
| Docker ブリッジ アドレス | Docker ブリッジのネットワーク アドレスは、すべての Docker インストールに存在する既定の *docker0* ブリッジのネットワーク アドレスを表します。 *docker0* ブリッジは AKS クラスターまたはポッド自体では使用されませんが、AKS クラスター内の *docker ビルド* などのシナリオを引き続きサポートするには、このアドレスを設定する必要があります。 Docker ブリッジのネットワーク アドレスの CIDR を選択する必要があります。そうしないと、他の CIDR と競合する可能性のあるサブネットが Docker によって自動的に選択されます。 ネットワーク上の残りの CIDR と競合しないアドレス空間を選択する必要があります。これには、クラスターのサービス CIDR とポッド CIDR が含まれます。 既定値は 172.17.0.1/16 です。 この範囲は、さまざまな AKS クラスター間で再利用できます。 |

## <a name="maximum-pods-per-node"></a>ノードごとの最大ポッド数

AKS クラスターのノードごとの最大ポッド数は 250 です。 ノードごとの "*既定*" の最大ポッド数は、*kubenet* ネットワークと *Azure CNI* ネットワークで、またクラスターのデプロイ方法によって異なります。

| デプロイ方法 | kubenet の既定値 | Azure CNI の既定値 | デプロイ時に構成可能 |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | はい (最大 250) |
| Resource Manager テンプレート | 110 | 30 | はい (最大 250) |
| ポータル | 110 | 110 ([ノード プール] タブで構成済み) | いいえ |

### <a name="configure-maximum---new-clusters"></a>最大値の構成 - 新しいクラスター

ノードごとの最大ポッド数を構成できるのは、クラスターのデプロイ時、または新しいノード プールを追加するときです。 Azure CLI または Resource Manager テンプレートを使用してデプロイする場合、ノードごとの最大ポッド数の値を最大 250 に設定できます。

新しいノード プールを作成するときに maxPods を指定しない場合は、Azure CNI の既定値 30 を受け取ります。

ノードあたりの最大ポッドの最小値は、クラスターの正常性にとって重要なシステム ポッド用の領域を保証するために適用されます。 ノードごとの最大ポッドに対して設定できる最小値は、各ノード プールの構成に最低 30 個のポッド用の領域がある場合に限り、10 です。 たとえば、ノードあたりの最大ポッドを 10 以上に設定するには、個々のノード プールに少なくとも 3 つのノードが必要です。 この要件は、新しく作成された各ノード プールにも適用されます。したがって、ノードあたりの最大ポッドとして 10 が定義されている場合は、追加された以降の各ノード プールには少なくとも 3 つのノードが必要です。

| ネットワーク | 最小値 | 最大値 |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> 上記の表の最小値は、AKS サービスによって厳密に強制されます。 示されている最小値より小さい maxPods 値を設定することはできません。そうすると、クラスターが起動できなくなることがあります。

* **Azure CLI**:[az aks create][az-aks-create] コマンドを使用して、クラスターをデプロイするときに `--max-pods` 引数を指定します。 最大値は 250 です。
* **Resource Manager テンプレート**:Resource Manager テンプレートを使用してクラスターをデプロイするときに、[ManagedClusterAgentPoolProfile] オブジェクトに `maxPods` プロパティを指定します。 最大値は 250 です。
* **Azure ポータル**:Azure portal を使用してクラスターをデプロイするときに、ノードごとのポッドの最大数を変更することはできません。 Azure portal を使用してデプロイした Azure CNI ネットワーク クラスターのポッド数は、ノードあたり 30 に制限されています。

### <a name="configure-maximum---existing-clusters"></a>最大値の構成 - 既存のクラスター

新しいノード プールを作成するときに、ノードごとの maxPod 設定を定義することができます。 既存のクラスターでノードごとの maxPod の設定値を増やす必要がある場合は、新しく必要な maxPod カウントで新しいノード プールを追加します。 ポッドを新しいプールに移行した後、古いプールを削除します。 クラスター内の古いプールを削除するには、[システム ノード プールのドキュメント][system-node-pools]の定義に従ってノード プール モードを設定していることを確認してください。

## <a name="deployment-parameters"></a>展開のパラメーター

AKS クラスターを作成するときに、Azure CNI ネットワーク用に以下のパラメーターを構成できます。

**仮想ネットワーク**:Kubernetes クラスターをデプロイする仮想ネットワーク。 クラスターに新しい仮想ネットワークを作成する場合は、 *[新規作成]* を選択し、 *[仮想ネットワークの作成]* セクションの手順に従います。 Azure 仮想ネットワークの制限とクォータについては、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)」を参照してください。

**サブネット**:クラスターをデプロイする仮想ネットワーク内のサブネット。 クラスターの仮想ネットワークに新しいサブネットを作成する場合は、 *[新規作成]* を選択し、 *[サブネットの作成]* セクションの手順に従います。 ハイブリッド接続する場合、ご使用の環境のその他の仮想ネットワークのアドレス範囲と重複しないようにする必要があります。

**Azure ネットワーク プラグイン**: Azure ネットワーク プラグインが使用されている場合、"externalTrafficPolicy = Local" の内部 LoadBalancer サービスには、AKS クラスターに属さない clusterCIDR の IP で VM からアクセスすることはできません。

**Kubernetes サービスのアドレス範囲**: このパラメーターは、Kubernetes によってクラスターの内部 [サービス][services]に割り当てられる仮想 IP のセットです。 次の要件を満たす任意のプライベート アドレス範囲を使用できます。

* クラスターの仮想ネットワークの IP アドレス範囲に含まれていてはなりません
* クラスターの仮想ネットワークがピアリングする他の仮想ネットワークと重複していてはなりません
* オンプレミスのどの IP アドレスとも重複していてはなりません
* `169.254.0.0/16`、`172.30.0.0/16`、`172.31.0.0/16`、または `192.0.2.0/24` の範囲内にあってはなりません

技術的には、クラスターと同じ仮想ネットワーク内のサービス アドレス範囲を指定できますが、お勧めはしません。 重複する IP アドレス範囲を使うと、予期しない動作になる可能性があります。 詳細については、この記事の [FAQ](#frequently-asked-questions) のセクションを参照してください。 Kubernetes サービスについて詳しくは、Kubernetes ドキュメントの「[Services][services]」(サービス) をご覧ください。

**Kubernetes DNS サービスの IP アドレス**:クラスターの DNS サービスの IP アドレス。 *[Kubernetes service address range]\(Kubernetes サービス アドレスの範囲\)* 内に含まれるアドレスを指定する必要があります。 アドレス範囲内の最初の IP アドレス (.1 など) は使用しないでください。 サブネット範囲の最初のアドレスは、*kubernetes.default.svc.cluster.local* アドレスに使用されます。

**Docker ブリッジのアドレス**:Docker ブリッジのネットワーク アドレスは、すべての Docker インストールに存在する既定の *docker0* ブリッジのネットワーク アドレスを表します。 *docker0* ブリッジは AKS クラスターまたはポッド自体では使用されませんが、AKS クラスター内の *docker ビルド* などのシナリオを引き続きサポートするには、このアドレスを設定する必要があります。 Docker ブリッジのネットワーク アドレスの CIDR を選択する必要があります。そうしないと、他の CIDR と競合する可能性のあるサブネットが Docker によって自動的に選択されます。 ネットワーク上の残りの CIDR と競合しないアドレス空間を選択する必要があります。これには、クラスターのサービス CIDR とポッド CIDR が含まれます。

## <a name="configure-networking---cli"></a>ネットワークを構成する - CLI

Azure CLI を使って AKS クラスターを作成するときも、Azure CNI ネットワークを構成できます。 Azure CNI ネットワークを有効にして新しい AKS クラスターを作成するには、次のコマンドを使います。

最初に、AKS クラスターを参加させる既存のサブネットのサブネット リソース ID を取得します。

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

[az aks create][az-aks-create] コマンドに `--network-plugin azure` 引数を指定して実行し、高度なネットワークでクラスターを作成します。 前の手順で収集したサブネット ID で、`--vnet-subnet-id` の値を更新します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>ネットワークを構成する - ポータル

Azure Portal の次のスクリーン ショットは、AKS クラスターの作成時にこれらの設定を構成する場合の例を示しています。

![Azure portal の詳細なネットワーク構成][portal-01-networking-advanced]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>動的 IP 割り当てと拡張サブネット サポート (プレビュー)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

> [!NOTE] 
> このプレビュー機能は現在、次のリージョンでご利用いただけます:
>
> * 米国中西部

従来の CNI の欠点は、AKS クラスターが大きくなったときにポッド IP アドレスが枯渇することです。その結果、より大きなサブネットでクラスター全体を再構築する必要が生じます。 Azure CNI の新しい動的 IP 割り当て機能は、AKS クラスターをホストしているサブネットとは別のサブネットから ポッド IP を割り当てることで、この問題を解決するものです。  次のような利点があります。

* **IP の使用効率の向上**: IP は、ポッド サブネットからクラスター ポッドへと動的に割り当てられます。 これにより、すべてのノードで IP が静的に割り当てられる従来の CNI ソリューションと比べて、クラスター内での IP の使用効率が向上します。  

* **スケーラブルで柔軟**: ノードとポッドのサブネットを個別にスケーリングできます。 1 つのポッド サブネットを、クラスターの複数のノード プール間で共有することも、同じ VNet にデプロイされた複数の AKS クラスター間で共有することもできます。 ノード プール用に個別のポッド サブネットを構成することもできます。  

* **ハイ パフォーマンス**: ポッドには VNet IP が割り当てられるため、VNet 内の他のクラスター ポッドとリソースに直接接続できます。 このソリューションでは、パフォーマンスを低下させることなく、非常に大規模なクラスターをサポートできます。

* **ポッドに対する個別の VNet ポリシー**: ポッドには個別のサブネットがあるので、ノード ポリシーとは異なる VNet ポリシーを構成できます。 そのため、ノードではなくポッドに対してのみインターネット接続を許可したり、VNet ネットワーク NAT を使用してノード プール内のポッドのソース IP を修正したり、NSG を使用してノード プール間のトラフィックをフィルター処理したりと、さまざまな方法で便利なシナリオを実現できます。  

* **Kubernetes ネットワーク ポリシー**: Azure ネットワーク ポリシーと Calico は、この新しいソリューションと連携して動作します。  

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI をインストールする

*aks-preview* Azure CLI 拡張機能が必要になります。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>`PodSubnetPreview` プレビュー機能を登録する

この機能を使用するには、サブスクリプションで `PodSubnetPreview` 機能フラグも有効にする必要があります。

`PodSubnetPreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="additional-prerequisites"></a>追加の前提条件

Azure CNI について既に記載されている前提条件は引き続き適用されますが、いくつか追加の制限事項があります。

* Linux のノード クラスターとノード プールのみがサポートされています。
* AKS Engine クラスターと DIY クラスターはサポートされていません。

### <a name="planning-ip-addressing"></a>IP アドレスの計画

この機能を使用すると、計画が大幅に簡単になります。 ノードとポッドは独立してスケーリングされるため、アドレス空間も個別に計画することができます。 ポッド サブネットはノード プールの粒度に合わせて構成できるので、ユーザーはノード プールを追加する際、常に新しいサブネットを追加できます。 クラスター/ノード プール内のシステム ポッドはポッド サブネットからも IP を受信するので、この動作については把握をしておく必要があります。

K8S サービスと Docker ブリッジの IP の計画については、変更はありません。

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>クラスター内のノードあたりの最大ポッド数 (動的 IP 割り当てと拡張サブネット サポートを利用する場合)

Azure CNI で動的 IP 割り当てを使用する場合は、ノードあたりのポッド数の値が、従来の CNI での動作とは若干異なります。

|CNI|配置方法|Default|デプロイ時に構成可能|
|--|--| :--: |--|
|従来の Azure CNI|Azure CLI|30|はい (最大 250)|
|動的 IP 割り当てを使用した Azure CNI|Azure CLI|250|はい (最大 250)|

ポッドあたりの最大ノード数の構成に関連するその他のガイダンスについては、一切変更はありません。

### <a name="additional-deployment-parameters"></a>追加のデプロイ パラメーター

上記で説明したデプロイ パラメーターはすべて引き続き有効ですが、例外が 1 つあります。

* **subnet** パラメーターで、クラスターのノードに関連するサブネットが参照されるようになりました。
* 追加のパラメーター **pod subnet** は、IP アドレスがポッドに動的に割り当てられるサブネットを指定するために使用します。

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>ネットワークを構成する - 動的 IP 割り当てと拡張サブネット サポートを使用する場合の CLI

クラスターで動的 IP 割り当てと拡張サブネット サポートを使用する方法は、クラスターの Azure CNI を構成する際の既定の方法と同様です。 次の例は、ノードのサブネットとポッドのサブネットを使った新しい仮想ネットワークを作成し、Azure CNI を使ったクラスターを作成して動的 IP 割り当てと拡張サブネット サポートを利用する手順について説明したものです。 `$subscription` などの変数は、実際の値に置き換えてください。

まず、2 つのサブネットを持つ仮想ネットワークを作成します。

```azurecli-interactive
$resourceGroup="myResourceGroup"
$vnet="myVirtualNetwork"

# Create our two subnet network 
az network vnet create -g $rg --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

次に、`--vnet-subnet-id` を使用してノード サブネットを参照し、`--pod-subnet-id` を使用してポッド サブネットを参照して、クラスターを作成します。

```azurecli-interactive
$clusterName="myAKSCluster"
$location="eastus"
$subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location --max-pods 250 --node-count 2 --network-plugin azure --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>ノード プールの追加

ノード プールを追加する場合は、`--vnet-subnet-id` を使用してノード サブネットを参照し、`--pod-subnet-id` を使用してポッド サブネットを参照します。 次の例では、新しいノード プールの作成時に参照される、2 つの新しいサブネットを作成しています。

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newNodepool --max-pods 250 --node-count 2 --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet --no-wait 
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

次の質問と回答は、**Azure CNI** ネットワークの構成に関するものです。

* *クラスター サブネットに VM を展開できますか。*

  はい。

* *Azure CNI 対応ポッドで発生したトラフィックに対して、外部システムではどのソース IP が認識されますか。*

  AKS クラスターと同じ仮想ネットワーク内のシステムでは、ポッドの IP が、ポッドからのすべてのトラフィックの発信元アドレスとして認識されます。 AKS クラスター仮想ネットワークの外部にあるシステムでは、ノードの IP が、ポッドからのすべてのトラフィックの発信元アドレスとして認識されます。

* *ポッドごとのネットワーク ポリシーを構成できますか。*

  はい、Kubernetes ネットワーク ポリシーは AKS で使用できます。 開始するには、[AKS でネットワーク ポリシーを使用してポッド間のトラフィックをセキュリティで保護する][network-policy]方法に関する記事を参照してください。

* *ノードに展開できるポッドの最大数は構成できますか。*

  はい。Azure CLI または Resource Manager テンプレートを使用してクラスターをデプロイするときに構成することができます。 「[ノードごとの最大ポッド数](#maximum-pods-per-node)」を参照してください。

  既存のクラスターでノードごとのポッドの最大数を変更することはできません。

* *AKS クラスターの作成時に作成したサブネットの追加のプロパティを構成するにはどうすればよいですか。たとえば、サービス エンドポイントなどのプロパティです。*

  AKS クラスターの作成中時に作成する仮想ネットワークとサブネットのプロパティの詳細な一覧は、Azure portal の標準の仮想ネットワーク構成ページで構成できます。

* **[Kubernetes サービスのアドレス範囲]** *のクラスター仮想ネットワーク内で別のサブネットを使用できますか*。

  お勧めはしませんが、この構成は可能です。 サービスのアドレス範囲は、Kubernetes によってクラスターの内部サービスに割り当てられる仮想 IP (VIP) のセットです。 Azure のネットワークには、Kubernetes クラスターのサービスの IP 範囲の可視性がありません。 クラスターのサービス アドレス範囲には可視性がないため、後でクラスターの仮想ネットワークにサービスのアドレス範囲と重複する新しいサブネットが作成される可能性があります。 このような重複が発生した場合、Kubernetes は、サブネット内の他のリソースによって既に使用されている IP をサービスに割り当てる可能性があり、予期しない動作やエラーの原因となります。 クラスターの仮想ネットワークの外部のアドレス範囲を使用することで、この重複のリスクを回避できます。

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>IP アドレスの動的割り当てと拡張サブネット サポートに関する FAQ

次の質問と回答は、**IP アドレスの動的割り当てと拡張サブネット サポートを使用する場合の Azure CNI ネットワークの構成** に適用されます。

* *1 つのクラスター/ノード プールに複数のポッド サブネットを割り当てることはできますか?*

  1 つのクラスターやノード プールに対しては、サブネットを 1 つしか割り当てることができません。 ただし、複数のクラスターやノード プールで、1 つのサブネットを共有することはできます。

* *別の VNet からのポッド サブネットを一緒に割り当てることはできますか?*

  ポッド サブネットは、クラスターと同じ VNet からのものである必要があります。  

* *クラスター内の一部のノード プールで従来の CNI を使用し、他のノード プールで新しい CNI を使用することはできますか?*

  クラスター全体で、1 種類の CNI のみを使用する必要があります。

## <a name="aks-engine"></a>AKS Engine

[Azure Kubernetes Service Engine (AKS Engine)][aks-engine] は、Azure に Kubernetes クラスターをデプロイする場合に使用できる Azure Resource Manager テンプレートを生成するオープンソース プロジェクトです。

AKS Engine で作成された Kubernetes クラスターは、[kubenet][kubenet] および [Azure CNI][cni-networking] プラグインの両方をサポートしています。 そのため、AKS Engine では両方のネットワーク シナリオがサポートされています。

## <a name="next-steps"></a>次のステップ

AKS のネットワークの詳細については、次の記事を参照してください。

* [Azure Kubernetes Service (AKS) ロード バランサーで静的 IP アドレスを使用する](static-ip.md)
* [Azure Container Service (AKS) で内部ロード バランサーを使用する](internal-lb.md)

* [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
* [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
* [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
* [動的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-tls]
* [静的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-static-tls]
<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png [portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
