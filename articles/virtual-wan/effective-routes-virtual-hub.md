---
title: 仮想ハブの有効なルートを表示する:Azure Virtual WAN | Microsoft Docs
description: Azure Virtual WAN の仮想ハブの有効なルートを表示する方法
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983658"
---
# <a name="view-virtual-hub-effective-routes"></a>仮想ハブの有効なルートを表示する

Azure portal で Virtual WAN ハブのすべてのルートを表示できます。 この記事では、有効なルートを表示する手順について説明します。 仮想ハブのルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>接続またはルート テーブルを選択する

1. 仮想ハブに移動し、 **[ルーティング]** を選択します。 [ルーティング] ページで、 **[有効なルート]** を選択します。
1. ドロップダウンから、 **[ルート テーブル]** を選択できます。 [ルート テーブル] オプションが表示されない場合は、この仮想ハブにカスタムまたは既定のルート テーブルが設定されていません。

## <a name="view-output"></a><a name="output"></a>出力を表示する

ページの出力には、次のフィールドが表示されます。

* **プレフィックス**: 現在のエンティティに認識されるアドレス プレフィックス (仮想ハブ ルーターから学習)
* **次ホップの種類**: 仮想ネットワーク接続、VPN_S2S_Gateway、ExpressRoute ゲートウェイ、リモート ハブ、または Azure Firewall のいずれかです。
* **次ホップ**:これは次のホップのリソース ID へのリンクです。あるいはこれは単純に、現在のハブを意味するオンリンクを示します。
* **配信元**:ルーティング ソースのリソース ID。
* **AS パス**: BGP 属性の AS (自律システム) パスは、パスにアタッチされているプレフィックスのアドバタイズ元の場所に到達するために走査する必要があるすべての AS 番号が一覧表示されます。

### <a name="example"></a><a name="example"></a>例

次の例の表に示す値は、仮想ハブの接続またはルート テーブルが、10.2.0.0/24 (分岐プレフィックス) というルートを学習したことを示しています。 また、**VPN の次ホップの種類** としての VPN_S2S_Gateway、**次ホップ** としての VPN Gateway リソース ID によって、ルートが学習されています。 **ルートの発信元** は、発信元の VPN ゲートウェイ、ルート テーブル、または接続のリソース ID を指します。 **AS パス** は、分岐の AS パスを示します。

AS パスを表示するには、テーブルの下部にあるスクロールバーを使用します。

| **プレフィックス** |  **次ホップの種類** | **次ホップ** |  **ルートの発信元** |**AS パス** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/subscriptions/`<sub id>`/resourceGroups/`<resource group name>`/providers/Microsoft.Network/vpnGateways/vpngw|/subscriptions/`<sub id>`/resourceGroups/`<resource group name>`/providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**考慮事項:**

* **[Get Effective Routes]\(有効なルートを取得\)** に 0.0.0.0/0 と表示されている場合は、ルートがルート テーブルのいずれかに存在することを意味します。 ただし、このルートがインターネット用に設定されている場合は、接続時に追加のフラグ **"enableInternetSecurity": true** が必要となります。 接続時に "enableInternetSecurity" フラグが "false" になっている場合、VM NIC の有効なルートとしてルートが示されません。

* 仮想ネットワーク接続、VPN 接続、または ExpressRoute 接続の編集時に、Azure Virtual WAN ポータルに **[Propagate Default Route]\(既定のルートを伝達する\)** フィールドが表示されます。 このフィールドは **enableInternetSecurity** フラグを示します。このフラグは、ExpressRoute 接続と VPN 接続の場合は常に既定で "false" になりますが、仮想ネットワーク接続の場合は "true" になります。

* VM NIC 上で有効なルートを表示しているとき、次のホップが "仮想ネットワーク ゲートウェイ" であれば、それは、仮想 WAN ハブに接続されているスポークに VM があるとき、仮想ハブ ルーターを意味します。

* 仮想ハブ ルート テーブルの有効なルートのビューは、仮想ハブに少なくとも 1 つの種類の接続 (VPN、ER、または VNET) が接続されている場合にのみ入力されます。

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
* 仮想ハブのルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
