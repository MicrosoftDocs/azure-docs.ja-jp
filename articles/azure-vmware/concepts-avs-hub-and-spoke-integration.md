---
title: 概念 - ハブ アンド スポークのアーキテクチャで Azure VMware Solution のデプロイを統合する
description: Azure の既存または新規のハブ アンド スポークのアーキテクチャで Azure VMware Solution のデプロイを統合するための推奨事項について説明します。
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: deb2756f7e83250ff58836098dc4954ec482fbda
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684519"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>ハブ アンド スポークのアーキテクチャで Azure VMware Solution を統合する

この記事では、Azure の既存または新規の[ハブ アンド スポーク アーキテクチャ](/azure/architecture/reference-architectures/hybrid-networking/shared-services)で Azure VMware Solution のデプロイを統合するための推奨事項について説明します。 

ハブ アンド スポークのシナリオでは、次のワークロードを含むハイブリッド クラウド環境が想定されています。

* IaaS サービスまたは PaaS サービスを使用したネイティブ Azure
* Azure VMware Solution 
* オンプレミスの vSphere

## <a name="architecture"></a>アーキテクチャ

"*ハブ*" は、オンプレミスおよび Azure VMware Solution のプライベート クラウドへの接続の中心点として機能する Azure Virtual Network です。 "*スポーク*" は、仮想ネットワーク間通信を有効にするためにハブとピアリングされた仮想ネットワークです。

オンプレミスのデータセンター、Azure VMware Solution プライベート クラウド、およびハブの間のトラフィックは、ExpressRoute 接続を経由します。 スポーク仮想ネットワークには、通常、IaaS ベースのワークロードが含まれていますが、Virtual Network に直接統合されている PaaS サービス ([App Service Environment](../app-service/environment/intro.md) など) や、[Azure Private Link](../private-link/index.yml) が有効になっているその他の PaaS サービスを含めることができます。 

次の図は、ExpressRoute 経由でオンプレミスおよび Azure VMware Solution に接続されている Azure のハブ アンド スポークのデプロイの例を示したものです。

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Azure VMware Solution のハブ アンド スポークの統合デプロイ" border="false":::

このアーキテクチャには、次の主要なコンポーネントがあります。

-   **オンプレミス サイト:** ExpressRoute 接続を介して Azure に接続されている顧客のオンプレミスのデータセンター。

-   **Azure VMware Solution のプライベート クラウド:** 1 つ以上の vSphere クラスターによって形成された Azure VMware Solution SDDC。それぞれに最大 16 個のノードがあります。

-   **ExpressRoute ゲートウェイ:** Azure VMware Solution プライベート クラウド、オンプレミスのネットワーク、ハブ仮想ネットワーク上の共有サービス、およびスポーク仮想ネットワークで実行されているワークロードの間の通信を有効にします。

    > [!NOTE]
    > **S2S VPN に関する考慮事項:** Azure VMware Solution の運用環境のデプロイでは、HCX のネットワーク要件により、Azure S2S がサポートされていません。 ただし、HCX を必要としない PoC または非運用環境のデプロイでは、これを使用できます。

-   **ハブ仮想ネットワーク:** オンプレミスのネットワークおよび Azure VMware Solution プライベート クラウドへの中心となる接続ポイントとして機能します。

-   **スポーク仮想ネットワーク**

    -   **IaaS スポーク:** IaaS スポークによって、VM 可用性セットや仮想マシン スケール セットなどの Azure IaaS ベースのワークロードと、それに対応するネットワーク コンポーネントがホストされます。

    -   **PaaS スポーク:** PaaS スポークでは、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)と[プライベート リンク](../private-link/private-link-overview.md)により、プライベート アドレス指定を使用して Azure PaaS サービスがホストされます。

-   **Azure Firewall:** スポーク、オンプレミス、Azure VMware Solution の間でトラフィックをセグメント化するための中心的な要素として機能します。

-   **Application Gateway**Azure IaaS/PaaS または Azure VMware Solution 仮想マシン (VM) 上で実行される Web アプリを公開し、保護します。 これは、API Management などの他のサービスと統合されています。

## <a name="network-and-security-considerations"></a>ネットワークとセキュリティに関する考慮事項

ExpressRoute 接続を使用すると、オンプレミス、Azure VMware Solution、Azure ネットワーク ファブリックの間でトラフィックをフローさせることができます。 Azure VMware Solution によって [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) が使用されて、この接続が実装されます。

オンプレミスの接続では、ExpressRoute Global Reach も使用できますが、必須ではありません。

* **オンプレミスから Azure VMware Solution へのトラフィック フロー**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="オンプレミスから Azure VMware Solution へのトラフィック フロー" border="false":::


* **Azure VMware Solution からハブ VNET へのトラフィック フロー**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Azure VMware Solution からハブ仮想ネットワークへのトラフィック フロー" border="false":::


Azure VMware Solution ネットワークと相互接続の概念の詳細については、[Azure VMware Solution 製品のドキュメント](./concepts-networking.md)を参照してください。

### <a name="traffic-segmentation"></a>トラフィックのセグメント化

[Azure Firewall](../firewall/index.yml) はハブ アンド スポークのトポロジの中心的な要素であり、ハブ仮想ネットワークにデプロイされています。 Azure Firewall、またはその他の Azure でサポートされているネットワーク仮想アプライアンスを使用して、トラフィック規則を確立し、さまざまなスポーク、オンプレミス、Azure VMware Solution のワークロード間の通信をセグメント化します。

トラフィックを Azure Firewall に送信するためのルート テーブルを作成します。  スポーク仮想ネットワークの場合は、Azure Firewall の内部インターフェイスにデフォルト ルートを設定するルートを作成します。これにより、Virtual Network 内のワークロードが Azure VMware Solution アドレス空間に到達する必要がある場合に、ファイアウォールによってこのワークロードを評価し、対応するトラフィック規則を適用して許可または拒否することができます。  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="トラフィックを Azure Firewall に送信するためのルート テーブルを作成する":::


> [!IMPORTANT]
> **GatewaySubnet** 設定のアドレス プレフィックス 0.0.0.0/0 のルートはサポートされていません。

対応するルート テーブルで特定のネットワークのルートを設定します。 たとえば、Azure Firewall を介してオンプレミスから Azure VMware Solution プライベート クラウドにすべてのトラフィックをルーティングする、Azure VMware Solution 管理およびワークロードの IP プレフィックスとオンプレミスとの間のルートなどです。

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="対応するルート テーブルで特定のネットワークのルートを設定する":::

スポークおよびハブ内のネットワーク セキュリティ グループを使用して、より詳細なトラフィック ポリシーを作成する、2 番目のレベルのトラフィック セグメント。 


### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway V1 および V2 は、Azure VMware Solution VM 上でバックエンド プールとして実行される Web アプリでテストされています。 現在、Application Gateway は、Azure VMware Solution VM で実行されている Web アプリをインターネットに公開する、唯一のサポートされている方法です。 また、アプリを内部ユーザーに安全に公開することもできます。

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="ネットワーク セキュリティ グループを使用する 2 番目のレベルのトラフィック セグメント" border="false":::


### <a name="jumpbox-and-azure-bastion"></a>Jumpbox と Azure Bastion

ハブ仮想ネットワーク内の共有サービス サブネットにデプロイされている Windows 10 または Windows Server の VM である Jumpbox を使用して、Azure VMware Solution 環境にアクセスします。

セキュリティのベスト プラクティスとして、ハブ仮想ネットワーク内に [Microsoft Azure Bastion](../bastion/index.yml) サービスをデプロイすることをお勧めします。 Azure Bastion では、Azure にデプロイされている VM へのシームレスな RDP アクセスと SSH アクセスが提供されます。これらのリソースにパブリック IP アドレスをプロビジョニングする必要はありません。 Azure Bastion サービスをプロビジョニングすると、選択した VM に Azure portal からアクセスできるようになります。 接続を確立すると、新しいタブが開いて Jumpbox デスクトップが表示され、そのデスクトップから Azure VMware Solution プライベート クラウド管理プレーンにアクセスできるようになります。

> [!IMPORTANT]
> Jumpbox VM にパブリック IP アドレスを指定したり、パブリック インターネットに 3389/TCP ポートを公開したりしないでください。 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure Bastion ハブ仮想ネットワーク" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS の解決に関する考慮事項

Azure DNS の解決には、次の 2 つのオプションを使用できます。

-   ハブにデプロイされている Azure Active Directory (Azure AD) ドメイン コントローラーをネーム サーバーとして使用します (「[ID に関する考慮事項](#identity-considerations)」を参照)。

-   Azure DNS プライベート ゾーンをデプロイして構成します。

最善の方法は、両方を組み合わせて、Azure VMware Solution、オンプレミス、Azure に対して信頼性の高い名前解決を提供することです。

一般的な設計の推奨事項として、2 つ以上の Azure VM (ハブ仮想ネットワークにデプロイされており、DNS 設定で Azure DNS サーバーを使用するようにスポーク仮想ネットワークで構成されている) にデプロイされている既存の Azure DNS インフラストラクチャ (この場合は Active Directory 統合 DNS) を使用します。

Azure DNS プライベート ゾーンが仮想ネットワークにリンクされている場合も使用することができ、DNS サーバーは顧客の Azure プライベート DNS インフラストラクチャを利用して DNS を実行しているオンプレミスまたは Azure VMware Solution への条件付き転送にハイブリッド リゾルバーとして使用されます。

Azure DNS プライベート ゾーンについていくつかの考慮事項があります。

* スポーク仮想ネットワーク内にデプロイされている VM の DNS レコードのライフサイクルを自動的に管理するには、Azure DNS に対する自動登録を有効にする必要があります。
* 自動登録が有効な状態で仮想ネットワークをリンクできるプライベート DNS ゾーンの最大数は、1 つだけです。
* 自動登録が有効になっていない状態で仮想ネットワークをリンクできるプライベート DNS ゾーンの最大数は、1,000 です。

オンプレミスおよび Azure VMware Solution のサーバーでは、Azure プライベート DNS ゾーンに対する Azure 内のリゾルバー VM への条件付きフォワーダーを構成できます。

## <a name="identity-considerations"></a>ID に関する考慮事項

ID に関する理由により、共有サービス サブネットを使用して、ハブに少なくとも 1 つの AD ドメイン コントローラー (理想的には、ゾーン分散形式の 2 つまたは VM 可用性セット) をデプロイすることをお勧めします。 オンプレミスの AD ドメインを Azure に拡張する方法については、[Azure アーキテクチャ センター](/azure/architecture/reference-architectures/identity/adds-extend-domain)に関する記事を参照してください。

さらに、vSphere 環境内で ID および DNS ソースとして機能するように、別のドメイン コントローラーを Azure VMware Solution 側にデプロイします。

vCenter および SSO の場合は、Azure portal の **[管理]\>[ID]\>[ID ソース]** で ID ソースを設定します。

推奨されるベスト プラクティスとして、[AD ドメインと Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad) を統合することをお勧めします。

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
