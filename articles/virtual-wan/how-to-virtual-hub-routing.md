---
title: 仮想ハブのルーティングを構成する方法
titleSuffix: Azure Virtual WAN
description: この記事では、仮想ハブのルーティングを構成する方法について説明します
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313690"
---
# <a name="how-to-configure-virtual-hub-routing"></a>仮想ハブのルーティングを構成する方法

仮想ハブには、サイト間 VPN ゲートウェイ、ExpressRoute ゲートウェイ、ポイント対サイト ゲートウェイ、Azure Firewall などの複数のゲートウェイを含めることができます。 仮想ハブのルーティング機能は、Border Gateway Protocol (BGP) を使用してゲートウェイ間のすべてのルーティング (トランジット ルーティングを含む) を管理するルーターによって提供されます。 また、このルーターは、仮想ハブに接続する仮想ネットワーク間の転送接続を提供し、最大 50 Gbps の合計スループットをサポートできます。 これらのルーティング機能は、Standard Virtual WAN のお客様に適用されます。

詳細については、「[仮想ハブ ルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="create-a-route-table"></a><a name="create-table"></a>ルート テーブルの作成

1. Azure portal で仮想ハブに移動します。
2. **[接続]** の下で **[ルーティング]** を選択します。 [ルーティング] ページに、 **[既定]** と **[なし]** のルート テーブルが表示されます。

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="[ルーティング] ページ":::
3. **[+ ルート テーブルの作成]** を選択して、 **[ルート テーブルの作成]** ページを開きます。
4. [ルート テーブルの作成] ページの **[基本]** タブで、次のフィールドを入力します。

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="[基本] タブ":::

   * **名前**
   * **ルート**
   * **ルート名**
   * **送信先の種類**
   * **宛先プレフィックス**: プレフィックスを集約することができます。 次に例を示します。VNet 1: 10.1.0.0/24 と VNet 2: 10.1.1.0/24 は 10.1.0.0/16 として集約できます。 **ブランチ** ルートは、接続されているすべての VPN サイト、ExpressRoute 回線、およびユーザー VPN 接続に適用されます。
   * **次ホップ**:仮想ネットワーク接続の一覧、または Azure Firewall。

     仮想ネットワーク接続を選択すると、 **[Configure static routes] (静的ルートの構成)** が表示されます。 これはオプションの構成設定です。 詳細については、[静的ルートの構成](about-virtual-hub-routing.md#static)に関するページを参照してください。

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="次ホップ":::

5. **[ラベル]** タブを選択して、ラベル名を構成します。 ラベルは、ルート テーブルを論理的にグループ化するためのメカニズムを提供します。

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="ラベル名の構成":::

6. **[関連付け]** タブを選択して、ルート テーブルへの接続を関連付けます。
接続の **[ブランチ]** 、 **[仮想ネットワーク]** 、および **[現在の設定]** が表示されます。

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="ルート テーブルへの接続の関連付け":::

7. **[Propagations] (伝達)** タブを選択して、接続からルート テーブルへのルートを伝達します。

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="ルートの伝達":::

8. **[作成]** を選択して、ルート テーブルを作成します。

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>ルート テーブルを編集するには

Azure portal で、仮想ハブのルート テーブルを見つけます。 情報を編集するには、ルート テーブルを選択します。

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>ルート テーブルを削除するには

Azure portal で、仮想ハブのルート テーブルを見つけます。 [既定] または [なし] のルート テーブルは削除できません。 ただし、すべてのカスタムのルート テーブルを削除できます。 **...** をクリックし、**削除** を選択します。

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>有効なルートを表示するには

Azure portal で、仮想ハブのルート テーブルを見つけます。 **[...]** をクリックし、 **[有効なルート]** を選択して、選択したルート テーブルによって学習されたルートを表示します。 接続からルート テーブルに伝達されたルートは、ルート テーブルの **[有効なルート]** に自動的に設定されます。 詳細については、[有効なルート](effective-routes-virtual-hub.md)に関するページを参照してください。

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="有効なルートの表示" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>仮想ネットワーク接続に対するルーティング構成を設定するには

1. Azure portal で、仮想 WAN に移動し、 **[接続]** で **[Virtual Network 接続]** を選択します。
1. **[+ 接続の追加]** を選択します。
1. ドロップダウン リストから仮想ネットワークを選択します。
1. ルート テーブルに関連付けるルーティング構成を設定します。 **[ルート テーブルを関連付ける]** では、ドロップダウンからルート テーブルを選択します。
1. ルーティング構成を、1 つまたは複数のルート テーブルに伝達するように設定します。 **[Propagate to Route Table] (ルート テーブルに伝達する)** では、ドロップダウンから選択します。
1. **[静的ルート]** では、ネットワーク仮想アプライアンスの静的ルートを構成します (該当する場合)。 仮想 WAN では、仮想ネットワーク接続の静的ルートに対して 1 つの次ホップ IP がサポートされています。 たとえば、イングレスとエグレスのトラフィック フロー用に個別の仮想アプライアンスがある場合は、仮想アプライアンスを別の VNET に配置し、VNET を仮想ハブに接続することをお勧めします。


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="ルーティング構成の設定" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>次のステップ

仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
