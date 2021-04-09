---
title: 'クイックスタート: Azure PowerShell を使用して Azure Dedicated HSM を作成する'
description: Azure PowerShell を使用して Azure Dedicated HSM を作成する
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.date: 11/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eebfb257d0324cf2771bd3af979ddbebb8429fb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94905548"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>クイックスタート: Azure PowerShell を使用して Azure Dedicated HSM を作成する

この記事では、[Az.DedicatedHsm](/powershell/module/az.dedicatedhsm) PowerShell モジュールを使用して Azure 専用 HSM を作成する方法について説明します。

## <a name="requirements"></a>必要条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az.DedicatedHsm** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを別途インストールする必要があります。 この PowerShell モジュールは、一般提供されると、将来の Az PowerShell モジュール リリースに含まれ、既定で Azure Cloud Shell 内から使用できるようになります。

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* 複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、特定のサブスクリプションを選択します。

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、[Azure リソース グループ](../azure-resource-manager/management/overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、指定された名前のリソース グループを、指定された場所に作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>専用 HSM を作成する

専用 HSM を作成するには、[New-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm) コマンドレットを使用します。 次の例では、指定したサブスクリプションに専用 HSM を作成します。

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>専用 HSM を取得する

既存の専用 HSM に関する情報を取得するには、[Get-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm) コマンドレットを使用します。 次の例では、指定した専用 HSM を取得します。

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>専用 HSM を更新する

専用 HSM を更新するには、[Update-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm) コマンドレットを使用します。 次の例では、指定したサブスクリプションの専用 HSM を更新します。

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースが不要であれば、次の例を実行して削除できます。

### <a name="remove-a-dedicated-hsm"></a>専用 HSM を削除する

専用 HSM を削除するには、[Remove-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm) コマンドレットを使用します。 次の例では、指定した専用 HSM を削除します。

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>リソース グループを削除します

> [!CAUTION]
> 次の例では、指定されたリソース グループとそれに含まれるすべてのリソースを削除します。
> 指定したリソース グループにこの記事の範囲外のリソースが含まれている場合、それらも削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>次のステップ

[Azure Dedicated HSM](overview.md) の詳細を確認する。
