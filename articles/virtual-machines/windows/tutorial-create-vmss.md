---
title: チュートリアル:Windows 仮想マシン スケール セットを作成する
description: Azure PowerShell で仮想マシン スケール セットを使用して、Windows VM 上に高可用性アプリケーションを作成してデプロイする方法について説明します
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 11/30/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: b3853ddc71d1a9be26b2492764a9b341446e0eeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078743"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>チュートリアル:Azure PowerShell で仮想マシン スケール セットを作成して Windows に高可用性アプリをデプロイする
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM 数は、手動でスケーリングすることができます。 また、CPU の使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケーリングするルールを定義することもできます。 このチュートリアルでは、仮想マシン スケール セットを Azure にデプロイします。次の方法について説明します。

> [!div class="checklist"]
> * カスタム スクリプト拡張機能を使用して、スケールする IIS サイトを定義する
> * スケール セットのロード バランサーを作成する
> * 仮想マシン スケール セットを作成する
> * スケール セット内のインスタンスの数を増減させる
> * 自動スケール ルールを作成する

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="scale-set-overview"></a>スケール セットの概要
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM は、1 つ以上の *配置グループ* 内の論理障害ドメインおよび更新ドメインに配布されます。 配置グループは同じように構成された VM のグループであり、[可用性セット](tutorial-availability-sets.md)に似ています。

スケール セットには必要に応じて VM が作成されます。 スケール セットの VM を追加または削除する方法とタイミングを制御するには、自動スケール ルールを定義します。 これらのルールは、CPU の負荷、メモリの使用量、ネットワーク トラフィックなどのメトリックに基づいて発動できます。

Azure プラットフォーム イメージを使う場合、スケール セットは最大 1,000 個の VM をサポートします。 大量のインストールが必要なワークロードや、VM に大幅なカスタマイズが必要なワークロードについては、[カスタム VM イメージを作成する](tutorial-custom-images.md)という方法もあります。 カスタム イメージを使うと、最大 600 個の VM をスケール セットに作成できます。


## <a name="create-a-scale-set"></a>スケール セットを作成する
[New-AzVmss](/powershell/module/az.compute/new-azvmss) を使用して仮想マシン スケール セットを作成します。 次の例では、*Windows Server 2016 Datacenter* プラットフォーム イメージを使用する *myScaleSet* という名前のスケール セットを作成します。 仮想ネットワーク用の Azure ネットワーク リソース、パブリック IP アドレス、およびロード バランサーが自動的に作成されます。 メッセージが表示されたら、スケール セット内の VM インスタンス用の自分の管理者資格情報を設定できます。

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="deploy-sample-application"></a>サンプル アプリケーションをデプロイする
スケール セットをテストするには、基本的な Web アプリケーションをインストールします。 VM インスタンスに IIS をインストールするスクリプトをダウンロードして実行するために、Azure カスタム スクリプト拡張機能が使用されます。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 詳細については、「[Windows のカスタム スクリプト拡張機能](../extensions/custom-script-windows.md)」を参照してください。

カスタム スクリプト拡張機能を使用して、基本的な IIS Web サーバーをインストールします。 次のように、IIS をインストールするカスタム スクリプト拡張機能を適用します。

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>アプリケーションへのトラフィックを許可する

基本的な Web アプリケーションへのアクセスを許可するには、[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) と [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用してネットワーク セキュリティ グループを作成します。 詳細については、「[Azure 仮想マシン スケール セットのネットワーク](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md)」を参照してください。

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

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
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>スケール セットのテスト
スケール セットの動作を確認するには、[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) を使用してロード バランサーのパブリック IP アドレスを取得します。 以下の例では、スケール セットの一部として作成された *myPublicIP* の IP アドレスを表示します。

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myPublicIPAddress" | select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力します。 Web アプリが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の IIS サイト](./media/tutorial-create-vmss/running-iis-site.png)

動作しているスケール セットを確認するには、Web ブラウザーを強制的に最新の情報に更新して、アプリが動作しているすべての VM に対してロード バランサーからトラフィックが負荷分散されているのを見ることができます。


## <a name="management-tasks"></a>管理タスク
スケール セットのライフサイクルを通じて、1 つ以上の管理タスクを実行する必要がある場合があります。 さらに、各種ライフサイクルのタスクを自動化するスクリプトを作成するほうが便利な場合もあります。 Azure PowerShell には、これらのタスクを簡単に実行するための方法が用意されています。 一般的なタスクには、次のようなものがあります。

### <a name="view-vms-in-a-scale-set"></a>スケール セットの VM を表示する
スケール セット内の VM インスタンスの一覧を表示するには、次のように [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) を使用します。

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"
```

次の出力例には、スケール セット内の 2 つの VM インスタンスが示されています。

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

特定の VM インスタンスに関する追加情報を表示するには、[Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) に `-InstanceId` パラメーターを追加します。 次の例では、VM インスタンス *1* に関する情報を表示しています。

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" `
  -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>VM インスタンスを増減させる
現時点でスケール セットに存在するインスタンスの数を確認するには、[Get-AzVmss](/powershell/module/az.compute/get-azvmss) と *sku.capacity* に対するクエリを使います。

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" | `
  Select -ExpandProperty Sku
```

インスタンスの数が表示されたら、[Update-AzVmss](/powershell/module/az.compute/update-azvmss) を使用して、スケール セット内の仮想マシンの数を手動で増やしたり減らしたりできます。 次の例では、スケール セット内の VM の数を *3* に設定します。

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

スケール セット内のインスタンスの数を指定どおりに更新する処理が終わるまでには、数分かかります。


### <a name="configure-autoscale-rules"></a>自動スケール ルールを構成する
スケール セット内のインスタンスの数を手動でスケールする代わりに、自動スケール ルールを定義しておくこともできます。 自動スケール ルールを使うと、スケール セット内のインスタンスが監視され、定義しておいたメトリックとしきい値に基づいて適切な対応が自動で実行されます。 以下の例は、CPU に対する負荷の平均が 5 分間に 60% を上回った場合にインスタンスの数を 1 つ増やしてスケールアウトするためのものです。 CPU に対する負荷の平均が 5 分間に 30% を下回った場合に、インスタンスが 1 つ減ってスケールインされます。

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

自動スケールの使用に関する詳しい設計情報については、[自動スケールのベスト プラクティス](/azure/architecture/best-practices/auto-scaling)に関する記事をご覧ください。


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、仮想マシン スケール セットを作成しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * カスタム スクリプト拡張機能を使用して、スケールする IIS サイトを定義する
> * スケール セットのロード バランサーを作成する
> * 仮想マシン スケール セットを作成する
> * スケール セット内のインスタンスの数を増減させる
> * 自動スケール ルールを作成する

次のチュートリアルでは、仮想マシンでの負荷分散の概念について詳しく説明します。

> [!div class="nextstepaction"]
> [仮想マシンを負荷分散する](tutorial-load-balancer.md)
