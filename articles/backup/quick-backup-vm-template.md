---
title: Azure クイックスタート - Resource Manager テンプレートを使用した VM のバックアップ
description: Azure Resource Manager テンプレートを使用して仮想マシンをバックアップする方法について説明します
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 643ae23ef566cb433bd3890e6ab2f38050cb1f47
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467150"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure に仮想マシンをバックアップする

[Azure Backup](backup-overview.md) では、オンプレミスのマシンとアプリ、および Azure VM をバックアップします。 この記事では、Resource Manager テンプレートと Azure PowerShell を使用して Azure VM をバックアップする方法を紹介します。 このクイックスタートでは、Resource Manager テンプレートをデプロイして Recover Services コンテナーを作成するプロセスを中心に説明します。 Resource Manager テンプレートの開発に関する詳細については、[Resource Manager ドキュメント](/azure/azure-resource-manager/)と[テンプレート リファレンス](/azure/templates/microsoft.recoveryservices/allversions)をご覧ください。

また、[Azure PowerShell](./quick-backup-vm-powershell.md)、[Azure CLI](quick-backup-vm-cli.md)、または [Azure portal](quick-backup-vm-portal.md) を使用して VM をバックアップすることもできます。

## <a name="create-a-vm-and-recovery-services-vault"></a>VM と Recovery Services コンテナーを作成する

[Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)は、Azure VM などの保護されたリソースのバックアップ データを格納する論理コンテナーです。 バックアップ ジョブを実行すると、Recovery Services コンテナー内に復旧ポイントが作成されます。 この復元ポイントのいずれかを使用して、データを特定の時点に復元できます。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)からのものです。 このテンプレートを使用すると、保護のために DefaultPolicy を使用して構成された単純な Windows VM と Recovery Services コンテナーをデプロイできます。

テンプレートをデプロイするには、 **[試してみる]** を選択し、Azure Cloud Shell を開いて、シェル ウィンドウに次の PowerShell スクリプトを貼り付けます。 コードを貼り付けるには、シェル ウィンドウを右クリックして、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

このクイックスタートでは、Resource Manager テンプレートのデプロイに Azure PowerShell が使用されています。 また、[Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md)、[Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)、[Rest API](../azure-resource-manager/resource-group-template-deploy-rest.md) を使用してテンプレートをデプロイすることもできます。

## <a name="start-a-backup-job"></a>バックアップ ジョブを開始する

このテンプレートにより、VM が作成され、その VM へのバックアップが可能になります。 テンプレートをデプロイしたら、バックアップ ジョブを開始する必要があります。 詳細については、「[バックアップ ジョブを開始する](./quick-backup-vm-powershell.md#start-a-backup-job)」を参照してください。

## <a name="monitor-the-backup-job"></a>バックアップ ジョブを監視する

バックアップ ジョブを監視するには、「[バックアップ ジョブの監視](./quick-backup-vm-powershell.md#monitor-the-backup-job)」を参照してください。

## <a name="clean-up-the-deployment"></a>デプロイをクリーンアップする

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

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Recovery Services コンテナーを作成し、VM の保護を有効にし、最初の復元ポイントを作成しました。

- Azure portal 内で VM をバックアップする[方法の詳細](tutorial-backup-vm-at-scale.md)
- VM をすばやく復元する[方法の詳細](tutorial-restore-disk.md)
