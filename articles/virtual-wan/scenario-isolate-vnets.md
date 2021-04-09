---
title: シナリオ:VNet の分離
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - VNet の分離
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e5e2ce17be6d8a1fa82d8a92b9b788f0bd2a37b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424735"
---
# <a name="scenario-isolating-vnets"></a>シナリオ:VNet の分離

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 このシナリオでは、VNet が他方に到達できないようにすることを目標としています。 これは、VNet の分離と呼ばれます。 仮想ハブのルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="design"></a><a name="design"></a>設計

このシナリオでは、特定の VNet 内のワークロードは分離されたままであり、他の VNet と通信することはできません。 ただし、すべてのブランチ (VPN、ER、User VPN) に接続するには、VNet が必要です。 必要なルート テーブルの数を確認するために、接続性のマトリックスを構築できます。 このシナリオでは、次の表のようになります。各セルは、ソース (行) が宛先 (列) と通信できるかどうかを表します。

| ソース |   ターゲット |  *VNet* | *ブランチ* |
| -------------- | -------- | ---------- | ---|
| VNet     | &#8594;| 直接 |   直接    |
| ブランチ   | &#8594;|  直接  |   直接    |

前の表の各マスは、Virtual WAN 接続 (フローの "ソース" 側、行ヘッダー) が宛先プレフィックス (フローの "ターゲット" 側、斜体の列ヘッダー) と通信するかどうかを示しています。 このシナリオでは、ファイアウォールもネットワーク仮想アプライアンスも存在しないため、通信は Virtual WAN を介して直接行われます (そのため、表では "直接" という単語が使用されています)。

この接続性のマトリックスは、2 つのルート テーブルに変換される 2 種類の行パターンを提供しています。 仮想 WAN には既に既定のルート テーブルがあるため、別のルート テーブルが必要になります。 この例では、ルート テーブルに **[RT_VNET]** という名前を指定します。

VNet は、この **[RT_VNET]** ルート テーブルに関連付けられます。 ブランチへの接続性が必要なため、ブランチが **[RT_VNET]** に伝達される必要があります (それ以外の場合、VNet はブランチのプレフィックスを学習しません)。 ブランチは常に既定のルート テーブルに関連付けられるため、VNet は既定のルート テーブルに伝達される必要があります。 その結果、最終的な設計は次のようになります。

* 仮想ネットワーク:
  * 関連付けられたルート テーブル: **[RT_VNET]**
  * ルート テーブルへの伝達: **[Default]**
* ブランチ:
  * 関連付けられたルート テーブル: **[Default]**
  * ルート テーブルへの伝達: **[RT_VNET]** と **[Default]**

ブランチが伝達されるのはルート テーブル **[RT_VNET]** だけであり、これらが VNet が学習する唯一のプレフィックスとなります。他の VNet は該当しません。

仮想ハブのルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="workflow"></a><a name="workflow"></a>ワークフロー

このシナリオを構成するには、次の手順を考慮してください。

1. 各ハブにカスタム ルート テーブルを作成します。 この例では、ルート テーブルは **RT_VNet** です。 ルート テーブルを作成するには、「[仮想ハブ ルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。 ルート テーブルの詳細については、「[仮想ハブ ルーティングについて](about-virtual-hub-routing.md)」を参照してください。
2. **RT_VNet** ルート テーブルを作成する場合は、次の設定を構成します。

   * **Association**: 分離する VNet を選択します。
   * **伝達**: ブランチのオプションを選択します。ブランチ (VPN/ER/P2S) 接続を暗黙的に指定することで、ルートはこのルート テーブルに伝達されます。

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="分離された VNet":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
