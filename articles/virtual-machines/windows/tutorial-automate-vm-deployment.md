---
title: チュートリアル - Azure 内の Windows VM 上にアプリケーションをインストールする | Microsoft Docs
description: このチュートリアルでは、カスタム スクリプト拡張機能を使ってスクリプトを実行し、Azure 内の Windows 仮想マシンにアプリケーションを展開する方法を説明します
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 02c038a1eefefb62dceb42e511b9a895691ef47b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101721"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>チュートリアル - カスタム スクリプト拡張機能を使って Azure 内の Windows 仮想マシンにアプリケーションを展開する

迅速かつ一貫した方法で仮想マシン (VM) を構成するには、[Windows 用のカスタム スクリプト拡張機能](extensions-customscript.md)を使用することができます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * カスタム スクリプト拡張機能を使用して IIS をインストールする
> * カスタム スクリプト拡張機能を使用する仮想マシンを作成する
> * 拡張機能の適用後に実行中の IIS サイトを表示する

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="custom-script-extension-overview"></a>カスタム スクリプト拡張機能の概要
カスタム スクリプト拡張機能は、Azure VM でスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI、PowerShell、Azure Portal、または Azure 仮想マシン REST API を使用して実行することもできます。

カスタム スクリプト拡張機能は、Windows VM と Linux VM の両方で使用することができます。


## <a name="create-virtual-machine"></a>仮想マシンの作成
次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

これで、[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) を使用して VM を作成できるようになります。 次の例では、場所 *EastUS* に *myVM* という名前の VM を作成します。 これらが存在しない場合は、リソース グループ *myResourceGroupAutomate* と関連ネットワーク リソースが作成されます。 Web トラフィックを許可するには、このコマンドレットでポート *80* も開きます。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

リソースと VM が作成されるまで数分かかります。


## <a name="automate-iis-install"></a>IIS のインストールを自動化する
[Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) を使用して、カスタム スクリプト拡張機能をインストールします。 この拡張機能によって `powershell Add-WindowsFeature Web-Server` が実行され、IIS Web サーバーがインストールされます。次に、VM のホスト名を表示するように *Default.htm* ページが更新されます。

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Web サイトをテストする
ロード バランサーのパブリック IP アドレスを取得するには、[Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) を使用します。 次の例では、先ほど作成した *myPublicIPAddress* の IP アドレスを取得します。

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力できます。 次の例のように、Web サイトが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の IIS Web サイト](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、仮想マシンへの IIS のインストールを自動化しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * カスタム スクリプト拡張機能を使用して IIS をインストールする
> * カスタム スクリプト拡張機能を使用する仮想マシンを作成する
> * 拡張機能の適用後に実行中の IIS サイトを表示する

次のチュートリアルに進み、カスタムの VM イメージを作成する方法を学習してください。

> [!div class="nextstepaction"]
> [カスタム VM イメージを作成する](./tutorial-custom-images.md)
