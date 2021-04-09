---
title: 概念 - ハブ アンド スポークのアーキテクチャで Azure VMware Solution のデプロイを統合する
description: Azure のハブ アンド スポーク アーキテクチャで Azure VMware Solution のデプロイを統合する方法について説明します。
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: bfc442e569572349b1323500fbd0b2f912ebbc62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99062747"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>ハブ アンド スポークのアーキテクチャで Azure VMware Solution を統合する

この記事では、Azure の既存または新規の[ハブ アンド スポーク アーキテクチャ](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology)で Azure VMware Solution のデプロイを統合するための推奨事項について説明します。 


ハブ アンド スポークのシナリオでは、次のワークロードを含むハイブリッド クラウド環境が想定されています。

* IaaS サービスまたは PaaS サービスを使用したネイティブ Azure
* Azure VMware Solution 
* オンプレミスの vSphere

## <a name="architecture"></a>アーキテクチャ

"*ハブ*" は、オンプレミスおよび Azure VMware Solution のプライベート クラウドへの接続の中心点として機能する Azure Virtual Network です。 "*スポーク*" は、仮想ネットワーク間通信を有効にするためにハブとピアリングされた仮想ネットワークです。

オンプレミスのデータセンター、Azure VMware Solution プライベート クラウド、およびハブの間のトラフィックは、Azure ExpressRoute 接続を経由します。 スポーク仮想ネットワークには、通常、IaaS ベースのワークロードが含まれていますが、Virtual Network に直接統合されている PaaS サービス ([App Service Environment](../app-service/environment/intro.md) など) や、[Azure Private Link](../private-link/index.yml) が有効になっているその他の PaaS サービスを含めることができます。

>[!IMPORTANT]
>仮想ネットワークあたり 4 つの ExpressRoute 回路という上限を超えない限り、既存の ExpressRoute ゲートウェイを使用して Azure VMware Solution に接続できます。  ただし、オンプレミスから ExpressRoute 経由で Azure VMware Solution にアクセスするには、ExpressRoute Global Reach が必要です。これは、ExpressRoute ゲートウェイでは、それに接続されている回路との間で推移的なルーティングが提供されないためです。

次の図は、ExpressRoute Global Reach 経由でオンプレミスおよび Azure VMware Solution に接続されている Azure のハブ アンド スポークのデプロイの例を示したものです。

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Azure VMware Solution のハブ アンド スポークの統合デプロイ" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

このアーキテクチャには、次の主要なコンポーネントがあります。

- **オンプレミス サイト:** ExpressRoute 接続を介して Azure に接続されている顧客のオンプレミスのデータセンター。

- **Azure VMware Solution のプライベート クラウド:** 1 つ以上の vSphere クラスターによって形成された Azure VMware Solution SDDC。それぞれに最大 16 個のホストがあります。

- **ExpressRoute ゲートウェイ:** Azure VMware Solution プライベート クラウド、ハブ仮想ネットワーク上の共有サービス、およびスポーク仮想ネットワークで実行されているワークロードの間の通信を有効にします。

- **ExpressRoute Global Reach:** オンプレミスと Azure VMware ソリューションのプライベート クラウドの間の接続を有効にします。 Azure VMware Solution と Azure ファブリックを接続するものは ExpressRoute Global Reach だけです。 ExpressRoute Fast Path 以外のオプションは選択できません。  ExpressRoute Direct はサポートされていません。


- **S2S VPN に関する考慮事項:** Azure VMware Solution の運用環境のデプロイでは、VMware HCX のネットワーク要件により、Azure S2S VPN がサポートされていません。 ただし、PoC のデプロイに使用することはできます。


- **ハブ仮想ネットワーク:** オンプレミスのネットワークおよび Azure VMware Solution プライベート クラウドへの中心となる接続ポイントとして機能します。

- **スポーク仮想ネットワーク**

    - **IaaS スポーク:** IaaS スポークによって、VM 可用性セットや仮想マシン スケール セットなどの Azure IaaS ベースのワークロードと、それに対応するネットワーク コンポーネントがホストされます。

    - **PaaS スポーク:** PaaS スポークでは、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)と [プライベート リンク](../private-link/private-link-overview.md)により、プライベート アドレス指定を使用して Azure PaaS サービスがホストされます。

- **Azure Firewall:** スポークと Azure VMware Solution の間でトラフィックをセグメント化するための中心的な要素として機能します。

- **Application Gateway** Azure IaaS/PaaS または Azure VMware Solution 仮想マシン (VM) 上で実行される Web アプリを公開し、保護します。 これは、API Management などの他のサービスと統合されています。

## <a name="network-and-security-considerations"></a>ネットワークとセキュリティに関する考慮事項

ExpressRoute 接続を使用すると、オンプレミス、Azure VMware Solution、Azure ネットワーク ファブリックの間でトラフィックをフローさせることができます。 Azure VMware Solution によって [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) が使用されて、この接続が実装されます。

ExpressRoute ゲートウェイの場合、それに接続されている回路間で推移的ルーティングが与えられないため、オンプレミスの接続では ExpressRoute Global Reach も使用しないと、オンプレミス vSphere 環境と Azure VMware Solution の間で通信できません。 

* **オンプレミスから Azure VMware Solution へのトラフィック フロー**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="オンプレミスから Azure VMware Solution へのトラフィック フロー" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Azure VMware Solution からハブ VNET へのトラフィック フロー**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Azure VMware Solution からハブ仮想ネットワークへのトラフィック フロー" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Azure VMware Solution ネットワークと接続の概念の詳細については、[Azure VMware Solution 製品のドキュメント](./concepts-networking.md)を参照してください。

### <a name="traffic-segmentation"></a>トラフィックのセグメント化

[Azure Firewall](../firewall/index.yml) はハブ アンド スポークのトポロジの中心的な要素であり、ハブ仮想ネットワークにデプロイされています。 Azure Firewall、またはその他の Azure でサポートされているネットワーク仮想アプライアンスを使用して、トラフィック規則を確立し、さまざまなスポークと Azure VMware Solution のワークロード間の通信をセグメント化します。

トラフィックを Azure Firewall に送信するためのルート テーブルを作成します。  スポーク仮想ネットワークの場合は、Azure Firewall の内部インターフェイスにデフォルト ルートを設定するルートを作成します。 これにより、Virtual Network 内のワークロードが Azure VMware Solution アドレス空間に到達する必要がある場合に、ファイアウォールによってこのワークロードを評価し、対応するトラフィック規則を適用して許可または拒否することができます。  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="トラフィックを Azure Firewall に送信するためのルート テーブルを作成する" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> **GatewaySubnet** 設定のアドレス プレフィックス 0.0.0.0/0 のルートはサポートされていません。

対応するルート テーブルで特定のネットワークのルートを設定します。 たとえば、Azure VMware Solution 管理に到達するまでのルートと、スポーク ワークロードからのワークロード IP プレフィックス (またはその逆) です。

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="対応するルート テーブルで特定のネットワークのルートを設定する" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

スポークおよびハブ内のネットワーク セキュリティ グループを使用して、より詳細なトラフィック ポリシーを作成する、2 番目のレベルのトラフィック セグメント。

> [!NOTE]
> **オンプレミスから Azure VMware Solution までのトラフィック** オンプレミス ワークロード間のトラフィックは、vSphere ベースであれ、その他であれ、Global Reach によって有効になりますが、トラフィックはハブ上で Azure Firewall を通過しません。 このシナリオでは、オンプレミスまたは Azure VMware Solution にトラフィック セグメント化メカニズムを実装する必要があります。

### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway V1 および V2 は、Azure VMware Solution VM 上でバックエンド プールとして実行される Web アプリでテストされています。 現在、Application Gateway は、Azure VMware Solution VM で実行されている Web アプリをインターネットに公開する、唯一のサポートされている方法です。 また、アプリを内部ユーザーに安全に公開することもできます。

詳細については、[Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md) に関する Azure VMware Solution 特集記事をご確認ください。

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="ネットワーク セキュリティ グループを使用する 2 番目のレベルのトラフィック セグメント" border="false":::


### <a name="jump-box-and-azure-bastion"></a>ジャンプ ボックスと Azure Bastion

ハブ仮想ネットワーク内の共有サービス サブネットにデプロイされている Windows 10 または Windows Server の VM であるジャンプ ボックスを使用して、Azure VMware Solution 環境にアクセスします。

>[!IMPORTANT]
>Azure Bastion は、Azure VMware Solution がインターネットに公開されないようにジャンプ ボックスに接続する場合に推奨されるサービスです。 Azure VMware Solution の VM は Azure IaaS オブジェクトではないため、Azure Bastion を使用してそれらの VM に接続することはできません。  

セキュリティのベスト プラクティスとして、ハブ仮想ネットワーク内に [Microsoft Azure Bastion](../bastion/index.yml) サービスをデプロイすることをお勧めします。 Azure Bastion では、Azure にデプロイされている VM へのシームレスな RDP アクセスと SSH アクセスが提供されます。これらのリソースにパブリック IP アドレスをプロビジョニングする必要はありません。 Azure Bastion サービスをプロビジョニングすると、選択した VM に Azure portal からアクセスできるようになります。 接続を確立すると、新しいタブが開いてジャンプ ボックスのデスクトップが表示されます。そのデスクトップから、Azure VMware Solution プライベート クラウドの管理プレーンにアクセスできます。

> [!IMPORTANT]
> ジャンプ ボックス VM にパブリック IP アドレスを付与したり、パブリック インターネットに 3389/TCP ポートを公開したりしないでください。 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure Bastion ハブ仮想ネットワーク" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS の解決に関する考慮事項

Azure DNS の解決には、次の 2 つのオプションを使用できます。

-   ハブにデプロイされているドメイン コントローラーをネーム サーバーとして使用します (「[ID に関する考慮事項](#identity-considerations)」で説明されます)。

-   Azure DNS プライベート ゾーンをデプロイして構成します。

最善の方法は、両方を組み合わせて、Azure VMware Solution、オンプレミス、Azure に対して信頼性の高い名前解決を提供することです。

一般的な設計の推奨事項として、2 つ以上の Azure VM (ハブ仮想ネットワークにデプロイされており、DNS 設定で Azure DNS サーバーを使用するようにスポーク仮想ネットワークで構成されている) にデプロイされている既存の Azure DNS インフラストラクチャ (この場合は Active Directory 統合 DNS) を使用します。

Azure プライベート DNS ゾーンが仮想ネットワークにリンクされる Azure プライベート DNS を使用できます。  DNS サーバーは、顧客の Azure プライベート DNS インフラストラクチャを使用して DNS が実行されている、オンプレミスまたは Azure VMware Solution への条件付き転送にハイブリッド リゾルバーとして使用されます。 

スポーク仮想ネットワーク内にデプロイされている VM に対して DNS レコードのライフサイクルを自動管理するには、自動登録を有効にします。 有効にすると、プライベート DNS ゾーンの最大数が 1 つのみになります。 無効にすると、最大数は 1000 です。

オンプレミスおよび Azure VMware Solution のサーバーでは、Azure プライベート DNS ゾーンに対する Azure 内のリゾルバー VM への条件付きフォワーダーを構成できます。

## <a name="identity-considerations"></a>ID に関する考慮事項

ID に関する理由により、ハブに少なくとも 1 つのドメイン コントローラーをデプロイすることをお勧めします。 ゾーン分散方式または VM 可用性セットで 2 つの共有サービス サブネットを使用します。 オンプレミスの Active Directory (AD) ドメインを Azure に拡張する方法の詳細については、[Azure アーキテクチャ センター](/azure/architecture/reference-architectures/identity/adds-extend-domain)に関するページを参照してください。

さらに、vSphere 環境内で ID および DNS ソースとして機能するように、別のドメイン コントローラーを Azure VMware Solution 側にデプロイします。

推奨されるベスト プラクティスとして、[AD ドメインと Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad) を統合することをお勧めします。

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
