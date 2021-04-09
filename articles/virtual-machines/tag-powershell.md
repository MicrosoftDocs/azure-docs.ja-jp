---
title: PowerShell を使用して VM にタグを付ける方法
description: PowerShell を使用して仮想マシンにタグを付ける方法について説明します
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: bf13d5c0caeb0bf31a383cd23155a6856c81c53b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897393"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>PowerShell を使用して Azure で仮想マシンにタグを付ける方法

この記事では、PowerShell を使用して Azure の VM にタグを付ける方法について説明します。 タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。 現在、Azure では、1 つのリソースまたはリソース グループにつき最大 50 個のタグがサポートされます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。 Azure CLI を使用して仮想マシンにタグを付ける場合は、[Azure CLI を使用して Azure で仮想マシンにタグを付ける方法](tag-cli.md)に関するページを参照してください。

`Get-AzVM` コマンドレットを使用して、VM の現在のタグの一覧を表示します。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

仮想マシンにタグが既に含まれている場合、リすべてのタグが一覧形式で表示されます。

タグを追加するには、`Set-AzResource` コマンドを使用します。 PowerShell でタグを更新すると、タグ全体が更新されます。 既にタグが設定されているリソースに 1 つのタグを追加する場合、リソースに設定するすべてのタグを含める必要があります。 次に、PowerShell コマンドレットでリソースにタグを追加する例を示します。

`Get-AzResource` と `Tags` プロパティを使用して、VM の現在のタグをすべて `$tags` 変数に割り当てます。

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

現在のタグを表示するには、変数を入力します。

```azurepowershell-interactive
$tags
```

出力は次のようになります。

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

次の例では、値が `myLocation` の `Location` というタグを追加します。 新しいキーと値のペアを `$tags` のリストに追加するには、`+=` を使用します。

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

VM 上の *$tags* 変数で定義されているすべてのタグを設定するには、`Set-AzResource` を使用します。

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

リソースのすべてのタグを表示するには、`Get-AzResource` を使用します。

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

出力は次のようになります。新しいタグが含まれています。

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

### <a name="next-steps"></a>次のステップ

- Azure リソースへのタグ付けについて詳しくは、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」と「[タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)」をご覧ください。
- Azure リソースの使用を管理するときにタグを役立てる方法については、「[Azure の課金内容の確認」](../cost-management-billing/understand/review-individual-bill.md)を参照してください。
