---
title: クイックスタート - Resource Manager テンプレートによる VM バックアップ
description: Azure Resource Manager テンプレートを使用して仮想マシンをバックアップする方法について説明します
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: e64aa10d8ddadd367d04b2b480770a99f3ece1dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88826533"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Azure で仮想マシンをバックアップする

[Azure Backup](backup-overview.md) では、オンプレミスのマシンとアプリ、および Azure VM をバックアップします。 この記事では、Azure Resource Manager テンプレート (ARM テンプレート) と Azure PowerShell を使用して Azure VM をバックアップする方法を紹介します。 このクイックスタートでは、ARM テンプレートをデプロイして Recovery Services コンテナーを作成するプロセスを中心に説明します。 ARM テンプレートの開発に関する詳細については、[Azure Resource Manager ドキュメント](../azure-resource-manager/index.yml)と[テンプレート リファレンス](/azure/templates/microsoft.recoveryservices/allversions)をご覧ください。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

[Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)は、Azure VM などの保護されたリソースのバックアップ データを格納する論理コンテナーです。 バックアップ ジョブを実行すると、Recovery Services コンテナー内に復旧ポイントが作成されます。 この復元ポイントのいずれかを使用して、データを特定の時点に復元できます。 また、[Azure PowerShell](./quick-backup-vm-powershell.md)、[Azure CLI](quick-backup-vm-cli.md)、または [Azure portal](quick-backup-vm-portal.md) を使用して VM をバックアップすることもできます。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)からのものです。 このテンプレートを使用すると、保護のために DefaultPolicy を使用して構成された単純な Windows VM と Recovery Services コンテナーをデプロイできます。

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json":::

テンプレート内に定義されているリソース:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/vaults**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、 **[使ってみる]** を選択し、Azure Cloud Shell を開いて、シェル ウィンドウに次の PowerShell スクリプトを貼り付けます。 コードを貼り付けるには、シェル ウィンドウを右クリックして、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

このクイックスタートでは、ARM テンプレートのデプロイに Azure PowerShell が使用されています。 また、[Azure portal](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Rest API](../azure-resource-manager/templates/deploy-rest.md) を使用してテンプレートをデプロイすることもできます。

## <a name="validate-the-deployment"></a>デプロイの検証

### <a name="start-a-backup-job"></a>バックアップ ジョブを開始する

このテンプレートにより、VM が作成され、その VM へのバックアップが可能になります。 テンプレートをデプロイしたら、バックアップ ジョブを開始する必要があります。 詳細については、「[バックアップ ジョブを開始する](./quick-backup-vm-powershell.md#start-a-backup-job)」を参照してください。

### <a name="monitor-the-backup-job"></a>バックアップ ジョブを監視する

バックアップ ジョブを監視するには、「[バックアップ ジョブの監視](./quick-backup-vm-powershell.md#monitor-the-backup-job)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

VM をバックアップする必要がなくなった場合は、クリーンアップできます。

- VM の復元を試す場合は、クリーンアップをスキップします。
- 既存の VM を使用した場合は、最後の [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを省略すると、リソース グループと VM をそのままの状態にしておくことができます。

保護を無効にし、復元ポイントとコンテナーを削除します。 その後、リソース グループおよび関連付けられているVM リソースを次のように削除します。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Recovery Services コンテナーを作成し、VM の保護を有効にし、最初の復元ポイントを作成しました。

- Azure portal 内で VM をバックアップする[方法の詳細](tutorial-backup-vm-at-scale.md)
- VM をすばやく復元する[方法の詳細](tutorial-restore-disk.md)
- ARM テンプレートを作成する[方法の詳細](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
