---
title: Azure CLI を使用して Bastion ホストを作成する | Azure Bastion
description: この記事では、Bastion ホストを作成および削除する方法について説明します
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: e7f80bb7f9be2e01aa24090d7305b1a5d882da04
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255516"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Azure CLI を使用して Azure Bastion ホストを作成する

この記事では、Azure CLI を使用して Azure Bastion ホストを作成する方法について示します。 お使いの仮想ネットワークに Azure Bastion サービスをプロビジョニングすると、同じ仮想ネットワークのすべての VM でシームレスに RDP/SSH をご利用いただけます。 Azure Bastion デプロイは、サブスクリプションやアカウント、仮想マシン単位ではなく、仮想ネットワーク単位です。

必要に応じて、[Azure portal](bastion-create-host-portal.md) を使用するか、[Azure PowerShell](bastion-create-host-powershell.md) を使用して、Azure Bastion ホストを作成できます。

## <a name="before-you-begin"></a>開始する前に

Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion ホストの作成

このセクションでは、Azure CLI を使用して新しい Azure Bastion リソースを作成する方法について説明します。

1. 仮想ネットワークと Azure Bastion サブネットを作成します。 名前の値として **AzureBastionSubnet** を使用して Azure Bastion サブネットを作成する必要があります。 この値によって、Azure でリソースをデプロイするサブネットを把握できます。 これはゲートウェイ サブネットとは異なります。 /27 またはそれより大きいサブネットを使用する必要があります (/27、/26 など)。 ルート テーブルまたは委任なしで **AzureBastionSubnet** を作成します。 **AzureBastionSubnet** でネットワーク セキュリティ グループを使用する場合、[NSG の使用](bastion-nsg.md)に関する記事を参照してください。

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Azure Bastion のパブリック IP アドレスを作成します。 このパブリック IP は (ポート 443 経由で) RDP/SSH がアクセスされる Bastion リソースのパブリック IP アドレスです。 パブリック IP アドレスは、作成している Bastion リソースと同じリージョン内にある必要があります。

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp --sku Standard
   ```

3. 仮想ネットワークの AzureBastionSubnet 内に新しい Azure Bastion リソースを作成します。 Bastion リソースを作成してデプロイするには、約 5 分かかります。

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>次のステップ

* 詳細については、[Bastion に関する FAQ](bastion-faq.md) に関するページを参照してください。

* Azure Bastion サブネットでネットワーク セキュリティ グループを使用する方法については、[NSG の使用](bastion-nsg.md)に関するページを参照してください。
