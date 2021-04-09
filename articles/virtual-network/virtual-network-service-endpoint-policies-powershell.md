---
title: データ流出を Azure Storage に制限する - Azure PowerShell
description: この記事では、Azure PowerShell を使って仮想ネットワーク サービス エンドポイント ポリシーで Azure Storage リソースへの仮想ネットワーク データ流出を制限する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: d8e300c9be8f3e59dc9443bf1f1806e4228992ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790395"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Azure PowerShell を使って仮想ネットワーク サービス エンドポイント ポリシーで Azure Storage アカウントへのデータの流出を管理する

仮想ネットワーク サービス エンドポイント ポリシーを使用すると、サービス エンドポイント経由で仮想ネットワーク内から Azure Storage アカウントにアクセス制御を適用できます。 これは、ワークロードのセキュリティ保護のために必要であり、どのストレージ アカウントが許可され、どのようなデータ流出が許可されるかが管理されます。
この記事では、次のことについて説明します。

* 仮想ネットワークを作成します。
* サブネットを追加し、Azure Storage に対してサービス エンドポイントを有効にします。
* 2 つの Azure Storage アカウントを作成し、上記で作成したサブネットからのネットワーク アクセスを許可します。
* いずれかのストレージ アカウントへのアクセスのみを許可するサービス エンドポイント ポリシーを作成します。
* サブネットに仮想マシン (VM) をデプロイします。
* サブネットから許可されているストレージ アカウントへのアクセスを確認します。
* サブネットから許可されていないストレージ アカウントへのアクセスが拒否されていることを確認します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用する場合、この記事では Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

仮想ネットワークを作成する前に、仮想ネットワークのリソース グループと、この記事で作成された他のすべてのリソースを作成する必要があります。 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを作成します。 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、アドレス プレフィックスが *10.0.0.0/16* の、*myVirtualNetwork* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>サービス エンドポイントを有効にする

仮想ネットワークのサブネットを作成します。 この例では、*Microsoft.Storage* のサービス エンドポイントを持つ *Private* という名前のサブネットが作成されます。 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>サブネットのネットワーク アクセスを制限する

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して、ネットワーク セキュリティ グループのセキュリティ規則を作成します。 次の規則では、Azure Storage サービスに割り当てられたパブリック IP アドレスへの発信アクセスを許可します。 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
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
  -Priority 110 -Protocol * `
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
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Azure Storage アカウントに対するネットワーク アクセスを制限する

サービス エンドポイントを有効にした Azure サービスを介して作成されたリソースへのネットワーク アクセスを制限するために必要な手順は、サービスによって異なります。 各サービスの具体的な手順については、それぞれのサービスのドキュメントをご覧ください。 この記事の残りの部分では、例として、Azure ストレージ アカウントのネットワーク アクセスを制限する手順を示します。

### <a name="create-two-storage-accounts"></a>ストレージ アカウントを 2 つ作成する

[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) を使用して Azure ストレージ アカウントを作成します。

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

ストレージ アカウントが作成されたら、[Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) を使用して、変数に読み込むストレージ アカウントのキーを取得します。

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

キーは、後の手順でファイル共有を作成するときに使用します。 `$storageAcctKey` を入力し、値をメモします。後の手順でファイル共有を VM のドライブにマップするときは、この値も手動で入力する必要があります。

ここで、上記の手順を繰り返して、2 つ目のストレージ アカウントを作成します。

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

また、後でファイル共有の作成に使用できるように、このアカウントからストレージ アカウント キーを取得します。

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>各ストレージ アカウントにファイル共有を作成する

[New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext) を使用して、ご自身のストレージ アカウントとキーのコンテキストを作成します。 コンテキストによってストレージ アカウント名とアカウント キーがカプセル化されます。

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

[New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare) を使用してファイル共有を作成します。

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>ストレージ アカウントへのすべてのネットワーク アクセスを拒否する

既定では、ストレージ アカウントは、任意のネットワーク上のクライアントからのネットワーク接続を受け入れます。 選択したネットワークのみにアクセスを制限するには、[Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset) を使用して、既定のアクションを *Deny* に変更します。 ネットワーク アクセスが拒否されると、ストレージ アカウントには、どのネットワークからもアクセスできなくなります。

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>VNet サブネットからのネットワーク アクセスのみを有効にする

[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用して作成済み仮想ネットワークを取得し、[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) を使用して、変数に読み込むプライベート サブネット オブジェクトを取得します。

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

[Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig) を使用して、*Private* サブネットからストレージ アカウントへのネットワーク アクセスを許可します。

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>有効なストレージ アカウントへのアクセスを許可するポリシーを適用する

仮想ネットワーク内のユーザーが、安全で許可されている Azure Storage アカウントにのみアクセスできるようにするため、定義で許可されているストレージ アカウントの一覧を使用してサービス エンドポイント ポリシーを作成できます。 このポリシーは、その後サービス エンドポイントを介してストレージに接続されている仮想ネットワーク サブネットに適用されます。

### <a name="create-a-service-endpoint-policy"></a>サービス エンドポイント ポリシーを作成する

このセクションでは、サービス エンドポイント経由でのアクセスに許可されているリソースの一覧を使用して、ポリシー定義が作成されます

最初の (許可された) ストレージ アカウントのリソース ID を取得する 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

上記のリソースを許可するポリシー定義を作成する

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

上で作成したポリシー定義を使用して、サービス エンドポイント ポリシーを作成する

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>仮想ネットワーク サブネットにサービス エンドポイント ポリシーを関連付ける

サービス エンドポイント ポリシーを作成したら、それを Azure Storage 用のサービス エンドポイント構成を含むターゲット サブネットに関連付けます。

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Azure Storage アカウントへのアクセス制限を検証する

### <a name="deploy-the-virtual-machine"></a>仮想マシンをデプロイする

ストレージ アカウントへのネットワーク アクセスをテストするには、サブネットに VM をデプロイします。

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して、"*プライベート*" サブネット内に仮想マシンを作成します。 次のコマンドを実行するとき、資格情報の入力を求められます。 入力した値は、VM のユーザー名とパスワードとして構成されます。 `-AsJob` オプションを使用すると、VM はバックグラウンドで作成されるため、次の手順に進むことができます。

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

次のサンプル出力のような出力が返されます。

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>"*許可されている*" ストレージ アカウントへのアクセスを確認する

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

"*myVmPrivate*" VM で、PowerShell を使って、許可されているストレージ アカウントの Azure ファイル共有を Z ドライブにマップします。 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell によって、次のサンプル出力のような出力が返されます。

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Azure ファイル共有は Z ドライブに正常にマップされました。

*myVmPrivate* VM へのリモート デスクトップ セッションを閉じます。

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>"*許可されていない*" ストレージ アカウントへのアクセスが拒否されていることを確認する

同じ "*myVmPrivate*" VM で、Azure ファイル共有の X ドライブへのマップを試行します。 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

共有へのアクセスが拒否され、`New-PSDrive : Access is denied` エラーが発生します。 ストレージ アカウント "*notallowedaccount*" がサービス エンドポイント ポリシーの許可されたリソースの一覧にないため、アクセスが拒否されます。 

*myVmPublic* VM へのリモート デスクトップ セッションを閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要なくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

この記事では、Azure 仮想ネットワーク サービス エンドポイントのサービス エンドポイント ポリシーを Azure Storage に適用しました。 Azure Storage アカウントを作成し、ネットワーク アクセスを、仮想ネットワーク サブネットの特定のストレージ アカウントだけに制限 (つまり、他は拒否) しました。 サービス エンドポイント ポリシーの詳細については、[サービス エンドポイント ポリシーの概要](virtual-network-service-endpoint-policies-overview.md)に関する記事を参照してください。
