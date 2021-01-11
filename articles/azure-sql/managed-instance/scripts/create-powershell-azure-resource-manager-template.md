---
title: マネージド インスタンスを作成する (ARM テンプレートと PowerShell)
titleSuffix: Azure SQL Managed Instance
description: この Azure PowerShell サンプル スクリプトを使用して、マネージド インスタンスを作成します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 6758aed67949032bb93086527623b60af33e716e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079559"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>PowerShell と Azure Resource Manager テンプレートを使用してマネージド インスタンスを作成する

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

マネージド インスタンスは、Azure PowerShell ライブラリと Azure Resource Manager テンプレートを使用して作成できます。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成します。

Azure PowerShell コマンドで、定義済みの Azure Resource Manager テンプレートを使用してデプロイを開始できます。 テンプレート内に次のプロパティを指定できます。

- マネージド インスタンス名
- SQL 管理者のユーザー名とパスワード。
- インスタンスのサイズ (コアの数と最大ストレージ サイズ)。
- インスタンスが配置される VNet とサブネット。
- インスタンスのサーバーレベルの照合順序 (プレビュー)。

インスタンス名、SQL 管理者ユーザー名、VNet/サブネット、および照合順序は、後で変更することはできません。 その他のインスタンスのプロパティは変更できます。

## <a name="prerequisites"></a>前提条件

このサンプルでは、お使いのマネージド インスタンスに対する[有効なネットワーク環境の作成](../virtual-network-subnet-create-arm-template.md)または[既存の VNet の変更](../vnet-existing-add-subnet.md)が済んでいるものとします。 必要であれば、別の [Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)を使用して、ネットワーク環境を準備することができます。 


サンプルではコマンドレット [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) と [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) を使用するので、次の PowerShell モジュールをインストールしてあることを確認してください。

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート


次のスクリプトを .json ファイルに保存し、その保存先をメモします。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

先ほど保存した .json ファイルの正しいパスで次の PowerShell スクリプトを更新し、スクリプト内のオブジェクトの名前を変更します。

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

スクリプトが完了すると、すべての Azure サービスおよび構成済みの IP アドレスからマネージド インスタンスにアクセスすることができます。

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

Azure SQL Managed Instance 用の PowerShell サンプル スクリプトは、他にも [Azure SQL Managed Instance 用の PowerShell スクリプト](../../database/powershell-script-content-guide.md)のページでご覧いただけます。
