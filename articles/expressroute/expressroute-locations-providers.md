---
title: "場所と接続プロバイダー: Azure ExpressRoute | Microsoft Docs"
description: "この記事では、サービスが提供されている場所と Azure リージョンに接続する方法の詳細について説明します。 場所ごとにソートされています。"
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2017
ms.author: kaanan
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: bc9803b9ab5c4c6329ffa5933cbc8ff758faa20b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

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

| **地理的リージョン** | **Azure リージョン** | **ExpressRoute の場所** |
| --- | --- | --- |
| **北米** |米国東部、米国西部、米国東部 2、米国西部 2、米国中部、米国中南部、米国中北部、米国中西部、カナダ中部、カナダ東部 |アトランタ、シカゴ、ダラス、デンバー、ラスベガス、ロサンゼルス、マイアミ、ニューヨーク、サンアントニオ、シアトル、シリコン バレー、ワシントン DC、モントリオール、ケベックシティ、トロント |
| **南アメリカ** |ブラジル南部 |サンパウロ |
| **ヨーロッパ** |北ヨーロッパ、西ヨーロッパ、英国西部、英国南部 |アムステルダム、ダブリン、ロンドン、ニューポート (ウェールズ)、パリ |
| **アジア** |東アジア、東南アジア |香港、シンガポール |
| **日本** |西日本、東日本 |大阪、東京 |
| **オーストラリア** |オーストラリア東部、オーストラリア南東部 |メルボルン、シドニー |
| **インド** |インド西部、インド中部、インド南部 |チェンナイ、ムンバイ |
| **韓国** |韓国中部、韓国南部 |釜山、ソウル |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>各国のクラウドのリージョンと地理的境界
次の表は、国内のクラウドのリージョンと地政学的境界に関する情報をまとめたものです。

| **地理的リージョン** | **Azure リージョン** | **ExpressRoute の場所** |
| --- | --- | --- |
| **米国政府のクラウド** |米国政府アイオワ、米国政府バージニア、US DoD 中部、US DoD 東部  |シカゴ、ダラス、ニューヨーク、シアトル、シリコン バレー、ワシントン DC |
| **中国** |中国北部、中国東部 |北京、上海 |
| **ドイツ** |ドイツ中部、ドイツ東部 |ベルリン、フランクフルト |

標準の ExpressRoute SKU では、地理的リージョン間の接続はサポートされていません。 グローバル接続に対応するには、ExpressRoute Premium アドオンを有効にする必要があります。 国内のクラウド環境には接続できません。 国内のクラウド環境に接続する必要がある場合、接続プロバイダーにご相談ください。

## <a name="locations"></a>接続プロバイダーの場所

次の表に、接続する場所と、各場所のサービス プロバイダーを示します。 サービス プロバイダーと、そのサービス プロバイダーがサービスを提供できる場所を確認する場合は、[サービス プロバイダー別の場所](expressroute-locations.md#locations)に関するページを参照してください。 


### <a name="production-azure"></a>運用 Azure
| **場所** | **サービス プロバイダー** |
| --- | --- |
| **アムステルダム** |Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT、InterCloud、Internet Solutions - Cloud Connect、Interxion、KPN、Level 3 Communications、Megaport、Orange、Tata Communications、TeleCity Group、Telefonica、Telenor、Verizon、Zayo Group |
| **アトランタ** |Equinix |
| **釜山** |LG CNS |
| **チェンナイ** | Airtel+、Global CloudXchange (GCX)、SIFY、Tata Communications |
| **シカゴ** |AT&T NetBond、Comcast、Coresite、Equinix、Level 3 Communications、Megaport、Verizon、Zayo Group |
| **ダラス** |Aryaka Networks、AT&T NetBond、Cologix、Equinix、Level 3 Communications、Megaport、Verizon、Zayo Group+ |
| **デンバー** |CoreSite |
| **ダブリン** |Colt、Interxion、Telecity Group |
| **香港** |Aryaka Networks、British Telecom、China Telecom Global、Equinix、Megaport、Orange、PCCW Global Limited、Tata Communications、Verizon |
| **ラスベガス** |Level 3 Communications、Megaport |
| **ロンドン** |AT&T NetBond、British Telecom、Colt、Equinix、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、Megaport、MTN、NTT Communications、Orange、Tata Communications、Telecity Group、Telehouse - KDDI、Telenor、Verizon、Vodafone、Zayo Group+ |
| **ロサンゼルス** |CoreSite、Equinix、Megaport、NTT、Zayo Group |
| **メルボルン** |AARNet、Equinix、Megaport、NEXTDC、Telstra Corporation |
| **マイアミ** |C3ntro+、Megaport、Neutrona Networks |
| **モントリオール** |Bell Canada、Cologix |
| **ムンバイ** |Airtel+、Global CloudXchange (GCX)、Sify、Tata Communications |
| **ニューヨーク** |CoreSite、Equinix、Megaport、Zayo Group |
| **ニューポート (ウェールズ)** |Next Generation Data |
| **大阪** |Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、NTT SmartConnect、Softbank |
| **パリ** |Colt、Interxion、Equinix、Orange |
| **ケベック シティ** | Megaport |
| **サンアントニオ** |Megaport |
| **サンパウロ** |Ascenty Data Centers+、Equinix、Level 3 Communications、Neutrona Networks、Telefonica、UOLDIVEO |
| **シアトル** |Equinix、Level 3 Communications、Megaport |
| **ソウル** |KINX、LG CNS、Sejong Telecom |
| **シリコン バレー** |Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink+、Comcast、Console、Coresite、Equinix、Level 3 Communications、Megaport、Orange、Tata Communications、Verizon、Zayo Group |
| **シンガポール** |Aryaka Networks、AT&T NetBond、British Telecom、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Verizon |
| **シドニー** |AARNet、AT&T NetBond、British Telecom、Equinix、Megaport、NEXTDC、Orange、Telstra Corporation、Verizon |
| **東京** |Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、Softbank、Verizon |
| **トロント** |AT&T NetBond、Bell Canada、Cologix、Console、Equinix、Megaport、Telus、Zayo Group |
| **ワシントン DC** |Aryaka Networks、AT&T NetBond、British Telecom、Comcast、Coresite、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、Tata Communications、Verizon、Zayo Group |

 **+** は近日対応予定を表します

### <a name="national-cloud-environments"></a>国内のクラウド環境

### <a name="us-government-cloud"></a>米国政府のクラウド
| **場所** | **サービス プロバイダー** |
| --- | --- |
| **シカゴ** |AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **ダラス** |Equinix、Megaport、Verizon |
| **ニューヨーク** |Equinix、Level 3 Communications+、Verizon |
| **シリコン バレー** | Equinix、Level 3 Communications |
| **シアトル** | Equinix |
| **ワシントン DC** |AT&T NetBond、Equinix、Level 3 Communications、Verizon |

### <a name="china"></a>中国
| **場所** | **サービス プロバイダー** |
| --- | --- |
| **北京** |China Telecom |
| **上海** |China Telecom |

詳細については、 [中国の ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>ドイツ
| **場所** | **サービス プロバイダー** |
| --- | --- |
| **ベルリン** |Colt+、e-shelter、Megaport+、T-Systems |
| **フランクフルト** |Colt、Equinix、Interxion |

## <a name="c1partners"></a>Exchange プロバイダー経由の接続
接続プロバイダーが上記のセクションの一覧にない場合でも、接続を作成できます。

* 接続プロバイダーが上の表に記載されているいずれかの Exchange に接続されているかどうかをその接続プロバイダーに確認します。 次のリンクから、Exchange プロバイダーが提供するサービスの詳細情報を収集できます。 一部の接続プロバイダーは既にイーサネット Exchange に接続されています。
  * [Cologix](http://www.cologix.com/)
  * [Console](https://www.consoleconnect.com/partners/cloudsaas/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](http://www.interxion.com/)
  * [NextDC](http://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* その接続プロバイダーに、選択したピアリングの場所までネットワークを拡張してもらいます。
  * 単一障害点がないように、接続プロバイダーが可用性の高い方法で接続を拡張していることを確認します。
* Microsoft に接続するために、接続プロバイダーとしてその Exchange で ExpressRoute 回線を要求します。
  * 「 [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md) 」の手順に従い、接続を構築します。

## <a name="c1partners"></a>その他のサービス プロバイダー経由の接続
| **場所** | **Exchange** | **接続プロバイダー** |
| --- | --- | --- |
| **アムステルダム** | Equinix、Telecity | Eurofiber 、Fastweb S.p.A、Nianet |
| **シカゴ** | Equinix | Lightower、Windstream |
| **ダラス** | Equinix、Megaport | C3ntro Telecom、Cox Business、Data Foundry、Transtelco |
| **フランクフルト** | Telecity | Nianet、QSC AG |
| **香港** | Equinix | Macroview Telecom |
| **ロンドン** | Equinix、euNetworks、Telecity | Bezeq International Ltd.、Epsilon、Exponential E、HSO、NexGen Networks、Tamares Telecom、Zain |
| **ロサンゼルス** | Equinix |Transtelco |
| **マドリッド** | Level3 | Zertia |
| **モントリオール** | Cologix、Equinix | Airgate Technologies. Inc、Cogeco Peer 1、Rogers、Zirro |
| **ニューヨーク** |Equinix、Megaport | Altice Business、Lightower、Webair |
| **シアトル** |Equinix | Alaska Communications |
| **シリコン バレー** |Equinix | Cox Business、Windstream |
| **シンガポール** |Equinix |1CLOUDSTAR、Epsilon Telecommunications Limited、LGA Telecom、United Information Highway (UIH) |
| **スラウ** | Equinix | HSO|
| **シドニー** | Megaport | Macquarie Telecom Group|
| **東京** | Equinix | アルテリア・ネットワークス株式会社、株式会社ブロードバンドタワー |
| **トロント** | Equinix | Airgate Technologies. Inc、Cogeco Peer 1、Rogers、Thinktel、Zirro|
| **ワシントン DC** |Equinix | Altice Business、Gtt Communications Inc、Epsilon、Lightower、Masergy、Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute システム インテグレーター
ネットワークの規模によっては、ニーズに合わせてプライベート接続を有効にするのは難しい場合があります。 次の表のいずれかのシステム インテグレーターを使用すると、ExpressRoute の利用開始に役立ちます。

| **大陸** | **システム インテグレーター** |
| --- | --- |
| **アジア** |Avanade Inc.、OneAs1a |
| **オーストラリア** | Ensyst、IT Consultancy、MOQdigital、Vigilant.IT |
| **ヨーロッパ** |Avanade Inc.、Altogee、Bright Skies GmbH、Inframon、MSG Services、New Signature、Nelite、Orange Networks、sol-tec |
| **北米** |Avanade Inc.、Equinix Professional Services、FlexManage、Perficient、Presidio |
| **南アメリカ** |Avanade Inc. |
## <a name="next-steps"></a>次のステップ
* ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
* すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "場所のマップ"

