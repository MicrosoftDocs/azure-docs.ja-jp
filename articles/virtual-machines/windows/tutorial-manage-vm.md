---
title: "Azure PowerShell モジュールを使用して Windows VM を作成および管理する | Microsoft Docs"
description: "チュートリアル - Azure PowerShell モジュールを使用して Windows VM を作成および管理する"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: a26f33247710431d433f3309ecdaca20e605c75a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017

---

# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Azure PowerShell モジュールを使用して Windows VM を作成および管理する

Azure 仮想マシンは、完全に構成可能で柔軟なコンピューティング環境を提供します。 このチュートリアルでは、VM サイズや VM イメージの選択、VM のデプロイなどの Azure 仮想マシンのデプロイに関する基本事項について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * VM を作成して VM に接続する
> * VM イメージを選択して使用する
> * 特定の VM サイズを確認して使用する
> * VM のサイズを変更する
> * VM の状態を表示して理解する

このチュートリアルには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="create-resource-group"></a>Create resource group

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドでリソース グループを作成します。 

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンの前にリソース グループを作成する必要があります。 この例では、*myResourceGroupVM* という名前のリソース グループが *EastUS* リージョンに作成されます。 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location EastUS
```

このチュートリアル全体で示しているように、VM の作成時または変更時にリソース グループを指定します。

## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシンを仮想ネットワークに接続する必要があります。 パブリック IP アドレスを使用し、ネットワーク インターフェイス カードを介して、仮想マシンと通信します。

### <a name="create-virtual-network"></a>Create virtual network

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用してサブネットを作成します。

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
```

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークを作成します。

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>パブリック IP アドレスの作成

[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) を使用してパブリック IP アドレスを作成します。

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>ネットワーク インターフェイス カードの作成

[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) を使用してネットワーク インターフェイス カードを作成します。

```powershell
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location EastUS `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成

Azure [ネットワーク セキュリティ グループ](../../virtual-network/virtual-networks-nsg.md) (NSG) は、仮想マシンの受信トラフィックと送信トラフィックを制御します。 特定のポートまたは特定のポート範囲に対するネットワーク トラフィックが、ネットワーク セキュリティ グループの規則によって許可または拒否されます。 また、これらの規則に送信元アドレス プレフィックスを含めれば、あらかじめ決められた送信元からのトラフィックにのみ仮想マシンとの通信を許可することができます。 インストールした IIS Web サーバーにアクセスするには、NSG の受信規則を追加する必要があります。

NSG の受信規則を作成するには、[Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig) を使用します。 次の例では、仮想マシン用にポート *3389* を開く、*myNSGRule* という名前の NSG 規則を作成します。

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) で、*myNSGRule* を使用する NSG を作成します。

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupVM `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRule
```

[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) を使用して、仮想ネットワークのサブネットに NSG を追加します。

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -VirtualNetwork $vnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24
```

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) を使用して、仮想ネットワークを更新します。

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシンを作成するときに、オペレーティング システム イメージ、ディスクのサイズ、管理者資格情報など、いくつかの選択肢があります。 この例では、最新バージョンの Windows Server 2016 Datacenter を実行する、*myVM* という名前の仮想マシンを作成します。

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、仮想マシンの管理者アカウントに必要なユーザー名とパスワードを設定します。

```powershell
$cred = Get-Credential
```

[New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) を使用して、仮想マシンの初期構成を作成します。

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

[Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) を使用して、仮想マシンの構成にオペレーティング システムの情報を追加します。

```powershell
$vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM `
    -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

[Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) を使用して、仮想マシンの構成にイメージ情報を追加します。

```powershell
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
```

[Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) を使用して、仮想マシンの構成にオペレーティング システム ディスクの設定を追加します。

```powershell
$vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
```

[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) を使用して、先ほど作成したネットワーク インターフェイス カードを仮想マシンの構成に追加します。

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、仮想マシンを作成します。

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

## <a name="connect-to-vm"></a>VM への接続

デプロイが完了したら、仮想マシンとのリモート デスクトップ接続を作成します。

次のコマンドを実行して、仮想マシンのパブリック IP アドレスを取得します。 この IP アドレスを書き留めておきます。後の手順で Web 接続性をテストするときにブラウザーでこの IP アドレスに接続します。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

次のコマンドを使用して、仮想マシンとのリモート デスクトップ セッションを作成します。 IP アドレスを仮想マシンの *publicIPAddress* に置き換えます。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>VM イメージについて

Azure Marketplace には、新しい仮想マシンの作成に使用できる多数の仮想マシンのイメージが用意されています。 前の手順では、Windows Server 2016 Datacenter のイメージを使用して仮想マシンを作成しました。 この手順では、PowerShell モジュールを使用して、新しい VM のベースとしても使用できるその他の Windows イメージを Marketplace で検索します。 この処理は、発行元、プラン、およびイメージの名前 (SKU) の検索で構成されます。 

[Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) コマンドを使用して、イメージ発行元の一覧を取得します。  

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

[Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) を使用して、イメージ プランの一覧を取得します。 次のコマンドを使用すると、返される一覧が、指定した発行元でフィルター処理されます。 

```powershell
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```powershell
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

[Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) コマンドは、発行元とプラン名でフィルター処理して、イメージ名の一覧を返します。

```powershell
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

この情報は、特定のイメージを使用して VM をデプロイするために使用できます。 この例では、VM オブジェクトにイメージ名を設定します。 デプロイの完全な手順については、このチュートリアル内の前の例を参照してください。

```powershell
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter-with-Containers `
    -Version latest
```

## <a name="understand-vm-sizes"></a>VM のサイズについて

仮想マシンのサイズにより、CPU、GPU、メモリなど、仮想マシンで利用できるコンピューティング リソースの量が決定されます。 仮想マシンは、予想されるワークロードに適したサイズで作成する必要があります。 ワークロードが増えた場合は既存の仮想マシンのサイズを変更できます。

### <a name="vm-sizes"></a>VM サイズ

次の表は、ユース ケース別にサイズを分類したものです。  

| 型                     | サイズ           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| 汎用         |DSv2、Dv2、DS、D、Av2、A0 ～ 7| CPU とメモリのバランスがとれています。 開発/テスト環境や、小中規模のアプリケーションとデータ ソリューションに最適です。  |
| コンピューティングの最適化      | Fs、F             | メモリに対する CPU の比が大きくなっています。 トラフィックが中程度のアプリケーション、ネットワーク アプライアンス、バッチ処理に適しています。        |
| メモリの最適化       | GS、G、DSv2、DS、Dv2、D   | コアに対するメモリの比が大きくなっています。 リレーショナル データベース、中から大規模のキャッシュ、およびインメモリ分析に適しています。                 |
| ストレージの最適化       | Ls                | 高いディスク スループットと IO。 ビッグ データ、SQL、および NoSQL のデータベースに最適です。                                                         |
| GPU           | NV、NC            | 負荷の大きいグラフィックスのレンダリングやビデオ編集に特化した VM です。       |
| 高性能 | H、A8 ～ 11          | オプションで高スループットのネットワーク インターフェイス (RDMA) を備えた、最も強力な CPU VM です。 


### <a name="find-available-vm-sizes"></a>使用可能な VM サイズを確認する

特定のリージョンで利用可能な VM サイズのリストを確認するには、[Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) コマンドを使用します。

```powershell
Get-AzureRmVMSize -Location EastUS
```

## <a name="resize-a-vm"></a>VM のサイズを変更する

デプロイ後に VM のサイズを変更して、リソースの割り当てを増減できます。

VM のサイズを変更する前に、現在の VM クラスターで目的のサイズを利用可能であるか確認します。 [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) コマンドは、サイズの一覧を返します。 

```powershell
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

目的のサイズが使用可能な場合は電源を入れた状態で VM のサイズを変更できます。ただし、この操作中に再起動が行われます。

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

目的のサイズが現在のクラスターにない場合、サイズ変更の操作を行うには VM の割り当てを解除する必要があります。 VM の電源を再度入れると、一時ディスク上のデータはすべて削除され、静的 IP アドレスを使用している場合以外はパブリック IP アドレスが変更されるため、注意してください。 

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
```

## <a name="vm-power-states"></a>VM の電源の状態

Azure VM は、さまざまな電源状態のいずれかになります。 この状態は、ハイパーバイザーから見た VM の現在の状態を表しています。 

### <a name="power-states"></a>電源の状態

| 電源の状態 | 説明
|----|----|
| Starting | 仮想マシンが起動中であることを示します。 |
| 実行中 | 仮想マシンが実行中であることを示します。 |
| 停止中 | 仮想マシンが停止中であることを示します。 | 
| 停止済み | 仮想マシンが停止されていることを示します。 仮想マシンが停止済みの状態でも、コンピューティング料金は発生します。  |
| 割り当て解除中 | 仮想マシンの割り当てが解除中であることを示します。 |
| 割り当て解除済み | 仮想マシンがハイパーバイザーから完全に削除されているものの、コントロール プレーンでは使用可能であることを示します。 仮想マシンが割り当て解除済みの状態でも、コンピューティング料金は発生します。 |
| - | 仮想マシンの電源状態が不明であることを示します。 |

### <a name="find-power-state"></a>電源の状態を確認する

特定の VM の状態を取得するには、[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) コマンドを使用します。 必ず仮想マシンとリソース グループの有効な名前を指定してください。 

```powershell
Get-AzureRmVM `
    -ResourceGroupName myResourceGroupVM `
    -Name myVM `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

出力:

```powershell
Status
------
PowerState/running
```

## <a name="management-tasks"></a>管理タスク

仮想マシンのライフサイクルで、各種の管理タスクを実行する必要がある場合があります (仮想マシンの起動、停止、削除など)。 また、何度も行う作業や複雑な作業は、スクリプトを作成して自動化したい場合もあるでしょう。 日常的な管理タスクの多くは、Azure PowerShell を使ってコマンド ラインやスクリプトから実行できます。

### <a name="stop-virtual-machine"></a>仮想マシンの停止

[Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) を使用して仮想マシンを停止し、割り当てを解除します。

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

仮想マシンをプロビジョニングされた状態で保持する場合は、-StayProvisioned パラメーターを使用します。

### <a name="start-virtual-machine"></a>仮想マシンの起動

```powershell
Start-AzureRmVM -ResourceGroupName myResourceGroupVM -Name myVM
```

### <a name="delete-resource-group"></a>Delete resource group

リソース グループを削除すると、そこに含まれているリソースもすべて削除されます。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroupVM -Force
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のような基本的な VM の作成と管理を実行する方法について説明しました。

> [!div class="checklist"]
> * VM を作成して VM に接続する
> * VM イメージを選択して使用する
> * 特定の VM サイズを確認して使用する
> * VM のサイズを変更する
> * VM の状態を表示して理解する

次のチュートリアルに進み、VM ディスクについて確認してください。  

> [!div class="nextstepaction"]
> [VM ディスクの作成と管理](./tutorial-manage-data-disk.md)

