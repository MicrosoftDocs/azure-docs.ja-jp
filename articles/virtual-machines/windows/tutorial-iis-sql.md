---
title: チュートリアル - Azure での SQL、IIS、.NET スタックを実行する VM の作成
description: このチュートリアルでは、Azure で Windows 仮想マシンに Azure SQL/IIS/.NET スタックをインストールする方法について説明します。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c53194bd345c18ac582acd538f1e8f8e1e34d54
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027854"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して Windows VM に SQL、IIS、.NET スタックをインストールする

このチュートリアルでは、Azure PowerShell を使用して SQL、IIS、.NET スタックをインストールします。 このスタックは、1 つは IIS および .NET を、もう 1 つは SQL Server を使用して Windows Server 2016 を実行する 2 つの VM で構成されています。

> [!div class="checklist"]
> * VM の作成 
> * VM に IIS と .NET Core SDK をインストールする
> * SQL Server を実行する VM を作成する
> * SQL Server 拡張機能をインストールする

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="create-an-iis-vm"></a>IIS VM を作成する 

この例では、PowerShell Cloud Shell の [New-AzVM](/powershell/module/az.compute/new-azvm) コマンドレットを使用して、Windows Server 2016 仮想マシンをすばやく作成し、IIS と .NET Framework をインストールします。 IIS と Azure SQL VM はリソース グループと仮想ネットワークを共有するため、それらの名前となる変数を作成します。


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

カスタム スクリプト拡張機能と [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) コマンドレットを使用して、IIS および .NET framework をインストールします。

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>別のサブネットの作成

SQL VM に使用する 2 つ目のサブネットを作成します。 [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork} を使用して vNet を取得します。

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) を使用してサブネットの構成を作成します。


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

[Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) を使用し、新しいサブネット情報で vNet を更新します。
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>Azure SQL 仮想マシン

SQL Server の構成済みの Azure Marketplace イメージを使用して Azure SQL VM を作成します。 まず VM を作成し、VM に SQL Server 拡張機能をインストールします。 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

[Set-AzVMSqlServerExtension](/powershell/module/az.compute/set-azvmsqlserverextension) を使用して [SQL Server 拡張機能](../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md)を SQL VM に追加します。

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure PowerShell を使用して Azure SQL、IIS、および .NET スタックをインストールしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * VM の作成 
> * VM に IIS と .NET Core SDK をインストールする
> * SQL Server を実行する VM を作成する
> * SQL Server 拡張機能をインストールする

TLS/SSL 証明書を使用して IIS Web サーバーをセキュリティ保護する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [TLS/SSL 証明書を使用して IIS Web サーバーをセキュリティで保護する](tutorial-secure-web-server.md)
