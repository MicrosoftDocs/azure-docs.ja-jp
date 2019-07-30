---
title: Azure Policy を使用して VM 拡張機能のインストールを制限する | Microsoft Docs
description: Azure Policy を使用して拡張機能の展開を制限できます。
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz
ms.reviewer: cynthn
ms.openlocfilehash: 7060ef84a3483b1f74253a7f96ac33f7929b6799
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876736"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Azure Policy を使用して Windows VM への拡張機能のインストールを制限する

自分の Windows VM で特定の拡張機能が使用またはインストールされないようにする必要がある場合は、PowerShell を使用して Azure ポリシーを作成すると、リソース グループ内で VM の拡張機能を制限できます。 

このチュートリアルでは、Cloud Shell で Azure PowerShell を使用します。このバージョンは常に更新され最新になっています。 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-rules-file"></a>規則ファイルを作成する

どの拡張機能をインストールできるようにするかを制限するには、拡張機能を特定するロジックを提供する[規則](../../governance/policy/concepts/definition-structure.md#policy-rule)が必要です。

この例では、Azure Cloud Shell で規則ファイルを作成することで、"Microsoft.Compute" によって発行された拡張機能を拒否する方法を示していますが、ローカルの PowerShell で作業している場合は、ローカル ファイルを作成して、パス ($home/clouddrive) を、ご利用のマシンのそのローカル ファイルへのパスに置き換えることもできます。

[Cloud Shell](https://shell.azure.com/powershell) で、次のように入力します。

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

以下の .json をコピーして、ファイルに貼り付けます。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

完了したら、**Ctrl + O** キーを押し、**Enter** キーを押して、ファイルを保存します。 **Ctrl + X** キーを押してファイルを閉じ、終了します。

## <a name="create-a-parameters-file"></a>パラメーター ファイルを作成する

また、ブロックする拡張機能の一覧を渡すための構造体が自動的に作成されるように、[パラメーター](../../governance/policy/concepts/definition-structure.md#parameters) ファイルを作成する必要もあります。 

この例では、Cloud Shell で VM 用のパラメーター ファイルを作成する方法を示していますが、ローカルの PowerShell で作業している場合は、ローカル ファイルを作成して、パス ($home/clouddrive) を、ご利用のマシンのそのローカル ファイルへのパスに置き換えることもできます。

[Cloud Shell](https://shell.azure.com/powershell) で、次のように入力します。

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

以下の .json をコピーして、ファイルに貼り付けます。

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

完了したら、**Ctrl + O** キーを押し、**Enter** キーを押して、ファイルを保存します。 **Ctrl + X** キーを押してファイルを閉じ、終了します。

## <a name="create-the-policy"></a>ポリシーの作成

ポリシー定義は、使用したい構成を格納するためのオブジェクトです。 ポリシー定義では、規則とパラメーター ファイルを使用してポリシーを定義します。 [New-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition) コマンドレットを使用してポリシー定義を作成します。

 ポリシーの規則とパラメーターは、ご自身のクラウド シェルで、.json ファイルとして作成し格納したファイルです。


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>ポリシーを割り当てる

この例では、[New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment) を使用して、ポリシーをリソース グループに割り当てます。 **myResourceGroup** リソース グループに作成された VM はどれも、VM Access Agent やカスタム スクリプト拡張機能をインストールできません。 

[Get-AzSubscription | Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) コマンドレットを使用して、この例にあるものの代わりに使用するサブスクリプション ID を取得します。

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>ポリシーをテストする

ポリシーをテストするために、VM Access 拡張機能を使用してみます。 "Set-AzVMAccessExtension: リソース 'myVMAccess' はポリシーにより許可されませんでした" というメッセージが表示され、失敗します。

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

ポータルではパスワードの変更が失敗し、"ポリシー違反のためテンプレートのデプロイに失敗しました" という メッセージが表示されます。

## <a name="remove-the-assignment"></a>割り当てを解除する

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>ポリシーを削除する

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>次の手順
詳細については、[Azure Policy](../../governance/policy/overview.md) に関するページをご覧ください。
