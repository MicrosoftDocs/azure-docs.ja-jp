---
title: Azure Kubernetes サービス (AKS) で Azure CNI ネットワークを構成する
description: Azure Kubernetes サービス (AKS) で Azure CNI (高度な) ネットワークを構成する方法について説明します (既存の仮想ネットワークおよびサブネットへの AKS クラスターのデプロイなど)。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.openlocfilehash: 8e541834b31a762c65eabf07072d9b9f7333923e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441976"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Azure Kubernetes サービス (AKS) で Azure CNI ネットワークを構成する

既定では、AKS クラスターでは [kubenet][kubenet] が使用されて、仮想ネットワークとサブネットが自動的に作成されます。 *kubenet* を使用すると、ノードでは仮想ネットワーク サブネットから IP アドレスが取得されます。 その後、ノードにネットワーク アドレス変換 (NAT) が構成されて、ポッドはノードの IP の背後に "隠ぺいされた" IP アドレスを受け取ります。 この方法では、ポッド用にネットワーク空間で予約する必要がある IP アドレスの数が減ります。

[Azure Container Networking Interface (CNI)][cni-networking] では、すべてのポッドにサブネットから IP アドレスが割り当てられ、すべてのポッドに直接アクセスできます。 これらの IP アドレスは、ネットワーク空間全体で一意である必要があり、事前に計画する必要があります。 各ノードには、サポートされるポッドの最大数に対する構成パラメーターがあります。 ノードごとにそれと同じ数の IP アドレスが、そのノードに対して事前に予約されます。 この方法では詳細な計画が必要であり、多くの場合、IP アドレスが不足するか、アプリケーション需要の拡大に伴い、より大きなサブネットでのクラスターの再構築が必要になります。

この記事では、*Azure CNI* ネットワークを使用して、AKS クラスター用の仮想ネットワーク サブネットを作成して使用する方法を示します。 ネットワークのオプションと考慮事項について詳しくは、[Kubernetes および AKS のネットワークの概念][aks-network-concepts]に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* AKS クラスターの仮想ネットワークでは、送信インターネット接続を許可する必要があります。
* 同じサブネット内に複数の AKS クラスターを作成しないでください。
* AKS クラスターでは、Kubernetes サービスのアドレス範囲に `169.254.0.0/16`、`172.30.0.0/16`、`172.31.0.0/16`、`192.0.2.0/24` は使用できません。
* AKS クラスターで使用されるサービス プリンシパルには、少なくとも、ご利用の仮想ネットワーク内のサブネットに対する[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md#network-contributor)アクセス許可が必要です。 組み込みのネットワークの共同作成者ロールを使用する代わりに、[カスタム ロール](../role-based-access-control/custom-roles.md)を定義する場合は、次のアクセス許可が必要です。
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>クラスターの IP アドレス指定を計画する

Azure CNI ネットワークを使用して構成されたクラスターには、追加の計画が必要です。 仮想ネットワークとそのサブネットのサイズは、実行する予定のポッドの数とクラスターのノードの数に対応できる必要があります。

ポッドとクラスターのノードの IP アドレスは、仮想ネットワーク内の指定されたサブネットから割り当てられます。 各ノードは、プライマリ IP アドレスで構成されます。 既定では、ノードでスケジュールされたポッドに割り当てられている Azure CNI によって 30 の追加の IP アドレスが事前に構成されています。 クラスターをスケールアウトすると、サブネットの IP アドレスを使用して各ノードが同様に構成されます。 [ノードごとの最大ポッド数](#maximum-pods-per-node)も表示できます。

> [!IMPORTANT]
> 必要な IP アドレス数では、アップグレードとスケーリング操作も考慮する必要があります。 固定数のノードのみをサポートするよう、IP アドレスを範囲設定した場合、ご使用のクラスターをアップグレードしたり、スケーリングすることはできません。
>
> - ご使用の AKS クラスターを**アップグレード**する場合、新しいノードはそのクラスターにデプロイされます。 サービスやワークロードは新しいノードで実行され、古いノードはクラスターから削除されます。 このローリング アップグレードの手順では、IP アドレスが最低で追加で 1 ブロック利用可能になっている必要があります。 その場合、ノードのカウントは `n + 1` になります。
>   - Windows Server ノード プール (現在は AKS でプレビュー段階) を使用する場合、この考慮事項は特に重要です。 AKS の Windows Server ノードでは、Windows の更新プログラムを自動的に適用するのではなく、ノード プール上でアップグレードを実行します。 このアップグレードでは、最新の Window Server 2019 ベース ノード イメージとセキュリティ パッチを使用して新しいノードをデプロイします。 Windows Server ノード プールのアップグレードの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。
>
> - AKS クラスターを**スケーリングする**場合、新しいノードはそのクラスターにデプロイされます。 サービスとワークロードは新しいノードで実行開始されます。 クラスターでサポートするノードやポッド数をどのようにスケーリングするかを考慮して、使用する IP アドレスの範囲を考慮する必要があります。 アップグレード操作用に、ノードを追加で 1 つ含める必要もあります。 その場合、ノードのカウントは `n + number-of-additional-scaled-nodes-you-anticipate + 1` になります。

ノードで最大数のポッドを実行し、定期的にポッドを破棄およびデプロイする場合、ノードごとに追加の IP アドレスをいくつか用意することも考慮する必要もあります。 新しいサービスをデプロイし、アドレスを取得する場合、サービスを削除して IP アドレスを解放するために数秒かかります。これらの追加の IP アドレスでは、それらが考慮されています。

AKS クラスターの IP アドレス計画は、仮想ネットワーク、ノードとポッドの 1 つ以上のサブネット、および Kubernetes サービスのアドレス範囲で構成されます。

| アドレス範囲/Azure リソース | 制限とサイズ変更 |
| --------- | ------------- |
| 仮想ネットワーク | Azure の仮想ネットワークは、/8 と同じサイズが可能ですが、構成される IP アドレスの個数は 65,536 に制限されています。 |
| Subnet | クラスターにプロビジョニングされている可能性のあるノード、ポッド、すべての Kubernetes、および Azure のリソースを収容するのに十分な大きさである必要があります。 たとえば、内部に Azure Load Balancer をデプロイする場合は、そのフロントエンド IP は、パブリック IP ではなく、クラスター サブネットから割り当てられています。 アップグレード操作や今後のスケーリングのニーズも、サブネットのサイズで考慮される必要があります。<p />アップグレード操作用の追加のノードを含む*最小の*サブネットのサイズの計算には、`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)` を使用します。<p/>たとえば、50 のノードから構成されるクラスターは、`(51) + (51  * 30 (default)) = 1,581` (/21 以上) です。<p/>たとえば、追加でノードを 10 増やす用意がされている 50 のノードのクラスターは、`(61) + (61 * 30 (default)) = 1,891` (/21 以上) です。<p>クラスターを作成するときにノードごとの最大ポッド数を指定しないと、ノードごとの最大ポッド数は *30* に設定されます。 必要な最小 IP アドレス数はその値に基づきます。 別の最大値に基づいて最小 IP アドレス要件を計算する場合、[how to configure the maximum number of pods per node (ノードごとの最大ポッド数の構成方法)](#configure-maximum---new-clusters) を参照して、クラスターをデプロイするときにこの値を設定します。 |
| Kubernetes サービスのアドレス範囲 | この範囲は、この仮想ネットワーク上のネットワーク要素、またはこの仮想ネットワークに接続されているネットワーク要素では使用しないでください。 サービスのアドレスの CIDR は、/12 より小さくする必要があります。 |
| Kubernetes DNS サービスの IP アドレス | クラスター サービス検索 (kube-dns) で使用される、Kubernetes サービスのアドレス範囲内の IP アドレス。 アドレス範囲内の最初の IP アドレス (.1 など) は使用しないでください。 サブネット範囲の最初のアドレスは、*kubernetes.default.svc.cluster.local* アドレスに使用されます。 |
| Docker ブリッジ アドレス | ノード上の Docker ブリッジの IP アドレスとして使用される IP アドレス(CIDR 表記)。 既定値は 172.17.0.1/16 です。 |

## <a name="maximum-pods-per-node"></a>ノードごとの最大ポッド数

AKS クラスターのノードごとの最大ポッド数は 250 です。 ノードごとの "*既定*" の最大ポッド数は、*kubenet* ネットワークと *Azure CNI* ネットワークで、またクラスターのデプロイ方法によって異なります。

| デプロイ方法 | kubenet の既定値 | Azure CNI の既定値 | デプロイ時に構成可能 |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | はい (最大 250) |
| Resource Manager テンプレート | 110 | 30 | はい (最大 250) |
| ポータル | 110 | 30 | いいえ |

### <a name="configure-maximum---new-clusters"></a>最大値の構成 - 新しいクラスター

ノードごとの最大ポッド数を構成できるのは*クラスターのデプロイ時のみ*です。 Azure CLI または Resource Manager テンプレートを使用してデプロイする場合、ノードごとの最大ポッド数の値を最大 250 に設定できます。

| ネットワーク | 最小値 | 最大値 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

> [!NOTE]
> 上記の表の最小値は、AKS サービスによって厳密に強制されます。 示されている最小値より小さい maxPods 値を設定することはできません。そうすると、クラスターが起動できなくなることがあります。

* **Azure CLI**:[az aks create][az-aks-create] コマンドを使用して、クラスターをデプロイするときに `--max-pods` 引数を指定します。 最大値は 250 です。
* **Resource Manager テンプレート**:Resource Manager テンプレートを使用してクラスターをデプロイするときに、[ManagedClusterAgentPoolProfile] オブジェクトに `maxPods` プロパティを指定します。 最大値は 250 です。
* **Azure ポータル**:Azure portal を使用してクラスターをデプロイするときに、ノードごとのポッドの最大数を変更することはできません。 Azure portal を使用してデプロイした Azure CNI ネットワーク クラスターのポッド数は、ノードあたり 30 に制限されています。

### <a name="configure-maximum---existing-clusters"></a>最大値の構成 - 既存のクラスター

既存の AKS クラスターのノードごとの最大ポッド数を変更することはできません。 数の調整は、クラスターを初めてデプロイする場合にのみ実行できます。

## <a name="deployment-parameters"></a>展開のパラメーター

AKS クラスターを作成するときに、Azure CNI ネットワーク用に以下のパラメーターを構成できます。

**仮想ネットワーク**:Kubernetes クラスターをデプロイする仮想ネットワーク。 クラスターに新しい仮想ネットワークを作成する場合は、 *[新規作成]* を選択し、 *[仮想ネットワークの作成]* セクションの手順に従います。 Azure 仮想ネットワークの制限とクォータについては、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)」を参照してください。

**サブネット**:クラスターをデプロイする仮想ネットワーク内のサブネット。 クラスターの仮想ネットワークに新しいサブネットを作成する場合は、 *[新規作成]* を選択し、 *[サブネットの作成]* セクションの手順に従います。 ハイブリッド接続する場合、ご使用の環境のその他の仮想ネットワークのアドレス範囲と重複しないようにする必要があります。

**Kubernetes サービスのアドレス範囲**:これは、Kubernetes によってクラスターの内部[サービス][services]に割り当てられる仮想 IP のセットです。 次の要件を満たす任意のプライベート アドレス範囲を使用できます。

* クラスターの仮想ネットワークの IP アドレス範囲に含まれていてはなりません
* クラスターの仮想ネットワークがピアリングする他の仮想ネットワークと重複していてはなりません
* オンプレミスのどの IP アドレスとも重複していてはなりません
* `169.254.0.0/16`、`172.30.0.0/16`、`172.31.0.0/16`、または `192.0.2.0/24` の範囲内にあってはなりません

技術的には、クラスターと同じ仮想ネットワーク内のサービス アドレス範囲を指定できますが、お勧めはしません。 重複する IP アドレス範囲を使うと、予期しない動作になる可能性があります。 詳細については、この記事の [FAQ](#frequently-asked-questions) のセクションを参照してください。 Kubernetes サービスについて詳しくは、Kubernetes ドキュメントの「[Services][services]」(サービス) をご覧ください。

**Kubernetes DNS サービスの IP アドレス**:クラスターの DNS サービスの IP アドレス。 *[Kubernetes service address range]\(Kubernetes サービス アドレスの範囲\)* 内に含まれるアドレスを指定する必要があります。 アドレス範囲内の最初の IP アドレス (.1 など) は使用しないでください。 サブネット範囲の最初のアドレスは、*kubernetes.default.svc.cluster.local* アドレスに使用されます。

**Docker ブリッジのアドレス**:Docker ブリッジに割り当てる IP アドレスとネットマスク。 Docker ブリッジを使用することで、AKS ノードは基になる管理プラットフォームと通信できます。 この IP アドレスは、クラスターの仮想ネットワーク IP アドレス範囲に含まれていてはならず、ネットワークで使用されている他のアドレス範囲と重複していてもなりません。

## <a name="configure-networking---cli"></a>ネットワークを構成する - CLI

Azure CLI を使って AKS クラスターを作成するときも、Azure CNI ネットワークを構成できます。 Azure CNI ネットワークを有効にして新しい AKS クラスターを作成するには、次のコマンドを使います。

最初に、AKS クラスターを参加させる既存のサブネットのサブネット リソース ID を取得します。

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query [].id --output tsv

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

![Azure Portal の [高度] ネットワーク構成][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>よく寄せられる質問

次の質問と回答は、**Azure CNI** ネットワークの構成に関するものです。

* *クラスター サブネットに VM を展開できますか。*

  いいえ。 Kubernetes クラスターで使用されているサブネットに VM を展開することはできません。 VM を同じ仮想ネットワークにデプロイできますが、異なるサブネットにデプロイする必要があります。

* *ポッドごとのネットワーク ポリシーを構成できますか。*

  はい、Kubernetes ネットワーク ポリシーは AKS で使用できます。 開始するには、[AKS でネットワーク ポリシーを使用してポッド間のトラフィックをセキュリティで保護する][network-policy]方法に関する記事を参照してください。

* *ノードに展開できるポッドの最大数は構成できますか。*

  はい。Azure CLI または Resource Manager テンプレートを使用してクラスターをデプロイするときに構成することができます。 「[ノードごとの最大ポッド数](#maximum-pods-per-node)」を参照してください。

  既存のクラスターでノードごとのポッドの最大数を変更することはできません。

* *AKS クラスターの作成時に作成したサブネットの追加のプロパティを構成するにはどうすればよいですか。たとえば、サービス エンドポイントなどのプロパティです。*

  AKS クラスターの作成中時に作成する仮想ネットワークとサブネットのプロパティの詳細な一覧は、Azure portal の標準の仮想ネットワーク構成ページで構成できます。

* **[Kubernetes サービスのアドレス範囲]** *のクラスター仮想ネットワーク内で別のサブネットを使用できますか*。

  お勧めはしませんが、この構成は可能です。 サービスのアドレス範囲は、Kubernetes によってクラスターの内部サービスに割り当てられる仮想 IP (VIP) のセットです。 Azure のネットワークには、Kubernetes クラスターのサービスの IP 範囲の可視性がありません。 クラスターのサービス アドレス範囲には可視性がないため、後でクラスターの仮想ネットワークにサービスのアドレス範囲と重複する新しいサブネットが作成される可能性があります。 このような重複が発生した場合、Kubernetes は、サブネット内の他のリソースによって既に使用されている IP をサービスに割り当てる可能性があり、予期しない動作やエラーの原因となります。 クラスターの仮想ネットワークの外部のアドレス範囲を使用することで、この重複のリスクを回避できます。

## <a name="next-steps"></a>次の手順

AKS のネットワークの詳細については、次の記事を参照してください。

- [Azure Kubernetes Service (AKS) ロード バランサーで静的 IP アドレスを使用する](static-ip.md)
- [Azure Container Service (AKS) で内部ロード バランサーを使用する](internal-lb.md)

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- [動的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-tls]
- [静的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-static-tls]

### <a name="aks-engine"></a>AKS Engine

[Azure Kubernetes Service Engine (AKS Engine)][aks-engine] は、Azure に Kubernetes クラスターをデプロイする場合に使用できる Azure Resource Manager テンプレートを生成するオープンソース プロジェクトです。

AKS Engine で作成された Kubernetes クラスターは、両方の [kubenet][kubenet]and [Azure CNI][cni-networking] プラグインをサポートしています。 そのため、AKS Engine では両方のネットワーク シナリオがサポートされています。

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
