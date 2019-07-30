---
title: Azure で Windows VM リソースにタグを付ける方法 | Microsoft Docs
description: Resource Manager デプロイ モデルを使用して Azure で作成した Windows 仮想マシンのタグ付けについて説明します。
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 26ee777f7db05ca1850e2a01c1716810624906c0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709851"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Azure で Windows 仮想マシンにタグを付ける方法
この記事では、Azure で Resource Manager デプロイ モデルを通して Windows 仮想マシンにタグを付けるさまざまな方法について説明します。 タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。 現在、Azure では、1 つのリソースまたはリソース グループにつき最大 15 個のタグを付けることができます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。 タグは、Resource Manager デプロイ モデル経由で作成されたリソースでのみサポートされます。 Linux 仮想マシンにタグを付けする場合は、「[Azure で Linux 仮想マシンにタグを付ける方法](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>PowerShell を使用したタグ付け
PowerShell でタグの作成、追加、および削除を行うには、まず [Azure リソース マネージャーで PowerShell 環境][PowerShell environment with Azure Resource Manager]を設定する必要があります。 設定を完了したら、PowerShell で Compute、Network、および Storage リソースを作成するとき、またはリソースを作成した後にタグを追加できます。 この記事では、Virtual Machines で設定したタグを表示または編集する方法を中心に説明します。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

まず `Get-AzVM` コマンドレットで仮想マシンを表示します。

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

仮想マシンにタグが既に含まれている場合、リソースのすべてのタグが表示されます。

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

PowerShell でタグを追加する場合、 `Set-AzResource` コマンドを使用できます。 PowerShell でタグを更新すると、タグ全体が更新されます。 そのため、既にタグが設定されているリソースに 1 つのタグを追加する場合、リソースに設定するすべてのタグを含める必要があります。 次に、PowerShell コマンドレットでリソースにタグを追加する例を示します。

この最初のコマンドレットでは、`Get-AzResource` と `Tags` プロパティを使用して、*MyTestVM* に追加されているすべてのタグを *$tags* 変数に設定します。

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

2 番目のコマンドで、指定した変数のタグが表示されます。

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

3 番目のコマンドで、 *$tags* 変数にタグが追加されます。 新しいキーと値のペアを **+=** リストに付加するために *$tags* を使用していることに注意してください。

        PS C:\> $tags += @{Location="MyLocation"}

4 番目のコマンドでは、 *$tags* 変数に定義されているすべてのタグが、指定したリソースに設定されます。 この例では MyTestVM です。

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

5 番目のコマンドでは、リソースのすべてのタグが表示されます。 次のように、値が *MyLocation* のタグとして *Location* の定義が追加されます。

```
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

## <a name="next-steps"></a>次の手順
* Azure リソースのタグ付けの詳細については、「[Azure Resource Manager の概要][Azure Resource Manager Overview]」and [Using Tags to organize your Azure Resources][Using Tags to organize your Azure Resources]を参照してください。
* Azure リソースの使用を管理するときにタグを役立てる方法については、「[Azure の課金内容の確認][Understanding your Azure Bill]」and [Gain insights into your Microsoft Azure resource consumption][Gain insights into your Microsoft Azure resource consumption]を参照してください。

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
