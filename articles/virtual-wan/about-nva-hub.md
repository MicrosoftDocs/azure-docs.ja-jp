---
title: Azure Virtual WAN:ハブのネットワーク仮想アプライアンスの概要
description: この記事では、Virtual WAN ハブのネットワーク仮想アプライアンスについて説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: 365ed60e73be9bb2098022fa767f4ae54b93c37c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028082"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Azure Virtual WAN ハブのネットワーク仮想アプライアンスについて (プレビュー)

Azure Virtual WAN は、ネットワーク パートナーと協力して、仮想ハブ内の Azure VPN ゲートウェイに顧客構内設備 (CPE) を簡単に接続できる自動化を構築しました。 Azure は厳選したネットワーク パートナーと協力して、お客様がサード パーティのネットワーク仮想アプライアンス (NVA) を仮想ハブに直接デプロイできるように取り組んでいます。 これにより、ブランチ CPE を仮想ハブにある同じブランドの NVA に接続したいお客様は、独自のエンドツーエンド SD-WAN 機能を活用できるようになります。

Barracuda Networks と Cisco Systems は、Virtual WAN ハブに直接デプロイできる NVA を提供する最初のパートナーです。  それぞれの製品ドキュメントについては、「[Barracuda CloudGen WAN](https://www.barracuda.com/products/cloudgenwan)」と「[Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701)」を参照してください。 Azure ではさらに多くのパートナーとの連携を行っているため、今後他のオファリングもご提供できる予定です。

> [!NOTE]
> Virtual WAN ハブにデプロイできるのは、Virtual WAN ハブへのデプロイが可能な NVA オファーのみです。 Azure の任意の仮想ネットワークにデプロイすることはできません。

## <a name="how-does-it-work"></a><a name="how"></a>動作のしくみ

Azure Virtual WAN ハブに直接デプロイできる NVA は、仮想ハブで使用するために特別に設計されています。 NVA オファーはマネージド アプリケーションとして Azure Marketplace に公開され、お客様は Azure Marketplace から直接オファーをデプロイすることも、Azure portal を介して仮想ハブからオファーをデプロイすることもできます。

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="プロセスの概要":::

各パートナーの NVA オファリングでは、そのデプロイ要件に基づいてエクスペリエンスと機能が若干異なります。 しかし、Virtual WAN ハブのすべての NVA のパートナー オファリングに共通するものもあります。

* Azure Marketplace を通じて提供されるマネージド アプリケーションのエクスペリエンス。
* NVA インフラストラクチャのユニットベースの容量と課金。
* 正常性メトリックは Azure Monitor を通じて表示。

### <a name="managed-application"></a><a name="managed"></a>マネージド アプリケーション

Virtual WAN ハブにデプロイできるすべての NVA サービスには、Azure Marketplace で入手できる **マネージド アプリケーション** があります。 パートナーは、マネージド アプリケーションで次のことを行うことができます。

* NVA のカスタム デプロイ エクスペリエンスを構築。
* 専用の Resource Manager テンプレートを指定して、Virtual WAN ハブで直接 NVA を作成。
* ソフトウェア ライセンスのコストを直接、または Azure Marketplace を通じて請求。
* カスタム プロパティとリソース メーターを公開。

NVA パートナーは、アプライアンスのデプロイ、構成のライセンス、および管理のニーズに応じて、異なるリソースを作成する場合があります。 お客様が Virtual WAN ハブで NVA を作成すると、すべてのマネージド アプリケーションと同様に、サブスクリプションに 2 つのリソース グループが作成されます。

* **カスタマー リソース グループ** - これには、マネージド アプリケーションのアプリケーション プレースホルダーが含まれます。 パートナーはこれを使用して、ここで選択したカスタマー プロパティを公開できます。
* **管理対象リソース グループ** - このリソース グループ内のリソースは、マネージド アプリケーションの公開元によって制御されるため、ユーザーが構成したり変更したりすることはできません。 このリソース グループには、**NetworkVirtualAppliances** リソースが含まれます。

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="マネージド アプリケーション リソース グループ":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA インフラストラクチャ ユニット

Virtual WAN ハブで NVA を作成する場合は、デプロイに使用する NVA インフラストラクチャ ユニットの数を選択する必要があります。 **NVA インフラストラクチャ ユニット** は、Virtual WAN ハブの NVA の集計帯域幅容量の単位です。 **NVA インフラストラクチャ ユニット** は、容量やサイズに関する考え方の点において、VPN [スケール ユニット](pricing-concepts.md#scale-unit)に似ています。

* 1 NVA インフラストラクチャ ユニットは、この NVA に入ってくるすべてのブランチ サイト接続の総帯域幅 (500 Mbps) を、1 時間あたり $0.25 のコストで表します。
* Azure では、特定の NVA 仮想ハブ デプロイに対して 1-80 NVA インフラストラクチャ ユニットがサポートされています。
* 各パートナーは、サポートされているすべての NVA インフラストラクチャ ユニット構成のサブセットである、異なる NVA インフラストラクチャ ユニット バンドルを提供します。

VPN スケール ユニットと同様に、*1 NVA インフラストラクチャ ユニット = 500 Mbps* を選択した場合は、冗長性のための 2 つのインスタンスが作成され、それぞれの最大スループットが 500 Mbps であることを意味します。 たとえば、ブランチで 10 Mbps を実行する 5 つのブランチがある場合、ヘッド エンドで 50 Mbps の集計が必要になります。 NVA の合計容量を計画する場合は、ハブに対するブランチ数をサポートするために必要な容量を評価した後に行う必要があります。

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>ネットワーク仮想アプライアンスの構成プロセス

パートナーは、デプロイ プロセスの一環として NVA を自動的に構成するエクスペリエンスを提供できるよう取り組んできました。 NVA が仮想ハブにプロビジョニングされた後は、NVA で必要になる可能性がある追加の構成は NVA パートナー ポータルまたは管理アプリケーションを使用して行う必要があります。 NVA への直接アクセスは使用できません。

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>NVA を使用したサイト リソースと接続リソース

Azure VPN Gateway 構成とは異なり、ブランチ サイトを Virtual WAN ハブの NVA に接続する際に **サイト** リソース、**サイト間接続** リソース、または **ポイント対サイト接続** リソースを作成する必要はありません。 これはすべて NVA パートナーを通じて管理されます。

Virtual WAN ハブを Azure 仮想ネットワークに接続するためには、今後も Hub-to-VNet 接続を作成する必要があります。

## <a name="supported-regions"></a><a name="regions"></a>サポートされているリージョン

仮想ハブの NVA は、以下のリージョンで、プレビューとして利用できます。

|地理的リージョン | Azure Azure リージョン|
|---|---|
| 北米| 米国西部、米国中南部、米国東部 2   |
| 南アメリカ | ブラジル南部 |
| ヨーロッパ | 西ヨーロッパ、英国南部|
|  中東 | アラブ首長国連邦北部 |
| アジア | 東日本 |
| オーストラリア | オーストラリア東部 |

## <a name="faq"></a>よく寄せられる質問

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>ネットワーク アプライアンス パートナーとして、提供する NVA をハブに追加したいと思っています。  このパートナー プログラムに参加することはできますか?

残念ながら、現時点では、新しいパートナー オファーをオンボードするキャパシティがございません。 11 月にもう一度ご確認ください。

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Azure Marketplace から Virtual WAN ハブに任意の NVA をデプロイすることはできますか?

現時点で Virtual WAN ハブにデプロイできるのは、[Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer) と [Cisco Cloud vWAN Application](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) のみになります。

### <a name="what-is-the-cost-of-the-nva"></a>NVA のコストはいくらですか?

NVA ベンダーから NVA のライセンスを購入する必要があります。  Barracuda ライセンスの Barracuda CloudGen WAN NVA については、[Barracuda の CloudGen WAN のページ](https://www.barracuda.com/products/cloudgenwan)を参照してください。 現在 Cisco では、CISCO から直性購入する必要がある BYOL (ライセンス持ち込み) のライセンス モデルのみが提供されています。 また、Microsoft からは消費した NVA インフラストラクチャ ユニットと、使用したその他のリソースについても料金が発生します。 詳細については、[価格の概念](pricing-concepts.md)に関するページを参照してください。

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>NVA を Basic ハブにデプロイすることはできますか?

いいえ。 NVA をデプロイする場合は、Standard ハブを使用する必要があります。

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>NVA をセキュリティで保護されたハブにデプロイすることはできますか?

はい。 Barracuda CloudGen WAN は、Azure Firewall を使用してハブにデプロイすることができます。

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>ブランチ オフィス内のすべての Barracuda デバイスをハブの CloudGen WAN NVA に接続することはできますか?

いいえ。 Barracuda CloudGen WAN は、Barracuda CPE デバイスのみと互換性があります。 CloudGen WAN の要件の詳細については、[Barracuda の CloudGen WAN のページ](https://www.barracuda.com/products/cloudgenwan)を参照してください。 Cisco の場合、互換性がある SD-WAN デバイスがいくつかあります。 互換性がある CPI については、[Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) のドキュメントを参照してください。

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>ハブの NVA ではどのようなルーティング シナリオがサポートされていますか?

ハブの NVA では、Virtual WAN でサポートされているすべてのルーティング シナリオがサポートされています。

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
