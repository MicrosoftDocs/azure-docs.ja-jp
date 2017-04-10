---
title: "Azure で Windows VM リソースにタグを付ける方法 | Microsoft Docs"
description: "リソース マネージャー デプロイ モデルを使用して Azure で作成した Windows 仮想マシンのタグ付けについて説明します。"
services: virtual-machines-windows
documentationcenter: 
author: mmccrory
manager: timlt
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
translationtype: Human Translation
ms.sourcegitcommit: 6b80fa78fea505cd22bae2925ce1affbc1e48ab9
ms.openlocfilehash: 57b88baeeb5c27cfdf142c77aef37b66bb6640d6


---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Azure で Windows 仮想マシンにタグを付ける方法
この記事では、Azure で Resource Manager デプロイ モデルを通して Windows 仮想マシンにタグを付けるさまざまな方法について説明します。 タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。 現在、Azure では、1 つのリソースまたはリソース グループにつき最大 15 個のタグを付けることができます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。 タグは、Resource Manager デプロイ モデル経由で作成されたリソースでのみサポートされます。 Linux 仮想マシンにタグを付けする場合は、「[Azure で Linux 仮想マシンにタグを付ける方法](virtual-machines-linux-tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

[!INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>PowerShell を使用したタグ付け
PowerShell でタグの作成、追加、および削除を行うには、まず [Azure Resource Manager で PowerShell 環境][PowerShell environment with Azure Resource Manager]を設定する必要があります。 設定を完了したら、PowerShell で Compute、Network、および Storage リソースを作成するとき、またはリソースを作成した後にタグを追加できます。 この記事では、Virtual Machines で設定したタグを表示または編集する方法を中心に説明します。

まず `Get-AzureRmVM` コマンドレットで仮想マシンを表示します。

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

仮想マシンにタグが既に含まれている場合、リソースのすべてのタグが表示されます。

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

PowerShell でタグを追加する場合、 `Set-AzureRmResource` コマンドを使用できます。 PowerShell でタグを更新すると、タグ全体が更新されます。 そのため、既にタグが設定されているリソースに&1; つのタグを追加する場合、リソースに設定するすべてのタグを含める必要があります。 次に、PowerShell コマンドレットでリソースにタグを追加する例を示します。

この最初のコマンドレットでは、`Get-AzureRmResource` と `Tags` プロパティを使用して、*MyTestVM* に追加されているすべてのタグを *$tags* 変数に設定します。

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

2 番目のコマンドで、指定した変数のタグが表示されます。

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

3 番目のコマンドで、 *$tags* 変数にタグが追加されます。 新しいキーと値のペアを **+=** リストに付加するために *$tags* を使用していることに注意してください。

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

4 番目のコマンドでは、 *$tags* 変数に定義されているすべてのタグが、指定したリソースに設定されます。 この例では MyTestVM です。

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

5 番目のコマンドでは、リソースのすべてのタグが表示されます。 次のように、値が *MyLocation* のタグとして *Location* の定義が追加されます。

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

PowerShell でタグを付ける方法については、「[Azure リソース コマンドレット][Azure Resource Cmdlets]」をご覧ください。

[!INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>次のステップ
* Azure リソースへのタグ付けの詳細については、「[Azure Resource Manager の概要][Azure Resource Manager Overview]」と「[Using tags to organize your Azure resources (タグを使用した Azure リソースの整理)][Using Tags to organize your Azure Resources]」を参照してください。
* タグが Azure リソースの使用の管理にどのように役立つかを確認するには、[Azure の課金内容の確認][Understanding your Azure Bill]に関するページと「[Microsoft Azure リソースの消費を把握する][Gain insights into your Microsoft Azure resource consumption]」を参照してください。

[PowerShell environment with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager Overview]: ../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../billing/billing-usage-rate-card-overview.md



<!--HONumber=Jan17_HO4-->


