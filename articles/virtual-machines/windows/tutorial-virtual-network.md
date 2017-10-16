---
title: "Azure Virtual Networks と Windows 仮想マシン | Microsoft Docs"
description: "チュートリアル - Azure PowerShell を使用した Azure Virtual Networks と Windows 仮想マシンの管理"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: bbd0658a3bafc1b82ff6ddd39a4d23d015188337
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Azure PowerShell を使用した Azure Virtual Networks と Windows 仮想マシンの管理

Azure 仮想マシンでは、内部と外部のネットワーク通信に Azure ネットワークが使用されます。 このチュートリアルでは、仮想ネットワーク内に複数の仮想マシン (VM) を作成し、これらの仮想マシン間のネットワーク接続を構成します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * 仮想ネットワーク サブネットの作成
> * ネットワーク セキュリティ グループによるネットワーク トラフィックの制御
> * アクションでのトラフィック規則の表示

このチュートリアルには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="create-vnet"></a>VNet を作成する

VNet とは、クラウド内のユーザー独自のネットワークを表したものです。 サブスクリプション専用に Azure クラウドが論理的に分離されています。 VNet 内には、サブネット、これらのサブネットへの接続規則、VM からサブネットへの接続があります。

他の Azure リソースを作成する前に、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成する必要があります。 次の例では、*myRGNetwork* という名前のリソース グループを場所 *EastUS* に作成します。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

サブネットは、VNet の子リソースで、IP アドレスのプレフィックスを使用して、CIDR ブロック内のアドレス空間のセグメントの定義に役立ちます。 NIC をサブネットに追加し、VM に接続して、さまざまなワークロードへの接続を提供できます。

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用してサブネットを作成します。

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) で *myFrontendSubnet* を使用して、*myVNet* という名前の VNET を作成します。

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>フロントエンド VM を作成する

VM が VNet で通信するには、仮想ネットワーク インターフェイス (NIC) が必要です。 *myFrontendVM* にはインターネットからアクセスするため、パブリック IP アドレスも必要です。 

[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) を使用してパブリック IP アドレスを作成します。

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) を使用して、NIC を作成します。


```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、VM の管理者アカウントに必要なユーザー名とパスワードを設定します。 追加の手順でこれらの資格情報を使用して VM に接続します。

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig)、[Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem)、[Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage)、[Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk)、[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface)、[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、VM を作成します。 

```azurepowershell-interactive
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

## <a name="install-web-server"></a>Web サーバーのインストール

リモート デスクトップ セッションを使用して、*myFrontendVM* に IIS をインストールできます。 アクセスするには、VM のパブリック IP アドレスを取得する必要があります。

[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して、*myFrontendVM* のパブリック IP アドレスを取得できます。 次の例では、先ほど作成した *myPublicIPAddress* の IP アドレスを取得します。

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myRGNetwork `
    -Name myPublicIPAddress | select IpAddress
```

後の手順で使用できるように、この IP アドレスをメモしておきます。

次のコマンドを使用して、*myFrontendVM* とのリモート デスクトップ セッションを作成します。 *<publicIPAddress>* を先ほどメモしたアドレスに置き換えます。 メッセージが表示されたら、VM の作成時に使用した資格情報を入力します。

```
mstsc /v:<publicIpAddress>
``` 

*myFrontendVM* にログインしているので、1 行の PowerShell を使用して、IIS をインストールし、Web トラフィックを許可するローカル ファイアウォール規則を有効にできます。 RDP セッションから VM で PowerShell プロンプトを開き、次のコマンドを実行します。

[Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) を使用して、IIS Web サーバーをインストールするカスタム スクリプト拡張機能を実行します。

```azurepowershell-interactive
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

これで、VM を参照するためのパブリック IP アドレスを使用して IIS サイトにアクセスできるようになりました。

![IIS の既定のサイト](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>内部トラフィックを管理する

ネットワーク セキュリティ グループ (NSG) には、VNet に接続されたリソースへのネットワーク トラフィックを許可または拒否する一連のセキュリティ規則が含まれています。 NSG は、サブネット、または VM にアタッチされている個々の NIC に関連付けることができます。 ポートを介した VM へのアクセスの開始と終了は、NSG 規則を使用して行われます。 *myFrontendVM* を作成したときに、RDP 接続用の受信ポート 3389 が自動的に開かれました。

VM の内部通信は、NSG を使用して構成できます。 このセクションでは、ネットワークに追加のサブネットを作成し、このサブネットにポート 1433 での *myFrontendVM* から *myBackendVM* への接続を許可する NSG を割り当てる方法について説明します。 サブネットは、作成時に VM に割り当てられます。

バックエンド サブネットの NSG を作成することで、*myBackendVM* への内部トラフィックを *myFrontendVM* からのみに制限できます。 次の例では、[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) を使用して、*myBackendNSGRule* という名前の NSG 規則を作成します。

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) を使用して、*myBackendNSG* という名前のネットワーク セキュリティ グループを追加します。

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>バックエンド サブネットの追加

[Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) を使用して *myBackEndSubnet* を *myVNet* に追加します。

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
```

## <a name="create-back-end-vm"></a>バックエンド VM の作成

SQL Server イメージを使用すると、最も簡単にバックエンド VM を作成できます。 このチュートリアルではデータベース サーバーを使用して VM を作成しますが、データベースへのアクセスに関する情報は提供しません。

*myBackendNic* を作成します。

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Get-Credential を使用して、VM の管理者アカウントに必要なユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

*myBackendVM* を作成します。

```azurepowershell-interactive
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016SP1-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

使用されているイメージには SQL Server がインストールされていますが、このチュートリアルでは使用しません。 これが含まれているのは、Web トラフィックを処理する VM と、データベースの管理を処理する VM の構成の方法を示すためです。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、仮想マシンとの関連で Azure ネットワークを作成し、セキュリティで保護しました。 

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * 仮想ネットワーク サブネットの作成
> * ネットワーク セキュリティ グループによるネットワーク トラフィックの制御
> * アクションでのトラフィック規則の表示

次のチュートリアルに進み、仮想マシンのデータを Azure Backup で監視する方法を学習してください。 をクリックします。

> [!div class="nextstepaction"]
> [Azure の Windows 仮想マシンのバックアップ](./tutorial-backup-vms.md)
