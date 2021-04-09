---
title: チュートリアル - Azure PowerShell を使用してスケール セットにアプリケーションをインストールする
description: Azure PowerShell とカスタム スクリプト拡張機能を使用して仮想マシン スケール セットにアプリケーションをインストールする方法について説明します
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 11/08/2018
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: e783f7f0a9be413679e509e4d6124d50bb811821
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87059280"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>チュートリアル: Azure PowerShell を使用した仮想マシン スケール セットへのアプリケーションのインストール

スケール セット内の仮想マシン (VM) インスタンスでアプリケーションを実行する　には、まず、アプリケーション コンポーネントと必要なファイルをインストールする必要があります。 前のチュートリアルでは、カスタム VM イメージを作成および使用して VM インスタンスをデプロイする方法について学習しました。 このカスタム イメージには、手動によるアプリケーションのインストールと構成が含まれていました。 このほか、各 VM インスタンスがデプロイされた後のスケール セットへのアプリケーションのインストールを自動化したり、既にスケール セットで実行されているアプリケーションを更新したりできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * スケール セットへのアプリケーションの自動インストール
> * Azure カスタム スクリプト拡張機能の使用
> * スケール セットで実行中のアプリケーションの更新

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="what-is-the-azure-custom-script-extension"></a>Azure カスタム スクリプト拡張機能とは
カスタム スクリプト拡張機能は、Azure VM でスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合します。 Azure CLI、Azure PowerShell、Azure portal、REST API と組み合わせて使用することもできます。 詳細については、「[Windows のカスタム スクリプト拡張機能](../virtual-machines/extensions/custom-script-windows.md)」を参照してください。

カスタム スクリプト拡張機能が動作していることを確認するには、IIS Web サーバーをインストールしてスケール セット VM インスタンスのホスト名を出力する機能を備えたスケール セットを作成します。 カスタム スクリプト拡張機能の定義によって、GitHub からサンプル スクリプトがダウンロードされ、必要なパッケージがインストールされた後、VM インスタンスのホスト名が基本的な HTML ページに書き込まれます。


## <a name="create-a-scale-set"></a>スケール セットを作成する
次に、[New-AzVmss](/powershell/module/az.compute/new-azvmss) を使用して仮想マシン スケール セットを作成します。 個々の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 ロード バランサーには、TCP ポート 80 上のトラフィックを分散させるルールが含まれています。 また、TCP ポート 3389 上のリモート デスクトップ トラフィックと TCP ポート 5985 上の PowerShell リモート処理を許可するルールも含まれています。 メッセージが表示されたら、スケール セット内の VM インスタンス用の自分の管理者資格情報を設定できます。

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="create-custom-script-extension-definition"></a>カスタム スクリプト拡張機能の定義の作成
Azure PowerShell では、ハッシュテーブルを使用して、ダウンロードするファイルおよび実行するコマンドを格納します。 次の例では、GitHub のサンプル スクリプトを使用します。 最初に、次のようにこの構成オブジェクトを作成します。

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```


次に、[Add-AzVmssExtension](/powershell/module/az.Compute/Add-azVmssExtension) を使用してカスタム スクリプト拡張機能を適用します。 前に定義した構成オブジェクトが拡張機能に渡されます。 [Update-AzVmss](/powershell/module/az.compute/update-azvmss) を使用して、VM インスタンスの拡張機能を更新して実行します。


```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

スケール セット内の各 VM インスタンスが、GitHub からスクリプトをダウンロードして実行します。 より複雑な例では、複数のアプリケーション コンポーネントおよびファイルをインストールできます。 スケール セットがスケールアップされた場合、新しい VM インスタンスで同じカスタム スクリプト拡張機能定義が自動的に適用され、必要なアプリケーションがインストールされます。


## <a name="allow-traffic-to-application"></a>アプリケーションへのトラフィックを許可する

基本的な Web アプリケーションへのアクセスを許可するには、[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) と [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用してネットワーク セキュリティ グループを作成します。 詳細については、「[Azure 仮想マシン スケール セットのネットワーク](virtual-machine-scale-sets-networking.md)」を参照してください。

```azurepowershell-interactive

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroup" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroup" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

```



## <a name="test-your-scale-set"></a>スケール セットのテスト
Web サーバーが動いていることを確認するには、[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) を使用してロード バランサーのパブリック IP アドレスを取得します。 次の例は、*myResourceGroup* リソース グループに作成された IP アドレスを表示します。

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

ロード バランサーのパブリック IP アドレスを Web ブラウザーに入力します。 ロード バランサーは、次の例に示すように、VM インスタンスのいずれかにトラフィックを配分します。

![IIS の基本的な Web ページ](media/tutorial-install-apps-powershell/running-iis.png)

次の手順で更新されたバージョンを確認できるように、Web ブラウザーを開いたままにしておきます。


## <a name="update-app-deployment"></a>アプリのデプロイの更新
スケール セットのライフサイクル全体にわたり、アプリケーションの更新されたバージョンをデプロイする必要があります。 カスタム スクリプト拡張機能を使用すると、更新されたデプロイ スクリプトを参照し、拡張機能をスケール セットに再適用することができます。 前の手順で作成したスケール セットでは、`-UpgradePolicyMode` が *Automatic* に設定されていました。 この設定により、スケール セット内の VM インスタンスは、自動的にアプリケーションを更新して最新バージョンを適用できます。

*customConfigv2* という名前の新しい構成定義を作成します。 この定義では、アプリケーションのインストール スクリプトの更新された *v2* バージョンを実行します。

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

スケール セット内の VM インスタンスに対するカスタム スクリプト拡張機能構成を更新します。 *customConfigv2* 定義は、アプリケーションの更新されたバージョンを適用するために使用されています。

```azurepowershell-interactive
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

スケール セット内のすべての VM インスタンスが、サンプル Web ページの最新バージョンで自動的に更新されます。 更新されたバージョンを確認するには、ブラウザーで Web サイトを更新します。

![IIS の更新された Web ページ](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>リソースをクリーンアップする
スケール セットと追加のリソースを削除するには、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとそのすべてのリソースを削除します。 `-Force` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。 `-AsJob` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure PowerShell を使用して自動的にアプリケーションをスケール セットにインストールし、更新する方法について学習しました。

> [!div class="checklist"]
> * スケール セットへのアプリケーションの自動インストール
> * Azure カスタム スクリプト拡張機能の使用
> * スケール セットで実行中のアプリケーションの更新

次のチュートリアルに進み、スケール セットを自動的にスケーリングする方法を学習してください。

> [!div class="nextstepaction"]
> [スケール セットを自動的にスケーリングする](tutorial-autoscale-powershell.md)
