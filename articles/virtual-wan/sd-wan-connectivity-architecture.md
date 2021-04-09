---
title: Virtual WAN と SD-WAN の接続とアーキテクチャ
titleSuffix: Azure Virtual WAN
description: プライベート SD-WAN と Azure Virtual WAN の相互接続について説明します
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sukishen
ms.openlocfilehash: ea9ddd05fe6fc745a3eefc29ab4f1d6aababc936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94564703"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Azure Virtual WAN を使用した SD-WAN 接続アーキテクチャ

Azure Virtual WAN は、単一の運用インターフェイスで多くのクラウド接続サービスやセキュリティ サービスを統合するネットワーク サービスです。 これらのサービスには、ブランチ (サイト間 VPN 経由)、リモート ユーザー (ポイント対サイト VPN)、プライベート (ExpressRoute) 接続、Vnet の推移的なクラウド内接続、VPN と ExpressRoute の相互接続、ルーティング、Azure ファイアウォール、プライベート接続の暗号化などが含まれます。

Azure Virtual WAN 自体はソフトウェア定義 WAN (SD-WAN) ですが、プレミスベースの SD-WAN テクノロジおよびサービスとのシームレスな相互接続も可能にするように設計されています。 このようなサービスの多くは、[Virtual WAN](virtual-wan-locations-partners.md) エコシステムと Azure ネットワーク マネージド サービス パートナー [(MSP)](../networking/networking-partners-msp.md) によって提供されます。 プライベート WAN を SD-WAN に変換する企業には、プライベート SD-WAN を Azure Virtual WAN と相互接続する際のオプションがあります。 企業は次のオプションから選択できます。

* 直接相互接続モデル
* NVA-in-VWAN-hub による直接相互接続モデル
* 間接相互接続モデル
* 任意のマネージド サービス プロバイダー ([MSP](../networking/networking-partners-msp.md)) を使用したマネージド ハイブリッド WAN モデル

このすべてのケースで、Virtual WAN と SD-WAN の相互接続は接続面ではほぼ同じですが、オーケストレーションおよび運用面で異なる場合があります。

## <a name="direct-interconnect-model"></a><a name="direct"></a>直接相互接続モデル

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct.png" alt-text="直接相互接続モデル":::

このアーキテクチャ モデルでは、SD-WAN ブランチの顧客構内機器 (CPE) は、IPsec 接続を介して Virtual WAN ハブに直接接続されます。 ブランチ CPE は、プライベート SD-WAN 経由で他のブランチに接続することも、ブランチ間の接続に Virtual WAN を利用することもできます。 Azure のワークロードにアクセスする必要があるブランチは、Virtual WAN ハブで終端する IPsec トンネルを介して Azure に直接、安全にアクセスできます。

SD-WAN CPE パートナーは、それぞれの CPE デバイスからの、通常は面倒でエラーが発生しやすい IPsec 接続を自動化するために自動化を有効にすることができます。 自動化により、SD-WAN コントローラーは、Virtual WAN API を介して Azure と通信して Virtual WAN サイトを構成し、必要な IPsec トンネル構成をブランチ CPE にプッシュできます。 さまざまな SD-WAN パートナーによる Virtual WAN 相互接続の自動化については、[自動化のガイドライン](virtual-wan-configure-automation-providers.md)に関する記事をご覧ください。

トラフィック最適化とパス選択は、SD-WAN CPE で引き続き実装され、適用されます。 

このモデルでは、Virtual WAN への接続が IPsec 経由であり、IPsec VPN が Virtual WAN VPN ゲートウェイで終端するため、リアルタイム トラフィックの特性に基づくベンダー独自のトラフィック最適化はサポートされない場合があります。 たとえば、ブランチ デバイスは、別の SD-WAN ノードとさまざまなネットワーク パケット情報を交換します。したがって、ブランチ デバイスによって、優先順位付けされたさまざまなトラフィックに使用する最適なリンクがブランチで動的に特定されるため、ブランチ CPE での動的パス選択が実現可能になります。 この機能は、ラスト マイルの最適化 (最も近い Microsoft POP への分岐) が必要な領域で役立つ場合があります。

Virtual WAN では、ユーザーは Azure パス選択を利用できます。これは、ブランチ CPE から Virtual WAN VPN ゲートウェイへの複数の ISP リンクでのポリシーベースのパス選択です。 Virtual WAN では、同じ SD-WAN ブランチ CPE から複数のリンク (パス) を設定できます。各リンクは、SD-WAN CPE の一意のパブリック IP から Azure Virtual WAN VPN ゲートウェイの 2 つの異なるインスタンスへのデュアル トンネル接続を表します。 SD-WAN ベンダーは、CPE リンク上のポリシー エンジンによって設定されたトラフィック ポリシーに基づいて、Azure への最適なパスを実装できます。 Azure エンドでは、すべての受信接続が同等に扱われます。

## <a name="direct-interconnect-model-with-nva-in-vwan-hub"></a><a name="direct"></a>NVA-in-VWAN-hub による直接相互接続モデル

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct-nva.png" alt-text="NVA-in-VWAN-hub による直接相互接続モデル":::

このアーキテクチャ モデルでは、サードパーティ製の[ネットワーク仮想アプライアンス (NVA) を仮想ハブに直接](./about-nva-hub.md)デプロイできます。 これにより、ブランチ CPE を仮想ハブにある同じブランドの NVA に接続したいお客様は、Azure ワークロードに接続するとき、独自のエンドツーエンド SD-WAN 機能を活用できるようになります。 

数社の Virtual WAN パートナーが、デプロイ プロセスの一環として NVA を自動的に構成するエクスペリエンスを提供できるよう取り組んできました。 NVA が仮想ハブにプロビジョニングされた後は、NVA で必要になる可能性がある追加の構成は NVA パートナー ポータルまたは管理アプリケーションを使用して行う必要があります。 NVA への直接アクセスは使用できません。 Azure Virtual WAN ハブに直接デプロイできる NVA は、仮想ハブで使用するために特別に設計されています。 NVA-in-VWAN-hub をサポートするパートナーとそのデプロイ ガイドについては、[Virtual WAN パートナー](virtual-wan-locations-partners.md#partners-with-integrated-virtual-hub-offerings)に関する記事を参照してください。

トラフィック最適化とパス選択は、SD-WAN CPE で引き続き実装され、適用されます。
このモデルでは、Virtual WAN への接続がハブ内の SD-WAN NVA 経由になるため、リアルタイムのトラフィック特性に基づくベンダー独自のトラフィック最適化がサポートされます。

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>間接相互接続モデル

:::image type="content" source="./media/sd-wan-connectivity-architecture/indirect.png" alt-text="間接相互接続モデル":::

このアーキテクチャ モデルでは、SD-WAN ブランチの CPE は Virtual WAN ハブに間接的に接続されます。 図に示すように、SD-WAN 仮想 CPE はエンタープライズ VNet にデプロイされます。 この仮想 CPE は、IPsec を使用して Virtual WAN ハブに接続されます。 仮想 CPE は、Azure への SD-WAN ゲートウェイとして機能します。 Azure のワークロードにアクセスする必要があるブランチは、v-CPE ゲートウェイ経由でアクセスできます。

Azure への接続は v-CPE ゲートウェイ (NVA) 経由であるため、Azure ワークロード VNet へのトラフィックと Azure ワークロード VNet から他の SD-WAN ブランチへのトラフィックはすべて NVA を経由します。 このモデルでは、高可用性、スケーラビリティ、ルーティングなど、SD-WAN NVA の管理と運用はユーザーが担当します。
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>マネージド ハイブリッド WAN モデル

:::image type="content" source="./media/sd-wan-connectivity-architecture/hybrid.png" alt-text="マネージド ハイブリッド WAN モデル":::

このアーキテクチャ モデルでは、企業はマネージ ドサービス プロバイダー (MSP) パートナーが提供するマネージド SD-WAN サービスを利用できます。 このモデルは、上記の直接モデルまたは間接モデルに似ています。 ただし、このモデルでは、SD-WAN の設計、オーケストレーション、運用は、SD-WAN プロバイダーによって行われます。

[Azure ネットワーク MSP パートナー](../networking/networking-partners-msp.md)は、[Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) を使用して、エンタープライズ顧客の Azure サブスクリプションに SD-WAN と Virtual WAN サービスを実装し、顧客に代わってエンド ツー エンドのハイブリッド WAN を運用できます。 これらの MSP は、Azure ExpressRoute を Virtual WAN に実装し、エンド ツー エンドのマネージド サービスとして運用することもできます。

## <a name="additional-information"></a>追加情報

* [FAQ](virtual-wan-faq.md)
* [リモート接続の解決](work-remotely-support.md)