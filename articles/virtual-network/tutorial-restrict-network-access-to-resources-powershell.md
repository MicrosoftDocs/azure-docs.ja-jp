---
title: PaaS リソースへのネットワーク アクセスを制限する - Azure PowerShell
description: この記事では、Azure PowerShell を使って仮想ネットワーク サービス エンドポイントで Azure Storage、Azure SQL Database などの Azure リソースへのアクセスを制限する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6770486158b9c5f2e896951d91ff41643b6c8813
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790142"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>PowerShell を使用して仮想ネットワーク サービス エンドポイントで PaaS リソースへのネットワーク アクセスを制限する

仮想ネットワーク サービス エンドポイントを使うと、一部の Azure サービス リソースへのネットワーク アクセスを、仮想ネットワーク サブネットに制限できます。 また、リソースに対するインターネット アクセスを排除することもできます。 サービス エンドポイントにより、使用している仮想ネットワークからサポートされている Azure サービスへの直接接続が提供されるため、ご自身の仮想ネットワークのプライベート アドレス スペースを使用して、Azure サービスにアクセスできるようになります。 サービス エンドポイントを介して Azure リソースに送信されるトラフィックは、常に Microsoft Azure のバックボーン ネットワーク上に留まります。 この記事では、次のことについて説明します。

* 1 つのサブネットを含む仮想ネットワークを作成する
* サブネットを追加し、サービス エンドポイントを有効にする
* Azure リソースを作成し、サブネットからのみネットワーク アクセスできるようにする
* 各サブネットに仮想マシン (VM) をデプロイする
* サブネットからリソースへのアクセスを確認する
* サブネットおよびインターネットからリソースへのアクセスが拒否されたことを確認する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用する場合、この記事では Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

仮想ネットワークを作成する前に、仮想ネットワークのリソース グループと、この記事で作成された他のすべてのリソースを作成する必要があります。 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを作成します。 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、アドレス プレフィックスが *10.0.0.0/16* の、*myVirtualNetwork* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) を使用してサブネット構成を作成します。 次の例では、*Public* という名前のサブネットの構成を作成します。

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

[Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) を使用してサブネット構成を仮想ネットワークに書き込むことで、仮想ネットワークにサブネットを作成します。

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>サービス エンドポイントを有効にする

サービス エンドポイントをサポートするサービスについてのみ、サービス エンドポイントを有効にできます。 [Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice) を使用して、Azure の場所で使用できるサービス エンドポイント対応のサービスを表示します。 次の例では、*eastus* リージョンで使える、サービス エンドポイント対応サービスの一覧が返されます。 将来的にサービス エンドポイント対応の Azure サービスが増えるにつれて、返されるサービスの一覧も大きくなる予定です。

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

追加のサブネットを仮想ネットワークに作成します。 この例では、*Microsoft.Storage* のサービス エンドポイントを持つ *Private* という名前のサブネットが作成されます。 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>サブネットのネットワーク アクセスを制限する

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して、ネットワーク セキュリティ グループのセキュリティ規則を作成します。 次の規則では、Azure Storage サービスに割り当てられたパブリック IP アドレスへの発信アクセスを許可します。 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

次の規則では、すべてのパブリック IP アドレスへのアクセスを拒否します。 この規則は、優先度が高い前の規則によってオーバーライドされます。これにより、Azure Storage のパブリック IP アドレスへのアクセスが許可されます。

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

次の規則では、任意の場所からサブネットへのリモート デスクトップ プロトコル (RDP) 受信トラフィックを許可します。 サブネットへのリモート デスクトップ接続が許可されるため、後の手順でリソースへのネットワーク アクセスを確認できます。

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して、ネットワーク セキュリティ グループを作成します。 次の例では、*myNsgPrivate* という名前のネットワーク セキュリティ グループを作成します。

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) を使用してこのネットワーク セキュリティ グループを *Private* サブネットに関連付けて、サブネット構成を仮想ネットワークに書き込みます。 次の例では、*myNsgPrivate* ネットワーク セキュリティ グループを *Private* サブネットに関連付けます。

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>リソースへのネットワーク アクセスを制限する

サービス エンドポイントを有効にした Azure サービスを介して作成されたリソースへのネットワーク アクセスを制限するために必要な手順は、サービスによって異なります。 各サービスの具体的な手順については、それぞれのサービスのドキュメントをご覧ください。 この記事の残りの部分では、例として、Azure ストレージ アカウントのネットワーク アクセスを制限する手順を示します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) を使用して Azure ストレージ アカウントを作成します。 `<replace-with-your-unique-storage-account-name>` を Azure 全体における一意の名前 (3 から 24 文字で、数字と小文字のみを使用) に置き換えます。

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

ストレージ アカウントが作成されたら、[Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) を使用して、変数に読み込むストレージ アカウントのキーを取得します。

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

キーは、後の手順でファイル共有を作成するときに使用します。 `$storageAcctKey` を入力し、値をメモします。後の手順でファイル共有を VM のドライブにマップするときは、この値も手動で入力する必要があります。

### <a name="create-a-file-share-in-the-storage-account"></a>ストレージ アカウントにファイル共有を作成する

[New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext) を使用して、ご自身のストレージ アカウントとキーのコンテキストを作成します。 コンテキストによってストレージ アカウント名とアカウント キーがカプセル化されます。

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

[New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare) を使用してファイル共有を作成します。

$share = New-AzStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>ストレージ アカウントへのすべてのネットワーク アクセスを拒否する

既定では、ストレージ アカウントは、任意のネットワーク上のクライアントからのネットワーク接続を受け入れます。 選択したネットワークのみにアクセスを制限するには、[Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset) を使用して、既定のアクションを *Deny* に変更します。 ネットワーク アクセスが拒否されると、ストレージ アカウントには、どのネットワークからもアクセスできなくなります。

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>サブネットからのネットワーク アクセスを有効にする

[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用して作成済み仮想ネットワークを取得し、[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) を使用して、変数に読み込むプライベート サブネット オブジェクトを取得します。

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

[Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig) を使用して、*Private* サブネットからストレージ アカウントへのネットワーク アクセスを許可します。

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

ストレージ アカウントへのネットワーク アクセスをテストするには、各サブネットに VM を展開します。

### <a name="create-the-first-virtual-machine"></a>最初の仮想マシンを作成する

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して、"*パブリック*" サブネット内に仮想マシンを作成します。 次のコマンドを実行するとき、資格情報の入力を求められます。 入力した値は、VM のユーザー名とパスワードとして構成されます。 `-AsJob` オプションを使用すると、VM はバックグラウンドで作成されるため、次の手順に進むことができます。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

次のサンプル出力のような出力が返されます。

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>2 番目の仮想マシンを作成する

*Private* サブネットに仮想マシンを作成します。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Azure では VM の作成に数分かかります。 Azure が VM の作成を完了し、出力を PowerShell に返すまでは、次の手順に進まないでください。

## <a name="confirm-access-to-storage-account"></a>ストレージ アカウントへのアクセスを確認する

[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) を使用して、VM のパブリック IP アドレスを返します。 次の例では、*myVmPrivate* VM のパブリック IP アドレスを返しています。

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

次のコマンドの `<publicIpAddress>` を、前のコマンドで返されたパブリック IPアドレスで置換して、次のコマンドを入力します。

```powershell
mstsc /v:<publicIpAddress>
```

リモート デスクトップ プロトコル (.rdp) ファイルが作成され、お使いのコンピューターにダウンロードされます。 ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]** 、 **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。 **[OK]** を選択します。 サインイン処理中に証明書の警告が表示される場合があります。 警告を受け取ったら、 **[はい]** または **[続行]** を選択して接続処理を続行します。

*myVmPrivate* VM で、PowerShell を使って、Azure ファイル共有を Z ドライブにマップします。 次のコマンドを実行する前に、`<storage-account-key>` と `<storage-account-name>` を、ご自身で指定した値、または「[ストレージ アカウントの作成](#create-a-storage-account)」で取得した値で置き換えます。

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell によって、次のサンプル出力のような出力が返されます。

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Azure ファイル共有は Z ドライブに正常にマップされました。

VM から他のパブリック IP アドレスへの送信接続がないことを確認します。

```powershell
ping bing.com
```

応答はありません。これは、*Private* サブネットに関連付けられているネットワーク セキュリティ グループでは、Azure Storage サービスに割り当てられているアドレス以外のパブリック IP アドレスへの発信アクセスが許可されていないためです。

*myVmPrivate* VM へのリモート デスクトップ セッションを閉じます。

## <a name="confirm-access-is-denied-to-storage-account"></a>ストレージ アカウントへのアクセスが拒否されたことを確認する

*myVmPublic* VM のパブリック IP アドレスを取得します。

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

次のコマンドの `<publicIpAddress>` を、前のコマンドで返されたパブリック IPアドレスで置換して、次のコマンドを入力します。 

```powershell
mstsc /v:<publicIpAddress>
```

*myVmPublic* VM で、Z ドライブへの Azure ファイル共有のマッピングを試みます。次のコマンドを実行する前に、`<storage-account-key>` と `<storage-account-name>` を、ご自身で指定した値、または「[ストレージ アカウントの作成](#create-a-storage-account)」で取得した値で置き換えます。

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

共有へのアクセスが拒否され、`New-PSDrive : Access is denied` エラーが発生します。 *myVmPublic* VM が *Public* サブネットに展開されているため、アクセスが拒否されました。 *Public* サブネットでは、Azure Storage に対してサービス エンドポイントが有効になっていません。ストレージ アカウントが許可しているのは *Public* サブネットからではなく、*Private* サブネットからのネットワーク アクセスです。

*myVmPublic* VM へのリモート デスクトップ セッションを閉じます。

自分のコンピューターから、次のコマンドを使用してストレージ アカウントのファイル共有の表示を試みます。

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

アクセスが拒否され、*Get-AzStorageFile :リモート サーバーがエラー(403) 禁止。HTTP 状態コード: 403 -HTTP エラー メッセージ:"This request is not authorized to perform this operation (この要求には、この操作を実行する権限がありません)* " というエラーが発生します。これは、使っているコンピューターが *MyVirtualNetwork* 仮想ネットワークの *Private* サブネットにないためです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要なくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

この記事では、仮想ネットワーク サブネットのサービス エンドポイントを有効にしました。 複数の Azure サービスでデプロイされているリソースに対して、サービス エンドポイントを有効にできることを学習しました。 Azure ストレージ アカウントを作成し、そのストレージ アカウントへのネットワーク アクセスを、仮想ネットワーク サブネット内のリソースだけに制限しました。 サービス エンドポイントの詳細については、[サービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)と[サブネットの管理](virtual-network-manage-subnet.md)に関するページをご覧ください。

アカウントに複数の仮想ネットワークがある場合は、各仮想ネットワーク内のリソースが相互に通信できるように、2 つの仮想ネットワークを接続することもできます。 方法については、[仮想ネットワークの接続](tutorial-connect-virtual-networks-powershell.md)に関するページをご覧ください。
