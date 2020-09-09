---
title: シナリオ:共有サービスの VNet へのルート
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - すべての VNet とブランチがアクセスする必要がある、ワークロードを持つ共有サービス VNet にアクセスするためのルートを設定します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6045c491ea68d759b2a1739e20aa2f12b8520c87
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88006487"
---
# <a name="scenario-route-to-shared-services-vnets"></a>シナリオ:共有サービスの VNet へのルート

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 このシナリオの目標は、すべての VNet とブランチ (VPN/ER/P2S) がアクセスする必要がある、ワークロードを持つ**共有サービス** VNet にアクセスするためのルートを設定することです。 このような共有ワークロードの例には、ドメイン コントローラーやファイル共有などのサービスが含まれる Virtual Machines や、[Azure プライベート エンドポイント](../private-link/private-endpoint-overview.md)経由で内部公開される Azure サービスなどがあります。

仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="design"></a><a name="design"></a>デザイン

接続マトリックスを使用してこのシナリオの要件をまとめることができます。 このマトリックスの各セルには、特定のトラフィック フローについて、Virtual WAN 接続 (フローの "ソース" 側、表の行ヘッダー) により接続先プレフィックス (フローの "ターゲット" 側、表の斜体の列ヘッダー) が学習されるかどうかが示されています。

**接続マトリックス**

| ソース             | 移動先:   |*分離された VNet*|*共有 VNet*|*ブランチ*|
|---|---|---|---|---|
|**分離された VNet**|&#8594;|                |        X        |       X      |
|**共有 VNet**  |&#8594;|       X        |        X        |       X      |
|**ブランチ**      |&#8594;|       X        |        X        |       X      |

[分離 VNet シナリオ](scenario-isolate-vnets.md)と同様に、この接続マトリックスからは、2 つのルート テーブルに変換される 2 つの異なる行パターンが与えられます (共有サービス VNet とブランチは接続要件が同じです)。 仮想 WAN には既にデフォルト ルート テーブルがあるため、別のカスタム ルート テーブルが必要になります。この例ではこのテーブルを **RT_SHARED** と呼んでいます。

VNet は、この **RT_SHARED** ルート テーブルに関連付けられます。 VNet をブランチと共有サービス VNet に接続する必要があるため、ブランチと共有サービス VNet は **RT_SHARED** に伝達する必要があります (そうしないと、VNet によりブランチと共有サービス VNet のプレフィックスが認識されません)。 ブランチは常に Default ルート テーブルに関連付けられ、共有サービス VNet の場合と接続要件が同じであるため、共有サービス VNet も Default ルート テーブルに関連付けます。

その結果、最終的な設計は次のようになります。

* 分離された仮想ネットワーク:
  * 関連付けられたルート テーブル:**RT_SHARED**
  * ルート テーブルへの伝達: **[Default]**
* 共有サービス仮想ネットワーク:
  * 関連付けられたルート テーブル: **[Default]**
  * ルート テーブルへの伝達:**RT_SHARED** と **Default**
* ブランチ:
  * 関連付けられたルート テーブル: **[Default]**
  * ルート テーブルへの伝達:**RT_SHARED** と **Default**

> [!NOTE]
> 仮想 WAN が複数のリージョンにデプロイされる場合、すべてのハブで **RT_SHARED** ルート テーブルを作成する必要があります。また、共有サービス VNet とブランチの各接続からのルートを、伝達ラベルを利用し、各仮想ハブのルート テーブルに伝達する必要があります。

仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="workflow"></a><a name="workflow"></a>ワークフロー

シナリオを構成するには、次の手順を考慮してください。

1. **共有サービス** VNet を特定します。
2. カスタム ルート テーブルを作成します。 この例では、ルート テーブルを **RT_SHARED** と呼んでいます。 ルート テーブルを作成する手順については、「[仮想ハブのルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。 ガイドラインとして、次の値を使用します。

   * **関連付け**
     * **共有サービス VNet 以外の Vnet *では、分離する Vnet を選択します。*** これで、これらのすべての Vnet (共有サービス VNet を除く) が、RT_SHARED ルート テーブルのルートに基づいて宛先に到達することができます。

   * **伝達**
      * **ブランチ**では、既に選択している他のルート テーブルに加えて、ルートをこのルート テーブルに伝達します。 この手順により、RT_SHARED ルート テーブルは、すべてのブランチ接続 (VPN/ER/ユーザー VPN) からのルートを取得します。
      * **Vnet** では、**共有サービス VNet** を選択します。 この手順により、RT_SHARED ルート テーブルは、共有サービス VNet 接続からのルートを取得します。

結果的に、次の図に示すようなルーティング構成になります。

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="共有サービス VNet" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
