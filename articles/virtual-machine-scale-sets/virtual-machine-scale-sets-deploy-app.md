---
title: 仮想マシン スケール セットへのアプリケーションのデプロイ
description: スケール セット内の Linux と Windows の仮想マシン インスタンスにアプリケーションを展開する方法について説明します。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: e7a4ddaf74df6e04c1597b9c106cd458ddebac55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079593"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>仮想マシン スケール セットへのアプリケーションのデプロイ

スケール セット内の仮想マシン (VM) インスタンスでアプリケーションを実行する　には、まず、アプリケーション コンポーネントと必要なファイルをインストールする必要があります。 この記事では、スケール セット内のインスタンス用にカスタム VM イメージを構築する、または既存の VM インスタンスにインストール スクリプトを自動的に実行する方法について説明します。 また、スケール セットのアプリケーションまたは OS 更新プログラムを管理する方法についても説明します。


## <a name="build-a-custom-vm-image"></a>カスタム VM イメージの構築
Azure プラットフォーム イメージの 1 つを使用してスケール セット内にインスタンスを作成するために、追加のソフトウェアをインストールしたり構成する必要はありません。 これらのコンポーネントのインストールは自動化できますが、スケール セットへ VM インスタンスをプロビジョニングするには時間がかかります。 VM インスタンスに多くの構成変更を適用する場合は、これらの構成スクリプトとタスクを実行するための管理オーバーヘッドが必要になります。

スケール セットにインスタンスがプロビジョニングされると同時にアプリケーションを実行できるカスタム VM イメージを構築すれば、構成管理と VM のプロビジョニングにかかる時間を短縮できます。 スケール セットでカスタム VM イメージを作成し、使用する方法については、次のチュートリアルをご覧ください。

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>カスタム スクリプト拡張機能を使用してアプリケーションをインストールする
カスタム スクリプト拡張機能は、Azure VM でスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。 カスタム スクリプト拡張機能を使用してアプリをインストールする方法の詳細については、次のチュートリアルを参照してください。

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager テンプレート](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>PowerShell DSC を使用して Windows VM にアプリをインストールする
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) は、ターゲット マシンの構成を定義するための管理プラットフォームです。 DSC 構成では、マシンにインストールするものと、ホストを構成する方法を定義します。 Local Configuration Manager (LCM) エンジンは、プッシュされた構成に基づいて要求されたアクションを処理する各ターゲット ノードで実行されます。

PowerShell DSC 拡張機能を使用すると、PowerShell を使用してスケール セット内の VM インスタンスをカスタマイズできます。 次に例を示します。

- GitHub から DSC パッケージをダウンロードするように VM インスタンスに指示する - *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- インストール スクリプトを実行するよう拡張機能を設定する - `configure-http.ps1`
- [Get-AzVmss](/powershell/module/az.compute/get-azvmss) を使用して、スケール セットに関する情報を取得する
- [Update-AzVmss](/powershell/module/az.compute/update-azvmss) を使用して、VM インスタンスに拡張機能を適用する

*myResourceGroup* という名前のリソース グループ内の *myScaleSet* VM インスタンスに DSC 拡張機能が適用されます。 独自の名前を次のように入力します。

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

スケール セットのアップグレード ポリシーが *手動* の場合は、[Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance) を使用して VM インスタンスを更新します。 このコマンドレットは、更新されたスケール セットの構成をVM インスタンスに適用し、アプリケーションをインストールします。


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>cloud-init を使用して Linux VM にアプリをインストールする
[cloud-Init](https://cloudinit.readthedocs.io/en/latest/index.html) は、Linux VM を初回起動時にカスタマイズするために広く使用されているアプローチです。 cloud-init を使って、パッケージをインストールしてファイルを書き込んだり、ユーザーとセキュリティを構成したりすることができます。 初回起動処理中に cloud-init が実行されるので、構成を適用するために追加の手順や必要なエージェントはありません。

cloud-init はディストリビューション全体でも有効です。 たとえば、パッケージをインストールするときに **apt-get install** や **yum install** は使用しません。 代わりに、cloud-init ではインストールするパッケージの一覧をユーザーが定義できます。 cloud-init によって、選択したディストリビューションに対してネイティブのパッケージ管理ツールが自動的に使用されます。

詳細については、*cloud-init.txt* ファイルの例を含む「[Use cloud-init to customize Azure VMs](../virtual-machines/linux/using-cloud-init.md)」(cloud-init を使用して Azure VM をカスタマイズする) を参照してください。

スケール セットを作成して cloud-init ファイルを使用するには、[az vmss create](/cli/azure/vmss) コマンドに `--custom-data` パラメーターを追加し、cloud-init ファイルの名前を指定します。 次の例では、*myScaleSet* という名前のスケール セットを *myResourceGroup* に作成し、*cloud-init.txt* という名前のファイルを使用して VM インスタンスを構成します。 独自の名前を次のように入力します。

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>OS 更新プログラムを使用してアプリケーションをインストールする
OS の新しいリリースが使用できる場合は、新しいカスタム イメージを使用または構築して、スケール セットに [OS の更新プログラムを展開](virtual-machine-scale-sets-upgrade-scale-set.md)できます。 各 VM インスタンスは、指定した最新のイメージにアップグレードされます。 アプリケーションが事前にインストールされたカスタム イメージ、カスタム スクリプト拡張機能、または PowerShell DSC を使用して、アップグレードを実行すると同時にアプリケーションが自動的に提供されるようにできます。 このプロセスを実行する場合、アプリケーションの保守を計画して、バージョンの互換性の問題がないことを確認する必要があります。

アプリケーションが事前にインストールされているカスタム VM イメージを使用すると、アプリケーションの更新プログラムをデプロイメント パイプラインに統合して新しいイメージを構築し、スケール セット全体に OS のアップグレードを展開することができます。 この方法を使用すると、パイプラインが最新のアプリケーション ビルドを選択し、VM を作成および認証し、スケール セットの VM インスタンスをアップグレードできます。 複数のカスタム VM イメージ間にわたってアプリケーション更新プログラムを構築してデプロイするデプロイ パイプラインを実行する場合は、[Packer イメージを作成し、Azure DevOps Services でデプロイする](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)ことができます。また、[Spinnaker](https://www.spinnaker.io/) や [Jenkins](https://jenkins.io/) などの別のプラットフォームを使用することもできます。


## <a name="next-steps"></a>次のステップ
アプリケーションを構築してスケール セットに展開します。[スケール セットの設計の概要](virtual-machine-scale-sets-design-overview.md)を参照してください。 スケール セットの管理方法の詳細については、[PowerShell を使用してスケール セットを管理する](./virtual-machine-scale-sets-manage-powershell.md)を参照してください。
