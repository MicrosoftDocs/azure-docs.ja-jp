---
title: 'シナリオ: 任意間'
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - 任意間
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 3b5002873160490dfb7b8d3ad9790f9c6f1e8ae6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525790"
---
# <a name="scenario-any-to-any"></a>シナリオ:任意の環境間

Virtual WAN で仮想ハブのルーティングを使用する場合、多くのシナリオを利用できます。 任意間シナリオでは、すべてのスポークが別のスポークに接続できます。 複数のハブが存在する場合、Standard Virtual WAN では、ハブツーハブ ルーティング (ハブ間ルーティングとも呼ばれます) が既定で有効になります。 Azure portal や [Azure クイック スタート テンプレート](quickstart-any-to-any-template.md)など、さまざまな方法を使用して、この構成を作成できます。 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。 

## <a name="design"></a><a name="design"></a>設計

Virtual WAN のシナリオで必要なルート テーブルの数を明らかにするには、接続マトリックスを作成できます。その各セルは、接続元 (行) が接続先 (列) と通信できるかどうかを表します。

| ソース |   ターゲット |  *VNet* | *ブランチ* |
| -------------- | -------- | ---------- | ---|
| VNet     | &#8594;| 直接 | 直接 |
| ブランチ   | &#8594;| 直接  | 直接 |

前の表の各マスは、Virtual WAN 接続 (フローの "ソース" 側、行ヘッダー) が宛先プレフィックス (フローの "ターゲット" 側、斜体の列ヘッダー) と通信するかどうかを示しています。 このシナリオでは、ファイアウォールもネットワーク仮想アプライアンスも存在しないため、通信は Virtual WAN を介して直接行われます (そのため、表では "直接" という単語が使用されています)。

VNet とブランチ (VPN、ExpressRoute、およびユーザー VPN) 両方からのすべての接続には同じ接続要件があるため、1 つのルート テーブルが必要です。 その結果、すべての接続が関連付けられ、同じルート テーブル (既定のルート テーブル) に伝達されます。

* 仮想ネットワーク:
  * 関連付けられたルート テーブル: **[Default]**
  * ルート テーブルへの伝達: **[Default]**
* ブランチ:
  * 関連付けられたルート テーブル: **[Default]**
  * ルート テーブルへの伝達: **[Default]**

仮想ハブのルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="architecture"></a><a name="architecture"></a>アーキテクチャ

**図 1** では、すべての VNet とブランチ (VPN、ExpressRoute、P2S) を相互に接続できます。 仮想ハブでは、接続は次のように機能します。

* VPN 接続では、VPN サイトが VPN ゲートウェイに接続されます。
* 仮想ネットワーク接続では、仮想ネットワークが仮想ハブに接続されます。 仮想ハブのルーターでは、VNet 間の転送機能が提供されます。
* ExpressRoute 接続では、ExpressRoute 回線が ExpressRoute ゲートウェイに接続されます。

接続のルーティング構成を **[なし]** またはカスタム ルートテーブルに設定しない限り、これらの接続は (既定では作成時に) 既定のルート テーブルに関連付けられます。 また、これらの接続では、既定では、既定のルート テーブルにルートが伝達されます。 これにより、すべてのスポーク (VNet、VPN、ER、P2S) が相互に接続できる任意間シナリオが有効になります。

**図 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="図 1":::

## <a name="workflow"></a><a name="workflow"></a>ワークフロー

このシナリオは、Standard Virtual WAN では既定で有効になっています。 WAN 構成でブランチ間の設定が無効になっている場合、ブランチ スポーク間の接続は許可されません。 Virtual WAN では、VPN/ExpressRoute/ユーザー VPN はブランチ スポークとみなされます。

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
