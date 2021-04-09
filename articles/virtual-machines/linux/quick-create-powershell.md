---
title: クイック スタート - Azure PowerShell を使用して Linux VM を作成する
description: このクイック スタートでは、Azure PowerShell を使用して Linux 仮想マシンを作成する方法について説明します
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/31/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2cef611fe79ca04303840076b09b4cf6344b7e7d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616232"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>クイック スタート:PowerShell を使用して Azure に Linux 仮想マシンを作成する

Azure PowerShell モジュールは、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure PowerShell モジュールを使って、Linux 仮想マシン (VM) を Azure に展開する方法を示します。 このクイック スタートでは、Canonical の Ubuntu 18.04 LTS マーケットプレース イメージを使用します。 また、VM の動作を確認するために、VM に SSH 接続し、NGINX Web サーバーをインストールします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="create-ssh-key-pair"></a>SSH キー ペアの作成

[ssh-keygen](https://www.ssh.com/ssh/keygen/) を使用して SSH キーの組を作成します。 既存の SSH キーの組がある場合は、この手順をスキップできます。


```azurepowershell-interactive
ssh-keygen -t rsa -b 4096
```

キーの組のファイル名を入力するように求められます。または、**Enter** キーを押して、既定の場所 `/home/<username>/.ssh/id_rsa` を使用することもできます。 必要に応じて、キーのパスワードを作成することもできます。

SSH キーの組の作成方法の詳細については、[Windows で SSH キーを使用する方法](ssh-from-windows.md)に関するページを参照してください。

Cloud Shell を使用して SSH キーの組を作成した場合、[Cloud Shell によって自動的に作成されるストレージ アカウント](../../cloud-shell/persisting-shell-storage.md)に格納されます。 キーを取得するまでは、ストレージ アカウント (またはその中のファイル共有) を削除しないでください。削除すると、VM にアクセスできなくなります。 

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースの展開と管理に使用する論理コンテナーです。

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>仮想ネットワークのリソースを作成する

仮想ネットワーク、サブネット、パブリック IP アドレスを作成します。 これらのリソースは、VM へのネットワーク接続を提供し、VM をインターネットに接続するために使われます。

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Azure ネットワーク セキュリティ グループとトラフィック規則を作成します。 ネットワーク セキュリティ グループは、受信規則と送信規則を使用して VM をセキュリティで保護します。 次の例では、TCP ポート 22 に対して SSH 接続を許可する受信規則を作成します。 受信 Web トラフィックを許可するため、TCP ポート 80 の受信規則も作成します。

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、仮想ネットワーク インターフェイス カード (NIC) を作成します。 仮想 NIC は、VM をサブネット、ネットワーク セキュリティ グループ、パブリック IP アドレスに接続します。

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

PowerShell で VM を作成するには、使用するイメージ、サイズ、認証オプションなど、各種設定を含んだ構成を作成します。 この構成を使用して VM を作成することになります。

SSH の資格情報、OS の情報、VM のサイズを定義します。 この例では、SSH キーを `~/.ssh/id_rsa.pub` に格納しています。 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "18.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

これまでに定義した構成を組み合わせて、[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して仮想マシンを作成します。

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

VM がデプロイされるまでに数分かかります。 デプロイが完了したら、次のセクションに移動してください。

## <a name="connect-to-the-vm"></a>VM に接続します

パブリック IP アドレスを使用して VM との SSH 接続を作成します。 VM のパブリック IP アドレスを確認するには、[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) コマンドレットを使用します。

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

SSH キーの組の作成に使用したものと同じシェルを使用し、次のコマンドをシェルに貼り付けて、SSH セッションを作成します。 *10.111.12.123* は自分の VM の IP アドレスに置き換えてください。

```bash
ssh azureuser@10.111.12.123
```

メッセージが表示されたら、ログイン ユーザー名には *azureuser* を指定します。 SSH キーでパスフレーズが使われている場合は、求められたら入力する必要があります。


## <a name="install-nginx"></a>NGINX のインストール

VM の動作を確認するために、NGINX Web サーバーをインストールします。 SSH セッションからパッケージ ソースを更新し、最新の NGINX パッケージをインストールします。

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

完了したら、`exit` と入力して SSH セッションを終了します。


## <a name="view-the-web-server-in-action"></a>動作中の Web サーバーを表示する

任意の Web ブラウザーを使用して、NGINX の既定のウェルカム ページを表示します。 Web アドレスとして、VM のパブリック IP アドレスを入力します。 パブリック IP アドレスは、VM の概要ページで確認できるほか、先ほど使用した SSH 接続文字列にも含まれています。

![NGINX の既定のウェルカム ページ](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、単純な仮想マシンをデプロイし、ネットワーク セキュリティ グループと規則を作成し、基本的な Web サーバーをインストールしました。 Azure 仮想マシンの詳細については、Linux VM のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Linux 仮想マシンのチュートリアル](./tutorial-manage-vm.md)
