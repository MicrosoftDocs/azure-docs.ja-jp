---
title: パブリック ロード バランサーを使用する
titleSuffix: Azure Kubernetes Service
description: Standard SKU でパブリック ロード バランサーを使用して、Azure Kubernetes Service (AKS) でサービスを公開する方法について説明します。
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: e37c5a748a8e99f49e3535946268427139bbbf44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184425"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でパブリック Standard Load Balancer を使用する

Azure Load Balancer は、受信と送信の両方のシナリオがサポートされる開放型システム間相互接続 (OSI) モデルの L4 上にあります。 これにより、ロード バランサーのフロントエンドに到着した受信フローが、バックエンド プールのインスタンスに分配されます。

**パブリック** ロード バランサーを AKS と統合すると、次の 2 つの目的を達成することができます。

1. AKS 仮想ネットワーク内のクラスター ノードへの送信接続を提供します。 この目的は、ノードのプライベート IP アドレスを、"*送信プール*" の一部であるパブリック IP アドレスに変換することによって達成されます。
2. 種類が `LoadBalancer` の Kubernetes サービスを経由してアプリケーションにアクセスできるようにします。 これにより、ユーザーはアプリケーションを簡単にスケーリングしたり、高可用性のサービスを作成したりすることができます。

**内部 (プライベート)** ロード バランサーは、プライベート IP のみがフロントエンドとして許可される場合に使用されます。 内部ロード バランサーは、仮想ネットワーク内でトラフィックを負荷分散させるために使用されます。 ハイブリッド シナリオでは、オンプレミスのネットワークからもロード バランサーのフロントエンドにアクセスできます。

このドキュメントでは、パブリック ロード バランサーとの統合について説明します。 内部ロード バランサーの統合については、[AKS の内部ロード バランサーに関するドキュメント](internal-lb.md)を参照してください。

## <a name="before-you-begin"></a>開始する前に

Azure Load Balancer は、*Basic* と *Standard* の 2 つの SKU で使用できます。 AKS クラスターを作成する場合、既定では *Standard* SKU が使用されます。 *Standard* SKU を使用すると、より大きなバックエンド プール、[**複数のノード プール**](use-multiple-node-pools.md)、[**Availability Zones**](availability-zones.md)などの追加機能にアクセスできます。 AKS の場合、この Load Balancer SKU が推奨されます。

*Basic* と *Standard* SKU の詳細については、「[Azure Load Balancer の SKU の比較][azure-lb-comparison]」をご覧ください。

この記事では、AKS クラスターで *Standard* SKU Azure Load Balancer が使用されていることを前提として、ロード バランサーの一部の機能を構成および使用する方法について説明します。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

> [!IMPORTANT]
> 送信接続を提供するために、Azure Load Balancer ではなく、独自のゲートウェイ、ファイアウォールまたはプロキシを使用する場合、[**UserDefinedRouting (UDR) として送信の種類**](egress-outboundtype.md)を使用することで、ロード バランサーの送信プールとそれぞれのフロントエンド IP の作成をスキップできます。 送信の種類では、クラスターのエグレス方法を定義します。既定の種類は、ロード バランサーです。

## <a name="use-the-public-standard-load-balancer"></a>パブリック Standard Load Balancer を使用する

送信の種類としてロード バランサー (既定) に設定して AKS クラスターを作成すると、クラスターは、ロード バランサーを使用してサービスも公開できるようになります。

そのために、次の例に示すように、種類が `LoadBalancer` のパブリック サービスを作成できます。 まず、`public-svc.yaml` という名前のサービス マニフェストを作成します。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

[kubectl apply][kubectl-apply] を使用してパブリック サービス マニフェストをデプロイし、YAML マニフェストの名前を指定します。

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Azure Load Balancer が、この新しいサービスを公開する新しいパブリック IP で構成されます。 Azure Load Balancer では複数のフロントエンド IP を持つことができるため、デプロイされたそれぞれの新しいサービスによって、一意にアクセスされる新しい専用のフロントエンド IP が取得されます。

たとえば、次を実行して、サービスが作成され、ロード バランサーが構成されたことを確認できます。

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

サービスの詳細を表示すると、ロード バランサー上のこのサービス用に作成されたパブリック IP アドレスが、*EXTERNAL-IP* 列に表示されます。 IP アドレスが、次の例に示すように *\<pending\>* から実際のパブリック IP アドレスに変わるまで、1 から 2 分かかることがあります。

## <a name="configure-the-public-standard-load-balancer"></a>パブリック Standard Load Balancer を構成する

Standard SKU パブリック ロード バランサーを使用する場合、作成時、またはクラスターの更新時にカスタマイズできる一連のオプションがあります。 これらのオプションを使用すると、ワークロードのニーズに合わせて Load Balancer をカスタマイズできます。適宜確認する必要があります。 Standard Load Balancer を使用すると、次の操作を行うことができます。

* マネージド送信 IP の数の設定またはスケーリング
* 独自のカスタム[送信 IP または送信 IP プレフィックス](#provide-your-own-outbound-public-ips-or-prefixes)の使用
* クラスターの各ノードに割り当てられる送信ポートの数のカスタマイズ
* アイドル状態の接続のタイムアウト設定の構成

> [!IMPORTANT]
> 特定の時点では、1 つの送信 IP オプションのみ (マネージド IP、独自の IP を使用する、または IP プレフィックス) を使用できます。

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>マネージド送信パブリック IP の数のスケーリング

Azure Load Balancer は、アウトバウンドだけでなく、仮想ネットワークからのインバウンド接続も提供します。 アウトバウンド規則を使用すると、パブリック Standard Load Balancer の送信ネットワーク アドレス変換の構成を簡素化できます。

すべてのロード バランサー規則と同様に、アウトバウンド規則は、負荷分散規則およびアウトバウンド NAT 規則と同じ構文に従います。

***フロントエンド IP + パラメーター + バックエンド プール***

アウトバウンド規則では、フロントエンドに変換され、バックエンド プールによって識別されるすべての仮想マシンの送信 NAT を構成します。 また、パラメーターにより、送信 NAT アルゴリズムをさらに細かく制御できます。

アウトバウンド規則は 1 つのパブリック IP アドレスでのみ使用できますが、アウトバウンド規則によって、アウトバウンド NAT をスケーリングするための構成の負担が軽減されます。 複数の IP アドレスを使用することで、大規模なシナリオを計画できます。また、アウトバウンド規則を使用して、SNAT が枯渇しやすいパターンを緩和することもできます。 フロントエンドによって提供される追加の IP アドレスごとに、Load Balancer で SNAT ポートとして使用される 64,000 個の一時的なポートが提供されます。 

既定で作成されるマネージド送信パブリック IP で *Standard* SKU ロード バランサーを使用する場合、 **`load-balancer-managed-ip-count`** パラメーターを使用して、マネージド送信パブリック IP の数をスケーリングすることができます。

既存のクラスターを更新するには、次のコマンドを実行します。 このパラメーターをクラスター作成時に設定し、複数の管理対象送信パブリック IP を与えることもできます。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

上の例では、*myResourceGroup* の *myAKSCluster* クラスターに対して、管理対象の送信パブリック IP の数が *2* に設定されます。 

また、 **`--load-balancer-managed-outbound-ip-count`** パラメーターを追加し、それを目的の値に設定してクラスターを作成する場合、 **`load-balancer-managed-ip-count`** パラメーターを使用して、マネージド送信パブリック IP の初期数を設定することもできます。 管理対象の送信パブリック IP の既定数は 1 です。

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>独自の送信パブリック IP またはプレフィックスを提供する

*Standard* SKU ロード バランサーを使用する場合、既定で、AKS クラスターにより AKS で管理されるインフラストラクチャ リソース グループに、パブリック IP が自動的に作成され、それがロード バランサーの送信プールに割り当てられます。

AKS によって作成されるパブリック IP は、AKS 管理対象リソースと見なされます。 つまり、そのパブリック IP のライフサイクルは、AKS によって管理され、ユーザーがパブリック IP リソースを直接操作する必要はありません。 代わりに、クラスターの作成時に独自のカスタム パブリック IP またはパブリック IP プレフィックスを割り当てることもできます。 また、既存のクラスターのロード バランサーのプロパティでカスタム IP を更新することもできます。

独自のパブリック IP またはプレフィックスを使用するための要件:

- カスタム パブリック IP アドレスは、ユーザーが作成して所有する必要があります。 管理の競合が発生するため、AKS によって作成されるマネージド パブリック IP アドレスを独自のカスタム IP として再使用することはできません。
- 送信 IP にアクセスするためのアクセス許可が AKS クラスター ID (サービス プリンシパルまたはマネージド ID) に付与されていることを確認する必要があります。 [必要なパブリック IP アクセス許可リストに従って](kubernetes-service-principal.md#networking)します。
- 送信 IP または送信 IP プレフィックスを構成するために必要な[前提条件と制約](../virtual-network/public-ip-address-prefix.md#constraints)を満たしていることをご確認ください。

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>クラスターを独自の送信パブリック IP で更新する

[az network public-ip show][az-network-public-ip-show] コマンドを使用すると、パブリック IP の ID が一覧表示されます。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

上のコマンドでは、*myResourceGroup* リソース グループの *myPublicIP* パブリック IP の IP が表示されます。

お使いのパブリック IP でクラスターを更新するには、 **`load-balancer-outbound-ips`** パラメーターを指定した `az aks update` コマンドを使用します。

次の例では、前のコマンドで得られた ID を指定した `load-balancer-outbound-ips` パラメーターを使用しています。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>独自の送信パブリック IP プレフィックスでクラスターを更新する

*Standard* SKU ロード バランサーと共に、エグレス用のパブリック IP プレフィックスを使用することもできます。 次の例では、[az network public-ip prefix show][az-network-public-ip-prefix-show] コマンドを使用し、パブリック IP プレフィックスの IP が一覧表示されます。

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

上のコマンドでは、*myResourceGroup* リソース グループの *myPublicIPPrefix* パブリック IP プレフィックスの IP が表示されます。

次の例では、*load-balancer-outbound-ip-prefixes* パラメーターと前のコマンドからの ID が使用されています。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>独自のパブリック IP またはプレフィックスでクラスターを作成する

エグレス エンドポイントを許可リストに追加するといったシナリオをサポートする目的で、クラスター作成時に、エグレス用に独自の IP アドレスや IP プレフィックスを導入することがあります。 クラスター作成手順に上のコードと同じパラメーターを追加し、クラスターのライフサイクルの開始時に独自のパブリック IP と IP プレフィックスを定義します。

*load-balancer-outbound-ips* パラメーターを指定して *az aks create* コマンドを使用し、開始時に自分のパブリック IP で新しいクラスターを作成します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*load-balancer-outbound-ip-prefixes* パラメーターを指定して *az aks create* コマンドを使用し、開始時に自分のパブリック IP プレフィックスで新しいクラスターを作成します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>割り当てられた送信ポートを構成する

> [!IMPORTANT]
> クラスター上に、少数の宛先に対して多数の接続を確立することが想定されるアプリケーションがある場合 (たとえば、多数のフロントエンド インスタンスを 1 つの SQL DB に接続する場合)、SNAT ポートの枯渇 (接続元のポートの不足) が検出される可能性が非常に高くなります。 このようなシナリオでは、ロード バランサーに割り当てられる送信ポートと送信フロントエンド IP を増加することを強くお勧めします。 増加する場合、1 つの追加 IP アドレスに対して 64,000 個のポートを追加し、すべてのクラスター ノード間で分散させることを考慮する必要があります。


特に指定がない限り、AKS では、構成時に Standard Load Balancer で定義される割り当て送信ポート数の既定値が使用されます。 次のコマンドで示すように、この値は、AKS API の場合は **null** で、SLB API の場合は **0** です。

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

上記のコマンドを実行すると、次の例に示すように、ロード バランサーのアウトバウンド規則が一覧表示されます。

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

この出力は、ポート数が 0 であることを意味しているものではなく、[バックエンド プールのサイズに基づく自動送信ポート割り当て][azure-lb-outbound-preallocatedports]を利用していることを意味しています。したがって、たとえば、クラスターのノード数が 50 以下の場合、各ノードに 1,024 個のポートが割り当てられます。その個数からノード数を増やしていくと、ノードあたりのポート数が徐々に削減されます。


割り当て送信ポート数を定義または増加するには、次の例のようにします。


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

この例では、クラスター内の各ノードの割り当て送信ポート数が 4000 に設定され、IP 数は 7 個です。"*ノードあたり 4000 ポート * 100 ノード = 合計 400,000 ポート <  = 合計 448,000 ポート = 7 個の IP * IP あたり 64,000 ポート*" になります。 これにより、100 ノードに安全にスケーリングすることができ、既定のアップグレード操作を行うことができます。 アップグレードやその他の操作に必要な追加ノードに十分な数のポートを割り当てることが重要です。 AKS では、アップグレード用のバッファー ノード数の既定値として 1 が使用されます。このため、この例では、任意の時点で 4,000 個の空きポートが必要です。 [maxSurge 値](upgrade-cluster.md#customize-node-surge-upgrade)を使用する場合、ノードあたりの送信ポート数に maxSurge 値を乗算します。

ノード数を 100 以上に安全にスケーリングするために、IP をさらに追加する必要があります。


> [!IMPORTANT]
> 接続またはスケーリングの問題を回避するために *allocatedOutboundPorts* をカスタマイズする前に、[必要なクォータを計算し、要件を確認する][requirements]必要があります。

また、クラスターの作成時に **`load-balancer-outbound-ports`** パラメーターを使用することもできます。ただし、 **`load-balancer-managed-outbound-ip-count`** 、 **`load-balancer-outbound-ips`** 、または **`load-balancer-outbound-ip-prefixes`** のいずれかも指定する必要があります。  次に例を示します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>ロード バランサーのアイドル タイムアウトを構成する

SNAT ポート リソースがなくなると、既存のフローによって SNAT ポートが解放されない限り、送信フローは失敗します。 Load Balancer は、フローが終了すると SNAT ポートを回収します。また、AKS で構成されたロード バランサーは、30 分間のアイドル タイムアウトを使用して、アイドル フローから SNAT ポートを回収します。
さあに、トランスポート (たとえば、 **`TCP keepalives`** ) または **`application-layer keepalives`** を使用して、アイドル フローを更新し、必要に応じて、このアイドル タイムアウトをリセットすることもできます。 このタイムアウトは、次の例で示すように構成することができます。 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

存続期間が短い接続が多数あることが想定される場合、および存続期間が長く、アイドル時間が長い可能性のある接続がない場合 (`kubectl proxy` または `kubectl port-forward` を利用する場合など)、4 分などの短いタイムアウト値の使用を検討してください。 また、TCP keepalives を使用する場合、接続の一方の側でそれを有効にするだけで十分です。 たとえば、フローのアイドル タイマーをリセットするには、TCP keepalives をサーバー側でのみ有効にすれば十分であり、両側で開始する必要はありません。 データベースのクライアント/サーバー構成など、アプリケーション レイヤーにも同様の概念があります。 サーバー側で、アプリケーション固有のキープアライブのどのようなオプションが存在するかを確認します。

> [!IMPORTANT]
> AKS では、既定で、アイドル時の TCP リセットが有効になっています。この構成を維持して、ご自分のシナリオでのアプリケーションの動作をより予測可能にするために使用することをお勧めします。
> TCP RST は、ESTABLISHED 状態の TCP 接続時のみ送信されます。 詳細については、[こちら](../load-balancer/load-balancer-tcp-reset.md)を参照してください。

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>割り当て送信ポート数とアイドル タイムアウトをカスタマイズするための要件

- *allocatedOutboundPorts* に指定する値は、8 の倍数である必要もあります。
- ノードの VM の数と割り当てる必要がある送信ポートの数に基づいた十分な送信 IP 容量が必要です。 送信 IP 容量が十分であることを確認するには、次の式を使用します。 
 
*outboundIPs* \* 64,000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*
 
たとえば、*nodeVMs* が 3 で、*desiredAllocatedOutboundPorts* が 50,000 の場合、必要な *outboundIPs* は 3 以上です。 必要最低限の量より多くの送信 IP 容量を組み込むことをお勧めします。 また、送信 IP 容量を計算するときは、クラスター オートスケーラーと、ノード プールのアップグレードの可能性を考慮する必要があります。 クラスター オートスケーラーについては、現在のノード数と最大ノード数を確認し、高い方の値を使用します。 アップグレードについては、アップグレードが可能なすべてのノード プールに対して追加のノード VM を考慮します。

- *IdleTimeoutInMinutes* を既定の 30 分とは異なる値に設定する場合は、ワークロードで送信接続が必要な時間を考慮します。 また、AKS の外部で使用される *Standard* SKU ロード バランサーの既定のタイムアウト値が 4 分であることを考慮します。 特定の AKS ワークロードをより正確に反映するように *IdleTimeoutInMinutes* の値を設定すると、使用されなくなった接続と関連付けられることによる SNAT の枯渇を減らすのに役立ちます。

> [!WARNING]
> *AllocatedOutboundPorts* と *IdleTimeoutInMinutes* の値を変更すると、ロード バランサーの動作が大幅に変更される可能性があるため、トレードオフおよびアプリケーションの接続パターンをよく理解してから変更してください。これらの値を変更する前に、後の [SNAT のトラブルシューティングに関するセクション][troubleshoot-snat]を確認し、[Load Balancer のアウトバウンド規則][azure-lb-outbound-rules-overview]および [Azure での送信接続][azure-lb-outbound-connections]を確認して、変更の影響を完全に理解してください。

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>受信トラフィックを特定の IP 範囲に制限する

次のマニフェストでは *loadBalancerSourceRanges* を使用して、受信外部トラフィックの新しい IP 範囲を指定します。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

> [!NOTE]
> 受信外部トラフィックは、ロード バランサーから AKS クラスターの仮想ネットワークに流れます。 この仮想ネットワークには、ロード バランサーからのすべての受信トラフィックを許可するネットワーク セキュリティ グループ (NSG) があります。 この NSG は *LoadBalancer* という種類の [サービス タグ][service-tags]を使用して、ロード バランサーからのトラフィックを許可します。

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>インバウンド接続時にクライアントの IP を維持する

既定では、[Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) および AKS 内の `LoadBalancer` 型のサービスは、ポッドへの接続時にクライアントの IP アドレスを保持しません。 ポッドに配信されるパケットの送信元 IP は、ノードのプライベート IP になります。 クライアントの IP アドレスを維持するには、サービス定義で `service.spec.externalTrafficPolicy` を `local` に設定する必要があります。 次のマニフェストに例を示します。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Kubernetes 注釈による追加のカスタマイズ

Kubernetes サービスでサポートされている、種類が `LoadBalancer` である注釈の一覧を次に示します。これらの注釈は、**INBOUND** フローにのみ適用されます。

| Annotation | 値 | 説明
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` または `false`                     | ロード バランサーが内部である必要があるかどうかを指定します。 設定しない場合、既定で public が使用されます。
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | サブネットの名前                    | 内部ロード バランサーをバインドする必要があるサブネットを指定します。 設定しない場合、既定で、クラウド構成ファイルで構成されているサブネットが使用されます。
| `service.beta.kubernetes.io/azure-dns-label-name`                 | パブリック IP 上の DNS ラベルの名前   | **パブリック** サービスの DNS ラベル名を指定します。 空の文字列に設定すると、パブリック IP の DNS エントリは使用されません。
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` または `false`                     | 別のサービスと共有される可能性のある Azure セキュリティ規則を使用してサービスを公開する必要があることを指定します。公開できるサービス数が増えるとサービスの特異性は低下します。 この注釈は、ネットワーク セキュリティ グループの Azure [拡張セキュリティ規則](../virtual-network/network-security-groups-overview.md#augmented-security-rules)機能に依存します。 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | リソース グループの名前            | クラスター インフラストラクチャと同一のリソース グループ (ノード リソース グループ) 内にないロード バランサーのパブリック IP のリソース グループを指定します。
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | 許可されるサービス タグの一覧          | 許可される[サービス タグ][service-tags]をコンマで区切った一覧指定します。
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | TCP アイドル タイムアウト (分)          | ロード バランサーで TCP 接続のアイドル タイムアウトが発生するまでの時間を分数で指定します。 既定値は 4 で、これが最小値です。 最大値は 30 です。 整数を指定する必要があります。
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | SLB の `enableTcpReset` を無効にします。


## <a name="troubleshooting-snat"></a>SNAT のトラブルシューティング

同じ宛先 IP アドレスとポートに対して多数の TCP 送信接続または UDP 送信接続が開始されることがわかっている場合、送信接続エラーが発生する場合、または SNAT ポート (PAT によって使用される一時的なポート) が不足しているとサポートから指摘された場合、一般的な軽減策としていくつかの選択肢があります。 これらのオプションを確認し、使用可能であり、実際のシナリオに最適な選択肢を判断してください。 それらを 1 つまたは複数組み合わせることが状況改善に役立つ場合もあります。 詳細については、[アウトバウンド接続のトラブルシューティング ガイド](../load-balancer/troubleshoot-outbound-connection.md)に関するページをご確認ください。

多くの場合、SNAT が枯渇する根本的な原因は、アウトバウンド接続の確立方法と管理方法、または構成可能なタイマーの既定値に対する変更のアンチパターンです。 このセクションの内容を慎重に検討してください。

### <a name="steps"></a>手順
1. 接続のアイドル状態が長時間続いているかどうか、および既定のアイドル タイムアウトに基づいてそのポートが解放されているかどうかを確認します。 そうである場合は、ご使用のシナリオのタイムアウト値を既定の 30 分より短くする必要があります。
2. 対象のアプリケーションで送信接続をどのように作成しているかを調査します (たとえば、コード レビューやパケット キャプチャ)。
3. このアクティビティが予期される動作であるかどうか、またはアプリケーションが誤動作しているかどうかを判断します。 Azure Monitor で[メトリック](../load-balancer/load-balancer-standard-diagnostics.md)と[ログ](../load-balancer/load-balancer-monitor-log.md)を使用して、調査結果を実証します。 たとえば、SNAT 接続メトリックの "失敗" カテゴリを使用します。
4. 適切な[パターン](#design-patterns)に従っているかどうかを評価します。
5. SNAT ポートの枯渇が、[送信 IP アドレスの追加と割り当て送信ポート数の追加](#configure-the-allocated-outbound-ports)によって軽減されるかどうかを評価します。

### <a name="design-patterns"></a>設計パターン
可能な場合は常に、接続の再利用と接続プールを利用してください。 これらのパターンは、リソース枯渇の問題を回避し、予測可能な動作をもたらします。 これらのパターンのプリミティブは、多くの開発ライブラリとフレームワークに含まれています。

- 通常、アトミック要求 (接続ごとに 1 つの要求) は、設計の選択肢として適切ではありません。 そのようなアンチパターンにより規模が制限され、パフォーマンスと信頼性が低下されます。 代わりに、HTTP/S 接続を再利用し、接続と関連付けられている SNAT ポートの数を減らします。 TLS を利用すると、ハンドシェイク、オーバーヘッド、暗号化操作による代償が減るため、アプリケーションのスケールとパフォーマンスが向上します。
- クラスター外、またはカスタム DNS を使用している場合、または coreDNS 上でカスタム アップストリーム サーバーを使用している場合は、クライアントが DNS リゾルバーの結果をキャッシュしていないときに、DNS で大量の個別フローが発生する可能性があることにご注意ください。 カスタム DNS サーバーを使用する代わりに、まず、coreDNS をカスタマイズして、適切なキャッシュ値を定義してください。
- UDP フロー (DNS 参照など) によって、アイドル時間中、SNAT ポートが割り当てられます。 アイドル時間が長くなると、SNAT ポートにかかる圧力がそれだけ高くなります。 短いアイドル タイムアウト (4 分など) を使用します。
接続プールを使用し、接続ボリュームを形成します。
- 警告なしで TCP フローを破棄し、TCP タイマーに依存してフローを消去することは決してしないでください。 TCP で明示的に接続を閉じなかった場合、中間システムとエンドポイントで状態が割り当てられたままになり、他の接続に SNAT ポートを利用できなくなります。 このパターンによって、アプリケーション エラーと SNAT 枯渇がトリガーされる可能性があります。
- OS レベルの TCP 終了関連のタイマー値は、影響が専門的にわからない場合、変更しないでください。 TCP スタックの復旧中、接続のエンドポイントで期待値が一致しない場合、アプリケーションのパフォーマンスが低下する可能性があります。 タイマーの変更が必要になる場合、通常、基になる設計に問題があることを示しています。 次の推奨事項をご確認ください。


上の例では、*MY_EXTERNAL_IP_RANGE* 範囲からの外部トラフィックのみを許可するように規則を更新します。 *MY_EXTERNAL_IP_RANGE* を内部サブネットの IP アドレスに置き換えると、トラフィックはクラスターの内部 IP のみに制限されます。 これにより、Kubernetes クラスター外部のクライアントは、ロード バランサーにアクセスできなくなります。

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>ロード バランサーを Basic SKU から Standard SKU に移行する

既存のクラスターに Basic SKU ロード バランサーが与えられている場合、Standard SKU ロード バランサーでクラスターを使用する目的で移行するとき、動作に重大な違いがあることにご留意ください。

たとえば、`load-balancer-sku` 型のクラスターをクラスターの作成時にのみ定義できる場合、一般的に blue/green のデプロイでクラスターを移行します。 ただし、*Basic SKU* ロード バランサーでは、*Basic SKU* IP アドレスが使用され、*Standard SKU* IP アドレスを必要とする *Standard SKU* ロード バランサーと互換性がありません。 クラスターを移行してロード バランサー SKU をアップグレードするとき、IP アドレス SKU に互換性がある新しい IP アドレスが必須となります。

クラスターの移行方法に関して他に注意すべき事項については、[移行時の考慮事項に関するドキュメント](aks-migration.md)を参照してください。移行時に考慮すべき重要なトピックが一覧表示されています。 以下の制限事項も、AKS で Standard SKU ロード バランサーを使用するときに注意すべき、動作上の重要な違いです。

## <a name="limitations"></a>制限事項

*Standard* SKU を使用するロード バランサーをサポートする AKS クラスターを作成し、管理する場合、次の制限が適用されます。

* AKS クラスターからのエグレス トラフィックを許可するために、パブリック IP または IP プレフィックスが少なくとも 1 つ必要です。 パブリック IP または IP プレフィックスは、コントロール プレーンとエージェント ノードの間の接続を維持するため、および前のバージョンの AKS との互換性を維持するためにも必要です。 *Standard* SKU ロード バランサーでパブリック IP または IP プレフィックスを指定するための次のオプションがあります:
    * 独自のパブリック IP を指定する。
    * 独自のパブリック IP プレフィックスを指定する。
    * 最大 100 までの数字を指定し、AKS クラスターとして作成された同じリソース グループでそれだけの数の *Standard* SKU パブリック IP を作成することを AKS クラスターに許可します。名前は通常、*MC_* で始まります。 AKS により、パブリック IP が *Standard* SKU ロード バランサーに割り当てられます。 既定では、パブリック IP、パブリック IP プレフィックス、または IP の数が指定されていない場合、同じリソース グループでパブリック IP が 1 つ自動的に作成されます。 また、パブリック アドレスを許可する必要があり、IP 作成を禁止する Azure Policy は作成しないようにする必要があります。
* AKS によって作成されるパブリック IP は、独自のカスタム パブリック IP アドレスとして再使用することはできません。 すべてのカスタム IP アドレスは、ユーザーによって作成され、管理される必要があります。
* ロード バランサー SKU は、AKS クラスターの作成時にのみ定義できます。 AKS クラスターが作成された後にロード バランサー SKU を変更することはできません。
* 1 つのクラスターで使用できるロード バランサー SKU (Basic または Standard) の種類は 1 つのみです。
* *Standard* SKU ロード バランサーでは、*Standard* SKU IP アドレスのみがサポートされています。


## <a name="next-steps"></a>次のステップ

[Kubernetes サービスのドキュメント][kubernetes-services]で Kubernetes サービスについて学習する。

受信トラフィックに内部 Load Balancer を使用する方法の詳細については、[AKS の内部ロード バランサーに関するドキュメント](internal-lb.md)を参照してください。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
