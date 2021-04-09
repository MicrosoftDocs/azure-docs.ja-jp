---
title: チュートリアル - Azure 内で Windows 仮想マシンの負荷分散を行う
description: このチュートリアルでは、Azure PowerShell を使用して、セキュリティで保護された高可用性アプリケーションのために 3 台の Windows 仮想マシン間で負荷分散を行うロード バランサーを作成する方法について説明します
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/03/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d6c872ecfbad6cb8bb01ad5a7c3df8c47aadaebe
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549109"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して Azure 内で Windows 仮想マシンの負荷分散を行って高可用性アプリケーションを作成する
負荷分散では、着信要求を複数の仮想マシンに分散させることで高可用性を提供します。 このチュートリアルでは、トラフィックを分散し高可用性を提供する、Azure Load Balancer のさまざまなコンポーネントについて説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure Load Balancer を作成する
> * ロード バランサーの正常性プローブの作成
> * ロード バランサーのトラフィック ルールを作成する
> * カスタム スクリプト拡張機能を使用して基本的な IIS サイトを作成する
> * 仮想マシンを作成してロード バランサーにアタッチする
> * 動作中のロード バランサーを表示する
> * ロード バランサーに VM を追加または削除する

## <a name="azure-load-balancer-overview"></a>Azure Load Balancer の概要
Azure Load Balancer は、着信トラフィックを正常な VM に分散することで高可用性を実現する第 4 層 (TCP、UDP) のロード バランサーです。 ロード バランサーの正常性プローブは、各 VM の特定のポートを監視し、稼働している VM にのみトラフィックを分散します。

1 つまたは複数のパブリック IP アドレスが含まれるフロントエンド IP 構成を定義します。 このフロントエンド IP 構成によって、ロード バランサーとアプリケーションにインターネット経由でアクセスできるようになります。 

仮想マシンは、仮想ネットワーク インターフェイス カード (NIC) を使用してロード バランサーに接続されます。 トラフィックを VM に分散するには、バックエンド アドレス プールに、ロード バランサーに接続される仮想 NIC の IP アドレスを含めます。

トラフィックのフローを制御するには、VM にマップする特定のポートとプロトコルについて、ロード バランサー規則を定義します。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="create-azure-load-balancer"></a>Azure Load Balancer を作成する
このセクションでは、ロード バランサーの各コンポーネントを作成および設定する方法について説明します。 ロード バランサーを作成する前に、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroupLoadBalancer* という名前のリソース グループを場所 *EastUS* に作成します。

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成
インターネット上のアプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用してパブリック IP アドレスを作成します。 次の例では、*myPublicIP* という名前のパブリック IP アドレスを *myResourceGroupLoadBalancer* リソース グループに作成します。

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>ロード バランサーの作成
[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、フロントエンド IP プールを作成します。 次の例では、*myFrontEndPool* という名前のフロントエンド IP プールを作成し、*myPublicIP* アドレスをアタッチします。 

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用して、バックエンド アドレス プールを作成します。 残りの手順では、VM をこのバックエンド プールにアタッチします。 次の例では、*myBackEndPool* という名前のバックエンド アドレス プールを作成します。

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "myBackEndPool"
```

次に、[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用して、ロード バランサーを作成します。 次の例では、前の手順で作成したフロントエンドとバックエンドの IP プールを使用して *myLoadBalancer* という名前のロード バランサーを作成します。

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>正常性プローブの作成
ロード バランサーでアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 既定では、15 秒間隔のチェックが 2 回連続でエラーになった VM はロード バランサーのローテーションから外されます。 正常性プローブは、プロトコルに基づいて作成するか、またはアプリの特定の正常性チェック ページに基づいて作成します。 

次の例では、TCP プローブを作成します。 よりきめ細やかな正常性チェックを行う場合は、カスタムの HTTP プローブを作成することもできます。 カスタム HTTP プローブを使用する場合は、*healthcheck.aspx* などの正常性チェック ページを作成する必要があります。 対象となるホストをロード バランサーのローテーションに維持するには、プローブが **HTTP 200 OK** 応答を返す必要があります。

TCP 正常性プローブを作成するには、[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) を使用します。 次の例では、*TCP* ポート *80* で各 VM を監視する *myHealthProbe* という名前の正常性プローブを作成します。

```azurepowershell-interactive
Add-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

正常性プローブを適用するには、[Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) を使用して、ロード バランサーを更新します。

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成
ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 正常な VM のみでトラフィックを受信するには、使用する正常性プローブの定義も行います。

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) を使用して、ロード バランサー規則を作成します。 次の例では、*myLoadBalancerRule* という名前のロード バランサー規則を作成し、*TCP* ポート *80* でトラフィックを負荷分散します。

```azurepowershell-interactive
$probe = Get-AzLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

[Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) を使用して、ロード バランサーを更新します。

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する
一部の VM をデプロイしてバランサーをテストする前に、関連する仮想ネットワーク リソースを作成します。 仮想ネットワークの詳細については、[Azure 仮想ネットワークの管理](tutorial-virtual-network.md)に関するチュートリアルを参照してください。

### <a name="create-network-resources"></a>ネットワーク リソースを作成する
[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、*mySubnet* と共に *myVnet* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

仮想 NIC は、[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して作成します。 以下の例では、3 つの仮想 NIC を作成します (以降の手順では、アプリ用に作成する VM ごとに仮想 NIC を 1 つ)。 いつでも追加の仮想 NIC と VM を作成してロード バランサーに追加することができます。

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>仮想マシンを作成する
アプリの高可用性を高めるには、可用性セットに VM を配置します。

可用性セットを作成するには、[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) を使用します。 次の例では、*myAvailabilitySet* という名前の可用性セットを作成します。

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

次のように、[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成できるようになりました。 次の例では、3 つの VM と必要な仮想ネットワーク コンポーネントがまだ存在しない場合にそれらを作成します。

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

`-AsJob` パラメーターにより、バックグラウンド タスクとして VM が作成されるため、PowerShell プロンプトが表示されます。 `Job` コマンドレットを使用して、バックグラウンド ジョブの詳細を表示できます。 3 台の VM をすべて作成して構成するには、数分かかります。


### <a name="install-iis-with-custom-script-extension"></a>カスタム スクリプト拡張機能を使用して IIS をインストールする
[Windows 仮想マシンをカスタマイズする方法](tutorial-automate-vm-deployment.md)に関する先行のチュートリアルで、Windows のカスタム スクリプト拡張機能を使用して VM のカスタマイズを自動化する方法が説明されています。 同じアプローチを使用して、VM に IIS をインストールして構成することができます。

[Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) を使用して、カスタム スクリプト拡張機能をインストールします。 この拡張機能によって `powershell Add-WindowsFeature Web-Server` が実行され、IIS Web サーバーがインストールされます。次に、VM のホスト名を表示するように *Default.htm* ページが更新されます。

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>ロード バランサーをテストする
ロード バランサーのパブリック IP アドレスを取得するには、[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) を使用します。 次の例では、先ほど作成した *myPublicIP* の IP アドレスを取得しています。

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力できます。 次の例のように、Web サイトが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の IIS Web サイト](./media/tutorial-load-balancer/running-iis-website.png)

アプリを実行している 3 つの VM すべての間で、ロード バランサーがトラフィックを負荷分散していることを確認するには、Web ブラウザーを強制的に最新の情報に更新します。


## <a name="add-and-remove-vms"></a>仮想マシンの追加と削除
アプリを実行している VM には、OS の更新プログラムをインストールするなどメンテナンスが必要になることもあります。 また、アプリのトラフィックが増大すれば、それに対処するために、新たに VM を追加しなければなりません。 このセクションでは、ロード バランサーから VM を除外したりロード バランサーに対して VM を追加したりする方法について説明します。

### <a name="remove-a-vm-from-the-load-balancer"></a>ロード バランサーから VM を除外する
[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) を使用してネットワーク インターフェイス カードを取得した後、仮想 NIC の *LoadBalancerBackendAddressPools* プロパティを *$null* に設定します。 最後に、仮想 NIC を更新します。

```azurepowershell-interactive
$nic = Get-AzNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzNetworkInterface -NetworkInterface $nic
```

アプリを実行している残りの 2 つの VM の間で、ロード バランサーがトラフィックを負荷分散していることを確認するには、Web ブラウザーを強制的に最新の情報に更新します。 これで VM に対して、OS 更新プログラムのインストールや VM の再起動などのメンテナンスを行うことができます。

### <a name="add-a-vm-to-the-load-balancer"></a>ロード バランサーに VM を追加する
VM のメンテナンスを実施した後、またはキャパシティの拡張が必要な場合は、[Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) を使用して、仮想 NIC の *LoadBalancerBackendAddressPools* プロパティを *BackendAddressPool* に設定します。

ロード バランサーを取得します。

```azurepowershell-interactive
$lb = Get-AzLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ロード バランサーを作成し、それに VM をアタッチしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * Azure Load Balancer を作成する
> * ロード バランサーの正常性プローブの作成
> * ロード バランサーのトラフィック ルールを作成する
> * カスタム スクリプト拡張機能を使用して基本的な IIS サイトを作成する
> * 仮想マシンを作成してロード バランサーにアタッチする
> * 動作中のロード バランサーを表示する
> * ロード バランサーに VM を追加または削除する

次のチュートリアルに進み、VM ネットワークの管理方法を学習してください。

> [!div class="nextstepaction"]
> [VM と仮想ネットワークの管理](./tutorial-virtual-network.md)
