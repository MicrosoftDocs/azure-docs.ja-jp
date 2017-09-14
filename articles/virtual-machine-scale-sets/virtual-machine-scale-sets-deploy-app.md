---
title: "仮想マシン スケール セットでアプリをデプロイします。"
description: "拡張機能を使用して、Azure 仮想マシン スケール セットにアプリをデプロイします。"
services: virtual-machine-scale-sets
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: fa7d9d3bef4cb326844ede76171e8c566e87116b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---

# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>仮想マシン スケール セットへのアプリケーションのデプロイ

この記事では、スケール セットのプロビジョニング時にソフトウェアをインストールするさまざまな方法について説明します。

仮想マシン スケール セットによる制限の一部について説明した、[スケール セットの設計の概要](virtual-machine-scale-sets-design-overview.md)に関する記事もご覧ください。

## <a name="capture-and-reuse-an-image"></a>イメージのキャプチャと再利用

Azure 内の仮想マシンを使用して、スケール セットの基本イメージを準備できます。 このプロセスでは、スケール セットの基本イメージとして参照できる管理ディスクをストレージ アカウントに作成します。 

次の手順を実行します。

1. Azure 仮想マシンを作成します。
   * [Linux][linux-vm-create]
   * [Windows][windows-vm-create]

2. 仮想マシンにリモート接続し、お好みに応じてシステムをカスタマイズします。

   必要な場合は、ここでアプリケーションをインストールできます。 ただし、ここでアプリケーションをインストールすると、アプリケーションをアップグレードする場合にまずアプリケーションを削除しなければならない場合があるため、アップグレードが複雑になる可能性があることに注意してください。 代わりに、この手順を実行して、特定のランタイムまたはオペレーティング システムの機能などの、アプリケーションに必要な前提条件をインストールできます。

3. [Linux][linux-vm-capture] または [Windows][windows-vm-capture] の "マシンのキャプチャ" に関するチュートリアルに従ってください。

4. 前の手順でキャプチャしたイメージ URI を使用して[仮想マシン スケール セット][vmss-create]を作成します。

ディスクについての詳細は、[Managed Disks の概要](../virtual-machines/windows/managed-disks-overview.md)と[接続されたデータ ディスクの使用](virtual-machine-scale-sets-attached-disks.md)に関するページをご覧ください。

## <a name="install-when-the-scale-set-is-provisioned"></a>スケール セットのプロビジョニング時のインストール

仮想マシン拡張機能は、仮想マシン スケール セットに適用できます。 仮想マシン拡張機能を使用すると、スケール セット内の仮想マシンを 1 つのグループとしてカスタマイズできます。 拡張機能について詳しくは、[仮想マシン拡張機能](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

お使いのオペレーティング システムが Linux ベースか Windows ベースかによって、3 つの主要な拡張機能を使用できます。

### <a name="windows"></a>Windows

Windows ベースのオペレーティング システムには、**カスタム スクリプト v1.8** 拡張機能、または **PowerShell DSC** 拡張機能を使用します。

#### <a name="custom-script"></a>カスタム スクリプト

カスタム スクリプト拡張機能は、スケール セット内の各仮想マシン インスタンスでスクリプトを実行します。 構成ファイルまたは変数によって、仮想マシンにダウンロードされるファイルと、実行するコマンドが示されます。 この機能を使用してインストーラー、スクリプト、バッチ ファイル、実行可能ファイルなどを実行できます。

PowerShell では、ハッシュテーブルを使用して設定します。 次の例ではカスタム スクリプト拡張機能を構成して、IIS をインストールする PowerShell スクリプトを実行します。

```powershell
# Setup extension configuration hashtable variable
$customConfig = @{
  "fileUris" = @("https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1");
  "commandToExecute" = "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> `"%TEMP%\StartupLog.txt`" 2>&1";
};

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Compute -Type CustomScriptExtension -TypeHandlerVersion 1.8 -Name "customscript1" -Setting $customConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "MyVmssTest143"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>機密情報が含まれる可能性のある設定には、`-ProtectedSetting` のスイッチを使用します。

---------


Azure CLI では、JSON ファイルを使用して設定します。 次の例ではカスタム スクリプト拡張機能を構成して、IIS をインストールする PowerShell スクリプトを実行します。 次の JSON ファイルを _settings.json_ として保存します。

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1"
  ],
  "commandToExecute": "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> \"%TEMP%\StartupLog.txt\" 2>&1"
}
```

次の Azure CLI コマンドを実行します。

```azurecli
az vmss extension set --publisher Microsoft.Compute --version 1.8 --name CustomScriptExtension --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>機密情報が含まれる可能性のある設定には、`--protected-settings` のスイッチを使用します。

### <a name="powershell-dsc"></a>PowerShell DSC

PowerShell DSC を使用して、スケール セット内の仮想マシン インスタンスをカスタマイズできます。 **Microsoft.Powershell** によって公開された **DSC** 拡張機能は、各仮想マシン インスタンスに、提供された DSC 構成をデプロイして実行します。 構成ファイルまたは変数によって、*.zip* パッケージの場所と、実行する_スクリプトと関数_の組み合わせが拡張機能に伝えられます。

PowerShell では、ハッシュテーブルを使用して設定します。 次の例では、IIS をインストールする DSC パッケージをデプロイします。

```powershell
# Setup extension configuration hashtable variable
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Powershell -Type DSC -TypeHandlerVersion 2.24 -Name "dsc1" -Setting $dscConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "myscaleset1"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>機密情報が含まれる可能性のある設定には、`-ProtectedSetting` のスイッチを使用します。

-----------

Azure CLI では、JSON ファイルを使用して設定します。 次の例では、IIS をインストールする DSC パッケージをデプロイします。 次の JSON ファイルを _settings.json_ として保存します。

```json
{
  "wmfVersion": "latest",
  "configuration": {
    "url": "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip",
    "script": "configure-http.ps1",
    "function": "WebsiteTest"
  }
}
```

次の Azure CLI コマンドを実行します。

```azurecli
az vmss extension set --publisher Microsoft.Powershell --version 2.24 --name DSC --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>機密情報が含まれる可能性のある設定には、`--protected-settings` のスイッチを使用します。

### <a name="linux"></a>Linux

Linux では、作成時に**カスタム スクリプト v2.0** 拡張機能または **cloud-init** のいずれかを使用できます。

カスタム スクリプトは、ファイルを仮想マシン インスタンスにダウンロードし、コマンドを実行する単純な拡張機能です。

#### <a name="custom-script"></a>カスタム スクリプト

次の JSON ファイルを _settings.json_ として保存します。

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
  ],
  "commandToExecute": "bash installserver.sh"
}
```

Azure CLI を使用して、既存の仮想マシン スケール セットにこの拡張機能を追加します。 スケール セットの各仮想マシンで、拡張機能が自動的に実行されます。

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>機密情報が含まれる可能性のある設定には、`--protected-settings` のスイッチを使用します。

#### <a name="cloud-init"></a>Cloud-Init

Cloud-Init は、スケール セットの作成時に使用されます。 最初に、_cloud-init.txt_ という名前のローカル ファイルを作成し、これに構成を追加します。 たとえば、[こちらの gist](https://gist.github.com/Thraka/27bd66b1fb79e11904fb62b7de08a8a6#file-cloud-init-txt) をご覧ください。

Azure CLI を使用してスケール セットを作成します。 `--custom-data` のフィールドは、cloud-init スクリプトのファイル名を受け入れます。

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

## <a name="how-do-i-manage-application-updates"></a>アプリケーションの更新プログラムを管理する方法

拡張機能を使用してアプリケーションをデプロイした場合は、何らかの方法で拡張機能の定義を変更してください。 この変更により、すべての仮想マシン インスタンスに拡張機能が再度デプロイされます。 拡張機能に何かしらの変更を加える (参照ファイルの名前を変更するなど) **必要**があります。そうしないと、拡張機能が変更されたことが Azure で認識されません。

独自のオペレーティング システム イメージにアプリケーションが組み込まれている場合は、自動デプロイ パイプラインをアプリケーションの更新プログラムに使用します。 ステージングされたスケール セットを、迅速に運用環境にスワップできるようにアーキテクチャを設計します。 このアプローチの典型的な例が、[Azure Spinnaker ドライバーの動作](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/)です。

[Packer](https://www.packer.io/) および [Terraform Azure](https://www.terraform.io/) でも Azure Resource Manager がサポートされているため、イメージを "コードとして" 定義し、Azure でビルドしてから、スケール セットで VHD を使用することもできます。 ただし、Marketplace イメージでは、これが問題になることがあります。Marketplace からはビットを直接操作しないため、拡張機能/カスタム スクリプトがより重要になります。

## <a name="what-happens-when-a-scale-set-scales-out"></a>スケール セットをスケール アウトした場合
スケール セットに 1 つまたは複数の仮想マシンを追加すると、アプリケーションが自動的にインストールされます。 たとえば、スケール セットで拡張機能が定義されていると、その拡張機能は、新しい仮想マシンが作成されるたびに実行されます。 スケール セットがカスタム イメージに基づいている場合は、新しい仮想マシンすべてがソース カスタム イメージのコピーです。 スケール セットの仮想マシンがコンテナーのホストの場合は、カスタム スクリプト拡張機能にコンテナーを読み込むためのスタートアップ コードがあります。 または、Azure Container Service などのクラスター オーケストレーターに登録されるエージェントが、拡張機能によってインストールされる場合があります。


## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>OS の更新プログラムが更新ドメインに展開される方法
仮想マシン スケール セットを実行しながら、OS イメージを更新する必要があるとします。 PowerShell と Azure CLI は仮想マシンのイメージを更新でき、一度に 1 つの仮想マシンが更新されます。 「[仮想マシン スケール セットのアップグレード](./virtual-machine-scale-sets-upgrade-scale-set.md)」でも、仮想マシン スケール セット全体でオペレーティング システムをアップグレードするときに利用できるオプションについて詳しく説明しています。

## <a name="next-steps"></a>次のステップ

* [PowerShell を使用したスケール セットの管理](virtual-machine-scale-sets-windows-manage.md)
* [スケール セット テンプレートの作成](virtual-machine-scale-sets-mvss-start.md)


[linux-vm-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[windows-vm-create]: ../virtual-machines/windows/tutorial-manage-vm.md
[linux-vm-capture]: ../virtual-machines/linux/capture-image.md
[windows-vm-capture]: ../virtual-machines/windows/capture-image.md 
[vmss-create]: virtual-machine-scale-sets-create.md


