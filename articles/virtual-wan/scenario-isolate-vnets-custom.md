---
title: シナリオ:VNet のカスタム分離
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - 選択された VNet が相互に通信できないようにする
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.openlocfilehash: 84c7b72e3ac7a5726dea38b21b14b5bd83b42340
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831024"
---
# <a name="scenario-custom-isolation-for-vnets"></a>シナリオ:VNet のカスタム分離

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 VNet のカスタム分離シナリオでは、特定の VNet のセットが他の特定の VNet のセットに到達できないようにすることが目標です。 ただし、すべてのブランチ (VPN/ER/User VPN) に接続するには、VNet が必要です。 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="design"></a><a name="design"></a>デザイン

必要なルート テーブルの数を確認するには、接続マトリックスを作成します。 このシナリオでは、次のようになります。各セルは、ソース (行) が宛先 (列) と通信できるかどうかを表します。

| ソース | 移動先:| *青の VNet* | *赤の VNet* | *ブランチ*|
|---|---|---|---|---|
| **青の VNet** |   &#8594;|      X        |               |       X      |
| **赤の VNet**  |   &#8594;|              |       X       |       X      |
| **ブランチ**   |   &#8594;|     X        |       X       |       X      |

前の表の各セルでは、特定のトラフィック フローについて、Virtual WAN 接続 (フローの "ソース" 側、表の行ヘッダー) により接続先プレフィックス (フローの "ターゲット" 側、表の斜体の列ヘッダー) が学習されるかどうかが示されています。

異なる行パターンの数は、このシナリオで必要になるルート テーブルの数になります。 この例では、仮想ネットワークの **RT_BLUE** と **RT_RED**、ブランチの **Default** とここでは呼ばれる、3 つのルートを持つルート テーブルです。 ブランチは常に Default ルーティング テーブルに関連付けられている必要があることに注意してください。

ブランチは、赤と青の両方の VNet からプレフィックスを学習する必要があるため、すべての VNet を Default (および **RT_BLUE** または **RT_RED** のいずれか) に伝達する必要があります。 青と赤の VNet は、ブランチのプレフィックスを学習する必要があるため、ブランチは **RT_BLUE** および **RT_RED** の両方のルート テーブルに伝達されます。 その結果、最終的な設計は次のようになります。

* 青の仮想ネットワーク:
  * 関連付けられたルート テーブル:**RT_BLUE**
  * ルート テーブルへの伝達:**RT_BLUE** および **Default**
* 赤の仮想ネットワーク:
  * 関連付けられたルート テーブル:**RT_RED**
  * ルート テーブルへの伝達:**RT_RED** および **Default**
* ブランチ:
  * 関連付けられたルート テーブル: **[Default]**
  * ルート テーブルへの伝達:**RT_BLUE**、**RT_RED** および **Default**

> [!NOTE]
> すべてのブランチは Default ルート テーブルに関連付けられ、同じルーティング テーブルのセットに伝達する必要があるため、すべてのブランチは同じ接続プロファイルを持つことになります。 言い換えると、VNet の赤と青の概念を、ブランチに当てはめることはできません。

> [!NOTE]
> Virtual WAN が複数のリージョンにデプロイされている場合、すべてのハブで **RT_BLUE** および **RT_RED** ルート テーブルを作成する必要があります。また、VNet の各接続からのルートを、伝達ラベルを利用し、各仮想ハブのルート テーブルに伝達する必要があります。

仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="workflow"></a><a name="architecture"></a>ワークフロー

**図 1** には、青と赤の VNet 接続があります。

* 青の接続された Vnet は互いに通信できるだけでなく、すべてのブランチ (VPN/ER/P2S) 接続にも対応できます。
* 赤の Vnet は互いに通信できるだけでなく、すべてのブランチ (VPN/ER/P2S) 接続にも対応できます。

ルーティングを設定するときは、次の手順を考慮してください。

1. Azure portal に 2 つのカスタム ルート テーブルを作成し、**RT_BLUE** および **RT_RED** を作成します。
2. ルート テーブル **RT_BLUE** の場合、次の設定に従います。
   * **Association**: 青の VNet をすべて選択します。
   * **Propagation**:ブランチについては、ブランチのオプションを選択します。ブランチ (VPN/ER/P2S) 接続を暗黙的に指定することで、ルートはこのルート テーブルに伝達されます。
3. 赤の VNet とブランチ (VPN/ER/P2S) の **RT_RED** ルート テーブルについても同じ手順を繰り返します。

これで、ルーティング構成が次の図に示すように変更されます

**図 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="図 1":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
