---
title: Azure PowerShell を使用して Azure プライベート エンドポイントを作成する | Microsoft Docs
description: Azure Private Link について学習する
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 0c6fc36be101679cea3a770f311005f63c3f0d66
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737378"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Azure PowerShell を使用してプライベート エンドポイントを作成する
プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 これによって、仮想マシン (VM) などの Azure リソースが Private Link リソースと非公開で通信できるようになります。 

このクイック スタートでは、Azure PowerShell を使用して、Azure 仮想ネットワーク上の VM と、Azure プライベート エンドポイントを含む論理 SQL サーバーを作成する方法を説明します。 その後、VM から SQL Database に安全にアクセスできます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>リソース グループを作成する

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

Azure では、仮想ネットワーク内のサブネットにリソースがデプロイされるため、サブネットを作成する必要があります。 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) を使用して、*mySubnet* というサブネット構成を作成します。 次の例では、プライベート エンドポイントのネットワーク ポリシー フラグを **Disabled** に設定して、*mySubnet* という名前のサブネットを作成します。

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> いずれも長い言葉であり、見た目が似ているため、`PrivateEndpointNetworkPoliciesFlag` パラメーターと利用できる別のフラグ `PrivateLinkServiceNetworkPoliciesFlag` は混同しやすくなっています。  必ず正しい方を、つまり、`PrivateEndpointNetworkPoliciesFlag` を使用してください。

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

## <a name="create-a-logical-sql-server"></a>論理 SQL サーバーの作成 

New-AzSqlServer コマンドを使用して論理 SQL サーバーを作成します。 サーバーの名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダーの値を独自の一意の値に必ず置き換えてください。

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

[New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection) を使用した仮想ネットワーク内のサーバーのプライベート エンドポイント: 

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
  -Subnet  $subnet `
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する 
SQL Database ドメイン用のプライベート DNS ゾーンを作成し、Virtual Network に対する関連付けリンクを作成します。また、プライベート エンドポイントをプライベート DNS ゾーンに関連付けるために、DNS ゾーン グループを作成します。

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  

$config = New-AzPrivateDnsZoneConfig -Name "privatelink.database.windows.net" -PrivateDnsZoneId $zone.ResourceId

$privateDnsZoneGroup = New-AzPrivateDnsZoneGroup -ResourceGroupName "myResourceGroup" `
 -PrivateEndpointName "myPrivateEndpoint" -name "MyZoneGroup" -PrivateDnsZoneConfig $config
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

[Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) を使用して、VM のパブリック IP アドレスを返します。 この例では、*myVM* VM のパブリック IP アドレスを返しています。

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
お使いのローカル コンピューターでコマンド プロンプトを開きます。 mstsc コマンドを実行します。 <publicIpAddress> を最後の手順で返されたパブリック IP アドレスに置き換えます。 


> [!NOTE]
> ローカル コンピューター上の PowerShell プロンプトからこれらのコマンドを実行済みで、Az PowerShell モジュール バージョン 1.0 以降を使用している場合、そのインターフェイスで続行できます。
```
mstsc /v:<publicIpAddress>
```

1. メッセージが表示されたら、 **[Connect]** を選択します。 
2. VM の作成時に指定したユーザー名とパスワードを入力します。
  > [!NOTE]
  > 場合によっては、[その他] > [別のアカウントを使用する] を選択して、VM の作成時に入力した資格情報を指定する必要があります。 
  
3. **[OK]** を選択します。 
4. 証明書の警告を受け取ることがあります。 この場合は、 **[はい]** または **[続行]** を選択します。 

## <a name="access-sql-database-privately-from-the-vm"></a>VM から SQL Database にプライベートにアクセスする

1. myVM のリモート デスクトップで、PowerShell を開きます。
2. 「`nslookup myserver.database.windows.net`」と入力します。 `myserver` は必ず SQL サーバー名に置き換えてください。

    次のようなメッセージが返されます。
    
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
    
3. [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) をインストールします。
4. **[サーバーに接続]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | --- | --- |
    | サーバーの種類 | データベース エンジン |
    | サーバー名 | myserver.database.windows.net |
    | ユーザー名 | 作成時に指定したユーザー名を入力します |
    | Password | 作成時に指定したパスワードを入力します |
    | パスワードを保存する | はい |
    
5. **[接続]** を選択します。
6. 左側のメニューで **[データベース]** を参照します。 
7. (省略可能) 情報を作成するか、mydatabase に対して情報のクエリを実行します。
8. *myVM* へのリモート デスクトップ接続を閉じます。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする 
プライベート エンドポイント、SQL Database、VM を使いおわったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとそのすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ
- [Azure Private Link](private-link-overview.md) の詳細
