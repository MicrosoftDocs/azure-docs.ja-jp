---
title: "Azure 仮想マシン スケール セットにアプリをデプロイする | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して、仮想マシン スケール セットにシンプルな自動スケール アプリケーションをデプロイする方法について説明します。"
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2017
ms.author: ryanwi
ms.openlocfilehash: 07883a33382cc660b043c99872312a9e77228253
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-autoscaling-app-using-a-template"></a>テンプレートを使用した自動スケール アプリのデプロイ

[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)は、関連するリソースのグループをデプロイするための優れた方法です。 このチュートリアルは、[単純なスケール セットのデプロイ](virtual-machine-scale-sets-mvss-start.md)に関する記事に基づいて作成されています。このチュートリアルでは、Azure Resource Manager テンプレートを使用して、スケール セットにシンプルな自動スケール アプリケーションをデプロイする方法について説明します。  PowerShell、CLI、またはポータルを使用して、自動スケールを設定することもできます。 詳細については、[自動スケールの概要](virtual-machine-scale-sets-autoscale-overview.md)に関するページを参照してください。

## <a name="two-quickstart-templates"></a>2 つのクイックスタート テンプレート
スケール セットをデプロイするときに、[VM 拡張機能](../virtual-machines/virtual-machines-windows-extensions-features.md)を使用して、プラットフォーム イメージに新しいソフトウェアをインストールできます。 VM 拡張機能は、アプリのデプロイなど、Azure 仮想マシンでのデプロイ後の構成タスクと自動タスクを提供する小規模なアプリケーションです。 [Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) には、VM 拡張機能を使用して自動スケール アプリケーションをスケール セットにデプロイする方法を示す、2 種類のサンプル テンプレートが用意されています。

### <a name="python-http-server-on-linux"></a>Linux 上の Python HTTP サーバー
[Linux 上の Python HTTP サーバー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)のサンプル テンプレートでは、Linux スケール セットで実行されるシンプルな自動スケール アプリケーションをデプロイします。  カスタム スクリプト VM 拡張機能を使用して、Python Web フレームワークである [Bottle](http://bottlepy.org/docs/dev/) と簡易 HTTP サーバーがスケール セットの各 VM にデプロイされます。 スケール セットは、すべての VM の平均 CPU 使用率が 60% を超えるとスケールアップされ、平均 CPU 使用率が 30% 未満になるとスケールダウンされます。

スケール セット リソースに加え、*azuredeploy.json* サンプル テンプレートでは、仮想ネットワーク、パブリック IP アドレス、ロード バランサー、自動スケール設定の各リソースも宣言されています。  テンプレートでこれらのリソースを作成する方法の詳細については、[自動スケールを使用した Linux スケール セット](virtual-machine-scale-sets-linux-autoscale.md)に関する記事をご覧ください。

*azuredeploy.json* テンプレートでは、`Microsoft.Compute/virtualMachineScaleSets` リソースの `extensionProfile` プロパティでカスタム スクリプト拡張機能を指定します。 `fileUris` ではスクリプトの場所を指定します。 この例では、簡易 HTTP サーバーを定義する *workserver.py* と、Bottle をインストールし、HTTP サーバーを起動する *installserver.sh* の 2 つのファイルを指定しています。 `commandToExecute` では、スケール セットのデプロイ後に実行するコマンドを指定します。

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>Windows 上の ASP.NET MVC アプリケーション
[Windows 上の ASP.NET MVC アプリケーション](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale)のサンプル テンプレートでは、IIS で実行されるシンプルな ASP.NET MVC アプリを Windows スケール セットにデプロイします。  [PowerShell Desired State Configuration (DSC)](virtual-machine-scale-sets-dsc.md) VM 拡張機能を使用して、IIS と MVC アプリがデプロイされます。  スケール セットは、5 分間の CPU 使用率が 50% を超えるとスケールアップされます (一度に 1 つの VM インスタンス)。 

スケール セット リソースに加え、*azuredeploy.json* サンプル テンプレートでは、仮想ネットワーク、パブリック IP アドレス、ロード バランサー、自動スケール設定の各リソースも宣言されています。 また、このテンプレートはアプリケーションのアップグレードも示しています。  テンプレートでこれらのリソースを作成する方法の詳細については、[自動スケールを使用した Windows スケール セット](virtual-machine-scale-sets-windows-autoscale.md)に関する記事をご覧ください。

*azuredeploy.json* テンプレートでは、`Microsoft.Compute/virtualMachineScaleSets` リソースの `extensionProfile` プロパティで [Desired State Configuration (DSC)](virtual-machine-scale-sets-dsc.md) 拡張機能を指定します。この拡張機能は、IIS と WebDeploy パッケージの既定の Web アプリをインストールします。  *IISInstall.ps1* スクリプトは、仮想マシンに IIS をインストールします。このスクリプトは *DSC* フォルダーにあります。  MVC Web アプリは *WebDeploy* フォルダーにあります。  インストール スクリプトと Web アプリのパスは、*azuredeploy.parameters.json* ファイルの `powershelldscZip` パラメーターと `webDeployPackage` パラメーターで定義されています。 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ
[Linux 上の Python HTTP サーバー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)または [Windows 上の ASP.NET MVC アプリケーション](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale)のテンプレートをデプロイする最も簡単な方法は、GitHub の readme ファイルにある **[Deploy to Azure]** ボタンを使用することです。  サンプル テンプレートは、PowerShell または Azure CLI を使用してデプロイすることもできます。

### <a name="powershell"></a>PowerShell
[Linux 上の Python HTTP サーバー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)または [Windows 上の ASP.NET MVC アプリケーション](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) のファイルを、GitHub リポジトリからローカル コンピューター上のフォルダーにコピーします。  *azuredeploy.parameters.json* ファイルを開き、`vmssName`、`adminUsername`、`adminPassword` の各パラメーターの既定値を更新します。 次の PowerShell スクリプトをサンプル フォルダーの *deploy.ps1* に *azuredeploy.json* テンプレートとして保存します。 サンプル テンプレートをデプロイするには、PowerShell コマンド ウィンドウから *deploy.ps1* スクリプトを実行します。

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.compute","microsoft.insights","microsoft.network");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

### <a name="azure-cli"></a>Azure CLI
```azurecli
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

# -e: immediately exit if any command has a non-zero exit status
# -o: prevents errors in a pipeline from being masked
# IFS new value is less likely to cause confusing bugs when looping arrays or arguments (e.g. $@)

usage() { echo "Usage: $0 -i <subscriptionId> -g <resourceGroupName> -n <deploymentName> -l <resourceGroupLocation>" 1>&2; exit 1; }

declare subscriptionId=""
declare resourceGroupName=""
declare deploymentName=""
declare resourceGroupLocation=""

# Initialize parameters specified from command line
while getopts ":i:g:n:l:" arg; do
    case "${arg}" in
        i)
            subscriptionId=${OPTARG}
            ;;
        g)
            resourceGroupName=${OPTARG}
            ;;
        n)
            deploymentName=${OPTARG}
            ;;
        l)
            resourceGroupLocation=${OPTARG}
            ;;
        esac
done
shift $((OPTIND-1))

#Prompt for parameters is some required parameters are missing
if [[ -z "$subscriptionId" ]]; then
    echo "Subscription Id:"
    read subscriptionId
    [[ "${subscriptionId:?}" ]]
fi

if [[ -z "$resourceGroupName" ]]; then
    echo "ResourceGroupName:"
    read resourceGroupName
    [[ "${resourceGroupName:?}" ]]
fi

if [[ -z "$deploymentName" ]]; then
    echo "DeploymentName:"
    read deploymentName
fi

if [[ -z "$resourceGroupLocation" ]]; then
    echo "Enter a location below to create a new resource group else skip this"
    echo "ResourceGroupLocation:"
    read resourceGroupLocation
fi

#templateFile Path - template file to be used
templateFilePath="template.json"

if [ ! -f "$templateFilePath" ]; then
    echo "$templateFilePath not found"
    exit 1
fi

#parameter file path
parametersFilePath="parameters.json"

if [ ! -f "$parametersFilePath" ]; then
    echo "$parametersFilePath not found"
    exit 1
fi

if [ -z "$subscriptionId" ] || [ -z "$resourceGroupName" ] || [ -z "$deploymentName" ]; then
    echo "Either one of subscriptionId, resourceGroupName, deploymentName is empty"
    usage
fi

#login to azure using your credentials
az account show 1> /dev/null

if [ $? != 0 ];
then
    az login
fi

#set the default subscription id
az account set --name $subscriptionId

set +e

#Check for existing RG
az group show $resourceGroupName 1> /dev/null

if [ $? != 0 ]; then
    echo "Resource group with name" $resourceGroupName "could not be found. Creating new resource group.."
    set -e
    (
        set -x
        az group create --name $resourceGroupName --location $resourceGroupLocation 1> /dev/null
    )
    else
    echo "Using existing resource group..."
fi

#Start deployment
echo "Starting deployment..."
(
    set -x
    az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath
)

if [ $?  == 0 ];
 then
    echo "Template has been successfully deployed"
fi
```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
