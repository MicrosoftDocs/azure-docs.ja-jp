---
title: Azure でテンプレートから Windows VM を作成する
description: Resource Manager テンプレートと PowerShell を使用して、新しい Windows VM を簡単に作成します。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 534a8480e783853f7497a0538b04e0302a9cda0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553614"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Resource Manager テンプレートから Windows 仮想マシンを作成する

Azure Resource Manager テンプレートと、Azure Cloud シェルの Azure PowerShell を使用して、Windows 仮想マシンを作成する方法を説明します。 この記事で作成されたテンプレートは、単一のサブネットを持つ新しい仮想ネットワークに、Windows Server を実行する単一の仮想マシンをデプロイします。 Linux 仮想マシンの作成方法については、「[Azure Resource Manager テンプレートを使用して Linux 仮想マシンを作成する方法](../linux/create-ssh-secured-vm-from-template.md)」を参照してください。

別の方法としては、Azure portal からテンプレートをデプロイする方法があります。 ポータルでテンプレートを開くには、 **[Azure に配置する]** ボタンを選択します。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

通常、Azure 仮想マシンの作成には、次の 2 つの手順が含まれます。

- リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンの前にリソース グループを作成する必要があります。
- 仮想マシンを作成します。

次の例では、[Azure クイック スタート テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)から VM を作成します。 テンプレートのコピーを次に示します。

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

PowerShell スクリプトを実行するには、**[使ってみる]** を選択して、Azure Cloud シェルを開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Azure Cloud シェルからではなく、PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュールが必要になります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

前の例では、GitHub に格納されているテンプレートが指定されています。 テンプレートをダウンロードまたは作成し、`--template-file` パラメーターを使用してローカル パスを指定することもできます。

次にその他のリソースを示します。

- Resource Manager テンプレートを開発する方法については、[Azure Resource Manager のドキュメント](../../azure-resource-manager/index.yml)を参照してください。
- Azure 仮想マシンのスキーマを表示するには、「[Azure テンプレート リファレンス](/azure/templates/microsoft.compute/allversions)」をご覧ください。
- さらに仮想マシン テンプレートのサンプルを表示するには、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)」を参照してください。

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続

前述のスクリプトの最後の PowerShell コマンドでは、仮想マシンの名前が表示されます。 仮想マシンへの接続方法については、「[Windows が実行されている Azure 仮想マシンに接続してサインオンする方法](./connect-logon.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- デプロイに問題がある場合は、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](../../azure-resource-manager/templates/common-deployment-errors.md)」を参照してください。
- 仮想マシンを作成して管理する方法については、「[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](tutorial-manage-vm.md)」を参照してください。

テンプレートの作成に関する詳細については、JSON 構文とデプロイしたリソースの種類のプロパティを参照してください。

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
