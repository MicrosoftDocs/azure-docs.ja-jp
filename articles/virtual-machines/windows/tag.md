---
title: Azure で Windows VM リソースにタグを付ける方法
description: Resource Manager デプロイ モデルを使用して Azure で作成した Windows 仮想マシンのタグ付けについて説明します。
author: mmccrory
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 84fc6e6fee0b4a85b909c9b43a17381b31f39abf
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291994"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Azure で Windows 仮想マシンにタグを付ける方法
この記事では、Azure で Resource Manager デプロイ モデルを通して Windows 仮想マシンにタグを付けるさまざまな方法について説明します。 タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。 現在、Azure では、1 つのリソースまたはリソース グループにつき最大 50 個のタグがサポートされます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。 タグは、Resource Manager デプロイ モデル経由で作成されたリソースでのみサポートされます。 Linux 仮想マシンにタグを付けする場合は、「[Azure で Linux 仮想マシンにタグを付ける方法](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>PowerShell を使用したタグ付け
PowerShell でタグの作成、追加、および削除を行うには、まず [Azure リソース マネージャーで PowerShell 環境][PowerShell environment with Azure Resource Manager]を設定する必要があります。 設定を完了したら、PowerShell で Compute、Network、および Storage リソースを作成するとき、またはリソースを作成した後にタグを追加できます。 この記事では、Virtual Machines で設定したタグを表示または編集する方法を中心に説明します。

 

まず `Get-AzVM` コマンドレットで仮想マシンを表示します。

```azurepowershell
PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"
```

仮想マシンにタグが既に含まれている場合、リソースのすべてのタグが表示されます。

```json
Tags : {
        "Application": "MyApp1",
        "Created By": "MyName",
        "Department": "MyDepartment",
        "Environment": "Production"
        }
```

PowerShell でタグを追加する場合、 `Set-AzResource` コマンドを使用できます。 PowerShell でタグを更新すると、タグ全体が更新されます。 そのため、既にタグが設定されているリソースに 1 つのタグを追加する場合、リソースに設定するすべてのタグを含める必要があります。 次に、PowerShell コマンドレットでリソースにタグを追加する例を示します。

この最初のコマンドレットでは、`Get-AzResource` と `Tags` プロパティを使用して、*MyTestVM* に追加されているすべてのタグを *$tags* 変数に設定します。

```azurepowershell
PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags
```

2 番目のコマンドで、指定した変数のタグが表示されます。

```azurepowershell
PS C:\> $tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

3 番目のコマンドで、 *$tags* 変数にタグが追加されます。 新しいキーと値のペアを **+=** リストに付加するために *$tags* を使用していることに注意してください。

```azurepowershell
PS C:\> $tags += @{Location="MyLocation"}
```

4 番目のコマンドでは、 *$tags* 変数に定義されているすべてのタグが、指定したリソースに設定されます。 この例では MyTestVM です。

```azurepowershell
PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

5 番目のコマンドでは、リソースのすべてのタグが表示されます。 次のように、値が *MyLocation* のタグとして *Location* の定義が追加されます。

```azurepowershell
PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

PowerShell でタグを付ける方法については、「 [Azure リソース コマンドレット][Azure Resource Cmdlets]」を参照してください。

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>次のステップ
* Azure リソースへのタグ付けについて詳しくは、「[Azure Resource Manager の概要][Azure Resource Manager Overview]」と「[タグを使用した Azure リソースの整理][Using Tags to organize your Azure Resources]」をご覧ください。
* タグが Azure リソースの使用の管理にどのように役立つかを確認するには、[Azure の課金内容の確認][Understanding your Azure Bill]に関するページと「[Microsoft Azure リソースの消費を把握する][Gain insights into your Microsoft Azure resource consumption]」を参照してください。

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: /powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
