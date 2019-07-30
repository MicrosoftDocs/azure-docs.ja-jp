---
title: 高速ネットワークを使った Azure 仮想マシンの作成 | Microsoft Docs
description: 高速ネットワークを使った Linux 仮想マシンの作成方法について説明します。
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: ef6086afa17f1ab864d70678a6da6df2a78e0c16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190292"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>高速ネットワークを使った Windows 仮想マシンの作成

このチュートリアルでは、高速ネットワークを使った Windows 仮想マシン (VM) の作成方法について説明します。 高速ネットワークを使って Linux VM を作成する場合は、「[高速ネットワークを使った Linux 仮想マシンの作成](create-vm-accelerated-networking-cli.md)」をご覧ください。 高速ネットワークによって、VM との間でシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされる VM タイプの最も要件の厳しいネットワーク ワークロードで使用した場合でも、待ち時間、ジッター、CPU 使用率が軽減されます。 次の図は、2 台の VM 間の通信を、高速ネットワークを使う場合と使わない場合とで比較したものです。

![比較](./media/create-vm-accelerated-networking/accelerated-networking.png)

高速ネットワークを使用しない場合は、VM に出入りするすべてのネットワーク トラフィックがホストと仮想スイッチをスキャンする必要があります。 仮想スイッチでは、ネットワーク セキュリティ グループ、アクセス制御リスト、分離、その他のネットワーク仮想化サービスなど、すべてのポリシーが適用されます。 仮想スイッチの詳細については、[Hyper-V ネットワーク仮想化と仮想スイッチ](https://technet.microsoft.com/library/jj945275.aspx)に関する記事を参照してください。

高速ネットワークを使用した場合、ネットワーク トラフィックは、VM のネットワーク インターフェイス (NIC) に到達した後、VM に転送されます。 仮想スイッチによって適用されるすべてのネットワーク ポリシーはオフロードされ、ハードウェアで適用されるようになりました。 ハードウェアにポリシーを適用することによって、ホストに適用されるポリシーをすべて維持したまま、ホストや仮想スイッチをバイパスして、NIC からネットワーク トラフィックを直接 VM に転送できます。

高速ネットワークのメリットが得られるのは、高速ネットワークが有効になっている VM だけです。 最適な結果を得るには、同じ Azure 仮想ネットワーク (VNet) に接続された 2 台以上の VM でこの機能を有効にしておくことをお勧めします。 VNet の境界を越えて通信するときや、オンプレミスで接続する場合、全体的な待ち時間に対してこの機能がもたらす効果は限定的です。

## <a name="benefits"></a>メリット
* **待機時間の短縮/1 秒あたりのパケット数 (pps) の向上:** データパスから仮想スイッチを削除することで、ホストにおけるパケットのポリシー処理に必要な時間がなくなるため、VM 内で処理できるパケット数が増加します。
* **ジッターの削減:** 仮想スイッチの処理は、適用するポリシーの量と、処理を行う CPU のワークロードによって異なります。 ハードウェアへのポリシーの適用をオフロードすると、パケットが直接 VM に配信され、ホストと VM 間の通信とソフトウェアによる干渉やコンテキスト スイッチがなくなるため、そのばらつきはなくなります。
* **CPU 使用率の削減:** ホストの仮想スイッチをバイパスすることによって、ネットワーク トラフィックを処理するための CPU 使用率を削減できます。

## <a name="limitations-and-constraints"></a>制限と制約

### <a name="supported-operating-systems"></a>サポートされているオペレーティング システム
Azure ギャラリーでは次のディストリビューションが既定でサポートされています。
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>サポートされている VM インスタンス
高速ネットワークは、2 つ以上の vCPU を持つ、コンピューティングに最適化された多くの汎用のインスタンス サイズでサポートされています。  サポートされているシリーズは、D/DSv2 と F/Fs です。

ハイパースレッディングをサポートするインスタンスでは、4 以上の vCPU を持つ VM インスタンスで高速ネットワークがサポートされています。 サポートされている系列は、D/Dsv3、E/Esv3、Fsv2、Lsv2、Ms/Mms、および Ms/Mmsv2。

VM インスタンスの詳細については、[Windows VM のサイズ](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

### <a name="regions"></a>リージョン
すべてのパブリック Azure リージョンと Azure Government クラウドで使用できます。

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>実行中の VM で高速ネットワークを有効にする
サポートされる VM サイズで、高速ネットワークが有効になっていない場合は、VM が停止され割り当てを解除されているときにだけ機能を有効にできます。

### <a name="deployment-through-azure-resource-manager"></a>Azure Resource Manager によるデプロイ
仮想マシン (クラシック) は、高速ネットワークを使用したデプロイはできません。

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Azure 高速ネットワークが有効な Windows VM を作成する
## <a name="portal-creation"></a>ポータルの作成
この記事では、高速ネットワークを使用した仮想マシンを、Azure PowerShell を使って作成する手順について説明しますが、[高速ネットワークを使用した仮想マシンは、Azure portal を使って作成することもできます](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 portal で仮想マシンを作成するときは、 **[仮想マシンの作成]** ブレードで **[ネットワーク]** タブを選択します。このタブには、 **[高速ネットワーク]** のオプションがあります。  [サポートされるオペレーティングシステム](#supported-operating-systems)と [VM サイズ](#supported-vm-instances)を選択している場合、このオプションは自動的に [オン] になります。  そうでない場合は、高速ネットワークに対して [オフ] オプションが選択され、有効にならない理由がユーザーに示されます。   
* *注:* ポータルからは、サポートされているオペレーティング システムのみを有効にできます。  カスタム イメージを使用していて、そのイメージで高速ネットワークがサポートされている場合は、CLI または Powershell を使用して VM を作成してください。 

仮想マシンが作成されたら、「高速ネットワークが有効化されていることを確認する」の手順に従って、高速ネットワークが有効であることを確認できます。

## <a name="powershell-creation"></a>PowerShell の作成
## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-az-ps) バージョン 1.0.0 以降をインストールします。 現在インストールされているバージョンを見つけるには、`Get-Module -ListAvailable Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/Az)から最新バージョンの Az モジュールをインストールします。 PowerShell セッションで、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) を使用して Azure アカウントにログインします。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myNic*、*myVM* といったパラメーター名にします。

[New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) を使用して Azure リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを場所 *centralus* に作成します。

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

まず、[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig) を使用してサブネット構成を作成します。 次の例では、*mySubnet* という名前のサブネットを作成します。

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

[New-AzuVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork) を使用して、*mySubnet* サブネットを持つ仮想ネットワークを作成します。

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

最初に、[New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig) を使用して、ネットワーク セキュリティ グループの規則を作成します。

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

[New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) を使用してネットワーク セキュリティ グループを作成し、*Allow-RDP-All* セキュリティ規則を割り当てます。 *Allow-RDP-All* 規則に加え、ネットワーク セキュリティ グループには複数の既定の規則が含まれています。 ある既定の規則は、インターネットからのすべての着信アクセスを無効にします。そのため、仮想マシンが作成されたら、リモートでその仮想マシンに接続できるように、*Allow-RDP-All* 規則をネットワーク セキュリティ グループに割り当てます。

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig) を使用して、ネットワーク セキュリティ グループを *mySubnet* サブネットに関連付けます。 ネットワーク セキュリティ グループ内の規則は、サブネットにデプロイされているすべてのリソースに対して有効です。

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>高速ネットワークを使ったネットワーク インターフェイスの作成
[New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress) を使用してパブリック IP アドレスを作成します。 インターネットから仮想マシンにアクセスする計画がない場合、パブリック IP アドレスは不要ですが、この記事の手順を完了するには必要です。

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

高速ネットワークを有効にし、[New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) を使用してネットワーク インターフェイスを作成し、パブリック IP アドレスをこのネットワーク インターフェイスに割り当てます。 次の例では、*myVnet* 仮想ネットワークの *mySubnet* サブネット内に *myNic* という名前のネットワーク インターフェイスを作成し、*myPublicIp* パブリック IP アドレスを割り当てます。

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>仮想マシンの作成

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して、VM の資格情報を変数 `$cred` に設定します。

```powershell
$cred = Get-Credential
```

最初に、[New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) を使用して VM を定義します。 次の例では、高速ネットワークをサポートする VM サイズ (*Standard_DS4_v2*) を使用して、*myVM* という VM を定義します。

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

すべての VM のサイズと特性の一覧は、[Windows VM のサイズ](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

[Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) と [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) を使用して、残りの VM 構成を作成します。 次の例では、Windows Server 2016 の VM を作成します。

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

以前に作成したネットワーク インターフェイスを [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) にアタッチします。

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

最後に、[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成します。

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>オペレーティング システムでドライバーがインストールされていることを確認する

Azure で VM を作成したら、VM に接続して、Windows でドライバーがインストールされていることを確認します。

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、自分の Azure アカウントでサインインします。
2. Azure Portal の上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに「*myVm*」と入力します。 検索結果に **myVm** が表示されたら、それをクリックします。 **[接続]** ボタンの下に **[作成中]** と表示されている場合、Azure による VM の作成がまだ完了していません。 概要の左上隅にある **[接続]** は必ず、 **[接続]** ボタンの下の **[作成中]** が見えなくなってからクリックしてください。
3. 「[仮想マシンの作成](#create-the-virtual-machine)」で入力したユーザー名とパスワードを入力します。 Azure で Windows VM に接続したことがない場合は、「[仮想マシンへの接続](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)」を参照してください。
4. Windows の [スタート] ボタンを右クリックし、 **[デバイス マネージャー]** をクリックします。 **[ネットワーク アダプター]** ノードを展開します。 **[Mellanox ConnectX-3 Virtual Function Ethernet Adapter]** が表示されていることを確認します (下図参照)。

    ![[デバイス マネージャー]](./media/create-vm-accelerated-networking/device-manager.png)

VM の高速ネットワークが有効になりました。

## <a name="enable-accelerated-networking-on-existing-vms"></a>既存の VM 上で高速ネットワークを有効にする
高速ネットワークを有効にしないで VM を作成した場合は、既存の VM に対してこの機能を有効にすることができます。  VM は、上記で説明した次の前提条件を満たすことによって、高速ネットワークをサポートしている必要があります。

* VM は高速ネットワークがサポートされるサイズである必要があります
* VM はサポートされる Azure ギャラリー イメージ (および Linux のカーネル バージョン) である必要があります
* 可用性セット内または VMSS 内のすべての VM は、NIC 上で高速ネットワークを有効にする前に、停止/割り当てを解除しておく必要があります

### <a name="individual-vms--vms-in-an-availability-set"></a>個別の VM および可用性セット内の VM
最初に、VM (可用性セットの場合は、セット内のすべての VM) を停止/割り当てを解除します。

```azurepowershell
Stop-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

可用性セットを使用しないで個別に作成した VM の場合、高速ネットワークを有効にするには、個別の VM を停止/割り当てを解除することだけが必要です。  可用性セットを使用して作成された VM の場合は、いずれかの NIC 上で高速ネットワークを有効にする前に、可用性セットに含まれるすべての VM を停止/割り当てを解除する必要があります。 

停止した後、VM の NIC 上で高速ネットワークを有効にします。

```azurepowershell
$nic = Get-AzureRMNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzureRMNetworkInterface
```

VM (可用性セットの場合はセット内のすべての VM) を再起動し、高速ネットワークが有効になっていることを確認します。

```azurepowershell
Start-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS は若干異なりますが、同じワークフローに従います。  最初に、VM を停止します。

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

VM が停止した後、ネットワーク インターフェイスにおいて高速ネットワークのプロパティを更新します。

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

VMSS には、3 つの異なる設定 (自動、ローリング、手動) を使用して更新を適用する VM のアップグレードがあります。  この手順では、再起動後すぐに VMSS がポリシーを選択するように、ポリシーを自動に設定します。  変更がすぐに選択されるように自動に設定するには、次のようにします。

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

最後に、VMSS を再起動します。

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

再起動した後、アップグレードが完了するまで待ちます。完了すると、VM 内に VF が表示されます。  (サポートされる OS と VM サイズを使用していることを確認してください)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>高速ネットワークが有効な既存の VM のサイズを変更する

高速ネットワークが有効になっている VM のサイズは、高速ネットワークをサポートする VM にのみ変更できます。  

高速ネットワークが有効になっている VM のサイズを、高速ネットワークをサポートしていない VM インスタンスに、サイズ変更操作を使って変更することはできません。  このような VM のサイズを変更するには、次のようにします。

* VM を停止/割り当てを解除します。可用性セット/VMSS の場合は、セット/VMSS 内のすべての VM を停止/割り当てを解除します。
* VM の NIC 上で、高速ネットワークを無効にする必要があります。可用性セット/VMSS の場合は、セット/VMSS 内のすべての VM において無効にします。
* 高速ネットワークが無効になったら、高速ネットワークをサポートしていない新しいサイズに VM/可用性セット/VMSS を移動して、再起動します。
