---
title: シナリオ:ネットワーク仮想アプライアンス (NVA) 経由のトラフィックのルーティング
titleSuffix: Azure Virtual WAN
description: NVA を経由するトラフィックのルーティング
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 24671a34214864e253d96c356dc8b2853bf6d560
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519798"
---
# <a name="scenario-route-traffic-through-an-nva"></a>シナリオ:NVA を経由するトラフィックのルーティング

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 この NVA シナリオでの目標は、NVA (ネットワーク仮想アプライアンス) を経由するブランチへのトラフィックを VNet にルーティングし、VNet へのトラフィックをブランチにルーティングすることです。 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

> [!NOTE]
> 新機能の[仮想ハブのルーティングを構成する方法](how-to-virtual-hub-routing.md)が利用可能になる前のルートを既に設定している場合は、次のバージョンの記事の手順を使用してください。
>* [Azure portal に関する記事](virtual-wan-route-table-nva-portal.md)
>* [PowerShell に関する記事](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>デザイン

このシナリオでは、次の名前付け規則を使用します。

* ユーザーが NVA をデプロイし、その他の仮想ネットワークをスポークとして接続している仮想ネットワークの場合は "NVA VNet" を使用 (この記事の下の方にある **図 2** の、VNet 2 および VNet 4)。
* NVA VNet に接続されている仮想ネットワークの場合は "NVA スポーク" を使用 (この記事の下の方にある **図 2** の VNet 5、VNet 6、VNet 7 および VNet 8)。
* NVA またはその他の VNet がピアリングされていない Virtual WAN に接続されている仮想ネットワークの場合は "NVA 以外の VNet" を使用 (この記事の下の方にある **図 2** の VNet 1 および VNet 3)。
* NVA VNet が接続されている Microsoft マネージドの Virtual WAN ハブの場合は "ハブ" を使用。 NVA スポーク VNet は、Virtual WAN ハブに接続されている必要はありません。その必要があるのは NVA VNet のみです。

次の接続マトリックスは、このシナリオでサポートされるフローの概要を示しています。

**接続マトリックス**

| ソース             | 移動先:|   *NVA スポーク*|*NVA VNet*|*NVA 以外の VNet*|*ブランチ*|
|---|---|---|---|---|---|
| **NVA スポーク**   | &#8594; | NVA VNet 経由 | ピアリング | NVA VNet 経由 | NVA VNet 経由 |
| **NVA VNet**    | &#8594; | ピアリング | 直接 | 直接 | 直接 |
| **NVA 以外の VNet**| &#8594; | NVA VNet 経由 | 直接 | 直接 | 直接 |
| **ブランチ**     | &#8594; | NVA VNet 経由 | 直接 | 直接 | 直接 |

接続マトリックスの各セルは、特定のトラフィック フローについて、VNet またはブランチ (フローの "ソース" 側、表の行ヘッダー) と、接続先 VNet またはブランチ (フローの "移動先" 側、表の斜体の列ヘッダー) との通信方法を説明しています。 "直接" は、Virtual WAN によって接続がネイティブに提供されることを意味します。"ピアリング" は、VNet 内のユーザー定義ルートによって接続が提供されることを意味します。"NVA VNet 経由" は、NVA VNet にデプロイされた NVA を介して接続が行われることを意味します。 以下、具体例に沿って説明します。

* NVA スポークは、Virtual WAN によって管理されていません。 そのため、他の VNet またはブランチとの通信に使用されるメカニズムは、ユーザーによって管理されます。 NVA VNet への接続性は、VNet ピアリングによって提供され、次ホップとして NVA を指す 0.0.0.0/0 への既定のルートは、インターネット、その他のスポーク、およびブランチへの接続に対応している必要があります
* NVA VNet では自身の NVA スポークは認識されますが、他の NVA VNet に接続されている NVA スポークは認識されません。 たとえば、この記事の下の方にある図 2 では、VNet 2 によって VNet 5 と VNet 6 が認識されますが、VNet 7 や VNet 8 などの他のスポークは認識されません。 他のスポークのプレフィックスを NVA VNet に挿入するには、静的ルートが必要です
* 同様に、NVA スポークは Virtual WAN ハブに接続されていないため、ブランチ、および NVA 以外の VNet で NVA スポークは認識されません。 そのため、ここでも静的ルートが必要になります。

NVA スポークは Virtual WAN によって管理されていないことを考慮すると、他のすべての行は同じ接続パターンを示しています。 その結果、1 つのルート テーブル (既定のもの) で十分ということになります。

* 仮想ネットワーク (ハブ以外の VNet とユーザーハブ VNet):
  * 関連付けられたルート テーブル: **[Default]**
  * ルート テーブルへの伝達: **[Default]**
* ブランチ:
  * 関連付けられたルート テーブル: **[Default]**
  * ルート テーブルへの伝達: **[Default]**

ただし、このシナリオでは、どの静的ルートを構成するかを考える必要があります。 各静的ルートには 2 つのコンポーネントがあります。**図 1** に示されているように、1 つは Virtual WAN ハブ内にあり、各スポークに使用する接続を Virtual WAN コンポーネントに伝え、もう 1 つは、その特定の接続内にあり、NVA (または複数の NVA の前に配置されているロード バランサー) に割り当てられた具体的な IP アドレスをポイントしています。

**図 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="図 1":::

そのため、NVA VNet の後に配置されている NVA スポークにトラフィックを送信するために、既定のテーブルに必要な静的ルートは次のようになります。

| Description | ルート テーブル | 静的ルート              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | Default     | 10.2.0.0/16 -> eastusconn |
| VNet 4       | Default     | 10.4.0.0/16 -> weconn     |

これで、パケットを送信する接続を Virtual WAN が認識するようになりましたが、接続はこれらのパケットを受信したときに行う処理を認識している必要があります。ここで、接続ルート テーブルが使用されます。 ここでは、より短いプレフィックス (より長い /16 ではなく /24) を使用して、これらのルートが NVA VNet (VNet 2 および VNet 4) からインポートされたルートよりも優先されるようにします。

| 説明 | Connection | 静的ルート            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24 -> 10.2.0.5 |
| VNet 6       | eastusconn | 10.2.2.0/24 -> 10.2.0.5 |
| VNet 7       | weconn     | 10.4.1.0/24 -> 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24 -> 10.4.0.5 |

NVA VNet、NVA 以外の VNet、およびブランチで、すべての NVA スポークへの接続方法が認識されました。 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="architecture"></a><a name="architecture"></a>アーキテクチャ

**図 2** には、2 つのハブ (**Hub1** と **Hub2**) があります。

* **Hub1** と **Hub2** は、NVA VNet の **VNet 2** および **VNet 4** に直接接続されています。

* **VNet 5** と **VNet 6** は、**VNet 2** とピアリングされています。

* **VNet 7** と **VNet 8** は、**VNet 4** とピアリングされています。

* **VNet 5、6、7、8** は間接的なスポークであり、仮想ハブに直接接続されてはいません。

**図 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="図 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>シナリオのワークフロー

NVA 経由のルーティングを設定するには、次のような手順を検討します。

1. NVA スポークの VNet 接続を明らかにします。 これらは、**図 2** における **VNet 2 の接続 (eastusconn)** と **VNet 4 の接続 (weconn)** です。

   UDR が設定されていることを確認します。
   * VNet 5 および VNet 6 から VNet 2 NVA IP へ
   * VNet 7 および VNet 8 から VNet 4 NVA IP へ 
   
   VNet 5、6、7、8 を仮想ハブに直接接続する必要はありません。 VNet 5、6、7、8 の NSG で、ブランチ (VPN/ER/P2S) またはリモート VNet に接続されている VNet に対するトラフィックが許可されていることを確認します。 たとえば、VNet 5、6 の NSG では、リモート Hub 2 に接続されているオンプレミスのアドレス プレフィックスと VNet 7、8 に対するトラフィックが確実に許可されている必要があります。

Vnet 5、6 が仮想ハブに接続され、VNet 2 NVA IP 経由で通信することから、同様に Vnet 5、6 を VNet2、および VNet 7、8 を VNet 4 に接続する必要があるというシナリオを、Virtual WAN ではサポートしていません。

2. VNet 2、5、6 に対する集約静的ルート エントリを、Hub 1 の既定のルート テーブルに追加します。

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="例":::

3. VNet 2 の仮想ネットワーク接続で、VNet 5、6 に対する静的ルートを構成します。 仮想ネットワーク接続に対するルーティング構成の設定については、[仮想ハブルーティング](how-to-virtual-hub-routing.md#routing-configuration)に関する記事を参照してください。

4. VNet 4、7、8 に対する集約静的ルート エントリを、Hub 1 の既定のルート テーブルに追加します。

5. Hub 2 の既定のルート テーブルについて、ステップ 2、3、4 を繰り返します。

これにより、以下の **図 3** に示すように、ルーティング構成が変更されます。

**図 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="図 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
