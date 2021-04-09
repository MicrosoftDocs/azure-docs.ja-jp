---
title: 高速ネットワークを有効にして Windows VM を作成する - Azure PowerShell
description: 高速ネットワークを使用する Windows 仮想マシン (VM) を作成して、ネットワークのパフォーマンスを大幅に向上させます。
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: b0ebb75530858a589c3166e21261e2f737fff50d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919971"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Azure PowerShell を使用して高速ネットワークが有効な Windows VM を作成する

このチュートリアルでは、高速ネットワークを有効にして Windows 仮想マシン (VM) を作成する方法について説明します。

> [!NOTE]
> Linux 仮想マシンで高速ネットワークを使用する場合は、[高速ネットワークを利用する Linux 仮想マシンの作成](create-vm-accelerated-networking-cli.md)に関するページを参照してください。

高速ネットワークによって、VM との間でシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータ パスからホストがバイパスされ、サポートされている VM の種類において最も要求の厳しいネットワーク ワークロードで、待ち時間、ジッター、CPU 使用率が低下します。 次の図は、高速ネットワークを使用した場合と使用しない場合の、2 台の VM 間の通信を比較したものです。

![高速ネットワークがある場合とない場合の、Azure 仮想マシン間の通信](./media/create-vm-accelerated-networking/accelerated-networking.png)

高速ネットワークを使用しない場合は、VM に出入りするすべてのネットワーク トラフィックがホストと仮想スイッチをスキャンする必要があります。 仮想スイッチでは、ネットワーク セキュリティ グループ、アクセス制御リスト、分離、その他のネットワーク仮想化サービスなど、すべてのポリシーが適用されます。

> [!NOTE]
> 仮想スイッチの詳細については、「[Hyper-V 仮想スイッチ](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch)」を参照してください。

高速ネットワークを使用した場合、ネットワーク トラフィックは、VM のネットワーク インターフェイス (NIC) に到達した後、VM に転送されます。 仮想スイッチによって適用されるすべてのネットワーク ポリシーはオフロードされ、ハードウェアで適用されるようになりました。 ポリシーはハードウェアにおいて適用されるため、NIC でネットワーク トラフィックを VM に直接転送できます。 NIC ではホストと仮想スイッチはバイパスされますが、ホストにおいて適用されるすべてのポリシーは保持されます。

高速ネットワークのメリットが得られるのは、高速ネットワークが有効になっている VM だけです。 最適な結果を得るには、同じ Azure 仮想ネットワークに接続されている少なくとも 2 台の VM で、この機能を有効にします。 仮想ネットワークの境界を越えて通信するときや、オンプレミスで接続する場合、全体的な待ち時間に対してこの機能がもたらす影響は最小限です。

## <a name="benefits"></a>メリット

- **待ち時間の短縮/1 秒あたりのパケット数 (pps) の向上**:データ パスから仮想スイッチがなくなるので、パケットからホストでポリシー処理に消費される時間がなくなります。 また、VM 内で処理できるパケットの数も増えます。

- **ジッターの削減**:仮想スイッチの処理は、適用する必要があるポリシーの量によって異なります。 また、処理を行っている CPU のワークロードにも依存します。 ポリシーの適用をハードウェアにオフロードすると、パケットが VM に直接配信されるので、その変動が解消されます。 オフロードを行うと、ホストから VM への通信、すべてのソフトウェア割り込み、すべてのコンテキスト切り替えもなくなります。

- **CPU 使用率の削減**:ホストの仮想スイッチをバイパスすることによって、ネットワーク トラフィックを処理するための CPU 使用率を削減できます。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

Azure ギャラリーでは次のディストリビューションが直接サポートされています。

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>制限と制約

### <a name="supported-vm-instances"></a>サポートされている VM インスタンス

高速ネットワークは、2 つ以上の vCPU を持つ、コンピューティングに最適化された多くの汎用のインスタンス サイズでサポートされています。 ハイパースレッディングをサポートするインスタンスでは、4 以上の vCPU を持つ VM インスタンスで高速ネットワークがサポートされています。 

高速ネットワークのサポートは、個々の[仮想マシンのサイズ](../virtual-machines/sizes.md)に関するドキュメントで確認できます。 

### <a name="custom-images"></a>カスタム イメージ

カスタム イメージを使用する予定で、そのイメージで高速ネットワークをサポートする場合は、Azure 上の Mellanox ConnectX-3 および ConnectX-4 Lx NIC で動作する必須のドライバーを必ず含めるようにします。

### <a name="regions"></a>リージョン

高速ネットワークは、すべてのグローバル Azure リージョンと Azure Government クラウドで利用できます。

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>実行中の VM で高速ネットワークを有効にする

サポートされる VM サイズで、高速ネットワークが有効になっていない場合は、VM が停止されて割り当てを解除されるときにだけ、機能を有効にできます。

### <a name="deployment-through-azure-resource-manager"></a>Azure Resource Manager によるデプロイ

仮想マシン (クラシック) は、高速ネットワークを有効にしてデプロイすることはできません。

## <a name="vm-creation-using-the-portal"></a>ポータルを使用した VM の作成

この記事では、Azure PowerShell を使用して高速ネットワークが有効な VM を作成する手順について説明しますが、高速ネットワークが有効な[仮想マシンを Azure portal で作成する](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)こともできます。 ポータルで VM を作成するときに、 **[仮想マシンの作成]** ページで **[ネットワーク]** タブを選択します。このタブには、 **[高速ネットワーク]** のオプションがあります。 [サポートされているオペレーティング システム](#supported-operating-systems)と [VM サイズ](#supported-vm-instances)を選択した場合、このオプションは自動的に **[オン]** に設定されます。 それ以外の場合、オプションは **[オフ]** に設定され、有効にできない理由が表示されます。

> [!NOTE]
> ポータルからは、サポートされているオペレーティング システムのみを有効にできます。 カスタム イメージを使用していて、そのイメージで高速ネットワークがサポートされている場合は、CLI または PowerShell を使用して VM を作成してください。 

VM を作成した後で、高速ネットワークが有効になっているかどうかを確認できます。 以下の手順に従います。

1. [Azure portal](https://portal.azure.com) に移動して、VM を管理します。 **[仮想マシン]** を検索して選択します。

2. 仮想マシンの一覧で、新しい VM を選択します。

3. VM のメニュー バーで、 **[ネットワーク]** を選択します。

ネットワーク インターフェイスの情報で、 **[高速ネットワーク]** というラベルの横に、高速ネットワークの状態が **[無効]** か **[有効]** かが表示されます。

## <a name="vm-creation-using-powershell"></a>PowerShell を使用した VM の作成

先に進む前に、[Azure PowerShell](/powershell/azure/install-az-ps) バージョン 1.0.0 以降をインストールします。 現在インストールされているバージョンを見つけるには、`Get-Module -ListAvailable Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/Az)から最新バージョンの Az モジュールをインストールします。 PowerShell セッションで、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) を使用して Azure アカウントにサインインします。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myNic*、*myVM* といったパラメーター名にします。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) を使用して Azure リソース グループを作成します。 次のコマンドでは、*myResourceGroup* という名前のリソース グループが場所 *centralus* に作成されます。

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig) を使用してサブネット構成を作成します。 次のコマンドでは、*mySubnet* という名前のサブネットが作成されます。

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. [New-AzuVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork) を使用して、*mySubnet* サブネットを持つ仮想ネットワークを作成します。

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

1. [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig) を使用して、ネットワーク セキュリティ グループの規則を作成します。

    ```azurepowershell
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

2. [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) を使用してネットワーク セキュリティ グループを作成し、*Allow-RDP-All* セキュリティ規則を割り当てます。 *Allow-RDP-All* 規則だけでなく、ネットワーク セキュリティ グループには複数の既定の規則が含まれています。 既定の規則の 1 つでは、インターネットからのすべての受信アクセスが無効にされます。 作成された *Allow-RDP-All* 規則はネットワーク セキュリティ グループに割り当てられて、VM にリモート接続できるようになります。

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig) を使用して、ネットワーク セキュリティ グループを *mySubnet* サブネットに関連付けます。 ネットワーク セキュリティ グループ内の規則は、サブネットにデプロイされているすべてのリソースに対して有効です。

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>高速ネットワークを使ったネットワーク インターフェイスの作成

1. [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress) を使用してパブリック IP アドレスを作成します。 インターネットから VM にアクセスする予定がない場合、パブリック IP アドレスは不要です。 ただし、この記事の手順を完了するにはそれが必要です。

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. 高速ネットワークを有効にし、[New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) を使用してネットワーク インターフェイスを作成し、パブリック IP アドレスをそのネットワーク インターフェイスに割り当てます。 次の例では、*myVnet* 仮想ネットワークの *mySubnet* サブネット内に *myNic* という名前のネットワーク インターフェイスを作成し、パブリック IP アドレス *myPublicIp* をそれに割り当てます。

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>VM を作成してネットワーク インターフェイスをアタッチする

1. [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して、VM の資格情報を変数 `$cred` に設定します。このとき、サインインを求めるメッセージが表示されます。

    ```azurepowershell
    $cred = Get-Credential
    ```

2. [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) を使用して VM を定義します。 次のコマンドでは、高速ネットワークをサポートする VM サイズ (*Standard_DS4_v2*) を使用して、*myVM* という VM を定義します。

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    すべての VM のサイズと特性の一覧は、[Windows VM のサイズ](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

3. [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) と [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) を使用して、残りの VM 構成を作成します。 次のコマンドでは、Windows Server 2016 の VM を作成します。

    ```azurepowershell
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

4. 以前に作成したネットワーク インターフェイスを [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) にアタッチします。

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. [New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成します。

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Windows VM にイーサネット コントローラーがインストールされていることを確認する

Azure で VM を作成したら、VM に接続し、Windows にイーサネット コントローラーがインストールされていることを確認します。

1. [Azure portal](https://portal.azure.com) に移動して、VM を管理します。 **[仮想マシン]** を検索して選択します。

2. 仮想マシンの一覧で、新しい VM を選択します。

3. VM の概要ページで、VM の一覧に **[状態]** が **[作成中]** と表示されている場合は、Azure で VM の作成が完了するまで待ちます。 VM の作成が完了すると、 **[状態]** が **[実行中]** に変わります。

4. VM の概要のツール バーで、 **[接続]**  >  **[RDP]**  >  **[RDP ファイルのダウンロード]** を選択します。

5. .rdp ファイルを開き、「[VM を作成してネットワーク インターフェイスをアタッチする](#create-a-vm-and-attach-the-network-interface)」セクションで入力した資格情報を使用して VM にサインインします。 Azure で Windows VM に接続したことがない場合は、「[仮想マシンへの接続](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)」を参照してください。

6. VM のリモート デスクトップ セッションが表示されたら、Windows の [スタート] ボタンを右クリックし、 **[デバイス マネージャー]** を選択します。

7. **[デバイス マネージャー]** ウィンドウで、 **[ネットワーク アダプター]** ノードを展開します。

8. **Mellanox ConnectX-3 Virtual Function Ethernet Adapter** が表示されていることを確認します (下図参照)。

    ![Mellanox ConnectX-3 Virtual Function Ethernet Adapter、高速ネットワーク用の新しいネットワーク アダプター、デバイス マネージャー](./media/create-vm-accelerated-networking/device-manager.png)

これで、高速ネットワークが VM で有効になります。

> [!NOTE]
> Mellanox アダプターの起動が失敗する場合は、リモート デスクトップ セッションで管理者プロンプトを開き、次のコマンドを入力します。
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>既存の VM で高速ネットワークを有効にする

高速ネットワークを有効にしないで VM を作成した場合は、既存の VM に対してこの機能を有効にすることができます。 VM では、上で説明した次の前提条件が満たされ、高速ネットワークがサポートされている必要があります。

* VM は高速ネットワークに対してサポートされるサイズである必要があります。
* VM はサポートされる Azure ギャラリー イメージ (および Linux のカーネル バージョン) である必要があります。
* NIC 上で高速ネットワークを有効にする前に、可用性セットまたは仮想マシン スケール セットのすべての VM を停止するか、割り当てを解除する必要があります。

### <a name="individual-vms-and-vms-in-an-availability-set"></a>個別の VM および可用性セット内の VM

1. VM (可用性セットの場合は、セット内のすべての VM) を停止するか、割り当てを解除します。

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > 可用性セットを使用せず、VM を個別に作成する場合、高速ネットワークを有効にするために必要なのは、個別の VM を停止または割り当てを解除することだけです。 可用性セットを使用して VM を作成した場合は、高速ネットワークをサポートするクラスターで VM が終了するように、いずれかの NIC で高速ネットワークを有効にする前に、可用性セットに含まれているすべての VM を停止または割り当てを解除する必要があります。 高速ネットワークを無効にする場合は、高速ネットワークをサポートするクラスターは高速ネットワークを使用しない NIC でも問題なく動作するため、停止または割り当てを解除する必要はありません。

2. VM の NIC 上で高速ネットワークを有効にします。

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. VM (可用性セットの場合はセット内のすべての VM) を再起動し、高速ネットワークが有効になっていることを確認します。

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>仮想マシン スケール セット

仮想マシン スケール セットの場合は少し異なりますが、同じワークフローに従います。

1. VM を停止します。

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. ネットワークのインターフェイスで、高速ネットワークのプロパティを更新します。

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. 変更がすぐに選択されるように、適用された更新を自動に設定します。

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > スケール セットには、3 つの異なる設定 (自動、ローリング、手動) を使用して更新を適用する VM のアップグレードがあります。 これらの手順では、再起動後すぐにスケール セットによってポリシーが選択されるように、ポリシーは自動に設定されます。

4. スケール セットを再起動します。

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

再起動したら、アップグレードが完了するまで待ちます。 アップグレードが完了すると、仮想機能 (VF) が VM 内に表示されます。 サポートされている OS と VM サイズを使用していることを確認します。

### <a name="resizing-existing-vms-with-accelerated-networking"></a>高速ネットワークが有効な既存の VM のサイズを変更する

VM で高速ネットワークが有効になっている場合は、高速ネットワークをサポートする VM のみにサイズを変更できます。  

高速ネットワークが有効になっている VM のサイズを、高速ネットワークをサポートしていない VM インスタンスに、サイズ変更操作を使用して変更することはできません。 このような VM のサイズを変更するには、次のようにします。

1. VM を停止するか、または割り当てを解除します。 可用性セットまたはスケール セットの場合は、可用性セットまたはスケール セット内のすべての VM を停止または割り当てを解除します。

2. VM の NIC で高速ネットワークを無効にします。 可用性セットまたはスケール セットの場合は、可用性セットまたはスケール セット内のすべての VM の NIC で高速ネットワークを無効にします。

3. 高速ネットワークを無効にした後、高速ネットワークをサポートしていない新しいサイズに VM、可用性セット、スケール セットを移動してから、それらを再起動します。