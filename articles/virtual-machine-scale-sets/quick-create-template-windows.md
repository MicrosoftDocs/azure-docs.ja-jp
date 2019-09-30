---
title: クイック スタート - Azure テンプレートを使用して Windows 仮想マシン スケール セットを作成する | Microsoft Docs
description: サンプル アプリのデプロイと自動スケール ルールの構成を行う Azure Resource Manager テンプレートを使用して Windows 仮想マシン スケール セットをすばやく作成する方法について説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: bfbe3d6aac6e97f3568c3c0ce3643bb215726a0b
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376172"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>クイック スタート:Azure テンプレートを使用して Windows 仮想マシン スケール セットを作成する

仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケールする規則を定義したりすることができます。 その後、Azure ロード バランサーがトラフィックをスケール セット内の VM インスタンスに分散します。 このクイック スタートでは、Azure Resource Manager テンプレートを使用して、仮想マシン スケール セットを作成し、サンプル アプリケーションをデプロイします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="define-a-scale-set-in-a-template"></a>テンプレート内にスケール セットを定義する
Azure Resource Manager テンプレートを使用して、関連するリソースのグループをデプロイできます。 テンプレートは JavaScript Object Notation (JSON) で記述され、アプリケーションの Azure インフラストラクチャ環境全体を定義します。 1 つのテンプレートで、仮想マシン スケール セットの作成、アプリケーションのインストール、および自動スケール ルールの構成を実行できます。 変数やパラメーターを使用してこのテンプレートを再利用することで、既存のスケール セットを更新したり、追加のスケール セットを作成したりできます。 テンプレートは、Azure portal、Azure CLI、Azure PowerShell からデプロイすることも、継続的インテグレーション/継続的デリバリー (CI/CD) パイプラインから呼び出すこともできます。

テンプレートの詳細については、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process)」を参照してください。 JSON 構文とプロパティについては、[Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets) のテンプレート リファレンスを参照してください。

テンプレートは、各リソースの種類の構成を定義します。 仮想マシン スケール セットのリソースの種類は、個々の VM に似ています。 仮想マシン スケール セットのリソースの種類のコア部分は次のとおりです。

| プロパティ                     | プロパティの説明                                  | テンプレート値の例                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | 作成する Azure リソースの種類                            | Microsoft.Compute/virtualMachineScaleSets |
| 名前                         | スケール セットの名前                                       | myScaleSet                                |
| location                     | スケール セットを作成する場所                     | East US                                   |
| sku.name                     | 各スケール セット インスタンスの VM サイズ                  | Standard_A1                               |
| sku.capacity                 | 最初に作成する VM インスタンスの数           | 2                                         |
| upgradePolicy.mode           | 変更が発生した場合の VM インスタンスのアップグレード モード              | 自動                                 |
| imageReference               | VM インスタンスに使用するプラットフォームまたはカスタム イメージ | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | 各 VM インスタンス名のプレフィックス                     | myvmss                                    |
| osProfile.adminUsername      | 各 VM インスタンスのユーザー名                        | azureuser                                 |
| osProfile.adminPassword      | 各 VM インスタンスのパスワード                        | P@ssw0rd!                                 |

 次の例は、コア スケール セットのリソース定義を示しています。 スケール セット テンプレートをカスタマイズするために、VM サイズや初期容量を変更したり、別のプラットフォームやカスタム イメージを使用したりできます。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 サンプルを短くするために、仮想ネットワーク インターフェイス カード (NIC) の構成は示されていません。 ロード バランサーなどの追加コンポーネントも示されていません。 [この記事の最後に](#deploy-the-template)完全なスケール セット テンプレートが示されています。


## <a name="add-a-sample-application"></a>サンプル アプリケーションを追加する
スケール セットをテストするには、基本的な Web アプリケーションをインストールします。 スケール セットをデプロイするとき、VM 拡張機能によって、デプロイ後の構成とオートメーション タスク (アプリのインストールなど) を提供できます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。 拡張機能をスケール セットに適用するには、上記のリソース例に *extensionProfile* セクションを追加します。 拡張機能プロファイルは、通常、次のプロパティを定義します。

- 拡張機能の種類
- 拡張機能の発行元
- 拡張機能のバージョン
- 構成またはインストール スクリプトの場所
- VM インスタンスで実行するコマンド

[Windows 上の ASP.NET アプリケーション](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) サンプル テンプレートでは、PowerShell DSC 拡張機能を使用して、IIS で実行される ASP.NET MVC アプリをインストールします。 

インストール スクリプトは、GitHub からダウンロードされます。これは *url* として定義されます。 次に、拡張機能は、*IISInstall.ps1* スクリプトの *InstallIIS* を実行します。これらは *Script*と *function* として定義されます。 ASP.NET アプリ自体は Web 配置パッケージとして提供され、GitHub からダウンロードされます。これは、*WebDeployPackagePath* として定義されます。

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
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ
[Windows 上の ASP.NET MVC アプリケーション](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) テンプレートは、次の **[Deploy to Azure]** ボタンを使用してデプロイできます。 このボタンは、Azure ポータルを開き、完全なテンプレートを読み込み、スケール セットの名前、インスタンス数、管理者の資格情報などのいくつかのパラメーターの入力を求めます。

[![テンプレートを Azure にデプロイする](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

次のように、Azure PowerShell の [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用して ASP.NET アプリケーションを Windows にインストールすることもできます。

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

プロンプトに応答して、VM インスタンスのスケール セットの名前と管理者の資格情報を指定します。 スケール セットが作成され、拡張機能を適用してアプリを構成するのに 10 ～ 15 分かかる可能性があります。


## <a name="test-your-scale-set"></a>スケール セットのテスト
動作中のスケール セットを表示するには、Web ブラウザーでサンプル Web アプリケーションにアクセスします。 次のように [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) を使用して、ロード バランサーのパブリック IP アドレスを取得します。

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

ロード バランサーのパブリック IP アドレスを *http:\//publicIpAddress/MyApp* 形式で Web ブラウザーに入力します。 ロード バランサーは、次の例に示すように、VM インスタンスのいずれかにトラフィックを配分します。

![実行中の IIS サイト](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ
必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループ、スケール セットを削除できます。 `-Force` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。 `-AsJob` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>次の手順
このクイック スタートでは、Azure テンプレートを使用して Windows スケール セットを作成し、PowerShell DSC 拡張機能を使用して基本的な ASP.NET アプリを VM インスタンスにインストールしました。 さらに学習するには、Azure 仮想マシン スケール セットを作成および管理する方法についてのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure 仮想マシン スケール セットの作成と管理](tutorial-create-and-manage-powershell.md)
