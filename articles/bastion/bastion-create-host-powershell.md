---
title: Azure PowerShell を使用して Bastion ホストを作成する | Microsoft Docs
description: この記事では、Azure Bastion ホストを作成する方法について学習します
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: ba2716613a0e950cbae5c65add410ac8a8b38955
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92077727"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Azure PowerShell を使用して Azure Bastion ホストを作成する

この記事では、PowerShell を利用し、Azure Bastion ホストを作成する方法について示します。 お使いの仮想ネットワークに Azure Bastion サービスをプロビジョニングすると、同じ仮想ネットワークのすべての VM でシームレスに RDP/SSH をご利用いただけます。 Azure Bastion デプロイは、サブスクリプションやアカウント、仮想マシン単位ではなく、仮想ネットワーク単位です。

任意で、[Azure portal](./tutorial-create-host-portal.md) を使用して Azure Bastion ホストを作成できます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion ホストの作成

このセクションは、Azure PowerShell を使用して新しい Azure Bastion リソースを作成するのに役立ちます。

1. 仮想ネットワークと Azure Bastion サブネットを作成します。 名前の値として **AzureBastionSubnet** を使用して Azure Bastion サブネットを作成する必要があります。 この値によって、Azure でリソースをデプロイするサブネットを把握できます。 これはゲートウェイ サブネットとは異なります。 /27 またはそれより大きいサブネットを使用する必要があります (/27、/26 など)。 ルート テーブルまたは委任なしで **AzureBastionSubnet** を作成します。 **AzureBastionSubnet** でネットワーク セキュリティ グループを使用する場合、[NSG の使用](bastion-nsg.md)に関する記事を参照してください。

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Azure Bastion のパブリック IP アドレスを作成します。 このパブリック IP は (ポート 443 経由で) RDP/SSH がアクセスされる Bastion リソースのパブリック IP アドレスです。 パブリック IP アドレスは、作成している Bastion リソースと同じリージョン内にある必要があります。

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. 仮想ネットワークの AzureBastionSubnet 内に新しい Azure Bastion リソースを作成します。 Bastion リソースを作成してデプロイするには、約 5 分かかります。

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>次のステップ

* 詳細については、[Bastion に関する FAQ](bastion-faq.md) に関するページを参照してください。
* Azure Bastion サブネットでネットワーク セキュリティ グループを使用する方法については、[NSG の使用](bastion-nsg.md)に関するページを参照してください。
