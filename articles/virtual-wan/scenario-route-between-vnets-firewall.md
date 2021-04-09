---
title: シナリオ:Virtual WAN 用の Azure Firewall のカスタム ルーティング
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - VNet 間ではトラフィックを直接ルーティングする一方、"VNet -> インターネットまたはブランチ" と "ブランチ対 VNet" のトラフィック フローには Azure Firewall を使用します
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: d083607782f96744ecbd7d23976f77ee53fec49d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94515571"
---
# <a name="scenario-azure-firewall---custom"></a>シナリオ:Azure Firewall - カスタム

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 このシナリオでは、VNet 間ではトラフィックを直接ルーティングする一方、"VNet 対インターネットまたはブランチ" と "ブランチ対 VNet" のトラフィック フローには Azure Firewall を使用することを目標とします。

## <a name="design"></a><a name="design"></a>デザイン

必要なルート テーブルの数を明らかにするには、各セルで接続元 (行) が接続先 (列) と通信できるかどうかを表す、接続マトリックスを作成します。 このシナリオの接続マトリックスは簡易的なものですが、他のシナリオの一貫性を持たせるために参照することができます。

**接続マトリックス**

| ソース           | 移動先:      | *VNet*      | *ブランチ*    | *Internet*   |
|---             |---       |---           |---            |---           |
| **VNet**      |   &#8594;|    直接    |     AzFW      |     AzFW     |
| **ブランチ**   |   &#8594;|    AzFW      |    直接     |    直接    |

前の表では、"Direct" は Virtual WAN でトラフィックが Azure Firewall を経由しない 2 つの接続間の直接接続を表し、"AzFW" はフローが Azure Firewall を経由することを示しています。 マトリックスには 2 つの異なる接続パターンがあるため、次のように構成される 2 つのルート テーブルが必要になります。

* 仮想ネットワーク:
  * 関連付けられたルート テーブル:**RT_VNet**
  * ルート テーブルへの伝達:**RT_VNet**
* ブランチ:
  * 関連付けられたルート テーブル: **[Default]**
  * ルート テーブルへの伝達: **[Default]**

> [!NOTE]
> 各リージョンで 1 つのセキュリティ保護付き仮想ハブを含む個別の Virtual WAN インスタンスを作成した後、サイト間 VPN 経由で各 Virtual WAN を互いに接続することができます。

仮想ハブのルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="workflow"></a><a name="workflow"></a>ワークフロー

このシナリオでは、VNet 対インターネット、VNet 対ブランチ、またはブランチ対 VNet のトラフィックを Azure Firewall 経由でルーティングする一方、VNet 対 VNet のトラフィックについては直接ルーティングします。 Azure Firewall Manager を使用している場合、ルート設定は自動的に **既定のルート テーブル** に入力されます。 プライベート トラフィックは VNet とブランチに適用され、インターネット トラフィックは 0.0.0.0/0 に適用されます。

VPN、ExpressRoute、およびユーザー VPN 接続は、総称して "ブランチ" と呼ばれ、同じ (既定の) ルート テーブルに関連付けられます。 すべての VPN 接続、ExpressRoute 接続、ユーザー VPN 接続は、同じルート テーブルのセットにルートを伝達します。 このシナリオを構成するには、次の手順を考慮してください。

1. カスタム ルート テーブル **RT_VNet** を作成します。
1. VNet 対インターネットおよび VNet 対ブランチをアクティブにするルートを作成します (次ホップが Azure Firewall を指す 0.0.0.0/0)。 [Propagation]\(伝達\) セクションで、VNet が選択され、より具体的なルートが確保されていることを確認します。これにより、VNet 対 VNet の直接トラフィック フローが可能になります。

   * **[Association]\(関連付け\):** [VNet] を選択します。これは、このルート テーブルのルートに従って VNet が宛先に到達することを意味します。
   * **[Propagation]\(伝達\):** [VNet] を選択します。これは、VNet がこのルート テーブルに伝達されることを意味します。言い換えると、より具体的なルートがこのルート テーブルに伝達されるため、VNet 間の直接のトラフィック フローが確保されます。

1. VNet の集約された静的ルートを **既定のルート テーブル** に追加して、Azure Firewall を経由するブランチ対 VNet フローをアクティブ化します。

   * ブランチが関連付けられ、既定のルート テーブルに伝達されることに注意してください。
   * ブランチは RT_VNet ルート テーブルに伝達されません。 これにより、Azure Firewall を経由する VNet 対ブランチのトラフィック フローが確保されます。

これにより、**図 1** に示すように、ルーティング構成が変更されます。

**図 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="図 1":::

> [!NOTE]
> Virtual WAN ハブと接続された仮想ネットワークは、同じ Azure リージョンに存在する必要があります。

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
* 仮想ハブ ルーティングの構成方法の詳細については、「[仮想ハブのルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。
