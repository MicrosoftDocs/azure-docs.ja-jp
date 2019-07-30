---
title: 概念 - Azure Kubernetes サービス (AKS) におけるネットワーク
description: Azure Kubernetes Service (AKS) におけるネットワークについて説明します。kubenet と Azure CNI ネットワーク、イングレス コントローラー、ロード バランサー、静的 IP アドレスの説明が含まれます。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: 459c11448280b63bafdfd54c13a6cad5983ef1b5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615893"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>チュートリアル: Azure Kubernetes Service (AKS) でのアプリケーションに対するネットワークの概念

アプリケーション開発に対するコンテナー ベースのマイクロサービス アプローチでは、アプリケーション コンポーネントは、連携して各自のタスクを処理する必要があります。 Kubernetes には、このアプリケーションの通信を可能にするさまざまなリソースが提供されています。 アプリケーションへの接続と内部または外部への公開を実行できます。 高可用性アプリケーションをビルドするために、アプリケーションの負荷を分散させることができます。 複雑なアプリケーションでは、SSL/TLS 終端または複数のコンポーネントのルーティングのためのイングレス トラフィックの構成が必要な場合があります。 セキュリティ上の理由で、ポッドとノードに対するネットワーク トラフィックまたはポッドとノード間でのネットワーク トラフィックのフロー制限が必要な場合もあります。

この記事では、AKS 内でアプリケーションにネットワークを提供する主要な概念について説明します。

- [サービス](#services)
- [Azure 仮想ネットワーク](#azure-virtual-networks)
- [イングレス コントローラー](#ingress-controllers)
- [ネットワーク ポリシー](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes の基本

アプリケーションへのアクセスまたはアプリケーション コンポーネントの相互通信を可能にするため、Kubernetes には、仮想ネットワークに対する抽象化レイヤーが提供されています。 仮想ネットワークに接続された Kubernetes ノードは、ポッドに対して受信接続と送信接続を実行できます。 これらのネットワーク機能を提供するために、各ノードで *kube-proxy* コンポーネントが実行されます。

Kubernetes では、特定のポートで IP アドレスまたは DNS 名を使用した直接アクセスを許可するように、"*サービス*" によってポッドが論理的にグループ化されます。 "*ロード バランサー*" を使用してトラフィックを分散させることもできます。 "*イングレス コントローラー*" を使用して、アプリケーションのトラフィックの複雑なルーティングも実現できます。 ポッドに対するネットワーク トラフィックのセキュリティ保護とフィルター処理は、Kubernetes の "*ネットワーク ポリシー*" (AKS ではプレビュー段階) によって実行できます。

Azure プラットフォームは、AKS クラスターの仮想ネットワークを簡略化するためにも役立ちます。 Kubernetes ロード バランサーを作成すると、基になる Azure ロード バランサーのリソースが作成されて構成されます。 ポッドへのネットワーク ポートを開くと、対応する Azure ネットワーク セキュリティ グループ規則が構成されます。 HTTP アプリケーション ルーティングでは、新しいイングレス ルートが構成されると、Azure によって "*外部 DNS*" も構成されます。

## <a name="services"></a>サービス

アプリケーション ワークロードのネットワーク構成を簡素化するため、Kubernetes では、"*サービス*" を使用して、一連のポッドを論理的にグループ化してネットワーク接続を行います。 次の種類のサービスを使用できます。

- **クラスター IP** - AKS クラスター内で使用する内部 IP アドレスを作成します。 クラスター内で他のワークロードをサポートする内部専用アプリケーションに適しています。

    ![AKS クラスター内のクラスター IP トラフィック フローを示す図][aks-clusterip]

- **NodePort** - ノード IP アドレスとポートによるアプリケーションへの直接アクセスを許可する、基になるノード上にポート マッピングを作成します。

    ![AKS クラスターでの NodePort トラフィック フローを示す図][aks-nodeport]

- **LoadBalancer** - Azure ロード バランサー リソースを作成し、外部 IP アドレスを構成し、要求されたポッドをロード バランサーのバックエンド プールに接続します。 顧客のトラフィックによるアプリケーションへのアクセスを許可するために、目的のポート上に負荷分散規則が作成されます。 

    ![AKS クラスター内の負荷分散トラフィック フローを示す図][aks-loadbalancer]

    制御と受信トラフィックのルーティングを追加するため、[イングレス コントローラー](#ingress-controllers)を代わりに使用できます。

- **ExternalName** - 特定の DNS エントリを作成して、アプリケーションに簡単にアクセスできるようにします。

ロード バランサーとサービスの IP アドレスは動的に割り当てることができます。または、使用する既存の静的 IP アドレスを指定できます。 内部と外部の静的 IP アドレスの両方を割り当てることができます。 この既存の静的 IP アドレスは、多くの場合、DNS エントリに関連付けられています。

"*内部*" ロード バランサーと "*外部*" ロード バランサーの両方を作成できます。 内部ロード バランサーにはプライベート IP アドレスのみが割り当てられるため、インターネットからアクセスすることはできません。

## <a name="azure-virtual-networks"></a>Azure 仮想ネットワーク

AKS では、次の 2 つのネットワーク モデルのいずれかを使用するクラスターをデプロイできます。

- *Kubenet* ネットワーク - AKS クラスターのデプロイ時に、通常はネットワーク リソースが作成され、構成されます。
- *Azure Container Networking Interface (CNI)* ネットワーク - AKS クラスターは、既存の仮想ネットワーク リソースと構成に接続されます。

### <a name="kubenet-basic-networking"></a>Kubenet (基本) ネットワーク

*kubenet* ネットワーク オプションは、AKS クラスターを作成するための既定の構成です。 *kubenet* を使用して、ノードでは Azure 仮想ネットワーク サブネットから IP アドレスを取得します。 ポッドでは、ノードの Azure 仮想ネットワーク サブネットとは論理的に異なるアドレス空間から IP アドレスを受け取ります。 その後、ポッドで Azure 仮想ネットワークのリソースに到達できるように、ネットワーク アドレス変換 (NAT) が構成されます。 トラフィックの発信元 IP アドレスは、ノードのプライマリ IP アドレスに NAT 変換されます。

ノードでは、[kubenet][kubenet] Kubernetes プラグインを使用します。 Azure プラットフォームで仮想ネットワークを作成および構成させることができます。または、既存の仮想ネットワーク サブネットに AKS クラスターをデプロイするように選択できます。 ここでも、ルーティング可能な IP アドレスを受信するのはノードのみであり、ポッドでは NAT を使用して、AKS クラスター外の他のリソースと通信します。 この方法では、ポッド用にネットワーク空間で予約する必要がある IP アドレスの数が大幅に減ります。

詳細については、[AKS クラスター用の kubenet ネットワークの構成][aks-configure-kubenet-networking]に関するページを参照してください。

### <a name="azure-cni-advanced-networking"></a>Azure CNI (高度) ネットワーク

Azure CNI を使用して、すべてのポッドでサブネットから IP アドレスを取得します。ポッドには直接アクセスできます。 これらの IP アドレスは、ネットワーク空間全体で一意である必要があり、事前に計画する必要があります。 各ノードには、サポートされるポッドの最大数に対する構成パラメーターがあります。 ノードごとにそれと同じ数の IP アドレスが、そのノードに対して事前に予約されます。 この方法では詳細な計画が必要であり、そうでない場合、IP アドレスが不足するか、アプリケーション需要の拡大に伴い、より大きなサブネットでのクラスターの再構築が必要になる可能性があります。

ノードでは [Azure Container Networking Interface (CNI)][cni-networking] Kubernetes プラグインが使用されます。

![各ブリッジが 1 つの Azure VNet に接続している 2 つのノードを示す図][advanced-networking-diagram]

詳細については、[AKS クラスター用の Azure CNI の構成][aks-configure-advanced-networking]に関するページを参照してください。

### <a name="compare-network-models"></a>ネットワーク モデルを比較する

kubenet と Azure CNIは、両方とも AKS クラスターのネットワーク接続を提供します。 ただし、それぞれに長所と短所があります。 大まかに言えば、次の考慮事項が適用されます。

* **kubenet**
    * IP アドレス空間を節約します。
    * クラスターの外部からのポッドに到達するには、Kubernetes の内部または外部ロード バランサーを使用します。
    * ユーザー定義のルート (UDR) は、手動で管理および保守する必要があります。
    * クラスターあたり最大 400 ノード。
* **Azure CNI**
    * ポッドは完全な仮想ネットワーク接続を取得するため、クラスターの外部から直接アクセスできます。
    * より多くの IP アドレス空間を必要とします。

kubenet と Azure CNI には、次のような動作の違いが存在します。

| 機能                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| 既存または新規の仮想ネットワークにクラスターをデプロイする                                            | サポートされています - UDR は手動で適用されます | サポートされています |
| ポッド間接続                                                                         | サポートされています | サポートされています |
| ポッドと VM の接続。同一仮想ネットワーク内の VM                                          | ポッドによって開始されたときに動作します | 両方の方法で動作します |
| ポッドと VM の接続。ピアリングされた仮想ネットワーク内の VM                                            | ポッドによって開始されたときに動作します | 両方の方法で動作します |
| VPN または Express Route を使用したオンプレミスへのアクセス                                                | ポッドによって開始されたときに動作します | 両方の方法で動作します |
| サービス エンドポイントによって保護されているリソースへのアクセス                                             | サポートされています | サポートされています |
| ロード バランサー サービス、App Gateway、またはイングレス コントローラーを使用して、Kubernetes サービスを公開する | サポートされています | サポートされています |
| 既定の Azure DNS およびプライベート ゾーン                                                          | サポートされています | サポートされています |

### <a name="support-scope-between-network-models"></a>ネットワーク モデル間のサポート範囲

kubenet と Azure CNI は、両方とも、使用するネットワーク モデルに関係なく次のいずれかの方法でデプロイすることができます。

* Azure プラットフォームは、AKS クラスターを作成したときに自動的に仮想ネットワーク リソースを作成して構成することができます。
* 仮想ネットワーク リソースを手動で作成して構成し、AKS クラスターを作成するときにそれらのリソースにアタッチすることができます。

サービス エンドポイントや UDR のような機能は kubenet と Azure CNI の両方でサポートされていますが、[AKS のサポート ポリシー][support-policies]は、どのような変更を行うことができるかを定義します。 例:

* AKS クラスターの仮想ネットワーク リソースを手動で作成する場合は、独自の UDR またはサービス エンドポイントを構成するときにサポートされます。
* Azure プラットフォームが AKS クラスター用の仮想ネットワーク リソースを自動的に作成する場合、それらの AKS 管理対象リソースを手動で変更して独自の UDR またはサービスエンドポイントを構成することはサポートされていません。

## <a name="ingress-controllers"></a>イングレス コントローラー

LoadBalancer 型のサービスを作成すると、基になる Azure ロード バランサー リソースが作成されます。 ロード バランサーは、特定のポートでサービス内のポッドにトラフィックを分散するように構成されます。 LoadBalancer はレイヤー 4 でのみ動作します。サービスは実際のアプリケーションを認識せず、追加のルーティングを考慮することはできません。

"*イングレス コントローラー*" はレイヤー 7 で動作し、インテリジェントなルールを使用してアプリケーションのトラフィックを分散させることができます。 イングレス コントローラーの一般的な用途は、受信 URL に基づいて別のアプリケーションに HTTP トラフィックをルーティングすることです。

![AKS クラスターでのイングレス トラフィック フローを示す図][aks-ingress]

AKS では、NGINX などを使用してイングレス リソースを作成するか、AKS の HTTP アプリケーションのルーティング機能を使用できます。 AKS クラスターで HTTP アプリケーションのルーティングを有効にすると、Azure プラットフォームがイングレス コントローラーと*External-DNS* コントローラーを作成します。 新しいイングレス リソースが Kubernetes に作成されると、必要な DNS A レコードがクラスター固有のDNS ゾーンに作成されます。 詳細については、「[HTTP アプリケーション ルーティング][aks-http-routing]」を参照してください。

イングレスのもう 1 つの一般的な機能は、SSL/TLS 終端です。 HTTPS 経由でアクセスされる大規模な Web アプリケーションでは、アプリケーション自体の中ではなく、イングレス リソースによって TLS 終端を処理できます。 TLS 証明書の自動生成と構成を提供することで、Let's Encrypt などのプロバイダーを使用するイングレス リソースを構成できます。 Let's Encrypt を使用した NGINX イングレス コントローラーの構成の詳細については、[イングレスと TLS][aks-ingress-tls] に関する記事を参照してください。

イングレス コントローラーを構成して、クライアント ソース IP を AKS クラスター内のコンテナーへの要求上で保持することもできます。 クライアントの要求が、イングレス コントローラー経由で AKS クラスター内のコントローラーにルーティングされている場合、その要求の元のソース IP は、ターゲット コンテナーに対しては利用できません。 *クライアント ソース IP の保持*を有効にすると、クライアントに対するソース IP は、*X-Forwarded-For* 下にある要求ヘッダー内で利用できます。 クライアント ソース IP の保持機能をイングレス コントローラー上で使用している場合は、SSL パススルーを使用することはできません。 クライアント ソース IP の保持と SSL パススルーは、*LoadBalancer* 型など、他のサービスによって使用できます。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループは、AKS ノードなどの VM のトラフィックをフィルター処理します。 LoadBalancer などのサービスを作成すると、必要なネットワーク セキュリティ グループ規則が Azure プラットフォームによって自動的に構成されます。 AKS クラスター内のポッドに対するトラフィックをフィルター処理するネットワーク セキュリティ グループ規則は手動で構成しないでください。 Kubernetes サービス マニフェストの一部として必要なポートと転送を定義し、適切なルールの作成または更新は Azure プラットフォームに任せてください。 次のセクションで説明するように、ネットワーク ポリシーを使用し、ポッドにトラフィックのフィルター規則を自動的に適用することもできます。

## <a name="network-policies"></a>ネットワーク ポリシー

既定では、AKS クラスター内のすべてのポッドは制限なしにトラフィックを送受信できます。 セキュリティ向上のために、トラフィック フローを制御する規則を定義することをお勧めします。 バックエンド アプリケーションは、多くの場合、必要とされるフロントエンド サービスにのみ公開され、データベース コンポーネントにアクセスできるのは、それらに接続するアプリケーション層だけです。

ネットワーク ポリシーは、ポッド間のトラフィック フローを制御できる、AKS で使用可能な Kubernetes の機能です。 割り当てられたラベル、名前空間、トラフィック ポートなどの設定に基づいて、トラフィックを許可するか拒否するかを選択できます。 ネットワーク セキュリティ グループは、ポッドではなく、AKS ノードに向いています。 トラフィックのフローを制御するためには、ネットワーク ポリシーの使用の方がより適切で、クラウド ネイティブな方法です。 ポッドは AKS クラスター内で動的に作成されるため、必要なネットワーク ポリシーを自動的に適用できます。

詳細については、「[Azure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護][use-network-policies]」を参照してください。

## <a name="next-steps"></a>次の手順

AKS ネットワークの使用を開始するために、[kubenet][aks-configure-kubenet-networking] or [Azure CNI][aks-configure-advanced-networking] を使用して、独自の IP アドレス範囲で AKS クラスターを作成および構成します。

関連付けられているベスト プラクティスについては、[AKS でのネットワーク接続とセキュリティに関するベスト プラクティス][operator-best-practices-network]に関するページを参照してください。

Kubernetes と AKS の中心概念の追加情報については、次の記事を参照してください。

- [Kubernetes/AKS クラスターとワークロード][aks-concepts-clusters-workloads]
- [Kubernetes/AKS のアクセスと ID][aks-concepts-identity]
- [Kubernetes/AKS のセキュリティ][aks-concepts-security]
- [Kubernetes/AKS のストレージ][aks-concepts-storage]
- [Kubernetes/AKS のスケール][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
