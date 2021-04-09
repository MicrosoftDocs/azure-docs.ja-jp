---
title: Azure 仮想ネットワークでサブネットの委任を追加または削除する
titlesuffix: Azure Virtual Network
description: Azure のサービスのために、委任されたサブネットを追加または削除する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 2bb80ba421617d5fd1699826deda00e56f1e43af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943664"
---
# <a name="add-or-remove-a-subnet-delegation"></a>サブネットの委任を追加または削除する

サブネット委任では、サービスのデプロイ時に一意の ID を利用してサブネットにサービス固有のリソースを作成するための明示的なアクセス許可がサービスに与えられます。 この記事では、Azure サービスの委任されたサブネットを追加または削除する方法について説明します。

## <a name="portal"></a>ポータル

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成

このセクションでは、仮想ネットワークと、後で Azure サービスに委任するサブネットを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に選択します。
1. **[仮想ネットワークの作成]** に次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | 「*MyVirtualNetwork*」と入力します。 |
    | アドレス空間 | 「*10.0.0.0/16*」と入力します。 |
    | サブスクリプション | サブスクリプションを選択します。|
    | Resource group | **[新規作成]** を選択し、「*myResourceGroup*」と入力して、 **[OK]** を選択します。 |
    | 場所 | **[EastUS]** を選択します。|
    | サブネット - 名前 | 「*mySubnet*」と入力します。 |
    | サブネット アドレス範囲 | 「*10.0.0.0/24*」と入力します。 |
    |||
1. 残りは既定値のままにして、 **[作成]** を選択します。

### <a name="permissions"></a>アクセス許可

Azure サービスに委任するサブネットを作成しなかった場合は、次のアクセス許可が必要です: `Microsoft.Network/virtualNetworks/subnets/write`。

組み込みの[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロールには、必要なアクセス許可も含まれています。

### <a name="delegate-a-subnet-to-an-azure-service"></a>サブネットを Azure サービスに委任する

このセクションでは、前のセクションで作成したサブネットを Azure サービスに委任します。

1. ポータルの検索バーに「*myVirtualNetwork*」と入力します。 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。
2. 検索結果で、 *[myVirtualNetwork]* を選択します。
3. **[設定]** で **[サブネット]** を選択し、次に **[mySubnet]** を選択します。
4. *[mySubnet]* ページで、 **[Subnet delegation]\(サブネットの委任\)** 一覧に対し、 **[Delegate subnet to a service]\(サブネットをサービスに委任\)** に一覧表示されているサービスから選択します (たとえば、**Microsoft.DBforPostgreSQL/serversv2**)。  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure サービスからサブネットの委任を削除する

1. ポータルの検索バーに「*myVirtualNetwork*」と入力します。 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。
2. 検索結果で、 *[myVirtualNetwork]* を選択します。
3. **[設定]** で **[サブネット]** を選択し、次に **[mySubnet]** を選択します。
4. *[mySubnet]* ページで、 **[Subnet delegation]\(サブネットの委任\)** 一覧に対し、 **[Delegate subnet to a service]\(サブネットをサービスに委任\)** に一覧表示されているサービスから **[None]\(なし\)** を選択します。 

## <a name="azure-cli"></a>Azure CLI

Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- この記事では、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

### <a name="create-a-resource-group"></a>リソース グループを作成する
[az group create](/cli/azure/group) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、**myResourceGroup** という名前のリソース グループを **eastus** に作成します。

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
[az network vnet create](/cli/azure/network/vnet) を使用して、**myVnet** という名前の仮想ネットワークを作成します。**myResourceGroup** に、**mySubnet** という名前のサブネットを含めます。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>アクセス許可

Azure サービスに委任するサブネットを作成しなかった場合は、次のアクセス許可が必要です: `Microsoft.Network/virtualNetworks/subnets/write`。

組み込みの[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロールには、必要なアクセス許可も含まれています。

### <a name="delegate-a-subnet-to-an-azure-service"></a>サブネットを Azure サービスに委任する

このセクションでは、前のセクションで作成したサブネットを Azure サービスに委任します。 

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) を使用して、Azure サービスへの委任を行うように **mySubnet** という名前のサブネットを更新します。  この例では、委任の例として **Microsoft.DBforPostgreSQL/serversv2** を使用しています。

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

委任が適用されたことを確認するには、[az network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) を使用します。 サービスが、**serviceName** プロパティの下のサブネットに委任されていることを確認します。

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure サービスからサブネットの委任を削除する

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) を使用して、**mySubnet** という名前のサブネットから委任を削除します。

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
委任が削除されたことを確認するには、[az network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) を使用します。 サービスが、**serviceName** プロパティの下のサブネットから削除されていることを確認します。

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
コマンドからの出力は、null のブラケットです。
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Azure に接続する

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>リソース グループを作成する
[New-AzResourceGroup](/cli/azure/group) を使用して Azure リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Create virtual network

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して **myVnet** という名前の仮想ネットワークを作成し、[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) を使用して、**myResourceGroup** 内に **mySubnet** という名前のサブネットを作成します。 この仮想ネットワークの IP アドレス空間は **10.0.0.0/16** です。 この仮想ネットワーク内のサブネットは **10.0.0.0/24** です。  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>アクセス許可

Azure サービスに委任するサブネットを作成しなかった場合は、次のアクセス許可が必要です: `Microsoft.Network/virtualNetworks/subnets/write`。

組み込みの[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロールには、必要なアクセス許可も含まれています。

### <a name="delegate-a-subnet-to-an-azure-service"></a>サブネットを Azure サービスに委任する

このセクションでは、前のセクションで作成したサブネットを Azure サービスに委任します。 

[Add-AzDelegation](/powershell/module/az.network/add-azdelegation) を使用して、**mySubnet** という名前のサブネットを、**myDelegation** という名前で Azure サービスへの委任を行うように更新します。  この例では、委任の例として **Microsoft.DBforPostgreSQL/serversv2** を使用しています。

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
[Get-AzDelegation](/powershell/module/az.network/get-azdelegation) を使用して委任を確認します。

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure サービスからサブネットの委任を削除する

[Remove-AzDelegation](/powershell/module/az.network/remove-azdelegation) を使用して、**mySubnet** という名前のサブネットから委任を削除します。

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
[Get-AzDelegation](/powershell/module/az.network/get-azdelegation) を使用して、委任が削除されたことを確認します。

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>次のステップ
- [Azure でサブネットを管理する](virtual-network-manage-subnet.md)方法を確認します。
