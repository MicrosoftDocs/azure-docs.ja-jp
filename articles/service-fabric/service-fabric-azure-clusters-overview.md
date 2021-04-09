---
title: Windows Server および Linux 上でクラスターを作成する
description: Service Fabric クラスターは、Windows Server および Linux で実行します。 Windows Server または Linux を実行できる場所であればどこでも Service Fabric アプリケーションをデプロイしてホストすることができます。
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: bbfdc0a30aa673e8602ec9233fde4236c99ef5aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97882213"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Azure 上での Service Fabric クラスターの概要
Service Fabric クラスターは、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターに属しているコンピューターまたは VM をクラスター ノードといいます。 クラスターは多数のノードにスケールできます。 新しいノードがクラスターに追加されると、Service Fabric は、増加したノード数全体で、サービスのパーティションのレプリカとインスタンスのバランスを再調整します。 アプリケーション全体のパフォーマンスが向上し、メモリへのアクセスの競合が減少します。 クラスター内のノードが効率的に使用されていない場合、クラスター内のノードの数を削減できます。 Service Fabric は、各ノードのハードウェアを効率的に利用できるように、減らされたノード数全体で、再度パーティションのレプリカとインスタンスのバランスを再調整します。

ノード タイプは、クラスターの一連のノード (仮想マシン) のサイズ、数、およびプロパティを定義します。 各ノードの種類は、個別にスケール アップまたはスケール ダウンすることができ、さまざまなセットのポートを開き、異なる容量のメトリックスを持つことができます。 ノード タイプを使用して、一連の "フロントエンド" または "バックエンド" などのクラスター ノードの役割を定義します。 クラスターには複数のノード タイプを指定できますが、運用環境クラスターの場合、プライマリ ノード タイプには少なくとも 5 つの VM が必要です (テスト環境のクラスターの場合は 3 つ以上)。 [Service Fabric のシステム サービス](service-fabric-technical-overview.md#system-services)は、プライマリ ノード タイプのノードに配置されます。 

## <a name="cluster-components-and-resources"></a>クラスター コンポーネントとリソース
Azure 上の Service Fabric クラスターは、次の他の Azure リソースを使用し、それらのリソースと対話する Azure リソースです。
* VM と仮想ネットワーク カード
* 仮想マシン スケール セット
* 仮想ネットワーク
* ロード バランサー
* ストレージ アカウント
* パブリック IP アドレス

![Service Fabric クラスター][Image]

### <a name="virtual-machine"></a>仮想マシン
クラスターの一部である[仮想マシン](../virtual-machines/index.yml)はノードと呼ばれます。ただし、技術的には、クラスター ノードは Service Fabric ランタイム プロセスです。 それぞれのノードには、ノード名 (文字列) が割り当てられます。 ノードには、[配置プロパティ](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)などの特性があります。 各コンピューターまたは VM には、自動的に開始されるサービス *FabricHost.exe* が存在します。このサービスは、ブート時に開始され、その後、ノードを構成する *Fabric.exe* と *FabricGateway.exe* の 2 つの実行可能ファイルを起動します。 運用環境デプロイは、物理マシンまたは仮想マシンあたり 1 ノードです。 テストのシナリオでは、*Fabric.exe* と *FabricGateway.exe* の複数のインスタンスを実行することによって、1 台のコンピューターまたは VM で複数のノードをホストできます。

各 VM は仮想ネットワーク インターフェイス カード (NIC) に関連付けられ、各 NIC にはプライベート IP アドレスが割り当てられます。  VM は、NIC を介して、仮想ネットワークおよびローカル バランサーに割り当てられます。

クラスター内のすべての VM は、仮想ネットワークに配置されます。  同じノード タイプ/スケール セットのすべてのノードは、仮想ネットワークの同じサブネットに配置されます。  これらのノードは、プライベート IP アドレスのみ持ち、仮想ネットワークの外部で直接アドレス指定できません。  クライアントは、Azure Load Balancer を介してノード上のサービスにアクセスできます。

### <a name="scale-setnode-type"></a>スケール セット/ノード タイプ
クラスターを作成する場合、ノード タイプを 1 つ以上定義します。  1 つのノード タイプのノード、つまり VM は、サイズが同じです。また、CPU の数、メモリー、ディスクの数、ディスク I/O などの特性も同じです。  たとえば、1 つのノード タイプを、インターネットに対してポートを開いた小規模なフロントエンド VM 用にし、別のノード タイプを、データを処理する大規模なバックエンド VM 用にすることができます。 Azure クラスターでは、ノード タイプはそれぞれ 1 つの[仮想マシン スケール セット](../virtual-machine-scale-sets/index.yml)に対応付けられます。

スケール セットを使用すると、仮想マシンのコレクションをセットとしてデプロイおよび管理できます。 Azure Service Fabric クラスターで定義するノード タイプごとに別個のスケール セットが設定されます。 Service Fabric ランタイムは、Azure VM 拡張機能を使用して、スケール セットの各仮想マシンにブートストラップされます。 各ノードの種類を個別にスケールアップまたはスケールダウンしたり、各クラスター ノードで実行されている OS SKU を変更したり、異なるポートのセットを開いたり、別の容量メトリックを使用したりできます。 1 つのスケールセットには、5 つの[アップグレード ドメイン](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains)と 5 つの[障害ドメイン](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains)があり、最大で 100 の VM を使用できます。  100 を超えるノードのクラスターを作成するには、複数のスケール セット/ノード タイプを作成します。

> [!IMPORTANT]
> クラスターのノード タイプの数、および各ノード タイプのプロパティ (サイズ、プライマリ、インターネット接続、VM の数など) を選択することが重要です。  詳細については、「[クラスターの容量計画に関する考慮事項](service-fabric-cluster-capacity.md)」を参照してください。

詳細については、[Service Fabric のノード タイプと仮想マシン スケール セット](service-fabric-cluster-nodetypes.md)に関するページを参照してください。

### <a name="azure-load-balancer"></a>Azure Load Balancer
VM インスタンスは、[パブリック IP アドレス](../virtual-network/public-ip-addresses.md)と DNS ラベルが関連付けられている [Azure Load Balancer](../load-balancer/load-balancer-overview.md) の背後で結合されます。  *&lt;clustername&gt;* でクラスターをプロビジョニングする場合、DNS 名 *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com* が、スケール セットの前のロード バランサーに関連付けられた DNS ラベルになります。

クラスター内の VM には[プライベート IP アドレス](../virtual-network/private-ip-addresses.md)しかありません。  管理トラフィックとサービス トラフィックは、パブリック ロード バランサーを介してルーティングされます。  ネットワーク トラフィックは、NAT 規則 (クライアントは特定のノード/インスタンスに接続します)、または負荷分散規則 (トラフィックは VM ラウンド ロビンに送られます) を介してこれらのコンピューターにルーティングされます。  ロード バランサーには、 *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com* という形式の DNS 名を持つパブリック IP が関連付けられています。  パブリック IP は、リソース グループ内の別の Azure リソースです。  クラスターで複数のノード タイプを定義する場合、ノード タイプ/スケール セットごとにロード バランサーが作成されます。 または、複数のノード タイプに対して 1 つのロード バランサーをセットアップできます。  プライマリ ノード タイプには、DNS ラベル *&lt;clustername&gt;.&lt;location&gt;.cloudapp.azure.com* があり、他のノード タイプには、DNS ラベル *&lt;clustername&gt;-&lt;nodetype&gt;.&lt;location&gt;.cloudapp.azure.com* があります。

### <a name="storage-accounts"></a>ストレージ アカウント
各クラスター ノード タイプが、[Azure Storage アカウント](../storage/common/storage-introduction.md)およびマネージド ディスクによってサポートされています。

## <a name="cluster-security"></a>クラスターのセキュリティ
Azure Service Fabric クラスターは、ユーザーが所有するリソースの 1 つです。  承認されていないユーザーが接続できないように、クラスターをセキュリティで保護する必要があります。 クラスターで実稼働ワークロードを実行している場合、セキュリティで保護されたクラスターは特に重要です。 

### <a name="node-to-node-security"></a>ノード間のセキュリティ
ノード間のセキュリティにより、クラスター内の VM やコンピューター間の通信が保護されます。 このセキュリティ シナリオでは、クラスターへの参加が許可されているコンピューターのみが、クラスター内でホストされているアプリケーションとサービスに参加できます。 Service Fabric では、X.509 証明書を使用してクラスターをセキュリティで保護し、アプリケーションのセキュリティ機能を提供します。  クラスター証明書は、クラスター トラフィックをセキュリティで保護するため、およびクラスターとサーバーの認証を提供するために必要です。  テスト クラスターでは自己署名証明書を使用できますが、運用環境クラスターをセキュリティで保護するには、信頼された証明機関からの証明書を使用する必要があります。

詳細については、「[ノード間のセキュリティ](service-fabric-cluster-security.md#node-to-node-security)」を参照してください。

### <a name="client-to-node-security"></a>クライアントとノードの間のセキュリティ
クライアントとノードの間のセキュリティでは、クライアントの認証を行い、クラスター内のクライアントと個々のノードの間の通信をセキュリティで保護できます。 この種類のセキュリティでは、権限のあるユーザーのみが、クラスターと、クラスターにデプロイされたアプリケーションにアクセスできます。 クライアントは、X.509 証明書のセキュリティ資格情報を通じて一意に識別されます。 クラスターで管理クライアントまたはユーザー クライアントを認証するために、必要に応じて任意の数のクライアント証明書を使用できます。

クラスターでクライアントを認証するために、クライアント証明書に加えて、Azure Active Directory も構成できます。

詳細については、「[クライアントとノードの間のセキュリティ](service-fabric-cluster-security.md#client-to-node-security)」を参照してください。

### <a name="role-based-access-control"></a>ロールベースのアクセス制御
Azure ロールベースのアクセス制御 (Azure RBAC) を使用すると、Azure リソースに対してきめ細かいアクセス制御を割り当てることができます。  別のアクセス規則を、サブスクリプション、リソース グループ、およびリソースに割り当てることができます。  Azure RBAC 規則は、下位のレベルでオーバーライドされない限り、リソースの階層に沿って継承されます。  指定のユーザーとグループがクラスターを変更できるようにするために、Azure RBAC 規則を使用して AAD にユーザーやユーザー グループを割り当てることができます。  詳細については、[Azure RBAC の概要](../role-based-access-control/overview.md)に関するページを参照してください。

Service Fabric では、ユーザーの各グループに対して特定のクラスター操作へのアクセスを制限するアクセス制御もサポートしています。 その結果、クラスターのセキュリティが強化されます。 クラスターに接続するクライアント用に、2 種類のアクセス制御 (管理者ロールとユーザー ロール) がサポートされています。  

詳細については、「[Service Fabric のロールベースのアクセス制御](service-fabric-cluster-security.md#service-fabric-role-based-access-control)」をご覧ください。

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ 
ネットワーク セキュリティ グループ (NSG) は、サブネット、VM、または特定 NIC の受信および送信のトラフィックを制御します。  既定では、複数の VM が同じ仮想ネットワークに配置される場合、それらの VM はポートを通じて相互に通信できます。  コンピューター間の通信を制限する必要がある場合、NSG を定義してネットワークをセグメント化することや、VM を相互に分離することができます。  クラスターに複数のノード タイプがある場合、異なるノード タイプに属するコンピューターが相互に通信するのを防ぐために、サブネットに NSG を適用できます。  

詳細については、「[セキュリティ グループ](../virtual-network/network-security-groups-overview.md)」を参照してください。

## <a name="scaling"></a>Scaling

アプリケーションの要求は、時間の経過と共に変化します。 増加したアプリケーション ワークロードやネットワーク トラフィックに対処するためにクラスター リソースを増やしたり、需要が低下したときにクラスター リソースを減らしたりする必要が生じる場合があります。 Service Fabric クラスターの作成後は、クラスターを水平方向 (ノードの数を変更する) または垂直方向 (ノードのリソースを変更する) にスケーリングすることができます。 クラスターは、クラスターでワークロードを実行中であっても、いつでもスケーリングできます。 クラスターをスケーリングすると、アプリケーションも自動的にスケーリングされます。

詳細については、[Azure クラスターのスケーリング](service-fabric-cluster-scaling.md)に関するページを参照してください。

## <a name="upgrading"></a>アップグレード中
Azure Service Fabric クラスターはお客様が所有するリソースですが、一部は Microsoft によって管理されます。 Microsoft は、基になる OS の修正プログラムの適用や、クラスターでの Service Fabric ランタイムのアップグレードの実行を担当します。 クラスターは、新しいバージョンのランタイムが Microsoft からリリースされたときに自動アップグレードを適用するように設定できます。また、目的のサポートされているランタイム バージョンを選択するように設定することもできます。 ランタイムのアップグレードだけでなく、証明書やアプリケーション ポートなどのクラスター構成を更新することもできます。

詳細については、[クラスターのアップグレード](service-fabric-cluster-upgrade.md)に関するページを参照してください。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
クラスターは、次のオペレーティング システムが実行されている仮想マシン上に作成できます。

| オペレーティング システム | サポートされる最も古い Service Fabric のバージョン | サポートされる最新の Service Fabric のバージョン |
| --- | --- | --- | 
| Windows Server 2019 | 6.4.654.9590 | 該当なし |
| Windows Server 2016 | すべてのバージョン | 該当なし |
| Windows Server 20H2 | 7.2.445.9590 | 該当なし |
| Windows Server 1809 | 6.4.654.9590 | 7.2.445.9590 |
| Windows Server 1803 | 6.4 | 7.2.445.9590 |
| Windows Server 1709 | 6.0 | 7.2.445.9590 |
| Windows Server 2012 | すべてのバージョン | 該当なし | 
| Linux Ubuntu 16.04 | 6.0 | 該当なし |
| Linux Ubuntu 18.04 | 7.1 | 該当なし |

詳細については、[Azure でサポートされているクラスター バージョン](./service-fabric-versions.md#supported-operating-systems)に関するページを参照してください

> [!NOTE]
> Windows Server 1709 で Service Fabric をデプロイする場合、(1) これは Long Term Servicing Branch ではないため、今後、バージョンの移動が必要になる可能性があります。また、(2) コンテナーをデプロイする場合、Windows Server 2016 で構築されたコンテナーは Windows Server 1709 で動作しません。その逆も同様です (デプロイするにはリビルドが必要です)。
>


## <a name="next-steps"></a>次のステップ
Azure クラスターの[セキュリティ保護](service-fabric-cluster-security.md)、[スケーリング](service-fabric-cluster-scaling.md)、および[アップグレード](service-fabric-cluster-upgrade.md)について詳細を確認します。

[Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG