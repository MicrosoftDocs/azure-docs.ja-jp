---
title: クイック スタート - Azure PowerShell を使用して Windows VM を作成する
description: このクイック スタートでは、Azure PowerShell を使用して Windows 仮想マシンを作成する方法について説明します
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 78b912dd649ff942e0187f9b3602d9213383b8c9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560737"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>クイック スタート:PowerShell を使用して Azure に Windows 仮想マシンを作成する

Azure PowerShell モジュールは、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure PowerShell モジュールを使って、Windows Server 2016 を実行する仮想マシン (VM) を Azure に展開する方法を示します。 また、VM に RDP 接続し、IIS Web サーバーをインストールして、動作中の VM を表示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。


## <a name="create-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成します。 各リソースの名前を指定すると、それらがまだ存在していない場合は、`New-AzVM` コマンドレットによって作成されます。

メッセージが表示されたら、VM のサインイン資格情報として使用するユーザー名とパスワードを入力します。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

展開が完了した後、RDP で VM に接続します。 VM の動作を確認するために、IIS Web サーバーがインストールされます。

VM のパブリック IP アドレスを確認するには、[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) コマンドレットを使用します。

```powershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

次のコマンドを使って、お使いのローカル コンピューターからリモート デスクトップ セッションを作成します。 IP アドレスは、実際の VM のパブリック IP アドレスに置き換えてください。 

```powershell
mstsc /v:publicIpAddress
```

**[Windows セキュリティ]** ウィンドウで、 **[その他]** 、 **[別のアカウントを使用する]** の順に選択します。 ユーザー名として「**localhost**\\*username*」と入力し、仮想マシン用に作成したパスワードを入力して、 **[OK]** をクリックします。

サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続を作成します。

## <a name="install-web-server"></a>Web サーバーのインストール

VM の動作を確認するために、IIS Web サーバーをインストールします。 VM で PowerShell プロンプトを開き、次のコマンドを実行します。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

終了したら、VM への RDP 接続を閉じます。

## <a name="view-the-web-server-in-action"></a>動作中の Web サーバーを表示する

IIS をインストールし、VM のポート 80 をインターネットから開いたら、任意の Web ブラウザーを使用して IIS の既定のウェルカム ページを表示することができます。 前の手順で取得した VM のパブリック IP アドレスを使用します。 次の例は、既定の IIS Web サイトを示しています。

![IIS の既定のサイト](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、単純な仮想マシンをデプロイし、Web トラフィック用のネットワーク ポートを開き、基本的な Web サーバーをインストールしました。 Azure 仮想マシンの詳細については、Windows VM のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Windows 仮想マシンのチュートリアル](./tutorial-manage-vm.md)
