---
title: チュートリアル - Windows VM 用の Azure 仮想ネットワークの作成と管理
description: このチュートリアルでは、Azure PowerShell を使用して、Windows 仮想マシン用の Azure 仮想ネットワークを作成し、管理する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.collection: windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e7f08e3c9869eae76f6c2e0127cfdab8f770d04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562182"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して Windows 仮想マシン用の Azure 仮想ネットワークを作成および管理する

Azure 仮想マシンでは、内部と外部のネットワーク通信に Azure ネットワークが使用されます。 このチュートリアルでは、2 台の仮想マシンをデプロイし、それらの VM に使用する Azure ネットワークを構成する手順について説明します。 このチュートリアルの例では、これらの VM が、データベース バックエンドを持つ Web アプリケーションのホストになっていることを想定していますが、アプリケーションのデプロイは、このチュートリアルでは行いません。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想ネットワークとサブネットの作成
> * パブリック IP アドレスの作成
> * フロントエンド VM の作成
> * ネットワーク トラフィックのセキュリティ保護
> * バックエンド VM の作成


## <a name="vm-networking-overview"></a>VM ネットワークの概要

Azure 仮想ネットワークを使用すると、仮想マシン、インターネット、その他の Azure サービス (Azure SQL Database など) の間でセキュリティ保護されたネットワーク接続を実現できます。 仮想ネットワークは、サブネットと呼ばれる論理セグメントに分割することができます。 サブネットは、ネットワーク フローを制御する目的のほか、セキュリティ境界としても使用されます。 VM をデプロイするときは、通常、そこに仮想ネットワーク インターフェイスが含まれているので、それをサブネットに接続することになります。

このチュートリアルを実行していく中で、次のようなリソースが作成されます。

![2 つのサブネットのある仮想ネットワーク](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - VM 間、およびインターネットと通信するために VM によって使用される仮想ネットワーク。
- *myFrontendSubnet* - フロントエンド リソースによって使用される、*myVNet* 内のサブネット。
- *myPublicIPAddress* - インターネットから *myFrontendVM* にアクセスするために使用するパブリック IP アドレス。
- *myFrontendNic* - *myFrontendVM* が *myBackendVM* と通信するために使用するネットワーク インターフェイス。
- *myFrontendVM* -インターネットと *myBackendVM* との間の通信に使用する VM。
- *myBackendNSG* - *myFrontendVM* と *myBackendVM* 間の通信を制御するネットワーク セキュリティ グループ。
- *myBackendSubnet* - *myBackendNSG* に関連付けられ、バックエンド リソースによって使用されるサブネット。
- *myBackendNic* - *myBackendVM* が *myFrontendVM* と通信するために使用するネットワーク インターフェイス。
- *myBackendVM* - ポート 1433 を使用して *myFrontendVM* と通信する VM。


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。


## <a name="create-subnet"></a>サブネットの作成 

このチュートリアルでは、2 つのサブネットと共に単一の仮想ネットワークが作成されます。 Web アプリケーションのホストとなるフロントエンド サブネットと、データベース サーバーのホストとなるバックエンド サブネットです。

仮想ネットワークを作成する前に、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用してリソース グループを作成します。 次の例では、*myRGNetwork* という名前のリソース グループを場所 *EastUS* に作成します。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

[New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) を使用して *myFrontendSubnet* という名前のサブネット構成を作成します。

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

また、*myBackendSubnet* という名前のサブネット構成を作成します。

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Create virtual network

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して、*myFrontendSubnet* と *myBackendSubnet* を使用する *myVNet* という名前の VNET を作成します。

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

この時点で、1 つのネットワークが作成され、フロントエンド サービス用とバックエンド サービス用の 2 つのサブネットに分割されました。 次のセクションでは、仮想マシンを作成してこれらのサブネットに接続します。

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

Azure リソースにインターネットからアクセスするためには、パブリック IP アドレスが必要です。 パブリック IP アドレスの割り当て方法は、動的または静的として構成することができます。 既定では、パブリック IP アドレスが動的に割り当てられます。 動的 IP アドレスは、VM の割り当てが解除されると解放されます。 この動作により、VM の割り当て解除を伴う操作の過程で IP アドレスが変化することになります。

割り当て方法を "静的" に設定することで、VM に割り当てた IP アドレスを確実に維持し、割り当てが解除された状態でも変更されないようにすることができます。 静的 IP アドレスを使用している場合、IP アドレス自体を指定することはできません。 それは、利用可能なアドレスのプールから割り当てられます。

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して *myPublicIPAddress* という名前のパブリック IP アドレスを作成します。

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

静的なパブリック IP アドレスを割り当てるには、-AllocationMethod パラメーターを `Static` に変更します。

## <a name="create-a-front-end-vm"></a>フロントエンド VM の作成

VM が仮想ネットワーク内で通信するには、仮想ネットワーク インターフェイス (NIC) が必要です。 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、NIC を作成します。

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して、VM の管理者アカウントに必要なユーザー名とパスワードを設定します。 追加の手順でこれらの資格情報を使用して VM に接続します。

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成します。

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>ネットワーク トラフィックのセキュリティ保護

ネットワーク セキュリティ グループ (NSG) には、Azure Virtual Network (VNet) に接続されたリソースへのネットワーク トラフィックを許可または拒否する一連のセキュリティ規則が含まれています。 NSG はサブネットに関連付けることができるほか、個々のネットワーク インターフェイスに関連付けることができます。 ネットワーク インターフェイスに関連付けられている NSG は、関連付けられている VM のみに適用されます。 NSG をサブネットに関連付けた場合、そのサブネットに接続されているすべてのリソースにその NSG のルールが適用されます。

### <a name="network-security-group-rules"></a>ネットワーク セキュリティ グループ ルール

NSG ルールは、トラフィックが許可または拒否されるネットワーク ポートを定義します。 このルールには、特定のシステム (またはサブネット) 間のトラフィックが制御されるように送信元 IP と送信先 IP のアドレス範囲を含めることができます。 また、NSG ルールには優先順位 (1 ～ 4096) も含まれます。 ルールは、優先順位に従って評価されます。 優先順位が 100 のルールは、優先順位が 200 のルールよりも前に評価されます。

すべての NSG に既定のルール一式が含まれています。 既定のルールは削除できませんが、割り当てられている優先順位は最も低いため、ルールを作成することでオーバーライドできます。

- **仮想ネットワーク** - 仮想ネットワーク内で発信および着信するトラフィックについては、受信方向と送信方向の両方で許可されます。
- **インターネット** - 送信トラフィックは許可されますが、受信トラフィックはブロックされます。
- **ロード バランサー** - Azure のロード バランサーによる VM とロール インスタンスの正常性プローブが許可されます。 負荷分散セットを使用していない場合は、このルールをオーバーライドできます。

### <a name="create-network-security-groups"></a>ネットワーク セキュリティ グループの作成

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して、*myFrontendVM* 上で受信 Web トラフィックを許可する *myFrontendNSGRule* という名前のインバウンド規則を作成します。

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

バックエンド サブネットの NSG を作成することで、*myBackendVM* への内部トラフィックを *myFrontendVM* からのみに制限できます。 次の例では、*myBackendNSGRule* という名前の NSG 規則を作成します。

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
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

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して、*myFrontendNSG* という名前のネットワーク セキュリティ グループを追加します。

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

ここで、New-AzNetworkSecurityGroup を使用して、*myBackendNSG* という名前のネットワーク セキュリティ グループを追加します。

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

ネットワーク セキュリティ グループをサブネットに追加します。

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>バックエンド VM の作成

SQL Server イメージを使用すると、このチュートリアル用に最も簡単にバックエンド VM を作成できます。 このチュートリアルではデータベース サーバーを使用して VM を作成しますが、データベースへのアクセスに関する情報は提供しません。

*myBackendNic* を作成します。

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Get-Credential を使用して、VM の管理者アカウントに必要なユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

*myBackendVM* を作成します。

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

この例のイメージには SQL Server がインストールされていますが、このチュートリアルでは使用しません。 これが含まれているのは、Web トラフィックを処理する VM と、データベースの管理を処理する VM の構成の方法を示すためです。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、仮想マシンとの関連で Azure ネットワークを作成し、セキュリティで保護しました。 

> [!div class="checklist"]
> * 仮想ネットワークとサブネットの作成
> * パブリック IP アドレスの作成
> * フロントエンド VM の作成
> * ネットワーク トラフィックのセキュリティ保護
> * バックエンド VM の作成

VM ディスクの保護の詳細については、[ディスクのバックアップとディザスター リカバリー](../backup-and-disaster-recovery-for-azure-iaas-disks.md)に関するページをご覧ください。