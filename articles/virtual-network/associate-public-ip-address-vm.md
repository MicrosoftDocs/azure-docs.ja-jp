---
title: 仮想マシンへのパブリック IP アドレスの関連付け
titlesuffix: Azure Virtual Network
description: Azure portal または Azure CLI を使用して仮想マシン (VM) にパブリック IP アドレスを関連付ける方法について説明します。
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 6ea16da3844b8098d87d65e1016f92c69ae34067
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945150"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>仮想マシンへのパブリック IP アドレスの関連付け

この記事では、既存の仮想マシン (VM) にパブリック IP アドレスを関連付ける方法について説明します。 インターネットから VM に接続する場合、VM は、関連付けられたパブリック IP アドレスを備えている必要があります。 パブリック IP アドレスを備えた新しい VM を作成する場合は、[Azure portal](virtual-network-deploy-static-pip-arm-portal.md)、[Azure コマンド ライン インターフェイス (CLI)](virtual-network-deploy-static-pip-arm-cli.md)、または [PowerShell](virtual-network-deploy-static-pip-arm-ps.md) を使用して行うことができます。 パブリック IP アドレスには、わずかな費用がかかります。 詳細については、[価格](https://azure.microsoft.com/pricing/details/ip-addresses/)のページを参照してください。 サブスクリプションごとに使用できるパブリック IP アドレスの数には制限があります。 詳しくは、[制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)に関する記事をご覧ください。

パブリック IP アドレスを VM に関連付けるには、[Azure portal](#azure-portal)、Azure [コマンド ライン インターフェイス](#azure-cli) (CLI)、または [PowerShell](#powershell) を使用できます。

## <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) にサインインします。
2. パブリック IP アドレスを追加する仮想マシンを参照または検索して、選択します。
3. 以下の画像に示すように、 **[設定]** 下にある **[ネットワーク]** を選択して、パブリック IP アドレスを追加するネットワーク インターフェイスを選択します。

   ![ネットワーク インターフェイスを選択する](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > パブリック IP アドレスは、VM にアタッチされたネットワーク インターフェイスに関連付けられます。 上記の画像では、VM には 1 つのネットワーク インターフェイスしかありません。 VM に複数のネットワーク インターフェイスがある場合は、それらすべてが表示され、パブリック IP アドレスを関連付けるネットワーク インターフェイスを選択します。

4. 以下の画像に示すように、 **[IP 構成]** を選択してから、1 つの IP 構成を選択します。

   ![IP 構成を選択する](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > パブリック IP アドレスは、ネットワーク インターフェイスに対応する IP 構成に関連付けられます。 上記の画像では、ネットワーク インターフェイスに IP 構成が 1 つしかありません。 ネットワーク インターフェイスに複数の IP 構成がある場合は、それらすべてが表示され、パブリック IP アドレスを関連付ける IP 構成を選択します。

5. **[有効]** を選択して、 **[IP アドレス] (" *[必要な設定の構成]* ")** を選択します。 既存のパブリック IP アドレスを選択します。 **[パブリック IP アドレスの選択]** ボックスは自動的に閉じられます。 使用可能なパブリック IP アドレスが一覧に何も表示されない場合は、作成する必要があります。 方法については、「[パブリック IP アドレスの作成](virtual-network-public-ip-address.md#create-a-public-ip-address)」をご覧ください。 以下の画像に示すように、 **[保存]** を選択してから、IP 構成のボックスを閉じます。

   ![パブリック IP アドレスを有効にする](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 表示されるパブリック IP アドレスは、VM と同じリージョン内に存在するものです。 リージョン内に複数のパブリック IP アドレスが作成されている場合は、ここにすべて表示されます。 灰色表示されているものは、アドレスが既に別のリソースに関連付けられているためです。

6. 次の画像に示すように、IP 構成に割り当てられているパブリック IP アドレスを表示します。 IP アドレスが表示されるまでに、数秒かかる場合があります。

   ![割り当てられているパブリック IP アドレスを表示する](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > アドレスは、各 Azure リージョン内で使用されるアドレスのプールから割り当てられます。 各リージョン内で使用されるアドレス プールの一覧を表示するには、[Microsoft Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページをご覧ください。 割り当てられるアドレスには、リージョンに使用されているプール内の任意のアドレスを指定できます。 リージョン内の特定のプールからアドレスが割り当てられる必要がある場合は、[パブリック IP アドレスのプレフィックス](public-ip-address-prefix.md)を使用してください。

7. ネットワーク セキュリティ グループにおけるセキュリティ規則を利用して、[VM へのネットワーク トラフィックを許可](#allow-network-traffic-to-the-vm)します。

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) をインストールするか、または Azure Cloud Shell を使用します。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 次の CLI コマンドの **[使ってみる]** ボタンを選択します。 **[使ってみる]** を選択すると Cloud Shell が起動され、お使いの Azure アカウントにサインインできます。

1. Bash でローカルに CLI を使用している場合は、`az login` を使って Azure にサインインします。
2. パブリック IP アドレスが、VM にアタッチされているネットワーク インターフェイスの IP 構成に関連付けられます。 [az network nic-ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) コマンドを使用して、IP 構成にパブリック IP アドレスを関連付けます。 次の例では、*myVMPublicIP* という既存のパブリック IP アドレスを、*myResourceGroup* というリソース グループ内に存在する *myVMVMNic* という既存のネットワーク インターフェイスの *ipconfigmyVM* という名前の IP 構成に関連付けています。
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 既存のパブリック IP アドレスがない場合は、[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) コマンドを使用して作成します。 たとえば、次のコマンドでは、*myResourceGroup* というリソース グループに、*myVMPublicIP* という名前のパブリック IP アドレスを作成します。
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 上記のコマンドでは、カスタマイズ可能ないくつかの設定には既定値を使用して、パブリック IP アドレスを作成しています。 すべてのパブリック IP アドレス設定について詳しくは、「[パブリック IP アドレスの作成](virtual-network-public-ip-address.md#create-a-public-ip-address)」をご覧ください。 アドレスは、各 Azure リージョンに使用されるパブリック IP アドレスのプールから割り当てられます。 各リージョン内で使用されるアドレス プールの一覧を表示するには、[Microsoft Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページをご覧ください。

   - VM にアタッチされているネットワーク インターフェイスの名前がわからない場合は、[az vm nic list](/cli/azure/vm/nic#az-vm-nic-list) コマンドを使用して表示します。 たとえば、次のコマンドは、*myResourceGroup* というリソース グループ内の *myVM* という VM にアタッチされているネットワーク インターフェイスの名前を一覧表示します。

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     出力には、次の例とよく似た 1 つ以上の行が含まれます。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     上記の例では、*myVMVMNic* がネットワーク インターフェイスの名前です。

   - ネットワーク インターフェイスの IP 構成の名前がわからない場合は、[az network nic ip-config list](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) コマンドを使用して取得します。 たとえば、次のコマンドは、*myResourceGroup* というリソース グループ内の *myVMVMNic* というネットワーク インターフェイスに対応する IP 構成の名前を一覧表示します。

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) コマンドを使って、IP 構成に割り当てられているパブリック IP アドレスを表示します。 次の例は、*myResourceGroup* というリソース グループ内の *myVM* という既存の VM に割り当てられている IP アドレスを表示します。

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > アドレスは、各 Azure リージョン内で使用されるアドレスのプールから割り当てられます。 各リージョン内で使用されるアドレス プールの一覧を表示するには、[Microsoft Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページをご覧ください。 割り当てられるアドレスには、リージョンに使用されているプール内の任意のアドレスを指定できます。 リージョン内の特定のプールからアドレスが割り当てられる必要がある場合は、[パブリック IP アドレスのプレフィックス](public-ip-address-prefix.md)を使用してください。

4. ネットワーク セキュリティ グループにおけるセキュリティ規則を利用して、[VM へのネットワーク トラフィックを許可](#allow-network-traffic-to-the-vm)します。

## <a name="powershell"></a>PowerShell

[PowerShell](/powershell/azure/install-az-ps) をインストールするか、または Azure Cloud Shell を使用します。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料のシェルです。 PowerShell が事前にインストールされており、アカウントで使用できるように構成されています。 次の PowerShell コマンドの **[使ってみる]** ボタンを選択します。 **[使ってみる]** を選択すると Cloud Shell が起動され、お使いの Azure アカウントにサインインできます。

1. PowerShell をローカルで使用している場合は、`Connect-AzAccount` を使って Azure にサインインします。
2. パブリック IP アドレスが、VM にアタッチされているネットワーク インターフェイスの IP 構成に関連付けられます。 [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) および [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) コマンドを使用して、そのネットワーク インターフェイスが属する仮想ネットワークとサブネットを取得します。 次に、[Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) コマンドを使用してネットワーク インターフェイスを取得し、[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) コマンドを使用して既存のパブリック IP アドレスを取得します。 その後、[Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) コマンドを使用してパブリック IP アドレスを IP 構成に関連付け、[Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) コマンドを使用してネットワーク インターフェイスへの新しい IP 構成を書き込みます。

   次の例では、*myVMPublicIP* という既存のパブリック IP アドレスを、*myVMVNet* という仮想ネットワーク内の *myVMSubnet* というサブネットに存在する *myVMVMNic* という既存のネットワーク インターフェイスの *ipconfigmyVM* という名前の IP 構成に関連付けています。 すべてのリソースは、*myResourceGroup* というリソース グループ内にあります。
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 既存のパブリック IP アドレスがない場合は、[New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) コマンドを使用して作成します。 たとえば、次のコマンドでは、*eastus* リージョンにある *myResourceGroup* というリソース グループに、*myVMPublicIP* という名前の *dynamic* パブリック IP アドレスを作成します。
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 上記のコマンドでは、カスタマイズ可能ないくつかの設定には既定値を使用して、パブリック IP アドレスを作成しています。 すべてのパブリック IP アドレス設定について詳しくは、「[パブリック IP アドレスの作成](virtual-network-public-ip-address.md#create-a-public-ip-address)」をご覧ください。 アドレスは、各 Azure リージョンに使用されるパブリック IP アドレスのプールから割り当てられます。 各リージョン内で使用されるアドレス プールの一覧を表示するには、[Microsoft Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページをご覧ください。

   - VM にアタッチされているネットワーク インターフェイスの名前がわからない場合は、[Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) コマンドを使用して表示します。 たとえば、次のコマンドは、*myResourceGroup* というリソース グループ内の *myVM* という VM にアタッチされているネットワーク インターフェイスの名前を一覧表示します。

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     出力には、次の例とよく似た行が 1 つ以上含まれます。 出力例では、*myVMVMNic* はネットワーク インターフェイスの名前です。
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - ネットワーク インターフェイスが存在する仮想ネットワークの名前またはサブネットがわからない場合は、`Get-AzNetworkInterface` コマンドを使用して情報を表示します。 たとえば、次のコマンドは、*myResourceGroup* というリソース グループ内の *myVMVMNic* というネットワーク インターフェイスに対応する仮想ネットワークおよびサブネット情報を取得します。

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     出力には、次の例とよく似た行が 1 つ以上含まれます。 出力例では、 *myVMVNET* は仮想ネットワークの名前であり、*myVMSubnet* はサブネットの名前です。
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - ネットワーク インターフェイスの IP 構成の名前がわからない場合は、[Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) コマンドを使用して取得します。 たとえば、次のコマンドは、*myResourceGroup* というリソース グループ内の *myVMVMNic* というネットワーク インターフェイスに対応する IP 構成の名前を一覧表示します。

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     出力には、次の例とよく似た行が 1 つ以上含まれます。 出力例では、*ipconfigmyVM* が IP 構成の名前です。
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) コマンドを使って、IP 構成に割り当てられているパブリック IP アドレスを表示します。 次の例では、*myResourceGroup* というリソース グループにある *myVMPublicIP* というパブリック IP アドレスに割り当てられたアドレスを示しています。

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   IP 構成に割り当てられているパブリック IP アドレスの名前がわからない場合は、次のコマンドを実行して取得します。

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   出力には、次の例とよく似た行が 1 つ以上含まれます。 出力例では、*myVMPublicIP* が IP 構成に割り当てられているパブリック IP アドレスの名前です。

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > アドレスは、各 Azure リージョン内で使用されるアドレスのプールから割り当てられます。 各リージョン内で使用されるアドレス プールの一覧を表示するには、[Microsoft Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページをご覧ください。 割り当てられるアドレスには、リージョンに使用されているプール内の任意のアドレスを指定できます。 リージョン内の特定のプールからアドレスが割り当てられる必要がある場合は、[パブリック IP アドレスのプレフィックス](public-ip-address-prefix.md)を使用してください。

4. ネットワーク セキュリティ グループにおけるセキュリティ規則を利用して、[VM へのネットワーク トラフィックを許可](#allow-network-traffic-to-the-vm)します。

## <a name="allow-network-traffic-to-the-vm"></a>VM へのネットワーク トラフィックを許可する

インターネットからパブリック IP アドレスに接続するには、事前に、ネットワーク インターフェイスまたはそのネットワーク インターフェイスが存在しているサブネット、あるいはその両方に関連付けたはずのネットワーク セキュリティ グループにおいて、必要なポートが開かれていることを確認してください。 セキュリティ グループでは、ネットワーク インターフェイスのプライベート IP アドレスへのトラフィックをフィルター処理しますが、受信したインターネット トラフィックがパブリック IP アドレスに到達すると、Azure によってパブリック アドレスがプライベート IP アドレスに変換されます。そのため、ネットワーク セキュリティ グループによってトラフィック フローが妨げられると、パブリック IP アドレスによる通信は失敗します。 [ポータル](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)、[CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)、または [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell) を使用して、ネットワーク インターフェイスとそのサブネットに対する有効なセキュリティ規則を確認できます。

## <a name="next-steps"></a>次のステップ

ネットワーク セキュリティ グループを利用して、VM への受信インターネット トラフィックを許可します。 ネットワーク セキュリティ グループを作成する方法については、[ネットワーク セキュリティ グループの操作](manage-network-security-group.md#work-with-network-security-groups)に関するページをご覧ください。 ネットワーク セキュリティ グループの詳細については、[セキュリティ グループ](./network-security-groups-overview.md)に関するページをご覧ください。
