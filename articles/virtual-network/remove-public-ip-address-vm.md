---
title: パブリック IP アドレスの関連付けを Azure VM から解除する
titlesuffix: Azure Virtual Network
description: パブリック IP アドレスの関連付けを VM から解除する方法を学ぶ
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900740"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>パブリック IP アドレスの関連付けを Azure VM から解除する 

この記事では、既存の仮想マシン (VM) からパブリック IP アドレスの関連付けを解除する方法について学習します。

パブリック IP アドレスの関連付けを VM から解除するには、[Azure portal](#azure-portal)、Azure [コマンド ライン インターフェイス](#azure-cli) (CLI)、または [PowerShell](#powershell) を使用できます。

## <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. パブリック IP アドレスの関連付けを解除したい仮想マシンを参照または検索して、選択します。
3. [VM] ページで、 **[概要]** を選択し、次の図に示すようにパブリック IP アドレスを選択します：

   ![パブリック IP アドレスを選択する](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. [パブリック IP アドレス] ページで、 **[概要]** を選択し、次の図に示すように **関連付け解除** を選択します：

    ![パブリック IP アドレスの関連付け解除](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. **パブリック IP アドレスの関連付け解除** で、 **[はい]** を選択します。

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) をインストールするか、または Azure Cloud Shell を使用します。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 次の CLI コマンドの **[使ってみる]** ボタンを選択します。 **[使ってみる]** を選択すると Cloud Shell が起動され、お使いの Azure アカウントにサインインできます。

1. Bash でローカルに CLI を使用している場合は、`az login` を使って Azure にサインインします。
2. パブリック IP アドレスが、VM にアタッチされているネットワーク インターフェイスの IP 構成に関連付けられます。 [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) コマンドを使用して、IP 構成からパブリック IP アドレスの関連付け解除します。 次の例では、*myVMPublicIP* という名前のパブリックIPアドレスを、*myResourceGroup* という名前のリソースグループ内にある *myVM* という名前の VM に接続されている *myVMVMNic* という名前の既存のネットワークインターフェイスの *ipconfigmyVM* という名前の IP 構成との関連付けから解除します。
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   VM にアタッチされているネットワーク インターフェイスの名前がわからない場合は、[az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) コマンドを使用して表示します。 たとえば、次のコマンドは、*myResourceGroup* というリソース グループ内の *myVM* という VM にアタッチされているネットワーク インターフェイスの名前を一覧表示します。

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     出力には、次の例とよく似た 1 つ以上の行が含まれます。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     上記の例では、*myVMVMNic* がネットワーク インターフェイスの名前です。

   - ネットワーク インターフェイスの IP 構成の名前がわからない場合は、[az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) コマンドを使用して取得します。 たとえば、次のコマンドは、*myResourceGroup* という名前のリソースグループ内の *myVMVMNic* という名前のネットワークインターフェイスのパブリック IP 構成の名前を一覧表示します：

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - ネットワーク インターフェイスのパブリック IP 構成の名前がわからない場合は、[az network nic ip-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) コマンドを使用して取得します。 たとえば、次のコマンドは、*myResourceGroup* という名前のリソースグループ内の *myVMVMNic* という名前のネットワークインターフェイスのパブリック IP 構成の名前を一覧表示します：

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

[PowerShell](/powershell/azure/install-az-ps) をインストールするか、または Azure Cloud Shell を使用します。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料のシェルです。 PowerShell が事前にインストールされており、アカウントで使用できるように構成されています。 次の PowerShell コマンドの **[使ってみる]** ボタンを選択します。 **[使ってみる]** を選択すると Cloud Shell が起動され、お使いの Azure アカウントにサインインできます。

1. PowerShell をローカルで使用している場合は、`Connect-AzAccount` を使って Azure にサインインします。
2. パブリック IP アドレスが、VM にアタッチされているネットワーク インターフェイスの IP 構成に関連付けられます。 ネットワークインターフェイスを取得するには、[Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) コマンドを使用します。 パブリック IP アドレスの値を null に設定し、[Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) コマンドを使用して、新しい IP 構成をネットワークインターフェイスに書き込みます。

   次の例では、*myVM* という名前の VM に接続されている *myVMVMNic* という名前のネットワークインターフェイスから *myVMPublicIP* という名前のパブリック IP アドレスの関連付けを解除します。 すべてのリソースは、*myResourceGroup* というリソース グループ内にあります。
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - VM にアタッチされているネットワーク インターフェイスの名前がわからない場合は、[Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) コマンドを使用して表示します。 たとえば、次のコマンドは、*myResourceGroup* というリソース グループ内の *myVM* という VM にアタッチされているネットワーク インターフェイスの名前を一覧表示します。

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     出力には、次の例とよく似た行が 1 つ以上含まれます。 出力例では、*myVMVMNic* はネットワーク インターフェイスの名前です。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - ネットワーク インターフェイスの IP 構成の名前がわからない場合は、[Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) コマンドを使用して取得します。 たとえば、次のコマンドは、*myResourceGroup* というリソース グループ内の *myVMVMNic* というネットワーク インターフェイスに対応する IP 構成の名前を一覧表示します。

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     出力には、次の例とよく似た行が 1 つ以上含まれます。 出力例では、*ipconfigmyVM* が IP 構成の名前です。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>次の手順

- [パブリック IP アドレスを VM に関連付ける](associate-public-ip-address-vm.md) 方法を学びます。
