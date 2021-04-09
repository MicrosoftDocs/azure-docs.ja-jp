---
title: ゲートウェイの VPN NAT 規則を構成する
titleSuffix: Azure Virtual WAN
description: VWAN VPN ゲートウェイの NAT 規則を構成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431218"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>仮想 WAN VPN ゲートウェイの NAT 規則を構成する - プレビュー

> [!IMPORTANT]
> NAT 規則は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

仮想 WAN VPN ゲートウェイを静的な 1 対 1 の NAT 規則を使用して構成できます。 NAT 規則には、IP アドレスの 1 対 1 の変換を設定するメカニズムが用意されています。 NAT を使用して、互換性のないまたは重複する IP アドレスを持つ 2 つの IP ネットワークを相互接続することができます。 代表的なシナリオは、重複する IP を持つブランチが Azure VNet リソースへのアクセスを必要とすることです。

この構成では、フロー テーブルを使用して、外部 (ホスト) IP アドレスから仮想ネットワーク内のエンドポイント (仮想マシン、コンピューター、コンテナーなど) に関連する内部 IP アドレスにトラフィックをルーティングします。

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="アーキテクチャを示す図。":::

## <a name="configure-nat-rules"></a><a name="rules"></a>NAT 規則を構成する

VPN ゲートウェイ設定における NAT 規則を、いつでも構成および表示できます。

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="編集規則を示すスクリーンショット。":::

1. ご使用の仮想ハブに移動します。
1. **[VPN (サイト対サイト)]** を選択します。
1. **[NAT 規則 (編集)]** を選択します。
1. **[Edit NAT Rule]\(NAT 規則の編集\)** ページで、次の値を使用して NAT 規則の **追加、編集、削除** を行うことができます。

   * **名前:** NAT 規則の一意の名前。
   * **タイプ:** 静的。 静的な 1 対 1 の NAT では、内部アドレスと外部アドレスの間に 1 対 1 のリレーションシップが確立されます。
   * **モード:** IngressSnat または EgressSnat。  
      * IngressSnat モード (イングレス ソース NAT とも呼ばれます) は、Azure ハブのサイト対サイト VPN ゲートウェイに入るトラフィックに適用されます。
      * EgressSnat モード (エグレス ソース NAT とも呼ばれます) は、Azure ハブのサイト対サイト VPN ゲートウェイを出ていくトラフィックに適用されます。
   * **InternalMapping:** 外部 IP のセットにマップされる、内部ネットワーク上の送信元 IP のアドレス プレフィックス範囲。 言い換えると、NAT 前のアドレス プレフィックス範囲です。
   * **ExternalMapping:** 送信元 IP がマップされる、外部ネットワーク上の宛先 IP のアドレス プレフィックス範囲。 言い換えると、NAT 後のアドレス プレフィックス範囲です。
   * **リンク接続:** VPN サイトを Azure ハブのサイト対サイト VPN ゲートウェイに仮想的に接続する接続リソース。

### <a name="configuration-considerations"></a><a name="considerations"></a>構成に関する考慮事項

* 内部と外部の両方のマッピングのサブネット サイズは、静的な 1 対の 1 NAT の場合は同じである必要があります。
* 必ず、Azure portal で VPN サイトを編集して、**ExternalMapping** プレフィックスを プライベート アドレス空間］ フィールドに追加してください。 現在、BGP が有効になっているサイトは、オンプレミス BGP アナウンサー (デバイスの BGP 設定) に外部マッピング プレフィックスのエントリが含まれていることを確認する必要があります。

## <a name="examples-and-verification"></a><a name="examples"></a>例と検証

### <a name="ingress-mode-nat"></a>イングレス モード NAT

イングレス モード NAT 規則は、Virtual WAN サイト間 VPN ゲートウェイを介して Azure に入るパケットに適用されます。 このシナリオでは、2 つのサイト間 VPN ブランチを Azure に接続します。 VPN サイト 1 はリンク 1 経由で接続し、VPN サイト 2 はリンク 2 経由で接続します。 各サイトには、192.169.1.0/24 というアドレス空間があります。

次の図は、予測される最終結果を示しています。

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="イングレス モード NAT を示す図。":::

1. NAT 規則を指定します。

   サイト間 VPN ゲートウェイが、アドレス空間 （192.168.1.0/24など） が重複している 2 つのブランチを確実に区別できるように、NAT 規則を指定します。 この例では、VPN サイト 1 のリンク 1 に注目します。

   次の NAT 規則を設定して、いずれかのブランチのリンク 1 に関連付けることができます。 これは静的な NAT 規則であるため、InternalMapping と ExternalMapping のアドレス空間には同じ数の IP アドレスが含まれています。

   * **名前:** IngressRule01
   * **種類:** 静的
   * **モード:** IngressSnat
   * **InternalMapping:** 192.168.1.0/24
   * **ExternalMapping:** 10.1.1.0/24
   * **リンク接続:** リンク 1

1. 正しい ExternalMapping をアドバタイズします。

   この手順では、サイト間 VPN ゲートウェイが、Azure リソースの残りの部分に対して正しい ExternalMapping アドレス空間をアドバタイズしていることを確認します。 BGP が有効になっているかどうかによって、手順が異なります。

   **例 1: BGP が有効になっている**

   * VPN サイト 1 にあるオンプレミスの BGP スピーカーが 10.1.1.0/24 アドレス空間をアドバタイズするように構成されていることを確認します。
   * このプレビューでは、BGP が有効になっているサイトは、オンプレミス BGP アナウンサー (デバイスの BGP 設定) に外部マッピング プレフィックスのエントリが含まれていることを確認する必要があります。

   **例 2: BGP が有効になっていない**

   * サイト間 VPN ゲートウェイを含む仮想ハブ リソースに移動します。 仮想ハブ ページの **[接続]** で、 **[VPN (Site-to-Site)]\(VPN (サイト間)\)** を選択します。
   * リンク 1 を介して Virtual WAN ハブに接続されている VPN サイトを選択します。 **[サイトの編集]** を選択し、VPN サイトのプライベート アドレス空間として「10.1.1.0/24」を入力します。

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="[VPN サイトの編集] ページを示すスクリーンショット。":::

### <a name="packet-flow"></a>パケット フロー

この例では、オンプレミスのデバイスがスポーク仮想ネットワークに接続しようとしています。 パケット フローは次のようになります。 NAT 変換は太字で示しています。

1. オンプレミスからのトラフィックが開始されます。
   * 送信元 IP アドレス: **192.168.1.1**
   * 宛先 IP アドレス: 30.0.0.1
1. トラフィックはサイト間ゲートウェイに入り、NAT 規則を使用して変換され、スポークに送信されます。
   * 送信元 IP アドレス: **10.1.1.1**
   * 宛先 IP アドレス: 30.0.0.1
1. スポークからの応答が開始されます。
   * 送信元 IP アドレス: 30.0.0.1
   * 宛先 IP アドレス: **10.1.1.1**
1. トラフィックはサイト間 VPN ゲートウェイに入り、変換は元に戻され、オンプレミスに送信されます。
   * 送信元 IP アドレス: 30.0.0.1
   * 宛先 IP アドレス: **192.168.1.1**

### <a name="verification-checks"></a>検証チェック

このセクションでは、構成が適切に設定されていることを確認するためのチェックを示します。

#### <a name="validate-defaultroutetable-rules-and-routes"></a>DefaultRouteTable、規則、ルートを検証する

Virtual WAN 内のブランチは **DefaultRouteTable** に関連付けられています。これは、すべてのブランチ接続は DefaultRouteTable 内に設定されたルートを学習することを意味します。 DefaultRouteTable の有効なルートに、外部マッピング プレフィックスを含む NAT 規則があることがわかります。

例:

* **プレフィックス:** 10.1.1.0/24  
* **ネクスト ホップの種類:** VPN_S2S_Gateway
* **ネクスト ホップ:** VPN_S2S_Gateway リソース

#### <a name="validate-address-prefixes"></a>アドレス プレフィックスを検証する

この例は、DefaultRouteTable に関連付けられている仮想ネットワーク内のリソースに適用されます。

Virtual WAN ハブに接続されているスポーク仮想ネットワーク内にある仮想マシンのネットワーク インターフェイス カード (NIC) の **有効なルート** には、NAT 規則 **ExternalMapping** のアドレス プレフィックスも含まれている必要があります。

#### <a name="validate-bgp-advertisements"></a>BGP アドバタイズを検証する

VPN サイト接続で BGP が構成されている場合は、オンプレミスの BGP スピーカーが外部マッピング プレフィックスのエントリをアドバタイズしていることを確認します。

## <a name="next-steps"></a>次のステップ

サイト対サイト構成について詳しくは、 [仮想 WAN サイト対サイト接続の構成](virtual-wan-site-to-site-portal.md)に関するページをご覧ください。
