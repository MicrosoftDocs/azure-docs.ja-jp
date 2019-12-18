---
title: チュートリアル:Azure で SSL 証明書を使用して Windows Web サーバーをセキュリティで保護する
description: このチュートリアルでは、Azure PowerShell を使用して、Azure Key Vault に格納されている SSL 証明書を使って IIS Web サーバーを実行する Windows 仮想マシンをセキュリティで保護する方法について説明します。
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
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c84bc3633ccd25fd2206c9e6912a6b8c8d46a5f4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974191"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-ssl-certificates-stored-in-key-vault"></a>チュートリアル:Key Vault に格納されている SSL 証明書を使用して Azure 内の Windows 仮想マシン上の Web サーバーをセキュリティで保護する

Web サーバーをセキュリティ保護するには、Secure Sockets Layer (SSL) 証明書を使用した Web トラフィックの暗号化が利用できます。 これらの SSL 証明書は Azure Key Vault に格納できるため、Azure 上の Windows 仮想マシン (VM) に、セキュリティで保護された証明書のデプロイが可能になります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Key Vault を作成する
> * 証明書を生成したり、Key Vault にアップロードしたりする
> * VM の作成と IIS Web サーバーのインストール
> * VM への証明書の取り込みと IIS のSSL バインドの構成


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。


## <a name="overview"></a>概要
Azure Key Vault では、証明書やパスワードなどの暗号化キーとシークレットが保護されます。 Key Vault は、証明書の管理プロセスを合理化し、証明書にアクセスするキーの管理を維持するのに役立ちます。 Key Vault 内に自己署名証明書を作成したり、既に所有している、既存の信頼された証明書をアップロードしたりできます。

組み込みの証明書を含むカスタム VM イメージを使用するのではなく、実行中の VM に証明書を取り込みます。 このプロセスでは、デプロイ時に最新の証明書が Web サーバーにインストールされていることを確認します。 証明書を更新するか置き換える場合には、新しいカスタム VM イメージを作成する必要もありません。 追加の VM を作成すると、最新の証明書が自動的に取り込まれます。 すべてのプロセスにおいて、証明書が Azure プラットフォームから流出したり、スクリプト、コマンドラインの履歴、またはテンプレートで公開されたりすることはありません。


## <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する
キー コンテナーと証明書を作成する前に、[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroupSecureWeb* という名前のリソース グループを*米国東部*に作成します。

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

[New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) を使用してキー コンテナーを作成します。 各 Key Vault には一意の名前が必要であり、その名前はすべて小文字にする必要があります。 次の例の `mykeyvault` は一意の Key Vault 名で置き換えてください。

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>証明書を生成して Key Vault に格納する
実際の運用では、[Import-AzKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/import-azkeyvaultcertificate) を使用して、信頼できるプロバイダーによって署名された有効な証明書をインポートする必要があります。 このチュートリアルでは、[Add-AzKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultcertificate) で、[New-AzKeyVaultCertificatePolicy](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy) からの既定の証明書ポリシーを使用する自己署名証明書を生成する方法を次の例に示します。 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>仮想マシンの作成
次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) を使用して VM を作成できるようになりました。 次の例では、場所 *EastUS* に *myVM* という名前の VM を作成します。 これらが存在しない場合は、関連ネットワーク リソースが作成されます。 セキュリティで保護された Web トラフィックを許可するには、コマンドレットでポート *443* も開きます。

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

VM が作成されるまで､数分間かかります｡ 最後に、Azure カスタム スクリプト拡張機能を使用して [Set-AzVmExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) で IIS web サーバーをインストールします。


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Key Vault からの証明書を VM に追加します。
Key Vault からの証明書を VM に追加するには、[Get-AzKeyVaultSecret](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvaultsecret) で証明書の ID を取得します。 [Add-AzVMSecret](https://docs.microsoft.com/powershell/module/az.compute/add-azvmsecret) で証明書を VM に追加します。

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>証明書を使用するように IIS を構成します。
再度、[Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) でカスタム スクリプト拡張機能を使用して IIS 構成を更新します。 この更新プログラムは、Key Vault から挿入した証明書を IIS に 適用し、Web バインド構成します。

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>セキュリティで保護された Web アプリをテストする
VM のパブリック IP アドレスを取得するには、[Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) を使用します。 次の例では、先ほど作成した `myPublicIP` の IP アドレスを取得しています。

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Web ブラウザーを開き、アドレス バーに「`https://<myPublicIP>`」と入力できるようになりました。 自己署名証明書を使用した場合、セキュリティ警告を受け入れるには、そのまま **[詳細]** を選択し **Web ページに進みます**。

![Web ブラウザーのセキュリティ警告を受け入れる](./media/tutorial-secure-web-server/browser-warning.png)

セキュリティで保護された IIS Web サイトは、次の例のように表示されます。

![セキュリティで保護された実行中の IIS サイトの表示](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Key Vault に格納されている SSL 証明書を使用して IIS Web サーバーをセキュリティ保護しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure Key Vault を作成する
> * 証明書を生成したり、Key Vault にアップロードしたりする
> * VM の作成と IIS Web サーバーのインストール
> * VM への証明書の取り込みと IIS のSSL バインドの構成

次のリンクをクリックして、あらかじめ用意されている仮想マシン スクリプト サンプルをご覧ください。

> [!div class="nextstepaction"]
> [Windows 仮想マシンのスクリプト サンプル](./powershell-samples.md)
