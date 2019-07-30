---
title: オペレーターのベスト プラクティス - Azure Kubernetes Services (AKS) のネットワーク接続
description: Azure Kubernetes Service (AKS) での仮想ネットワーク リソースと接続に関するクラスター オペレーターのベスト プラクティスについて説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: mlearned
ms.openlocfilehash: d1bc865b38b52c8a7c3ac6ec4dab6408a1d0430c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614760"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのネットワーク接続とセキュリティに関するベスト プラクティス

Azure Kubernetes Service (AKS) のクラスターを作成および管理する際には、ノードおよびアプリケーションに対するネットワーク接続を提供します。 これらのネットワーク リソースには、IP アドレス範囲、ロード バランサー、およびイングレス コントローラーが含まれます。 アプリケーションのサービスを高品質に維持するには、これらのリソースを計画し、構成する必要があります。

このベスト プラクティスの記事では、クラスター オペレーター向けのネットワーク接続とセキュリティに焦点を当てます。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * AKS で kubenet ネットワーク モードと Azure CNI ネットワーク モードを比較する
> * 必要な IP アドレス指定と接続を計画する
> * ロード バランサー、イングレス コントローラー、または Web アプリケーション ファイアウォール (WAF) を使用してトラフィックを分散する
> * クラスター ノードに安全に接続する

## <a name="choose-the-appropriate-network-model"></a>適切なネットワーク モデルを選択する

**ベスト プラクティス ガイダンス** - 既存の仮想ネットワークまたはオンプレミスのネットワークと統合するには、AKS で Azure CNI ネットワークを使用します。 このネットワーク モデルでは、エンタープライズ環境でリソースとコントロールをさらに分離することもできます。

仮想ネットワークは、AKS ノードおよび顧客がアプリケーションにアクセスするための基本的な接続を提供します。 仮想ネットワークに AKS クラスターをデプロイするには、次の 2 つの異なる方法があります。

* **Kubenet ネットワーク** - クラスターがデプロイされ、そのクラスターで [kubenet][kubenet] Kubernetes プラグインが使用される場合、Azure によって仮想ネットワーク リソースが管理されます。
* **Azure CNI ネットワーク** - 既存の仮想ネットワークにデプロイし、[Azure Container Networking Interface (CNI)][cni-networking] Kubernetes プラグインを使用します。 ポッドは、他のネットワーク サービスまたはオンプレミス リソースにルーティングできる個々の IP を受け取ります。

Container Networking Interface (CNI) は、コンテナー ランタイムがネットワーク プロバイダーに要求を行うことを可能にする、ベンダーに依存しないプロトコルです。 Azure CNI はポッドとノードの IP アドレスを割り当てられるし、既存の Azure 仮想ネットワークに接続する際に IP アドレス管理 (IPAM) 機能を提供します。 各ノードおよびポッド リソースは、Azure 仮想ネットワーク内の IP アドレスを受け取ります。他のリソースまたはサービスと通信するのに追加のルーティングは必要ありません。

![各ブリッジが 1 つの Azure VNet に接続している 2 つのノードを示す図](media/operator-best-practices-network/advanced-networking-diagram.png)

ほとんどの運用環境デプロイでは、Azure CNI ネットワークを使用する必要があります。 このネットワーク モデルでは、コントロールの分離とリソースの管理が可能です。 セキュリティの観点から、多くの場合に、それらのリソースを異なるチームが管理し、セキュリティで保護することが望まれます。 Azure CNI ネットワークでは、各ポッドに割り当てられた IP アドレスを使用して、既存の Azure リソース、オンプレミスのリソース、またはその他のサービスに直接接続することができます。

Azure CNI ネットワークを使用する場合、仮想ネットワーク リソースは AKS クラスターとは別個のリソース グループに含まれます。 これらのリソースにアクセスして管理するためのアクセス許可を AKS サービス プリンシパルに委任します。 AKS クラスターで使用されるサービス プリンシパルには、少なくとも、ご利用の仮想ネットワーク内のサブネットに対する[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md#network-contributor)アクセス許可が必要です。 組み込みのネットワークの共同作成者ロールを使用する代わりに、[カスタム ロール](../role-based-access-control/custom-roles.md)を定義する場合は、次のアクセス許可が必要です。
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

AKS サービス プリンシパルへの委任の詳細については、[他の Azure リソースへのアクセスの委任][sp-delegation]に関するページを参照してください。

各ノードとポッドは独自の IP アドレスを受け取ると、AKS サブネットのアドレス範囲を計画します。 これらのサブネットは、デプロイするすべてのノード、ポッド、およびネットワーク リソースの IP アドレスを提供するのに十分な大きさである必要があります。 各 AKS クラスターは、その独自のサブネットに配置する必要があります。 Azure でオンプレミスまたはピアリングされたネットワークへの接続を許可するには、既存のネットワーク リソースと重複する IP アドレス範囲を使用しないようにします。 各ノードが kubenet ネットワークと Azure CNI ネットワークの両方で実行するポッドの数には、既定の制限があります。 スケール アップ イベントまたはクラスター アップグレードを処理するには、割り当てられたサブネットで使用できる追加の IP アドレスも必要となります。 Windows Server コンテナー (現在 AKS でプレビュー段階) を使用している場合、これらの追加のアドレス空間は特に重要です。これらのノード プールをアップグレードして最新のセキュリティ パッチを適用する必要があるためです。 Windows Server ノードの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関する記事を参照してください。

必要な IP アドレスを計算するには、[AKS での Azure CNI ネットワークの構成][advanced-networking]に関するページを参照してください。

### <a name="kubenet-networking"></a>Kubenet ネットワーク

kubenet では、クラスターをデプロイする前に仮想ネットワークを設定する必要はないものの、次のような欠点があります。

* ノードとポッドが異なる IP サブネット上に配置されます。 ユーザー定義ルーティング (UDR) と IP 転送を使用すると、ポッドとノードの間のトラフィックをルーティングできます。 この追加のルーティングにより、ネットワーク パフォーマンスが低下する場合があります。
* 既存のオンプレミスのネットワークへの接続または他の Azure 仮想ネットワークへのピアリングが複雑である可能性があります。

Kubenet は、AKS クラスターから仮想ネットワークとサブネットを個別に作成する必要がないので、小規模の開発またはテスト ワークロードに適しています。 トラフィックの少ないシンプルな Web サイト (ワークロードをコンテナーにリフト アンド シフトする) は、Kubenet ネットワークを使用してデプロイされた AKS クラスターのシンプルさからもメリットを得られます。 ほとんどの運用環境デプロイでは、Azure CNI ネットワークを計画して使用する必要があります。 [kubenet を使用して独自の IP アドレス範囲と仮想ネットワークを構成][aks-configure-kubenet-networking]することもできます。

## <a name="distribute-ingress-traffic"></a>イングレス トラフィックを分散する

**ベスト プラクティス ガイダンス** - アプリケーションに HTTP または HTTPS トラフィックを分散するには、イングレス リソースとコントローラーを使用します。 イングレス コントローラーは、通常の Azure ロード バランサー経由で追加機能を提供し、ネイティブの Kubernetes リソースとして管理できます。

Azure ロード バランサーは、AKS クラスター内のアプリケーションに顧客のトラフィックを分散できますが、そのトラフィックについて認識できる内容は制限されます。 ロード バランサー リソースはレイヤー 4 で動作し、プロトコルまたはポートに基づいてトラフィックを分散します。 HTTP または HTTPS を使用するほとんどの Web アプリケーションは、レイヤー 7 で動作する Kuberenetes イングレス リソースおよびコントローラーを使用する必要があります。 イングレスは、アプリケーションの URL に基づいてトラフィックを分散し、TLS/SSL ターミネーションを処理することができます。 また、この機能により、公開してマップする IP アドレスの数が減少します。 ロード バランサーでは、通常、各アプリケーションのパブリック IP アドレスが AKS クラスター内のサービスに割り当てられてマップされている必要があります。 イングレス リソースでは、単一の IP アドレスが複数のアプリケーションにトラフィックを分散できます。

![AKS クラスターでのイングレス トラフィック フローを示す図](media/operator-best-practices-network/aks-ingress.png)

 イングレスには、次の 2 つのコンポーネントがあります。

 * イングレス *リソース*
 * イングレス *コントローラー*

イングレス リソースは、AKS クラスターで実行されるサービスにトラフィックをルーティングするためのホスト、証明書、およびルールを定義する、`kind: Ingress` の YAML マニフェストです。 次の YAML マニフェストの例は、*myapp.com* のトラフィックを *blogservice* と *storeservice* の 2 つのサービスのどちらかに分散します。 顧客は、アクセスする URL に基づいてどちらかのサービスに転送されます。

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

イングレス コントローラーは、AKS ノードで実行され、着信要求を監視するデーモンです。 その後、トラフィックは、イングレス リソースで定義されたルールに基づいて分散されます。 最も一般的なイングレス コントローラーは、[NGINX] に基づいています。 AKS では特定のコントローラーに限定されないので、[Contour][contour], [HAProxy][haproxy]、[Traefik][traefik] などのその他のコントローラーを使用できます。

イングレス コントローラーは Linux ノード上でスケジュールする必要があります。 Windows Server ノード (現在は AKS でプレビュー段階) では、イングレス コントローラーを実行しないでください。 YAML マニフェストまたは Helm グラフのデプロイでノード セレクターを使用して、リソースが Linux ベースのノード上で実行されるように指示します。 詳細については、[ノード セレクターを使用して AKS でポッドをスケジュールする場所を制御する][concepts-node-selectors]方法に関する記事を参照してください。

イングレスには、次の操作方法に関するガイドを含め、さまざまなシナリオがあります。

* [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
* [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
* [ご自身の TLS 証明書を使用するイングレス コントローラーを作成する][aks-ingress-own-tls]
* Let's Encrypt を使用して[動的パブリック IP アドレス付き][aks-ingress-tls] or [with a static public IP address][aks-ingress-static-tls]の TLS 証明書を自動的に生成するイングレス コントローラーを作成する

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Web アプリケーション ファイアウォール (WAF) を使用してトラフィックをセキュリティで保護する

**ベスト プラクティス ガイダンス** - 着信トラフィックをスキャンして潜在的な攻撃を検出するには、[Barracuda WAF for Azure][barracuda-waf] や Azure Application Gateway などの Web アプリケーション ファイアウォール (WAF) を使用します。 また、より高度なこれらのネットワーク リソースは、単なる HTTP 接続や HTTPS 接続または基本的な SSL ターミネーション以外でもトラフィックをルーティングできます。

サービスとアプリケーションにトラフィックを分散するイングレス コントローラーは、通常は AKS クラスター内の Kubernetes リソースです。 コントローラーは AKS ノード上のデーモンとして実行され、CPU、メモリ、ネットワーク帯域幅など、ノードのリソースの一部を消費します。 より大規模な環境では、多くの場合に、このトラフィック ルーティングまたは TLS ターミネーションの一部を、AKS クラスターの外部のネットワーク リソースにオフロードする必要があります。 また、着信トラフィックをスキャンして潜在的な攻撃を検出する必要もあります。

![Azure App Gateway などの Web アプリケーション ファイアウォール (WAF) は、AKS クラスターのトラフィックを保護および分散することができます](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Web アプリケーション ファイアウォール (WAF) は、着信トラフィックをフィルター処理して追加のセキュリティ層を提供します。 Open Web Application Security Project (OWASP) は、クロス サイト スクリプティングや cookie ポイズニングなどの攻撃を監視する一連のルールを提供します。 [Azure Application Gateway][app-gateway] (現在 AKS でプレビューの段階です) は、トラフィックが AKS クラスターおよびアプリケーションに到達する前にこれらのセキュリティ機能を提供できるよう、AKS クラスターと統合可能な WAF です。 他のサード パーティ製ソリューションでもこれらの関数が実行されるので、特定の製品で既存の投資や専門知識を引き続き使用できます。

ロード バランサーまたはイングレス リソースは、AKS クラスターで持続的に実行されて、トラフィックの分散をさらにきめ細かく調整します。 App Gateway は、リソース定義でイングレス コントローラーとして一元的に管理できます。 最初に、[Application Gateway イングレス コントローラーを作成します][app-gateway-ingress]。

## <a name="control-traffic-flow-with-network-policies"></a>ネットワーク ポリシーを使用してトラフィック フローを制御する

**ベスト プラクティス ガイダンス** - ネットワーク ポリシーを使用して、ポッドへのトラフィックを許可または拒否します。 既定では、1 つのクラスター内でポッド間のすべてのトラフィックが許可されます。 セキュリティを強化するには、ポッドの通信を制限するルールを定義します。

ネットワーク ポリシーは、ポッド間のトラフィック フローを制御できる Kubernetes の機能です。 割り当てられたラベル、名前空間、トラフィック ポートなどの設定に基づいて、トラフィックを許可するか拒否するかを選択できます。 トラフィックのフローを制御するには、ネットワーク ポリシーを使用するのがクラウドネイティブな方法です。 ポッドは AKS クラスター内で動的に作成されるため、必要なネットワーク ポリシーを自動的に適用できます。 ポッド間のトラフィック制御には Azure ネットワーク セキュリティ グループを使用せず、ネットワーク ポリシーを使用してください。

ネットワーク ポリシーを使用するには、AKS クラスターを作成するときにこの機能を有効にする必要があります。 既存の AKS クラスターでネットワーク ポリシーを有効にすることはできません。 クラスター上でネットワーク ポリシーを有効にして、必要に応じて使用できるように、事前に計画してください。 ネットワーク ポリシーは、AKS の Linux ベースのノードとポッドに対してのみ使用する必要があります。

ネットワーク ポリシーは、YAML マニフェストを使用して Kubernetes リソースとして作成されます。 ポリシーが定義済みのポッドに適用され、次にイングレス ルールまたはエグレス ルールによってトラフィックのフロー方法が定義されます。 次の例では、*app: backend* ラベルが適用されたポッドにネットワーク ポリシーを適用します。 次に、イングレス ルールでは、*app: frontend* ラベルが適用されたポッドからのトラフィックのみを許可します。

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

ポリシーの概要については、「[SAzure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護][use-network-policies]」を参照してください。

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>踏み台ホストを介してノードに安全に接続する

**ベスト プラクティス ガイダンス** - AKS ノードへのリモート接続は公開しないでください。 管理仮想ネットワーク内に踏み台ホスト (jump box) を作成します。 踏み台ホストを使用すると、AKS クラスターへのトラフィックをリモート管理タスクに安全にルーティングできます。

AKS のほとんどの操作は、Azure 管理ツールを使用するか Kubernetes API サーバー経由で完了することができます。 AKS ノードは、パブリック インターネットには接続されず、プライベート ネットワークでのみ使用できます。 ノードに接続し、問題のメンテナンスまたはトラブルシューティングを実行するには、踏み台ホスト (jump box) を介して接続をルーティングします。 このホストは、AKS クラスター仮想ネットワークに安全にピアリングされている別個の管理仮想ネットワーク内に存在しなければなりません。

![踏み台ホスト (jump box) を使用して AKS ノードに接続する](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

踏み台ホストの管理ネットワークは、セキュリティで保護する必要もあります。 [Azure ExpressRoute][expressroute] or [VPN gateway][vpn-gateway] を使用してオンプレミスのネットワークに接続し、ネットワーク セキュリティ グループを使用してアクセスを制御します。

## <a name="next-steps"></a>次の手順

この記事では、ネットワーク接続およびセキュリティに焦点を当てました。 Kubernetes におけるネットワークの基礎の詳細については、「[Azure Kubernetes Service (AKS) でのアプリケーションに対するネットワークの概念][aks-concepts-network]」を参照してください。

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool