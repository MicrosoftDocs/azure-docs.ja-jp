---
title: 静的パブリック IP アドレスを持つ VM を作成する - Azure CLI | Microsoft Docs
description: Azure コマンド ライン インターフェイス (CLI) を使用して、静的パブリック IP アドレスを持つ VM を作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: c4062dd086eeee712376a402da2792352fa3c3ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221344"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Azure CLI を使用して静的パブリック IP アドレスを持つ仮想マシンを作成する

静的パブリック IP アドレスを持つ仮想マシンを作成できます。 パブリック IP アドレスを使用して、インターネットから仮想マシンへの通信を行うことができます。 動的アドレスではなく静的パブリック IP アドレスを割り当てることで、アドレスの変更がないことが保証されます。 [静的パブリック IP アドレス](./public-ip-addresses.md#allocation-method)の詳細を参照してください。 既存の仮想マシンに割り当てられたパブリック IP アドレスの動的から静的への変更、またはプライベート IP アドレスの操作については、[IP アドレスの追加、変更、または削除](virtual-network-network-interface-addresses.md)に関する記事を参照してください。 パブリック IP アドレスには[ごくわずかな料金](https://azure.microsoft.com/pricing/details/ip-addresses)がかかり、サブスクリプションごとに使用できるパブリック IP アドレスの数には[制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)があります。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

ローカル コンピューターから、または Azure Cloud Shell を使用して、次の手順を行うことができます。 ローカル コンピューターを使用する場合は、[Azure CLI がインストールされている](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)ことを確認してください。 Azure Cloud Shell を使用する場合は、任意のコマンド ボックスの右上隅にある **[試してみる]** を選択します。 Cloud Shell によって、Azure にサインインされます。

1. Cloud Shell を使用する場合は、手順 2 に進みます。 コマンド セッションを開き、`az login` で Azure にサインインします。
2. [az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 次の例では、Azure の米国東部リージョンにリソース グループを作成します。

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. 仮想マシンを作成するには、[az vm create](/cli/azure/vm#az-vm-create) コマンドを使用します。 `--public-ip-address-allocation=static` オプションは、仮想マシンに静的パブリック IP アドレスを割り当てます。 次の例は、静的な Basic SKU パブリック IP アドレスを持つ *myPublicIpAddress* という名前の Ubuntu 仮想マシンを作成します。

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   パブリック IP アドレスが Standard SKU である必要がある場合は、上記のコマンドに `--public-ip-sku Standard` を追加します。 [パブリック IP アドレスの SKU](./public-ip-addresses.md#sku) の詳細を確認してください。 パブリック Azure Load Balancer のバックエンド プールに仮想マシンを追加する場合は、仮想マシンのパブリック IP アドレスの SKU がロード バランサーのパブリック IP アドレスの SKU と一致する必要があります。 詳細については、[Azure Load Balancer](../load-balancer/skus.md) に関する記事を参照してください。

4. [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用して、割り当てられたパブリック IP アドレスを表示し、それが静的な Basic SKU アドレスとして作成されたことを確認します。

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure では、仮想マシンを作成したリージョンで使用されるアドレスからパブリック IP アドレスが割り当てられます。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドの範囲 (プレフィックス) の一覧をダウンロードできます。

> [!WARNING]
> 仮想マシンのオペレーティング システム内の IP アドレス設定は変更しないでください。 オペレーティング システムは、Azure のパブリック IP アドレスを認識しません。 プライベート IP アドレスの設定をオペレーティング システムに追加できますが、必要でない限り、この操作は行わないことをお勧めします。必要な場合は、[オペレーティング システムへのプライベート IP アドレスの追加](virtual-network-network-interface-addresses.md#private)に関する記事を読んだ後で行うことをお勧めします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[az group delete](/cli/azure/group#az-group-delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次のステップ

- Azure の[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)の詳細を確認する
- [パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)の詳細を確認する
- [プライベート IP アドレス](./private-ip-addresses.md)と Azure 仮想マシンへの[静的プライベート IP アドレス](virtual-network-network-interface-addresses.md#add-ip-addresses)の割り当ての詳細を確認する
- [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンの作成の詳細を確認する
