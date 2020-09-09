---
title: 概念 - Azure Red Hat on OpenShift 4 のネットワーク図
description: Azure Red Hat OpenShift ネットワークのネットワーク図と概要
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: fb81405e85d6e2653e0cf6c007c363493992161a
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419973"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>Azure Red Hat on OpenShift 4 のネットワーク

このガイドでは、Azure Red Hat on OpenShift 4 クラスターにおけるネットワークの概要と、重要なエンドポイントの図と一覧を示します。

主要な OpenShift ネットワークの概念の詳細については、[Azure Red Hat OpenShift 4 ネットワークのドキュメント](https://docs.openshift.com/aro/4/networking/understanding-networking.html)を参照してください。

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift でのネットワークの概念

![Azure Red Hat OpenShift 4 のネットワーク図](./media/concepts-networking/aro4-networking-diagram.png)

Azure Red Hat on OpenShift 4 をデプロイすると、クラスター全体が仮想ネットワーク内に配置されます。 この仮想ネットワーク内では、マスター ノードとワーカー ノードがそれぞれ独自のサブネット内に存在します。 各サブネットでは、パブリックおよび内部ロード バランサーが使用されます。

## <a name="explanation-of-endpoints"></a>エンドポイントの説明

次の一覧は、Azure Red Hat OpenShift クラスターにおける重要なエンドポイントをまとめたものです。

* **aro-pls**
    * これは、クラスターの管理を支援するために Microsoft および Red Hat のサイト信頼性エンジニアによって使用される Azure Private Link エンドポイントです。
* **aro-internal-lb**
    * このエンドポイントによって、API サーバーへのトラフィックが分散されます。 このロード バランサーでは、マスター ノードはバックエンド プールにあります。
* **aro-public-lb**
    * API がパブリックである場合、このエンドポイントによって、API サーバーへのトラフィックがルーティングおよび分散されます。 このエンドポイントは、マスターが Azure Resource Manager にアクセスしてクラスターの正常性についてのレポートを返せるように、パブリック発信 IP を割り当てます。
* **aro-internal**
    * このエンドポイントによって、内部サービス トラフィックが分散されます。 このロード バランサーでは、ワーカー ノードはバックエンド プールにあります。
    * このロード バランサーは、既定では作成されません。 このロード バランサーは、正しい注釈を持つ LoadBalancer 型のサービスを自分で作成すると、作成されます。 たとえば、service.beta.kubernetes.io/azure-load-balancer-internal: "true" などです。
* **ネットワーク ポリシー (イングレス)**
    * OpenShift SDN の一部としてサポートされます
    * 既定で有効になり、顧客によって適用されます
    * V1 NetworkPolicy に準拠していますが、"エグレスおよび IPBlock" タイプはまだサポートされていません
    * **aro**
    * このエンドポイントによって、API サーバーへのトラフィックが分散されます。 このロード バランサーでは、マスター ノードはバックエンド プールにあります。
  * **aro-internal-lb**
    * このエンドポイントは、すべてのパブリック トラフィックに使用されます。 アプリケーションとルートを作成すると、これがイングレス トラフィックのパスになります。
    * また、このロード バランサーでは、Azure Load Balancer のアウトバウンド規則を通じて、ワーカー ノードで実行されているすべてのポッドからのエグレス インターネット接続も処理されます。
        * 現在、アウトバウンド規則は構成できません。 それらでは、各ノードに 1,024 個の TCP ポートが割り当てられます。
        * DisableOutboundSnat は LB ルールでは構成されないため、ポッドはこの ALB で構成されている任意のパブリック IP をエグレス IP として取得できます。
        * 上の 2 点の結果、エフェメラル SNAT ポートを追加する唯一の方法は、パブリック LoadBalancer タイプのサービスを ARO に追加することです。
* **ネットワーク ポリシー (エグレス)**
    * エグレス ポリシーは、OpenShift のエグレス ファイアウォール機能を使用してサポートされます。
    * 名前空間またはプロジェクトごとに 1 つだけです。
    * エグレス ポリシーは、"既定の" 名前空間ではサポートされていません。
    * エグレス ポリシー規則は、順番に (最初から最後に向かって) 評価されます。
    * **aro-outbound-pip**
        * このエンドポイントは、ワーカー ノードのパブリック IP (PIP) として機能します。
        * このエンドポイントを使用すると、サービスが、Azure Red Hat OpenShift クラスターから受信した特定の IP を許可リストに追加できるようになります。
* **aro-node-nsg**
    * サービスを公開すると、API によってこのネットワーク セキュリティ グループに規則が作成され、トラフィックがノードを通過したりノードに到着したりできるようになります。
    * 既定では、このネットワーク セキュリティ グループで、すべてのアウトバウンド トラフィックが許可されます。 現時点では、アウトバウンド トラフィックは Azure Red Hat OpenShift コントロール プレーンのみに制限できます。
* **aro-controlplane-nsg**
    * このエンドポイントでは、マスター ノードのポート 6443 を通じて受信するトラフィックのみが許可されます。
* **Azure Container Registry**
    * これは、Microsoft によって内部的に提供および使用されるコンテナー レジストリです。
        * このレジストリは、ホスト プラットフォーム イメージとクラスター コンポーネントを提供します。 たとえば、コンテナーの監視やログ記録などです。
        * Azure Red Hat OpenShift のお客様による使用は想定されていません。  
        * 読み取り専用。
        * このレジストリへの接続は、サービス エンドポイントを介して行われます (Azure サービス間の内部接続)。
        * 既定では、この内部レジストリはクラスターの外部では使用できません。
* **Private Link**
    * クラスター管理のために、管理プレーンからクラスターへのネットワーク接続を許可します。
    * クラスターの管理を支援する、Microsoft および Red Hat のサイト信頼性エンジニア。

## <a name="networking-basics-in-openshift"></a>OpenShift におけるネットワークの基礎

OpenShift Software Defined Networking (SDN) は、Open vSwitch (OVS) を使用するオーバーレイ ネットワークを構成するために使用されます。OVS は、Container Network Interface (CNI) 仕様に基づく OpenFlow 実装です。 SDN ではさまざまなプラグインがサポートされており、ネットワーク ポリシーは Azure Red Hat on OpenShift 4 で使用されるプラグインです。 すべてのネットワーク通信は SDN によって管理されているため、ポッド間通信を実現するために、自分の仮想ネットワークにルートを追加する必要はありません。

## <a name="azure-red-hat-openshift-networking-specifics"></a>Azure Red Hat OpenShift のネットワークの仕様

以下の機能は、Azure Red Hat OpenShift に固有です。
* 持ち込み仮想ネットワークがサポートされています。
* ポッドとサービス ネットワーク CIDR は構成可能です。
* ノードとマスターが異なるサブネットにあります。
* ノードとマスターの仮想ネットワーク サブネットは、最小でも /27 にする必要があります。
* ポッド CIDR は、最小でも /18 のサイズにする必要があります (ポッド ネットワークはルーティング不可能な IP であり、OpenShift SDN 内でのみ使用されます)。
* 各ノードには、ポッド用に /23 サブネット (512 IP) が割り当てられています。 この値は変更できません。
* ポッドを複数のネットワークに接続することはできません。
* エグレス静的 IP を構成することはできません (これは OpenShift 機能です。 詳細については、[エグレス IP の構成](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html)に関するページを参照してください)。

## <a name="network-settings"></a>ネットワーク設定

Azure Red Hat OpenShift 4 では、以下のネットワーク設定を使用できます。

* **API の可視性** - [az aro create コマンド](tutorial-create-cluster.md#create-the-cluster)を実行するときの API の可視性を設定します。
    * "パブリック" - 外部ネットワークから API サーバーにアクセスできます。
    * "プライベート" - マスター サブネットのプライベート IP を割り当てられている API サーバーには、接続されているネットワーク (ピアリングされた仮想ネットワーク、クラスター内のその他のサブネット) を使用した場合にのみアクセスできます。 プライベート DNS ゾーンが自動的に作成されます。
* **イングレスの可視性** - [az aro create コマンド](tutorial-create-cluster.md#create-the-cluster)を実行するときの API の可視性を設定します。
    * "パブリック" ルートは、既定でパブリック Azure Standard Load Balancer になります (これは変更可能です)。
    * "プライベート" ルートは、既定で内部ロード バランサーになります (これは変更可能です)。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
ネットワーク セキュリティ グループは、ノードのリソース グループに作成されます。これは、ロックされています。 ネットワーク セキュリティ グループは、ノードの NIC ではなく、サブネットに直接割り当てられます。 ネットワーク セキュリティ グループは不変であるため、それらを変更するためのアクセス許可はありません。 

ただし、公開されている API サーバーでは、ネットワーク セキュリティ グループを作成して NIC に割り当てることはできません。

## <a name="domain-forwarding"></a>ドメイン転送
Azure Red Hat OpenShift では、CoreDNS が使用されます。 ドメイン転送は構成できます (詳細については、[DNS 転送の使用](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator)に関するドキュメントを参照してください)。

現時点では、独自の DNS を仮想ネットワークに持ち込むことはできません。


アウトバウンド トラフィックについてと、Azure Red Hat OpenShift でエグレス用に何がサポートされているかについては、[サポート ポリシー](support-policies-v4.md)のドキュメントを参照してください。
