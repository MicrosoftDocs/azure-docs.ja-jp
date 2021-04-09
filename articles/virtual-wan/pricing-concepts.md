---
title: Virtual WAN の価格について
titleSuffix: Azure Virtual WAN
description: この記事では、Virtual WAN の価格に関する一般的な質問について説明します
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 13451291544f704000ab61d41ed5014fa69298e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100517690"
---
# <a name="about-virtual-wan-pricing"></a>Virtual WAN の価格について

Azure Virtual WAN では、複数のネットワーク サービスとセキュリティサービスが統一フレームワークとしてまとめられます。 これはハブとスポークのアーキテクチャに基づいています。ハブは Microsoft によって管理され、ハブ内で VPN、ExpressRoute、ユーザー VPN (ポイント対サイト)、ファイアウォール、ルーティングなどの各種サービスが提供されます。

Virtual WAN の各サービスに価格が設定されています。 そのため、単一の価格提案は Virtual WAN には当てはまりません。 [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)には、Virtual WAN でプロビジョニングするサービスに基づいてコストを導き出すためのメカニズムが用意されています。 この記事では、Virtual WAN の価格に関してよく寄せられる質問について説明します。

>[!NOTE]
>現在の価格に関する情報については、「[Virtual WAN の価格](https://azure.microsoft.com/pricing/details/virtual-wan/)」をご覧ください。 「Virtual WAN の価格」ページではリージョン間 (大陸内または大陸間) の料金 ([Azure のデータ転送料金](https://azure.microsoft.com/pricing/details/bandwidth/)) が適用されるため、ハブ間 (ハブからハブ) の料金は示されていません。

## <a name="common-pricing-questions"></a><a name="questions"></a>価格に関する一般的な質問

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>スケール ユニットとは何ですか?

**スケール ユニット** によって、仮想ハブ内のサイト間 (S2S)、ポイント対サイト (P2S)、および ExpressRoute (ER) の合計容量に対するユニットが指定されます。 次に例を示します。

* **1 S2S VPN スケール ユニット** は、仮想ハブにおける合計容量が 500 Mbps の VPN ゲートウェイ (回復性のために 2 つのインスタンスがデプロイされます) を意味し、1 時間あたり $0.361 の料金がかかります。
* **1 ER スケール ユニット** は、仮想ハブにおける合計 2 Gbps の ER ゲートウェイを意味し、1 時間あたり $0.42 の料金がかかります。
* **5 ER スケール ユニット** は、仮想ハブ VNet 内の合計 10 Gbps の ER ゲートウェイを意味し、1 時間あたり $0.42*5 の料金がかかります。 ER のスケール ユニット 6 から 10 では、1 時間あたり $0.25 の増分が発生します。

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>接続ユニットとは何ですか?

Azure ゲートウェイに対するすべてのオンプレミスまたは Microsoft 以外のエンドポイント接続には、**接続ユニット** が適用されます。 サイト間 VPN の場合、この値はブランチを意味します。 ユーザー VPN (ポイント対サイト) の場合、この値はリモート ユーザーを意味します。 ExpressRoute の場合、この値は ExpressRoute 回線接続を意味します。<br>次に例を示します。

* 仮想ハブの Azure VPN に接続する 1 つのブランチ接続には、1 時間あたり $0.05 の料金がかかります。 したがって、100 個のブランチ接続が Azure 仮想ハブに接続する場合は、1 時間あたり $0.05*100 の料金がかかります。

* 2 つの ExpressRoute 回線接続が仮想ハブに接続する場合、1 時間あたり $0.05*2 の料金がかかります。

* 3 つのリモート ユーザー接続が Azure 仮想ハブの P2S ゲートウェイに接続する場合、1 時間あたり $0.03*3 の料金がかかります。

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>データ転送の料金はどのように計算されますか?

* Azure に入ってくるトラフィックは課金されません。 (VPN、ExpressRoute、またはポイント対サイト ユーザー VPN 接続を介して) Azure から出ていくトラフィックには、標準の [Azure データ転送料金](https://azure.microsoft.com/pricing/details/bandwidth/)が適用されます。

* 1 つの Virtual WAN ハブと、ソース ハブとは異なるリージョンにあるリモート Virtual WAN ハブまたは VNet 間のデータ転送料金については、ハブから出ていくトラフィックに対してデータ転送料金が適用されます。 例:米国東部ハブから出ていくトラフィックには $0.02/GB の料金がかかり、米国西部ハブに送信されます。 米国西部ハブに入ってくるトラフィックには料金がかかりません。 すべてのハブ間トラフィックには、リージョン間 (大陸内/大陸間) 料金 [Azure データ転送料金](https://azure.microsoft.com/pricing/details/bandwidth/)が適用されます。 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Standard ハブの料金と Standard ハブの処理料金の違いは何ですか?

Virtual WAN には、次の 2 種類があります。

* **Basic Virtual WAN** では、ユーザーが複数のハブをデプロイし、VPN サイト間接続を利用できます。 Basic Virtual WAN には、フル メッシュ ハブ、ExpressRoute 接続、ユーザー VPN またはポイント対サイト VPN 接続、VNet 間のトランジット接続、VPN と ExpressRoute のトランジット接続、または Azure Firewall などの高度な機能はありません。Basic Virtual WAN では、ハブに対して基本料金やデータ処理料金は発生しません。

* **Standard Virtual WAN** には、フル メッシュ ハブ、ExpressRoute 接続、ユーザー VPN またはポイント対サイト VPN 接続、VNet 間のトランジット接続、VPN と ExpressRoute のトランジット接続、Azure Firewall などの高度な機能が備わっています。仮想ハブのルーティングはすべて、仮想ハブ内で複数のサービスを実現するルーターによって提供されます。 ハブには 1 時間あたり $0.25 の基本料金がかかります。 また、VNet 間のトランジット接続に対する仮想ハブ ルーター内でのデータ処理にも料金がかかります。 次の図を参照してください。

 下の **例** の図には、2 つの VNet、VNET 1 と VNET 2 があります。 VNET 1 の VM から VNET 2 の別の VM に 1 Gbps のデータが送信されているとします。 次の料金が適用されます。

* 仮想ハブの基本料金: 1 時間あたり $0.25

* 仮想ハブのデータ処理料金: 1 Gbps に対して $0.02/GB

**例**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="例":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。

* 現在の価格については、「[Virtual WAN の価格](https://azure.microsoft.com/pricing/details/virtual-wan/)」をご覧ください。
