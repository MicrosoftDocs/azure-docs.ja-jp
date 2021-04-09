---
title: パブリック IP アドレスのルーティング優先設定を構成する - Azure portal
description: インターネット トラフィックのルーティング優先設定を使用してパブリック IP アドレスを作成する方法について説明します
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: f445eab65e8d2448e57bad19c52a4b72732016bb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672903"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Azure portal を使用してパブリック IP アドレスのルーティング優先設定を構成する

この記事では、パブリック IP アドレスに対して ISP ネットワーク ( **[インターネット]** オプション) による [ルーティング優先設定](./routing-preference-overview.md)を構成する方法について示します。 パブリック IP アドレスを作成した後は、インターネットでの受信および送信トラフィック用に、次の Azure リソースと関連付けることができます。

* 仮想マシン
* 仮想マシン スケール セット
* Azure Kubernetes Service (AKS)
* インターネットに接続するロード バランサー
* Application Gateway
* Azure Firewall

既定では、パブリック IP アドレスに対するルーティング優先設定は、すべての Azure サービスについて Microsoft グローバル ネットワークに設定され、任意の Azure サービスと関連付けることができます。

Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>ルーティング優先設定を使用してパブリック IP アドレスを作成する
1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** を選択します。
3. 検索ボックスに「*パブリック IP アドレス*」と入力します。
3. 検索結果から **[パブリック IP アドレス]** を選択します。 次に、 **[パブリック IP アドレス]** ページで、 **[作成]** を選択します。
1. [SKU] には **[Standard]** を選択します。
1. **[ルーティングの優先順位]** で、 **[インターネット]** を選択します。

      ![パブリック IP アドレスを作成する](./media/routing-preference-portal/public-ip-new.png)
1. **[IPV4 IP アドレスの構成]** セクションで、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | サブスクリプション | サブスクリプションを選択します。|
    | Resource group | **[新規作成]** を選択し、「*RoutingPreferenceResourceGroup*」と入力して、 **[OK]** を選択します。 |
    | 場所 | **[米国東部]** を選択します。|
    | 可用性ゾーン | 既定値「**Zone-redundant**」はそのままにします。 |
1. **［作成］** を選択します

    > [!NOTE]
    > パブリック IP アドレスは、IPv4 または IPv6 アドレスを使用して作成されます。 ただし、現在、ルーティング優先設定では IPV4 のみがサポートされています。

上で作成したパブリック IP アドレスを、[Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンに関連付けることができます。 チュートリアル ページ「[仮想マシンへのパブリック IP アドレスの関連付け](associate-public-ip-address-vm.md#azure-cli)」の CLI セクションを使用して、パブリック IP アドレスを VM に関連付けます。 上で作成したパブリック IP アドレスを、ロード バランサーの **フロントエンド** 構成に割り当てることで、[Azure Load Balancer](../load-balancer/load-balancer-overview.md) と関連付けることもできます。 このパブリック IP アドレスは、負荷分散された仮想 IP アドレス (VIP) として機能します。

## <a name="next-steps"></a>次のステップ
- [ルーティング優先設定が指定されたパブリック IP アドレス](routing-preference-overview.md)について詳しく学習する。
- [VM 用にルーティング優先設定を構成する](tutorial-routing-preference-virtual-machine-portal.md)。
- [PowerShell を使用してパブリック IP アドレスのルーティング優先設定を構成する](routing-preference-powershell.md)。
- Azure の[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。