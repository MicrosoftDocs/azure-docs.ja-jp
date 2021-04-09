---
title: 仮想マシンの両方のルーティング優先設定オプションを構成する - Azure portal
description: 両方のルーティング優先設定を有効にする方法について説明します
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
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678665"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>仮想マシンの両方のルーティング優先設定オプションを構成する

この記事では、仮想マシン (VM) の両方の[ルーティング優先設定](./routing-preference-overview.md)オプション (インターネットと Microsoft グローバル ネットワーク) を構成する方法について説明します。 これは、Microsoft グローバル ネットワーク経由でルーティングされるパブリック IP を使用するネットワーク インターフェイスと、ISP ネットワーク経由でルーティングされるパブリック IP 使用するネットワーク インターフェイスの 2 つの仮想ネットワーク インターフェイスを使用することで実現します。

## <a name="prerequisites"></a>前提条件

「[Azure portal を使用して静的パブリック IP アドレスを持つ仮想マシンを作成する](./virtual-network-deploy-static-pip-arm-portal.md)」で説明されている手順に従って、Azure portal を使用してパブリック IP アドレスを持つ仮想マシンを作成します。 パブリック IP の既定のルーティング優先設定は、**Microsoft グローバル ネットワーク** を介して選択されます。 パブリック IP を持つ仮想マシンを作成したら、そのルーティング優先設定が **インターネット** に構成されているトラフィックを転送するトランジット ISP ネットワーク経由でトラフィックをルーティングする VM に 2番目のパブリック IP を追加します。

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>ルーティング優先設定が選択されたインターネットを使用してパブリック IP アドレスを作成する
1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** を選択します。 
3. 検索ボックスに「*パブリック IP アドレス*」と入力します。
4. 検索結果から **[パブリック IP アドレス]** を選択します。 次に、 **[パブリック IP アドレス]** ページで、 **[作成]** を選択します。
5. **[ルーティングの優先順位]** オプションで、 **[インターネット]** を選択します。

      ![パブリック IP アドレスを作成する](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > パブリック IP アドレスは、IPv4 または IPv6 アドレスを使用して作成されます。 ただし、現在、ルーティング優先設定では IPV4 のみがサポートされています。

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>ネットワーク インターフェイスを作成して、パブリック IP を関連付ける

1. 既定の設定で Azure portal を使用して、[ネットワーク インターフェイス](./routing-preference-overview.md)を作成します。 
1. 前のセクションで作成した[ネットワーク インターフェイスにパブリック IP を関連付け](./associate-public-ip-address-vm.md)ます。 IP アドレスが表示されるまでに、数秒かかる場合があります。 以下に示すように、IP 構成に割り当てられているパブリック IP アドレスを表示します。

    ![パブリック IP を関連付ける](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>VM にネットワーク インターフェイスをアタッチする

1. [前のセクションで作成したネットワーク インターフェイスを仮想マシンにアタッチ](./virtual-network-network-interface-vm.md)します。
2. 仮想マシンに関連付けられている 2 つの仮想ネットワーク インターフェイスを表示できるようになりました。1 つは Microsoft グローバル ネットワーク経由でルーティングされるパブリック IP が関連付けられ、もう 1 つは、示されているように ISP ネットワークを経由してルーティングされています: ![VM にネットワーク インターフェイスをアタッチする](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>次のステップ
- [ルーティング優先設定が指定されたパブリック IP アドレス](routing-preference-overview.md)について詳しく学習する。
- [VM 用にルーティング優先設定を構成する](tutorial-routing-preference-virtual-machine-portal.md)。
- [PowerShell を使用してパブリック IP アドレスのルーティング優先設定を構成する](routing-preference-powershell.md)。
- Azure の[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。