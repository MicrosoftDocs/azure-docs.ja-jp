---
title: Azure PowerShell を使用して Azure プライベート エンドポイントを作成する | Microsoft Docs
description: Azure Private Link について学習する
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 569bc021d978714472bf40bcf39f7134fec95970
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130344"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Azure PowerShell を使用してプライベート エンドポイントを作成する
プライベート エンドポイントは、Azure の Private Link の基本的な構成要素です。 これによって、仮想マシン (VM) などの Azure リソースが Private Link リソースと非公開で通信できるようになります。 

このクイック スタートでは、Azure PowerShell を使用して、Azure 仮想ネットワーク上の VM と、Azure プライベート エンドポイントを含む SQL Database サーバーを作成する方法を説明します。 その後、VM から SQL Database Server に安全にアクセスできます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>リソース グループの作成

リソースを作成する前に、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、仮想ネットワークとプライベート エンドポイントをホストするリソース グループを作成する必要があります。 次の例では、*myResourceGroup* という名前のリソース グループを場所 *WestUS* に作成します。

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>仮想ネットワークを作成します
このセクションでは、仮想ネットワークとサブネットを作成します。 次に、サブネットを仮想ネットワークに関連付けます。

### <a name="create-a-virtual-network"></a>仮想ネットワークを作成します

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用してプライベート エンドポイント用の仮想ネットワークを作成します。 次の例では、*MyVirtualNetwork* という名前の仮想ネットワークを作成します。
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>サブネットを追加する

Azure では、仮想ネットワーク内のサブネットにリソースがデプロイされるため、サブネットを作成する必要があります。  [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) を使用して、 *mySubnet*  という名前のサブネット構成を作成します。 次の例では、プライベート エンドポイントのネットワーク ポリシー フラグを **Disabled** に設定して、*mySubnet* という名前のサブネットを作成します。

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>サブネットを仮想ネットワークに関連付ける

 [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) を使用して、仮想ネットワークにサブネット構成を書き込むことができます。 このコマンドで、サブネットが作成されます。

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>仮想マシンを作成する

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して、仮想ネットワーク内に VM を作成します。 次のコマンドを実行すると、資格情報の入力が求められます。 VM のユーザー名とパスワードを入力します。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

`-AsJob` オプションを使用すると、バックグラウンドで VM が作成されます。 次の手順に進むことができます。

Azure でバックグラウンドで VM の作成を開始すると、次のような内容が返されます。

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>SQL Database サーバーを作成する 

New-AzSqlServer コマンドを使用して SQL Database サーバーを作成します。 SQL Database サーバーの名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダーの値を独自の一意の値に置き換えることを忘れないでください。

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントを作成する

[New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection) を使用した仮想ネットワーク内の SQL Database サーバーのプライベート エンドポイント: 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する 
SQL Database サーバー ドメイン用のプライベート DNS ゾーンを作成し、仮想ネットワークに対する関連付けリンクを作成します。 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

 [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress)  を使用して、VM のパブリック IP アドレスを返します。 この例では、 *myVM*  VM のパブリック IP アドレスを返しています。

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
お使いのローカル コンピューターでコマンド プロンプトを開きます。 mstsc コマンドを実行します。  <publicIpAddress>  を直前の手順で返されたパブリック IP アドレスに置き換えます。 


> [!NOTE]
> ローカル コンピューター上の PowerShell プロンプトからこれらのコマンドを実行済みで、Az PowerShell モジュール バージョン 1.0 以降を使用している場合、そのインターフェイスで続行できます。
```
mstsc /v:<publicIpAddress>
```

1. メッセージが表示されたら、 **[接続]** を選択します。 
2. VM の作成時に指定したユーザー名とパスワードを入力します。
  > [!NOTE]
  > 場合によっては、[その他] > [別のアカウントを使用する] を選択して、VM の作成時に入力した資格情報を指定する必要があります。 
  
3.  **[OK]** をクリックします。 
4. 証明書の警告を受け取ることがあります。 その場合は、 **[はい]**   または  **[続行]** を選択します。 

## <a name="access-sql-database-server-privately-from-the-vm"></a>VM から SQL Database サーバーにプライベートにアクセスする

1. myVM のリモート デスクトップで、PowerShell を開きます。
2. 「 `nslookup myserver.database.windows.net`」と入力します。 

    次のようなメッセージが返されます。
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
3. Install SQL Server Management Studio
4. In Connect to server, enter or select this information:
    Setting Value
      Server type   Select Database Engine.
      Server name   Select myserver.database.windows.net
      Username  Enter a username provided during creation.
      Password  Enter a password provided during creation.
      Remember password Select Yes.
5. Select Connect.
6. Browse Databases from left menu. 
7. (Optionally) Create or query information from mydatabase
8. Close the remote desktop connection to *myVM*. 

## Clean up resources 
When you're done using the private endpoint, SQL Database server and the VM, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) to remove the resource group and all the resources it has:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順
- [Azure Private Link](private-link-overview.md) の詳細
