---
title: "PowerShell コマンドレットを使用した仮想マシン スケール セットの作成 | Microsoft Docs"
description: "Azure PowerShell コマンドレットを使用した初めての Azure 仮想マシン スケール セットの作成と管理の概要です。"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 430d9d64-1f35-48f0-a4fd-9b69910ffa59
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: danielsollondon
translationtype: Human Translation
ms.sourcegitcommit: b2e89f0d5e6b14ce8d5847f8adc3d57a6122172e
ms.openlocfilehash: a3a36028a75d6cb7eb36277f3e2b5ab833c96a96
ms.lasthandoff: 02/21/2017


---
# <a name="creating-virtual-machine-scale-sets-using-powershell-cmdlets"></a>PowerShell コマンドレットを使用した仮想マシン スケール セットの作成
この記事では、仮想マシン スケール セット (VMSS) を作成する方法の例を示します。 関連するネットワークとストレージと共に、3 つのノードのスケール セットを作成します。

## <a name="first-steps"></a>最初の手順
最新の Azure PowerShell モジュールがインストールされていることを確認してください。これには、スケール セットの管理と作成に必要な PowerShell コマンドレットが含まれます。
利用可能な最新の Azure モジュールのコマンドライン ツールは、[こちら](http://aka.ms/webpi-azps)で入手できます。

VMSS 関連のコマンドレットを検索するには、検索文字列 \*VMSS\* を使います。 例: _gcm *vmss*_

## <a name="creating-a-vmss"></a>VMSS の作成
#### <a name="create-resource-group"></a>リソース グループの作成
```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

### <a name="create-networking-vnet--subnet"></a>ネットワークの作成 (VNET とサブネット)
#### <a name="subnet-specification"></a>サブネットの指定
```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

#### <a name="vnet-specification"></a>VNET の指定
```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

# In this case assume the new subnet is the only one
$subnetId = $vnet.Subnets[0].Id;
```

#### <a name="create-public-ip-resource-to-allow-external-access"></a>外部アクセスを許可するパブリック IP リソースの作成
これは Load Balancer にバインドされます。

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

#### <a name="create-load-balancer"></a>Load Balancer の作成
```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

# Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

#### <a name="configure-load-balancer"></a>Load Balancer の構成
バックエンド アドレス プール構成を作成します。これは、スケール セット内の VM の NIC によって共有されます。

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

負荷分散プローブ ポートを設定します。対象のアプリケーションに合わせて設定を変更してください。

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Load Balancer を経由してスケール セット内の VM に (負荷分散なしで) 直接ルーティングするための受信 NAT プールを作成します。 ここでは、デモンストレーションを目的として RDP を使用しますが、アプリケーションで必ずしも RDP が必要になるとは限りません。

```
$frontendpoolrangestart = 3360
$frontendpoolrangeend = 3370
$backendvmport = 3389
$inboundNatPool = New-AzureRmLoadBalancerInboundNatPoolConfig -Name $inboundNatPoolName -FrontendIPConfigurationId `
$frontend.Id -Protocol Tcp -FrontendPortRangeStart $frontendpoolrangestart -FrontendPortRangeEnd $frontendpoolrangeend -BackendPort $backendvmport;
```

負荷分散規則を作成します。次の例では、前の手順の設定を使用して負荷分散ポート 80 の要求を示しています。

```
$protocol = 'Tcp'
$feLBPort = 80
$beLBPort = 80

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name $lbruleName `
-FrontendIPConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -Protocol $protocol -FrontendPort $feLBPort -BackendPort $beLBPort `
-IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -Verbose;
```

上記の構成で Load Balancer を作成します。

```
$actualLb = New-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname -Location $loc `
-FrontendIpConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -LoadBalancingRule $lbrule -InboundNatPool $inboundNatPool -Verbose;
```

Load Balancer の設定を確認します。負荷分散ポートの構成を確認してください。スケール セット内の VM が作成されるまで受信 NAT 規則は表示されないことに注意してください。

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### <a name="configure-and-create-the-scale-set"></a>スケール セットの構成と作成
このインフラストラクチャの例では、スケール セット全体で Web トラフィックを分散、スケールするための設定方法を示していますが、ここで指定された VM イメージには Web サービスがインストールされていないことに注意してください。

```
# specify scale set Name
$vmssName = 'vmss' + $rgname;

## specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

###add an extension
$extname = 'BGInfo';
$publisher = 'Microsoft.Compute';
$exttype = 'BGInfo';
$extver = '2.1';
```

Load Balancer とサブネットへの NIC のバインド

```
$ipCfg = New-AzureRmVmssIPConfig -Name 'nic' `
-LoadBalancerInboundNatPoolsId $actualLb.InboundNatPools[0].Id `
-LoadBalancerBackendAddressPoolsId $actualLb.BackendAddressPools[0].Id `
-SubnetId $subnetId;
```

スケール セットの構成の作成

```
# Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
    | Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
    | Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
    | Set-AzureRmVmssStorageProfile -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName `
    | Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

スケール セットの構成のビルド

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

これで、スケール セットが作成されました。 次の例では、RDP を使用して個々の VM への接続をテストできます。

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```

