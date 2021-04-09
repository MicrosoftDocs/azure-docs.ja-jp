---
title: Azure PowerShell を使用して Windows 仮想マシンに SQL Server を作成する | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell を使用して、SQL Server 2017 を実行する Windows 仮想マシンを作成する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 350df1aede076120fd14dc46da351835c894c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97356839"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して Windows 仮想マシンに SQL Server を作成する

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

このクイックスタートでは、Azure PowerShell を使用して SQL Server 仮想マシン (VM) を作成する方法について説明します。

> [!TIP]
> - このクイック スタートでは、SQL VM を迅速にプロビジョニングしてそこに接続する方法について説明します。 SQL VM を作成するためのその他の Azure PowerShell オプションについては、[Azure PowerShell を使用した SQL Server VM のプロビジョニング ガイド](create-sql-vm-powershell.md)を参照してください。
> - SQL Server の仮想マシンに関するご質問については、[よくあるご質問](frequently-asked-questions-faq.md)に関するページをご覧ください。

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Azure サブスクリプションを取得する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


## <a name="get-azure-powershell"></a><a id="powershell"></a>Azure PowerShell を入手する

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>PowerShell の構成

1. PowerShell を開き、**Connect-AzAccount** コマンドを実行することで、Azure アカウントへのアクセスを確立します。

   ```powershell
   Connect-AzAccount
   ```

1. サインイン ウィンドウが表示されたら、資格情報を入力します。 Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

## <a name="create-a-resource-group"></a>リソース グループを作成する

1. 一意のリソース グループ名を持つ変数を定義します。 クイック スタートの以降の手順を簡略化するために、残りのコマンドでは、この名前を他のリソース名のベースとして使用します。

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. すべての VM リソースのターゲット Azure リージョンの場所を定義します。

   ```powershell
   $Location = "East US"
   ```

1. リソース グループを作成します。

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>ネットワーク設定の構成

1. 仮想ネットワーク、サブネット、パブリック IP アドレスを作成します。 これらのリソースは、仮想マシンへのネットワーク接続を提供し、その仮想マシンをインターネットに接続するために使用されます。

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. ネットワーク セキュリティ グループを作成します。 リモート デスクトップ (RDP) および SQL Server 接続を許可するように規則を構成します。

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. ネットワーク インターフェイスを作成します。

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>SQL VM の作成

1. VM にサインインするための資格情報を定義します。 ユーザー名は "azureadmin" です。 コマンドを実行する前に、\<password> を変更してください。

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. 仮想マシン構成オブジェクトを作成してから、VM を作成します。 次のコマンドでは、Windows Server 2016 で SQL Server 2017 Developer Edition VM を作成します。

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > VM の作成には数分かかります。

## <a name="register-with-sql-vm-rp"></a>SQL VM RP への登録 

ポータル統合および SQL VM の機能を利用するには、[SQL IaaS Agent 拡張機能](sql-agent-extension-manually-register-single-vm.md)に登録する必要があります。

すべての機能を利用するには、フル モードで拡張機能に登録する必要があります。 ただし、そのようにすると SQL Server サービスが再起動されるので、最初は軽量モードで登録しておき、メンテナンス期間中にフル モードにアップグレードする方法が推奨されます。 

まず、SQL Server VM を軽量モードで登録します。 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

その後、メンテナンス期間中にフル モードにアップグレードします。 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>VM へのリモート デスクトップ接続

1. 次のコマンドを使用して、新しい VM のパブリック IP アドレスを取得します。

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. 返された IP アドレスをコマンド ライン パラメーターとして **mstsc** に渡し、新しい VM へのリモート デスクトップ セッションを開始します。

   ```
   mstsc /v:<publicIpAddress>
   ```

1. 資格情報を求められたら、別のアカウントの資格情報の入力を選択します。 先頭にバックスラッシュを付けたユーザー名 (例: `\azureadmin`) と、このクイック スタートの前の手順で設定したパスワードを入力します。

## <a name="connect-to-sql-server"></a>SQL Server への接続

1. リモート デスクトップ セッションにサインインしたら、スタート メニューから **SQL Server Management Studio 2017** を起動します。

1. **[サーバーに接続]** ダイアログ ボックスでは、既定の設定をそのまま使用します。 サーバー名は VM の名前です。 認証は **[Windows 認証]** に設定されます。 **[接続]** を選択します。

これで、SQL Server にローカル接続されました。 リモート接続する場合は、Azure portal から、または手動で[接続を構成する](ways-to-connect-to-sql.md)必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

VM を継続的に実行する必要がない場合は、使用中でないときに停止することで、不要な料金の発生を回避できます。 次のコマンドでは、VM を停止しますが、後から使用できるように残しておきます。

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

**Remove-AzResourceGroup** コマンドを使用して、仮想マシンに関連付けられているすべてのリソースを完全に削除することもできます。 そのようにすると仮想マシンも完全に削除されるため、このコマンドは注意して使用してください。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure PowerShell を使用して SQL Server 2017 仮想マシンを作成しました。 新しい SQL Server にデータを移行する方法の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [SQL VM にデータベースを移行する](migrate-to-vm-from-sql-server.md)
