---
title: 概念 - Azure Red Hat on OpenShift 4 のネットワーク図
description: Azure Red Hat OpenShift ネットワークのネットワーク図と概要
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: 5d69aacb6e3f25e3414aa446c4c5ae7852cabdfc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720904"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO) のネットワークの概念

このガイドでは、Azure Red Hat OpenShift on OpenShift 4 クラスターにおけるネットワークの概要と、重要なエンドポイントの図と一覧を示します。 主要な OpenShift ネットワークの概念の詳細については、[Azure Red Hat OpenShift 4 ネットワークのドキュメント](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html)を参照してください。

![Azure Red Hat OpenShift 4 のネットワーク図](./media/concepts-networking/aro4-networking-diagram.png)

Azure Red Hat on OpenShift 4 をデプロイすると、クラスター全体が仮想ネットワーク内に配置されます。 この仮想ネットワーク内では、マスター ノードとワーカー ノードがそれぞれ独自のサブネット内に存在します。 各サブネットには、内部ロード バランサーとパブリック ロード バランサーが使用されています。

## <a name="networking-components"></a>ネットワーク コンポーネント

次の一覧は、Azure Red Hat OpenShift クラスターにおける重要なネットワーク コンポーネントをまとめたものです。

* **aro-pls**
    * これは、クラスターを管理する目的で Microsoft および Red Hat のサイト信頼性エンジニアによって使用される Azure Private Link エンドポイントです。
* **aro-internal-lb**
    * このエンドポイントによって、API サーバーへのトラフィックが分散されます。 このロード バランサーでは、マスター ノードはバックエンド プールにあります。
* **aro-public-lb**
    * API がパブリックである場合、このエンドポイントによって、API サーバーへのトラフィックがルーティングおよび分散されます。 このエンドポイントは、マスターが Azure Resource Manager にアクセスしてクラスターの正常性についてのレポートを返せるように、パブリック発信 IP を割り当てます。
* **aro-internal**
    * このエンドポイントによって、内部サービス トラフィックが分散されます。 このロード バランサーでは、ワーカー ノードはバックエンド プールにあります。
    * このロード バランサーは、既定では作成されません。 このロード バランサーは、正しい注釈を持つ LoadBalancer 型のサービスを自分で作成すると、作成されます。 たとえば、service.beta.kubernetes.io/azure-load-balancer-internal: "true" などです。
* **aro-internal-lb**
    * このエンドポイントは、すべてのパブリック トラフィックに使用されます。 アプリケーションとルートを作成すると、これがイングレス トラフィックのパスになります。
    * また、このロード バランサーでは、Azure Load Balancer のアウトバウンド規則を通じて、ワーカー ノードで実行されているすべてのポッドからのエグレス インターネット接続も処理されます。
        * 現在、アウトバウンド規則は構成できません。 それらでは、各ノードに 1,024 個の TCP ポートが割り当てられます。
        * DisableOutboundSnat は LB ルールでは構成されないため、ポッドはこの ALB で構成されている任意のパブリック IP をエグレス IP として取得できます。
        * 上の 2 点の結果、エフェメラル SNAT ポートを追加する唯一の方法は、パブリック LoadBalancer タイプのサービスを ARO に追加することです。
* **aro-outbound-pip**
    * このエンドポイントは、ワーカー ノードのパブリック IP (PIP) として機能します。
    * このエンドポイントを使用すると、サービスが、Azure Red Hat OpenShift クラスターから受信した特定の IP を許可リストに追加できるようになります。
* **aro-nsg**
    * サービスを公開すると、API によってこのネットワーク セキュリティ グループに規則が作成され、トラフィックがコントロール プレーンやノードを通過したりコントロール プレーンやノードに到着したりできるようになります。
    * 既定では、このネットワーク セキュリティ グループで、すべてのアウトバウンド トラフィックが許可されます。 現時点では、アウトバウンド トラフィックは Azure Red Hat OpenShift コントロール プレーンのみに制限できます。
* **aro-controlplane-nsg**
  * このエンドポイントでは、マスター ノードのポート 6443 を通じて受信するトラフィックのみが許可されます。
* **Azure Container Registry**
    * これは、Microsoft によって内部的に提供および使用されるコンテナー レジストリです。 このレジストリは読み取り専用であり、Azure Red Hat OpenShift ユーザーによる使用は想定されていません。
        * このレジストリは、ホスト プラットフォーム イメージとクラスター コンポーネントを提供します。 たとえば、コンテナーの監視やログ記録などです。
        * このレジストリへの接続は、サービス エンドポイントを介して行われます (Azure サービス間の内部接続)。
        * 既定では、この内部レジストリはクラスターの外部では使用できません。
* **Private Link**
    * 管理プレーンからクラスターへのネットワーク接続を可能にし、クラスターを管理する Microsoft と Red Hat のサイト信頼性エンジニアを支援します。

## <a name="networking-policies"></a>ネットワーク ポリシー

* **イングレス**: イングレス ネットワーク ポリシーは、[OpenShift SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html) の一部としてサポートされます。 このネットワーク ポリシーは既定で有効になっており、ユーザーによって実施されます。 イングレス ネットワーク ポリシーは V1 NetworkPolicy に準拠しますが、エグレスおよび IPBlock タイプはサポートされません。

* **エグレス**: エグレス ネットワーク ポリシーは、OpenShift の [エグレス ファイアウォール](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html)機能を使用してサポートされます。 エグレス ポリシーは、名前空間またはプロジェクトごとに 1 つだけ存在します。 エグレス ポリシーは、"既定" の名前空間ではサポートされず、順番に (最初から最後に向かって) 評価されます。

## <a name="networking-basics-in-openshift"></a>OpenShift におけるネットワークの基礎

OpenShift Software Defined Networking [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) は、Open vSwitch [(OVS)](https://www.openvswitch.org/) を使用するオーバーレイ ネットワークを構成するために使用されます。OVS は、Container Network Interface (CNI) 仕様に基づく OpenFlow 実装です。 SDN ではさまざまなプラグインがサポートされており、ネットワーク ポリシーは Azure Red Hat on OpenShift 4 で使用されるプラグインです。 すべてのネットワーク通信は SDN によって管理されているため、ポッド間通信を実現するために、自分の仮想ネットワークにルートを追加する必要はありません。

## <a name="networking--for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift のネットワーク

以下のネットワーク機能は、Azure Red Hat OpenShift に固有です。  
* ユーザーは、その ARO クラスターを既存の仮想ネットワークに作成するか、ARO クラスターの作成時に仮想ネットワークを作成することができます。
* ポッドとサービス ネットワーク CIDR は構成可能です。
* ノードとマスターが異なるサブネットにあります。
* ノードとマスターの仮想ネットワーク サブネットは、最小でも /27 にする必要があります。
* 既定のポッド CIDR は、10.128.0.0/14 です。
* 既定のサービス CIDR は、172.30.0.0/16 です。
* ポッドおよびサービスのネットワーク CIDR は、ネットワークで使用されている他のアドアレス範囲と重複しないようにする必要があります。また、クラスターの仮想ネットワーク IP アドレス範囲に含まれないようにする必要もあります。
* ポッド CIDR は、最小でも /18 のサイズにする必要があります (ポッド ネットワークはルーティング不可能な IP であり、OpenShift SDN 内でのみ使用されます)。
* 各ノードには、ポッド用に /23 サブネット (512 IP) が割り当てられています。 この値は変更できません。
* ポッドを複数のネットワークに接続することはできません。
* エグレス静的 IP を構成することはできません (これは OpenShift 機能です。 詳細については、[エグレス IP の構成](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html)に関するページを参照してください)。

## <a name="network-settings"></a>ネットワーク設定

Azure Red Hat OpenShift 4 クラスターでは、以下のネットワーク設定を使用できます。

* **API の可視性** - [az aro create コマンド](tutorial-create-cluster.md#create-the-cluster)を実行するときの API の可視性を設定します。
    * "パブリック" - 外部ネットワークから API サーバーにアクセスできます。
    * "プライベート" - マスター サブネットのプライベート IP を割り当てられている API サーバーには、接続されているネットワーク (ピアリングされた仮想ネットワーク、クラスター内のその他のサブネット) を使用した場合にのみアクセスできます。 プライベート DNS ゾーンが自動的に作成されます。
* **イングレスの可視性** - [az aro create コマンド](tutorial-create-cluster.md#create-the-cluster)を実行するときの API の可視性を設定します。
    * "パブリック" ルートは、既定でパブリック Standard Load Balancer になります (これは変更可能です)。
    * "プライベート" ルートは、既定で内部ロード バランサーになります (これは変更可能です)。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
ネットワーク セキュリティ グループは、ノードのリソース グループに作成されます。これは、ユーザーに対してはロックされています。 ネットワーク セキュリティ グループは、ノードの NIC ではなく、サブネットに直接割り当てられます。 ネットワーク セキュリティ グループは不変であるため、ユーザーにはそれらを変更するためのアクセス許可はありません。

公開されている API サーバーでは、ネットワーク セキュリティ グループを作成して NIC に割り当てることはできません。

## <a name="domain-forwarding"></a>ドメイン転送
Azure Red Hat OpenShift では、CoreDNS が使用されます。 ドメイン転送を構成できます。 独自の DNS を仮想ネットワークに持ち込むことはできません。 詳細については、[DNS 転送の使用](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator)に関するドキュメントを参照してください。

## <a name="whats-new-in-openshift-45"></a>OpenShift 4.5 の最新情報

OpenShift 4.5 のサポートに伴い、Azure Red Hat OpenShift のアーキテクチャに大幅な変更がいくつか加えられました。 これらの変更は、OpenShift 4.5 が実行されている、新しく作成されたクラスターにのみ適用されます。 OpenShift 4.5 にアップグレードされた既存のクラスターでは、そのネットワーク アーキテクチャがアップグレード プロセスによって変更されることはありません。 新しいアーキテクチャを使用するためには、ユーザーがそのクラスターを再作成する必要があります。

![Azure Red Hat OpenShift 4.5 のネットワーク図](./media/concepts-networking/aro-4-5-networking-diagram.png)

上の図を見るとわかるように、いくつかの変更点が見られます。
* 従来、ARO では、API サーバー用とワーカー ノード プール用に 2 つのパブリック ロードバランサーが使用されていました。 このアーキテクチャ更新により、これが 1 つのロードバランサーに統合されました。 
* 複雑さを軽減するために、専用の送信 IP アドレス リソースが排除されました。
* ARO のコントロール プレーンが、ARO ワーカー ノードと同じネットワーク セキュリティ グループを共有するようになりました。

OpenShift 4.5 の詳細については、[OpenShift 4.5 のリリース ノート](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html)を参照してください。

## <a name="next-steps"></a>次の手順
アウトバウンド トラフィックについてと、Azure Red Hat OpenShift でエグレス用に何がサポートされているかについては、[サポート ポリシー](support-policies-v4.md)のドキュメントを参照してください。
