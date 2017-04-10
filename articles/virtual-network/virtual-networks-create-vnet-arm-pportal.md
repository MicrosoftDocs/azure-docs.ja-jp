---
title: "仮想ネットワークの作成 - Azure Portal | Microsoft Docs"
description: "Azure ポータルを使用して仮想ネットワークを作成する方法を説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 54094c18dcbb751835bfa56d53358ce19e08387d
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Azure ポータルを使用した仮想ネットワークの作成

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure には、Azure Resource Manager とクラシックという&2; 種類のデプロイメント モデルがあります。 Resource Manager デプロイメント モデルを使用してリソースを作成することをお勧めします。 2 つのモデルの違いの詳細については、[Azure のデプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md)に関する記事を参照してください。
 
この記事では、Azure Portal を使用して Resource Manager デプロイメント モデルで VNet を作成する方法について説明します。 他のツールを使用して Resource Manager で VNet を作成することや、次の一覧から別のオプションを選択してクラシック デプロイメント モデルで VNet を作成することもできます。

> [!div class="op_single_selector"]
- [ポータル](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [テンプレート](virtual-networks-create-vnet-arm-template-click.md)
- [ポータル (クラシック)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (クラシック)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (クラシック)](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

Azure Portal を使用して仮想ネットワークを作成するには、次の手順を実行します。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. 次の図に示すように、**[新規]** > **[ネットワーク]** > **[仮想ネットワーク]** の順にクリックします。

    ![新しい仮想ネットワーク](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. 次の図に示すように、表示された **[仮想ネットワーク]** ブレードで、*[リソース マネージャー]* が選択されていることを確認し、**[作成]** をクリックします。

    ![仮想ネットワーク](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. 次の図に示すように、表示された **[仮想ネットワークの作成]** ブレードで、**[名前]** に「*TestVNet*」、**[アドレス空間]** に「*192.168.0.0/16*」、**[サブネット名]** に「*FrontEnd*」、**[サブネット アドレス範囲]** に「*192.168.1.0/24*」、**[リソース グループ]** に「*TestRG*」と入力し、**サブスクリプション**、**場所**を選択して、**[作成]** をクリックします。

    ![Create virtual network](./media/virtual-network-create-vnet-arm-pportal/3.png)

    既存のリソース グループを選択することもできます。 リソース グループについて詳しくは、[リソース グループの概要](../azure-resource-manager/resource-group-overview.md#resource-groups)に関する記事をご覧ください。 別の場所を選択することもできます。 Azure の場所とリージョンについて詳しくは、「[Azure リージョン](https://azure.microsoft.com/regions)」をご覧ください。

5. ポータルでは、VNet を作成するときに、サブネットを&1; つだけ作成できます。 このシナリオでは、VNet を作成した後に&2; つ目のサブネットを作成する必要があります。 2 つ目のサブネットを作成するには、次の図に示すように、**[すべてのリソース]** をクリックし、**[すべてのリソース]** ブレードで **[TestVNet]** をクリックします。

    ![作成された VNet](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. 次の図に示すように、表示された **[TestVNet]** ブレードで、**[サブネット]**、**[+サブネット]** の順にクリックし、**[サブネットの追加]** ブレードで、**[名前]** に「*BackEnd*」、**[アドレス範囲]** に「*192.168.2.0/24*」と入力して、**[OK]** をクリックします。

    ![サブネットの追加](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. 次の図に示すように、2 つのサブネットが表示されます。
    
    ![VNet 内のサブネットの一覧](./media/virtual-network-create-vnet-arm-pportal/6.png)

この記事では、テスト用の&2; つのサブネットを持つ仮想ネットワークを作成する方法について説明します。 運用環境で使用する仮想ネットワークを作成する前に、[仮想ネットワークの概要](virtual-networks-overview.md)に関する記事と[仮想ネットワークの計画と設計](virtual-network-vnet-plan-design-arm.md)に関する記事を読んで、仮想ネットワークとすべての設定を完全に理解することをお勧めします。 

## <a name="next-steps"></a>次のステップ

次の接続方法を確認してください。

- 仮想マシン (VM) を仮想ネットワークに接続する方法。[Windows VM の作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md)に関する記事または [Linux VM の作成](../virtual-machines/virtual-machines-linux-quick-create-portal.md)に関する記事をご覧ください。 この記事の手順で VNet とサブネットを作成する代わりに、VM を接続する既存の VNet とサブネットを選択できます。
- 仮想ネットワークを他の仮想ネットワークに接続する方法。[VNet の接続](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関する記事をご覧ください。
- サイト間の仮想プライベート ネットワーク (VPN) または ExpressRoute 回線を使用して、仮想ネットワークをオンプレミスのネットワークに接続する方法。 詳しくは、[サイト間 VPN を使用したオンプレミスのネットワークへの VNet の接続](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)に関する記事と「[ExpressRoute 回線への VNet のリンク](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)」をご覧ください。
