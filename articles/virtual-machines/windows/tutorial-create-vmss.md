---
title: "Azure で Windows 用の仮想マシン スケール セットを作成する | Microsoft Docs"
description: "仮想マシン スケール セットを使って、Windows VM に高可用性アプリケーションを作成およびデプロイします"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7fc2e841a193c219822e232fbc994df5e934ddc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows"></a>仮想マシン スケール セットを作成して Windows に高可用性アプリをデプロイする
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケーリングするルールを定義したりできます。 このチュートリアルでは、仮想マシン スケール セットを Azure にデプロイします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * カスタム スクリプト拡張機能を使用して、スケールする IIS サイトを定義する
> * スケール セットのロード バランサーを作成する
> * 仮想マシン スケール セットを作成する
> * スケール セット内のインスタンスの数を増減させる
> * 自動スケール ルールを作成する

このチュートリアルには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。


## <a name="scale-set-overview"></a>スケール セットの概要
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM は、1 つ以上の*配置グループ*内の論理障害ドメインおよび更新ドメインに配布されます。 これらは同じように構成された VM のグループであり、[可用性セット](tutorial-availability-sets.md)に似ています。

スケール セットには必要に応じて VM が作成されます。 スケール セットの VM を追加または削除する方法とタイミングを制御するには、自動スケール ルールを定義します。 これらのルールは、CPU の負荷、メモリの使用量、ネットワーク トラフィックなどのメトリックに基づいて発動できます。

Azure プラットフォーム イメージを使う場合、スケール セットは最大 1,000 個の VM をサポートします。 大量のインストールが必要なワークロードや、VM に大幅なカスタマイズが必要なワークロードについては、[カスタム VM イメージを作成する](tutorial-custom-images.md)という方法もあります。 カスタム イメージを使うと、最大 300 個の VM をスケール セットに作成できます。


## <a name="create-an-app-to-scale"></a>スケーリングするアプリを作成する
スケール セットを作成する前に、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroupAutomate* という名前のリソース グループを場所 *EastUS* に作成します。

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupScaleSet -Location EastUS
```

少し前のチュートリアルでは、カスタム スクリプト拡張機能を使用して [VM の構成を自動化](tutorial-automate-vm-deployment.md)する方法を説明しました。 IIS をインストールして構成するには、スケール セットの構成を作成したのち、カスタム スクリプト拡張機能を適用します。

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig `
    -Location EastUS `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings
```

## <a name="create-scale-load-balancer"></a>スケール ロード バランサーを作成する
Azure Load Balancer は、着信トラフィックを正常な VM に分散することで高可用性を実現する第 4 層 (TCP、UDP) のロード バランサーです。 ロード バランサーの正常性プローブは、各 VM の特定のポートを監視し、稼働している VM にのみトラフィックを分散します。 詳細については、次の [Windows 仮想マシンに負荷分散を実現する方法](tutorial-load-balancer.md)に関するチュートリアルを参照してください。

パブリック IP アドレスを備え、ポート 80 に対する Web トラフィックを分散させるロード バランサーを作成します。

```powershell
# Create a public IP address
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupScaleSet `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP

# Create a frontend and backend IP pool
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool

# Create the load balancer
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool

# Create a load balancer health probe on port 80
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2

# Create a load balancer rule to distribute traffic on port 80
Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

# Update the load balancer configuration
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="create-a-scale-set"></a>スケール セットを作成する
それでは、仮想マシン スケール セットの作成に移りましょう。これには、[New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm) を使います。 次の例では *myScaleSet* という名前のスケール セットを作成します。

```powershell
# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword P@ssword! `
  -ComputerNamePrefix myVM

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myVnet" `
  -Location "EastUS" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myScaleSet `
  -VirtualMachineScaleSet $vmssConfig
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="test-your-app"></a>アプリケーションをテストする
実際に動作している IIS Web サイトを確認するには、[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使ってロード バランサーのパブリック IP アドレスを取得します。 以下の例では、スケール セットの一部として作成された *myPublicIP* の IP アドレスを取得します。

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupScaleSet -Name myPublicIP | select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力します。 アプリが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の IIS サイト](./media/tutorial-create-vmss/running-iis-site.png)

動作しているスケール セットを確認するには、Web ブラウザーを強制的に最新の情報に更新して、アプリが動作しているすべての VM に対してロード バランサーからトラフィックが負荷分散されているのを見ることができます。


## <a name="management-tasks"></a>管理タスク
スケール セットのライフサイクルを通じて、1 つ以上の管理タスクを実行する必要がある場合があります。 さらに、各種ライフサイクルのタスクを自動化するスクリプトを作成するほうが便利な場合もあります。 Azure PowerShell には、これらのタスクを簡単に実行するための方法が用意されています。 一般的なタスクには、次のようなものがあります。

### <a name="view-vms-in-a-scale-set"></a>スケール セットの VM を表示する
スケール セットで実行されている VM の一覧を表示するには、[Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) を次のように使います。

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Loop through the instanaces in your scale set
for ($i=1; $i -le ($scaleset.Sku.Capacity - 1); $i++) {
    Get-AzureRmVmssVM -ResourceGroupName myResourceGroupScaleSet `
      -VMScaleSetName myScaleSet `
      -InstanceId $i
}
```


### <a name="increase-or-decrease-vm-instances"></a>VM インスタンスを増減させる
現時点でスケール セットに存在するインスタンスの数を確認するには、[Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) と *sku.capacity* に対するクエリを使います。

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -VMScaleSetName myScaleSet | `
    Select -ExpandProperty Sku
```

インスタンスの数が表示されたら、[Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) を使って、スケール セット内の仮想マシンの数を手動で増減させることができます。 次の例では、スケール セット内の VM の数を *5* に設定します。

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -Name myScaleSet `
    -VirtualMachineScaleSet $scaleset
```

スケール セット内のインスタンスの数を指定どおりに更新する処理が終わるまでには、数分かかります。


### <a name="configure-autoscale-rules"></a>自動スケール ルールを構成する
スケール セット内のインスタンスの数を手動でスケールする代わりに、自動スケール ルールを定義しておくこともできます。 自動スケール ルールを使うと、スケール セット内のインスタンスが監視され、定義しておいたメトリックとしきい値に基づいて適切な対応が自動で実行されます。 以下の例は、CPU に対する負荷の平均が 5 分間に 60% を上回った場合にインスタンスの数を 1 つ増やしてスケールアウトするためのものです。 このコードではほかにも、CPU に対する負荷の平均が 5 分間に 30% を下回った場合に、インスタンスを 1 つ減らしてスケールインすることが指定されています。

```powershell
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5 minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5 minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```

自動スケールの使用に関する詳しい設計情報については、[自動スケールのベスト プラクティス](/azure/architecture/best-practices/auto-scaling)に関する記事をご覧ください。


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、仮想マシン スケール セットを作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * カスタム スクリプト拡張機能を使用して、スケールする IIS サイトを定義する
> * スケール セットのロード バランサーを作成する
> * 仮想マシン スケール セットを作成する
> * スケール セット内のインスタンスの数を増減させる
> * 自動スケール ルールを作成する

次のチュートリアルでは、仮想マシンでの負荷分散の概念について詳しく説明します。

> [!div class="nextstepaction"]
> [仮想マシンを負荷分散する](tutorial-load-balancer.md)
