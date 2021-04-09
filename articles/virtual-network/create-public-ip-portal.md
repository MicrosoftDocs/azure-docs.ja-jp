---
title: パブリック IP の作成 - Azure portal
description: Azure portal でパブリック IP を作成する方法について説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: e6b7648188e2307da4ef40e0ab3daf6201f9d89d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694871"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Azure portal を使用してパブリック IP アドレスを作成する

この記事では、Azure portal を使用してパブリック IP アドレス リソースを作成する方法について説明します。 

このパブリック IP を関連付けることができるリソース、Basic と Standard SKU の違いについては、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。 

この記事では、IPv4 アドレスについて説明します。 IPv6 アドレスの詳細については、「[Azure VNet の IPv6](./ipv6-overview.md)」を参照してください。

# <a name="standard-sku"></a>[**Standard SKU**](#tab/option-create-public-ip-standard-zones)

以下の手順では、**myStandardZRPublicIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** を選択します。 
3. 検索ボックスに「**パブリック IP アドレス**」と入力します。 検索結果から **[パブリック IP アドレス]** を選択します。
4. **[パブリック IP アドレス]** ページで、 **[作成]** を選択します。
5. **[パブリック IP アドレスの作成]** ページで、次の情報を入力または選択します。 

    | 設定                 | 値                       |
    | ---                     | ---                         |
    | IP バージョン              | [IPv4] を選択します。                 |    
    | SKU                     | **[Standard]** を選択します。         |
    | サービス レベル*                   | **[地域]** を選択します。         |
    | 名前                    | 「**myStandardZRPublicIP**」と入力します。          |
    | IP アドレスの割り当て   | この選択肢は、[静的] としてロックされています。                                        |
    | ルーティングの優先順位      | 既定値の **[Microsoft ネットワーク]** のままにします。 </br> ルーティングの優先順位の詳細については、「[ルーティングの優先順位とは](./routing-preference-overview.md)」を参照してください。 |
    | アイドル タイムアウト (分)  | 既定値の **[4]** のままにします。        |
    | DNS 名ラベル          | この値は空白のままにします。    |
    | サブスクリプション            | サブスクリプションを選択します。   |
    | Resource group          | **[新規作成]** を選択し、「**MyResourceGroup**」と入力します。 </br> **[OK]** を選択します。 |
    | 場所                | **[米国東部 2]** を選択します      |
    | 可用性ゾーン       | **[ゾーン冗長]** 、ゾーンなし、または特定のゾーンを選択します (下記の注を参照)。 |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Azure portal での Standard IP アドレスの作成" border="false":::

> [!NOTE]
> これらの選択は、[Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) を提供するリージョンで有効です。 </br>
これらのリージョンでは特定のゾーンも選択できますが、ゾーン障害に対する回復性はありません。 </br> 可用性ゾーンの詳細については、[可用性ゾーンの概要](https://docs.microsoft.com/azure/availability-zones/az-overview)に関するページを参照してください。

\* = サービス レベルは、[リージョン間ロード バランサー](../load-balancer/cross-region-overview.md)機能 (現在はプレビュー) に関連しています。

# <a name="basic-sku"></a>[**Basic SKU**](#tab/option-create-public-ip-basic)

このセクションでは、**myBasicPublicIP** という名前の Basic IP アドレスを作成します。 

> [!NOTE]
> Basic パブリック IP では、Availability Zones はサポートされません。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** を選択します。 
3. 検索ボックスに「**パブリック IP アドレス**」と入力します。 検索結果から **[パブリック IP アドレス]** を選択します。
4. **[パブリック IP アドレス]** ページで、 **[作成]** を選択します。
5. **[パブリック IP アドレスの作成]** ページで、次の情報を入力または選択します。 

    | 設定                 | 値                       |
    | ---                     | ---                         |
    | IP バージョン              | [IPv4] を選択します。                 |    
    | SKU                     | **[Basic]** を選択します         |
    | 名前                    | 「*myBasicPublicIP*」と入力します。          |
    | IP アドレスの割り当て   | **[静的]** を選択します (下記の注を参照)                                     |
    | アイドル タイムアウト (分)  | 既定値の **[4]** のままにします。       |
    | DNS 名ラベル          | この値は空白のままにします。    |
    | サブスクリプション            | サブスクリプションを選択します。   |
    | Resource group          | **[新規作成]** を選択し、「**MyResourceGroup**」と入力します。 </br> **[OK]** を選択します。 |
    | 場所                | **[米国東部 2]** を選択します      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Azure portal での Standard IP アドレスの作成" border="false":::

時間の経過と共に IP アドレスが変更されても問題ない場合は、**動的** IP の割り当てを選択できます。

---

## <a name="additional-information"></a>関連情報 

上に示した個々のフィールドの詳細については、「[パブリック IP アドレスの管理](./virtual-network-public-ip-address.md#create-a-public-ip-address)」を参照してください。

## <a name="next-steps"></a>次のステップ
- [仮想マシンにパブリック IP アドレス](./associate-public-ip-address-vm.md#azure-portal)を関連付ける
- Azure の[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。