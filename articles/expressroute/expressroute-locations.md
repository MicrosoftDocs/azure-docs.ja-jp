---
title: '接続プロバイダーと場所: Azure ExpressRoute | Microsoft Docs'
description: この記事では、サービスが提供されている場所と Azure リージョンに接続する方法の詳細について説明します。 接続プロバイダーごとにソートされています。
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/02/2019
ms.author: jaredr80
ms.openlocfilehash: 573a8ec9c490b06d6004e36b4c569b1d2de94029
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537952"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute パートナーとピアリングの場所

> [!div class="op_single_selector"]
> * [プロバイダー別の場所](expressroute-locations.md)
> * [場所別のプロバイダー](expressroute-locations-providers.md)


この記事の表では、ExpressRoute 接続プロバイダー、ExpressRoute の地理的範囲、ExpressRoute でサポートされる Microsoft クラウド サービス、ExpressRoute システム インテグレーター (SI) に関する情報を提供します。

## <a name="partners"></a>ExpressRoute 接続プロバイダー
ExpressRoute は、すべての Azure のリージョンと場所でサポートされています。 次のマップは Azure のリージョンと ExpressRoute の場所を一覧しています。 ExpressRoute の場所は、Microsoft がいくつかのサービス プロバイダーと接続している場所を示します。

![場所のマップ][0]

地理的リージョン内の少なくとも 1 つの ExpressRoute の場所に接続している場合は、その地理的リージョン内のすべてのリージョンの Azure サービスにアクセスできます。

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>地理的リージョン内の Azure リージョンと ExpressRoute の場所の対応
次の表は、地理的リージョン、ExpressRoute の場所、Azure リージョンをまとめたものです。

| **地理的リージョン** | **ゾーン** | **Azure リージョン** | **ExpressRoute の場所** |
| --- | --- | --- | --- |
| **オーストラリア政府** | 1 | オーストラリア中部、オーストラリア中部 2 |キャンベラ、キャンベラ2 |
| **ヨーロッパ** | 1 |フランス中部、フランス南部、北ヨーロッパ、西ヨーロッパ、英国西部、英国南部 |アムステルダム、アムステルダム 2、ダブリン、フランクフルト、ロンドン、ロンドン 2、マルセイユ、ニューポート (ウェールズ)、パリ、チューリッヒ |
| **北米** | 1 |米国東部、米国西部、米国東部 2、米国西部 2、米国中部、米国中南部、米国中北部、米国中西部、カナダ中部、カナダ東部 |アトランタ、シカゴ、ダラス、デンバー、ラスベガス、ロサンゼルス、マイアミ、ニューヨーク、サンアントニオ、シアトル、シリコン バレー、シリコン バレー 2、ワシントン DC、ワシントン DC2、モントリオール、ケベックシティ、トロント |
| **アジア** | 2 |東アジア、東南アジア |香港特別行政区、クアラルンプール、シンガポール、シンガポール 2、台北 |
| **インド** | 2 |インド西部、インド中部、インド南部 |チェンナイ、チェンナイ 2、ムンバイ、ムンバイ 2 |
| **日本** | 2 |西日本、東日本 |大阪、東京 |
| **オセアニア** | 2 |オーストラリア東部、オーストラリア南東部 |オークランド、メルボルン、パース、シドニー |
| **韓国** | 2 |韓国中部、韓国南部 |釜山、ソウル|
| **アラブ首長国連邦** | 3 | アラブ首長国連邦中部、アラブ首長国連邦北部 | ドバイ、ドバイ 2 |
| **南アフリカ** | 3 |南アフリカ西部、南アフリカ北部 |ケープタウン、ヨハネスブルグ |
| **南アメリカ** | 3 |ブラジル南部 |サンパウロ |


### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>各国のクラウドのリージョンと地理的境界
次の表は、国内のクラウドのリージョンと地政学的境界に関する情報をまとめたものです。

| **地理的リージョン** | **Azure リージョン** | **ExpressRoute の場所** |
| --- | --- | --- |
| **米国政府のクラウド** |米国政府アリゾナ、US Gov アイオワ、米国政府テキサス、米国政府バージニア、US DoD 中部、US DoD 東部  |シカゴ、ダラス、ニューヨーク、フェニックス、サンアントニオ、シアトル、シリコン バレー、ワシントン DC |
| **中国東部** |中国東部、中国東部 2 |上海、上海 2 |
| **中国北部** |中国北部、中国北部 2 |北京、北京 2 |
| **ドイツ** |ドイツ中部、ドイツ東部 |ベルリン、フランクフルト |

標準の ExpressRoute SKU では、地理的リージョン間の接続はサポートされていません。 グローバル接続に対応するには、ExpressRoute Premium アドオンを有効にする必要があります。 国内のクラウド環境には接続できません。 国内のクラウド環境に接続する必要がある場合、接続プロバイダーにご相談ください。

## <a name="locations"></a>接続プロバイダーの場所

次の表に、サービス プロバイダー別の場所を示します。 場所別の使用可能なプロバイダーを確認する場合は、[場所別のサービス プロバイダー](expressroute-locations-providers.md#locations)に関するページを参照してください。


### <a name="production-azure"></a>運用 Azure

| **サービス プロバイダー** | **Microsoft Azure** | **Office 365 および Dynamics 365** | **場所** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |サポートされています |サポートされています |メルボルン、シドニー |
| **[Airtel](https://www.airtel.in/creatingsmiles/)** | サポートされています | サポートされています | チェンナイ 2、ムンバイ 2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |サポートされています |サポートされています |アムステルダム、シカゴ、ダラス、香港特別行政区、サンパウロ、シアトル、シリコン バレー、シンガポール、東京、ワシントン DC |
| **[Ascenty Data Centers](https://ascenty.com/servicos/cloud-connect/microsoft-expressroute/)** |サポートされています |サポートされています |サンパウロ |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |サポートされています |サポートされています |アムステルダム、シカゴ、ダラス、ロンドン、シリコン バレー、シンガポール、シドニー、東京、トロント、ワシントン DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |サポートされています |サポートされています |モントリオール、トロント、ケベック シティ |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |サポートされています |サポートされています |アムステルダム、香港特別行政区、ヨハネスブルグ、ロンドン、ニューポート (ウェールズ)、サンパウロ、シリコン バレー、シンガポール、シドニー、東京、ワシントン DC |
| **C3ntro** |近日対応予定 |近日対応予定 |マイアミ |
| **CDC** | サポートされています | サポートされています | キャンベラ、キャンベラ2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |サポートされています |サポートされています |シカゴ、香港、ラスベガス、ニューヨーク、サンアントニオ、シリコン バレー、東京、トロント、ワシントン DC |
| **Chief Telecom** |サポートされています |サポートされています |香港、台北 |
| **China Telecom Global** |サポートされています |サポートされていません |香港特別行政区 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |サポートされています |サポートされています |シカゴ、ダラス、モントリオール、トロント、ワシントン DC |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |サポートされています |サポートされています |アムステルダム、ダブリン、ロンドン、ニューポート、パリ、シンガポール 2、東京 |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |サポートされています |サポートされています |シカゴ、シリコン バレー、ワシントン DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |サポートされています |サポートされています |シカゴ、デンバー、ロサンゼルス、ニューヨーク、シリコン バレー、シリコン バレー 2、ワシントン DC、ワシントン DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | サポートされています |サポートされています |アムステルダム 2、フランクフルト|
| **[Devoli](https://devoli.com/expressroute)** | サポートされています |サポートされています | オークランド、メルボルン、シドニー |
| **du datamena** |サポートされています |サポートされています | ドバイ 2 |
| **eir** |サポートされています |サポートされています |ダブリン|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |サポートされています |サポートされています |シンガポール、シンガポール 2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |サポートされています |サポートされています |アムステルダム、アトランタ、シカゴ、ダラス、ダブリン、香港特別行政区、ロンドン、ロンドン 2、ロサンゼルス、メルボルン、マイアミ、ニューヨーク、大阪、パリ、サンパウロ、シアトル、シリコン バレー、シンガポール、シドニー、東京、トロント、ワシントン DC |
| **Etisalat UAE** |サポートされています |サポートされています |ドバイ|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |サポートされています |サポートされています |アムステルダム、ダブリン、ロンドン |
| **FarEasTone** |サポートされています |サポートされています |台北|
| **GÉANT** |サポートされています |サポートされています |アムステルダム |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | サポートされています| サポートされています | チェンナイ、ムンバイ |
| **[InterCloud](https://www.intercloud.com/)** |サポートされています |サポートされています |アムステルダム、シカゴ、ロンドン、ニューヨーク、パリ、シリコン バレー、シンガポール、ワシントン DC |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |サポートされています |サポートされています |シカゴ、ダラス、ワシントン DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |サポートされています |サポートされています |大阪、東京 |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |サポートされています |サポートされています |ケープタウン、ヨハネスブルグ、ロンドン |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |サポートされています |サポートされています |アムステルダム、アムステルダム 2、ダブリン、フランクフルト、ロンドン、マルセイユ、パリ、チューリッヒ |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|サポートされています |サポートされています | アムステルダム、ロンドン 2、シリコン バレー、トロント |
| **Jaguar Network** |サポートされています |サポートされています |マルセイユ|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |サポートされています |サポートされています |ロンドン |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |サポートされています |サポートされています |ソウル |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | サポートされています |サポートされています |シドニー |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | サポートされています | サポートされています | アムステルダム | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |サポートされています |サポートされています |アムステルダム、シカゴ、ダラス、ロンドン、ニューポート (ウェールズ)、サンパウロ、シアトル、シリコン バレー、シンガポール、ワシントン DC |
| **LG CNS** |サポートされています |サポートされています |釜山、ソウル |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |サポートされています |サポートされています |ケープタウン、ヨハネスブルグ |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |サポートされています |サポートされています |アムステルダム、アトランタ、オークランド、シカゴ、ダラス、デンバー、ダブリン、香港特別行政区、ラスベガス、ロンドン、ロサンゼルス、メルボルン、マイアミ、ニューヨーク、パース、ケベック シティ、サンアントニオ、シアトル、シリコン バレー、シンガポール、シンガポール 2、シドニー、トロント、ワシントン DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |サポートされています |サポートされています |ロンドン |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |サポートされています |サポートされています |ダラス、ロサンゼルス、マイアミ、サンパウロ、ワシントン DC |
| **[Next Generation Data](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |サポートされています |サポートされています |ニューポート (ウェールズ) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |サポートされています |サポートされています |メルボルン、パース、シドニー |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |サポートされています |サポートされています |アムステルダム、香港特別行政区、ロンドン、ロサンゼルス、大阪、シンガポール、シドニー、東京、ワシントン DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |サポートされています |サポートされています |東京 |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |サポートされています |サポートされています |大阪 |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |サポートされています |サポートされています |メルボルン、シドニー |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |サポートされています |サポートされています |アムステルダム、香港特別行政区、ロンドン、パリ、サンパウロ、シリコン バレー、シンガポール、シドニー、東京、ワシントン DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | サポートされています | サポートされています | ドバイ 2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |サポートされています |サポートされています |シカゴ、シリコン バレー、ワシントン DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |サポートされています |サポートされています |シカゴ、香港特別行政区、ロンドン |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |サポートされています |サポートされています |ソウル |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |サポートされています |サポートされています |チェンナイ、ムンバイ 2 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |サポートされています |サポートされています |シンガポール、シンガポール 2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |サポートされています |サポートされています |大阪、東京 |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |サポートされています |サポートされています |シカゴ、シリコン バレー、ワシントン DC |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |サポートされています |サポートされています |アムステルダム、チェンナイ、香港特別行政区、ロンドン、ムンバイ、サンパウロ、シリコン バレー、シンガポール、ワシントン DC |
| **Telecity Group** |サポートされています |サポートされています |アムステルダム |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |サポートされています |サポートされています |アムステルダム、サンパウロ |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)**  |サポートされています |サポートされています |ロンドン |
| **Telenor** |サポートされています |サポートされています |アムステルダム、ロンドン |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | サポートされています | サポートされています |アムステルダム、シカゴ、ダラス、ロンドン、パリ、ワシントン DC |
| **Telmex Uninet**| サポートされています | サポートされています | ダラス |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |サポートされています |サポートされています |メルボルン、シンガポール、シドニー |
| **[Telus](https://www.telus.com)** |サポートされています |サポートされています |モントリオール、シアトル、トロント |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |サポートされています |サポートされています |ケープタウン、ヨハネスブルグ |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | サポートされています | サポートされています | クアラルンプール |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |サポートされています |サポートされています |ダラス、ロサンゼルス|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |サポートされています |サポートされています |サンパウロ |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |サポートされています |サポートされています |アムステルダム、シカゴ、ダラス、香港特別行政区、ロンドン、ムンバイ、シリコン バレー、シンガポール、シドニー、東京、トロント、ワシントン DC |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |サポートされています |サポートされています |アムステルダム 2、ロンドン、シンガポール |
| **Vodafone Idea** | サポートされています | サポートされています | ムンバイ、ムンバイ 2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |サポートされています |サポートされています |アムステルダム、シカゴ、ダラス、デンバー、ロンドン、ロサンゼルス、モントリオール、ニューヨーク、パリ、シアトル、シリコン バレー、トロント、ワシントン DC |

 **+** は近日対応予定を表します

### <a name="national-cloud-environment"></a>各国のクラウド環境

### <a name="us-government-cloud"></a>米国政府のクラウド

| **サービス プロバイダー** | **Microsoft Azure** | **Office 365** | **場所** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |サポートされています |サポートされています |シカゴ、フェニックス、ワシントン DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |サポートされています |サポートされています |ニューヨーク、フェニックス、サンアントニオ |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |サポートされています |サポートされています |シカゴ、ダラス、ニューヨーク、シアトル、シリコン バレー、ワシントン DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |サポートされています |サポートされています |シカゴ、シリコン バレー、ワシントン DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |サポートされています | サポートされています | シカゴ、ダラス、サンアントニオ、シアトル、ワシントン DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |サポートされています |サポートされています |シカゴ、ダラス、ニューヨーク、シリコン バレー、ワシントン DC |

### <a name="china"></a>中国

| **サービス プロバイダー** | **Microsoft Azure** | **Office 365** | **場所** |
| --- | --- | --- | --- |
| **China Telecom** |サポートされています |サポートされていません |北京、北京 2、上海、上海 2 |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |サポートされています |サポートされていません |北京 2、上海 2 |

詳細については、 [中国の ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)に関するページを参照してください。

### <a name="germany"></a>ドイツ

| **サービス プロバイダー** | **Microsoft Azure** | **Office 365** | **場所** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |サポートされています |サポートされていません |フランクフルト |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |サポートされています |サポートされていません |フランクフルト |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |サポートされています |サポートされていません |ベルリン |
| **Interxion** |サポートされています |サポートされていません |フランクフルト |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |サポートされています  | サポートされていません | ベルリン |
| **T-Systems** |サポートされています |サポートされていません |ベルリン |

## <a name="connectivity-through-exchange-providers"></a>Exchange プロバイダー経由の接続

接続プロバイダーが上記のセクションの一覧にない場合でも、接続を作成できます。

* 接続プロバイダーが上の表に記載されているいずれかの Exchange に接続されているかどうかをその接続プロバイダーに確認します。 次のリンクから、Exchange プロバイダーが提供するサービスの詳細情報を収集できます。 一部の接続プロバイダーは既にイーサネット Exchange に接続されています。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* その接続プロバイダーに、選択したピアリングの場所までネットワークを拡張してもらいます。
  * 単一障害点がないように、接続プロバイダーが可用性の高い方法で接続を拡張していることを確認します。
* Microsoft に接続するために、接続プロバイダーとしてその Exchange で ExpressRoute 回線を要求します。
  * 「 [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md) 」の手順に従い、接続を構築します。

## <a name="connectivity-through-additional-service-providers"></a>追加のサービス プロバイダー経由の接続

| **接続プロバイダー** | **Exchange** | **場所** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |シンガポール |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix、Cologix | トロント、モントリオール |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |シアトル |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |ニューヨーク、ワシントン DC |
| **[アルテリア・ネットワークス株式会社](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |東京 |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |ダラス|
| **[Beanfield Metroconnect](https://www.beanfield.com/cloud-exchange/)** |Megaport |トロント|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | ロンドン |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | アムステルダム、フランクフルト、ロンドン、シンガポール、ワシントン DC |
| **[株式会社ブロードバンドタワー](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | 東京 |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix、Megaport | ダラス |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | 香港特別行政区 |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix、Megaport | フランクフルト、ハンブルク |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | アムステルダム | 
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | モントリオール、トロント |
| **[CoreAzure](http://coreazure.com/expressroute)**| Equinix | ロンドン |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | ダラス、シリコン バレー、ワシントン DC |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | アトランタ、シカゴ、ダラス、ロサンゼルス、ニューヨーク、ワシントン DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | ダラス |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | ロンドン、シンガポール、ワシントン DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | アムステルダム |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | ロンドン |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | アムステルダム |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | ケベック シティ |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | ワシントン DC |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | アムステルダム |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | ロンドン、スラウ |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | トロント |
| **LGA Telecom** |Equinix |シンガポール|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |香港特別行政区 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | シドニー |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | アムステルダム |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | ワシントン DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | ケープタウン、ヨハネスブルグ |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | ロンドン |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | アムステルダム、フランクフルト |
| **[Post](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | アムステルダム |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | アムステルダム、ダブリン、ロンドン、パリ |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | フランクフルト |  
| **Rogers** | Cologix、Equinix | モントリオール、トロント |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | シカゴ、ダラス、ロサンゼルス、ニューヨーク、シリコン バレー | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | ロンドン | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | アムステルダム | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | アムステルダム |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | アムステルダム |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | トロント | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | シンガポール |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | サンパウロ |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | ニューヨーク |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | シカゴ、シリコン バレー、ワシントン DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |シリコン バレー、シリコン バレー 2|
| **Zain** |Equinix |ロンドン|
| **[Zertia](https://www.zertia.es)**| レベル 3 | マドリッド |
| **[Zirro](https://zirro.com/services/)**| Cologix、Equinix | モントリオール、トロント |

## <a name="connectivity-through-datacenter-providers"></a>データセンター プロバイダー経由の接続

| **プロバイダー** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport、PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport、PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach、Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport、PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach、Megaport、PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport、PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach、Megaport、PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach、Megaport |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>国立研究教育ネットワーク (NREN) 経由の接続

| **プロバイダー**|
| --- |
| **AARNET**| 
| **DeIC、GÉANT を介して**|
| **GARR、GÉANT を介して**|
| **GÉANT**|
| **HEAnet、GÉANT を介して**|
| **Internet2**|
| **JISC**|
| **RedIRIS、GÉANT を介して**|
| **SINET**|
| **Surfnet、GÉANT を介して**|

* ご利用の接続プロバイダーがこの一覧にない場合は、そのプロバイダーが上に記載されている ExpressRoute Exchange パートナーに接続されているかどうかを確認してください。

## <a name="expressroute-system-integrators"></a>ExpressRoute システム インテグレーター
ネットワークの規模によっては、ニーズに合わせてプライベート接続を有効にするのは難しい場合があります。 次の表のいずれかのシステム インテグレーターを使用すると、ExpressRoute の利用開始に役立ちます。

| **システム インテグレーター** | **大陸** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | ヨーロッパ |
| **[Avanade Inc.](https://www.avanade.com/)** | アジア、ヨーロッパ、北米、南アメリカ |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | ヨーロッパ
| **[Ensyst](https://www.ensyst.com.au)** | アジア
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | 北米 |
| **[FlexManage](https://www.flexmanage.com/cloud)** | 北米 |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | 北米 |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | オーストラリア |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | オーストラリア |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | ヨーロッパ (ドイツ) |
| **[Nelite](https://www.nelite.com/offres-services/)** | ヨーロッパ |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | ヨーロッパ |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | アジア |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | ヨーロッパ |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 北米 |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | 北米 |
| **[sol-tec](https://www.sol-tec.com/services)** | ヨーロッパ |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | 南アメリカ |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | オーストラリア |

## <a name="next-steps"></a>次の手順
* ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
* すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "場所のマップ"
