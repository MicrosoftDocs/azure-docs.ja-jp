---
title: '場所と接続プロバイダー: Azure ExpressRoute | Microsoft Docs'
description: この記事では、サービスが提供されている場所と Azure リージョンに接続する方法の詳細について説明します。 場所ごとにソートされています。
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/02/2019
ms.author: jaredr80
ms.openlocfilehash: 90b18a2181dae7474141c478725b0c6f829b2ea8
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537981"
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

次の表に、接続する場所と、各場所のサービス プロバイダーを示します。 サービス プロバイダーと、そのサービス プロバイダーがサービスを提供できる場所を確認する場合は、[サービス プロバイダー別の場所](expressroute-locations.md#locations)に関するページを参照してください。 

**ローカル Azure リージョン**は、それぞれのピアリングの場所で [ExpressRoute Local](expressroute-faqs.md) がアクセスできるリージョンです。 **該当なし**は、そのピアリングの場所で ExpressRoute Local が使用できないことを示します。


### <a name="production-azure"></a>運用 Azure
| **Location** | **ピアリングの場所の所有者** | **ローカル Azure リージョン** | **サービス プロバイダー** |
| --- | --- | --- | --- |
| **アムステルダム** | Equinix | 西ヨーロッパ | Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT、InterCloud、Interxion、KPN、IX Reach、Level 3 Communications、Megaport、NTT Communications、Orange、Tata Communications、TeleCity Group、Telefonica、Telenor、Telia Carrier、Verizon、Zayo |
| **アムステルダム 2** | Interxion | 西ヨーロッパ | DE CIX、Interxion、Vodafone |
| **アトランタ** | Equinix | 該当なし | Equinix、Megaport |
| **オークランド** | Vocus | 該当なし | Devoli、Megaport |
| **釜山** |LG CNS | 韓国南部 | LG CNS |
| **キャンベラ** | CDC | オーストラリア中部 | CDC |
| **キャンベラ2** | CDC | オーストラリア中部 2| CDC |
| **ケープタウン** | Teraco | 南アフリカ西部 | Internet Solutions - Cloud Connect、Liquid Telecom、Teraco |
| **チェンナイ** | Tata Communications | インド南部 | Global CloudXchange (GCX)、SIFY、Tata Communications |
| **チェンナイ 2** | Airtel | インド南部 | Airtel |
| **シカゴ** | Equinix | 米国中北部 | Aryaka Networks、AT&T NetBond、CenturyLink Cloud Connect、Cologix、Comcast、Coresite、Equinix、InterCloud、Internet2、Level 3 Communications、Megaport、PacketFabric、PCCW Global Limited、Sprint、Telia Carrier、Verizon、Zayo |
| **ダラス** | Equinix | 該当なし | Aryaka Networks、AT&T NetBond、Cologix、Equinix、Internet2、Level 3 Communications、Megaport、Neutrona Networks、Telmex Uninet、Telia Carrier、Transtelco、Verizon、Zayo|
| **デンバー** | CoreSite | 米国中西部 | CoreSite、Megaport、Zayo |
| **ドバイ** | Etisalat UAE | アラブ首長国連邦北部 | Etisalat UAE |
| **ドバイ 2** | du datamena | アラブ首長国連邦北部 | du datamena、 Orixcom |
| **ダブリン** | Equinix | 北ヨーロッパ | Colt、eir、Equinix、Interxion、Megaport |
| **フランクフルト** | Interxion | 該当なし | DE CIX、Interxion |
| **香港特別行政区** | Equinix | 東アジア | Aryaka Networks、British Telecom、CenturyLink Cloud Connect、Chief Telecom、China Telecom Global、Equinix、Megaport、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Verizon |
| **ヨハネスブルグ** | Teraco | 南アフリカ北部 | British Telecom、Internet Solutions - Cloud Connect、Liquid Telecom、Teraco |
| **クアラルンプール** | TIME dotCom | 該当なし | TIME dotCom |
| **ラスベガス** | Switch | 該当なし | CenturyLink Cloud Connect、Megaport |
| **ロンドン** | Equinix | 英国南部 | AT&T NetBond、British Telecom、Colt、Equinix、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、Megaport、MTN、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telehouse - KDDI、Telenor、Telia Carrier、Verizon、Vodafone、Zayo |
| **London2** | Telehouse | 英国南部 | IX Reach、Equinix |
| **ロサンゼルス** | CoreSite | 該当なし | CoreSite、Equinix、Megaport、Neutrona Networks、NTT、Zayo |
| **マルセイユ** |Interxion | フランス南部 | Interxion、Jaguar Network |
| **メルボルン** | NextDC | オーストラリア南東部 | AARNet、Devoli、Equinix、Megaport、NEXTDC、Optus、Telstra Corporation、TPG Telecom |
| **マイアミ** | Equinix | 該当なし | C3ntro+、Equinix、Megaport、Neutrona Networks |
| **モントリオール** | Cologix | 該当なし | Bell Canada、Cologix、Telus、Zayo |
| **ムンバイ** | Tata Communications | インド西部 | Global CloudXchange (GCX)、Reliance Jio、Sify、Tata Communications、Verizon |
| **ムンバイ 2** | Airtel | インド西部 | Airtel、Sify、Vodafone Idea |
| **ニューヨーク** | Equinix | 該当なし | CenturyLink Cloud Connect、Coresite、Equinix、InterCloud、Megaport、Packet、Zayo |
| **ニューポート (ウェールズ)** | Next Generation Data | 英国西部 | British Telecom、Colt、Level 3 Communications、Next Generation Data |
| **大阪** | Equinix | 西日本 | Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、NTT SmartConnect、Softbank |
| **パリ** | Interxion | フランス中部 | Colt、Equinix、Intercloud、Interxion、Orange、Telia Carrier、Zayo |
| **パース** | NextDC | 該当なし | Megaport、NextDC |
| **ケベック シティ** | 4Degrees | カナダ東部 | Bell Canada、Megaport |
| **サンアントニオ** | CyrusOne | 米国中南部 | CenturyLink Cloud Connect、Megaport |
| **サンパウロ** | Equinix | ブラジル南部 | Aryaka Networks、Ascenty Data Centers、British Telecom、Equinix、Level 3 Communications、Neutrona Networks、Orange、Tata Communications、Telefonica、UOLDIVEO |
| **シアトル** | Equinix | 米国西部 2 | Aryaka Networks、Equinix、Level 3 Communications、Megaport、Telus、Zayo |
| **ソウル** | KINX | 韓国中部 | KINX、LG CNS、Sejong Telecom |
| **シリコン バレー** | Equinix | 米国西部 | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Comcast、Coresite、Equinix、InterCloud、IX Reach、Packet、PacketFabric、Level 3 Communications、Megaport、Orange、Sprint、Tata Communications、Verizon、Zayo |
| **シリコン バレー2** | Coresite | 米国西部 | Coresite | 
| **シンガポール** | Equinix | 東南アジア | Aryaka Networks、AT&T NetBond、British Telecom、Epsilon Global Communications、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Telstra Corporation、Verizon、Vodafone |
| **シンガポール 2** | Global Switch | 東南アジア | Colt、Epsilon Global Communications、Megaport、SingTel |
| **シドニー** | Equinix | オーストラリア東部 | AARNet、AT&T NetBond、British Telecom、Devoli、Equinix、Kordia、Megaport、NEXTDC、NTT Communications、Optus、Orange、Telstra Corporation、TPG Telecom、Verizon |
| **台北** | Chief Telecom | 該当なし | Chief Telecom、FarEasTone |
| **東京** | Equinix | 東日本 | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、NTT EAST、Orange、Softbank、Verizon |
| **トロント** | Cologix | カナダ中部 | AT&T NetBond、Bell Canada、CenturyLink Cloud Connect、Cologix、Equinix、IX Reach Megaport、Telus、Verizon、Zayo |
| **ワシントン DC** | Equinix | 米国東部、米国東部 2 | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Cologix、Comcast、Coresite、Equinix、Internet2、InterCloud、Level 3 Communications、Megaport、Neutrona Networks、NTT Communications、Orange、PacketFabric、Sprint、Tata Communications、Telia Carrier、Verizon、Zayo |
| **ワシントン DC2** | Coresite | 米国東部、米国東部 2 |Coresite | 
| **チューリッヒ** | Interxion | 該当なし | Interxion |

 **+** は近日対応予定を表します

### <a name="national-cloud-environments"></a>国内のクラウド環境

### <a name="us-government-cloud"></a>米国政府のクラウド
| **Location** | **サービス プロバイダー** |
| --- | --- |
| **シカゴ** |AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **ダラス** |Equinix、Megaport、Verizon |
| **ニューヨーク** |Equinix、CenturyLink Cloud Connect、Verizon |
| **Phoenix** | AT&T NetBond、CenturyLink Cloud Connect、Megaport |
| **サンアントニオ** | CenturyLink Cloud Connect、Megaport |
| **シリコン バレー** | Equinix、Level 3 Communications、Verizon |
| **シアトル** | Equinix、Megaport |
| **ワシントン DC** |AT&T NetBond、Equinix、Level 3 Communications、Megaport、Verizon |

### <a name="china"></a>中国
| **Location** | **サービス プロバイダー** |
| --- | --- |
| **北京** |China Telecom |
| **北京 2** | China Telecom、GDS |
| **上海** |China Telecom |
| **上海 2** | China Telecom、GDS |

詳細については、 [中国の ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>ドイツ
| **場所** | **サービス プロバイダー** |
| --- | --- |
| **ベルリン** |e-shelter、Megaport+、T-Systems |
| **フランクフルト** |Colt、Equinix、Interxion |

## <a name="c1partners"></a>Exchange プロバイダー経由の接続
接続プロバイダーが上記のセクションの一覧にない場合でも、接続を作成できます。

* 接続プロバイダーが上の表に記載されているいずれかの Exchange に接続されているかどうかをその接続プロバイダーに確認します。 次のリンクから、Exchange プロバイダーが提供するサービスの詳細情報を収集できます。 一部の接続プロバイダーは既にイーサネット Exchange に接続されています。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* その接続プロバイダーに、選択したピアリングの場所までネットワークを拡張してもらいます。
  * 単一障害点がないように、接続プロバイダーが可用性の高い方法で接続を拡張していることを確認します。
* Microsoft に接続するために、接続プロバイダーとしてその Exchange で ExpressRoute 回線を要求します。
  * 「 [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md) 」の手順に従い、接続を構築します。

## <a name="c1partners"></a>その他のサービス プロバイダー経由の接続
| **Location** | **Exchange** | **接続プロバイダー** |
| --- | --- | --- |
| **アムステルダム** | Equinix、Telecity | BICS、CloudXpress、Eurofiber、Fastweb S.p.A、Gulf Bridge International、MainOne、Nianet、Post、Proximus、TDC Erhverv、Telecom Italia Sparkle、Telia |
| **アトランタ** | Equinix| Crown Castle
| **ケープタウン** | Teraco | MTN |
| **シカゴ** | Equinix | Crown Castle、Spectrum Enterprise、Windstream |
| **ダラス** | Equinix、Megaport | Axtel、C3ntro Telecom、Cox Business、Crown Castle、Data Foundry、Spectrum Enterprise、Transtelco |
| **フランクフルト** | Telecity | BICS、Cinia、Nianet、QSC AG |
| **ハンブルク** | Equinix | Cinia |
| **香港特別行政区** | Equinix | Chief、Macroview Telecom |
| **ヨハネスブルグ** | Teraco | MTN |
| **ロンドン** | BICS、Equinix、euNetworks、Telecity | Bezeq International Ltd.、CoreAzure、Epsilon Telecommunications Limited、Exponential E、HSO、NexGen Networks、Proximus、Tamares Telecom、Zain |
| **ロサンゼルス** | Equinix |Crown Castle、Spectrum Enterprise、Transtelco |
| **マドリッド** | Level3 | Zertia |
| **モントリオール** | Cologix、Equinix | Airgate Technologies, Inc.Cogeco Peer 1、Rogers、Zirro |
| **ニューヨーク** |Equinix、Megaport | Altice Business、Crown Castle、Spectrum Enterprise、Webair |
| **パリ** | Equinix | Proximus |
| **ケベック シティ** | Megaport | Fibrenoire |
| **サンパウロ** | Equinix | Venha Pra Nuvem |
| **シアトル** |Equinix | Alaska Communications |
| **シリコン バレー** |Coresite、Equinix | Cox Business、Spectrum Enterprise、Windstream、X2nsat Inc. |
| **シンガポール** |Equinix |1CLOUDSTAR、BICS、Epsilon Telecommunications Limited、LGA Telecom、United Information Highway (UIH) |
| **スラウ** | Equinix | HSO|
| **シドニー** | Megaport | Macquarie Telecom Group|
| **東京** | Equinix | アルテリア・ネットワークス株式会社、株式会社ブロードバンドタワー |
| **トロント** | Equinix、Megaport | Airgate Technologies inc.、Beanfield Metroconnect、Cogeco Peer 1、IVedha Inc、Rogers、Thinktel、Zirro|
| **ワシントン DC** |Equinix | Altice Business、BICS、Cox Business、Crown Castle、Gtt Communications Inc、Epsilon Telecommunications Limited、Masergy、Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute システム インテグレーター
ネットワークの規模によっては、ニーズに合わせてプライベート接続を有効にするのは難しい場合があります。 次の表のいずれかのシステム インテグレーターを使用すると、ExpressRoute の利用開始に役立ちます。

| **大陸** | **システム インテグレーター** |
| --- | --- |
| **アジア** |Avanade Inc.、OneAs1a |
| **オーストラリア** | Ensyst、IT Consultancy、MOQdigital、Vigilant.IT |
| **ヨーロッパ** |Avanade Inc.、Altogee、Bright Skies GmbH、Inframon、MSG Services、New Signature、Nelite、Orange Networks、sol-tec |
| **北米** |Avanade Inc.、Equinix Professional Services、FlexManage、Lightstream、Perficient、Presidio |
| **南アメリカ** |Avanade Inc.、Venha Pra Nuvem |
## <a name="next-steps"></a>次の手順
* ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
* すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "場所のマップ"
