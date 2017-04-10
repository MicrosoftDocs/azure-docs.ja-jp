---
title: "Azure の Windows VM でパスワードまたはリモート デスクトップの構成をリセットする | Microsoft Docs"
description: "Azure Portal または Azure PowerShell を使用して、クラシック デプロイ モデルで作成された Windows VM でアカウントのパスワードまたはリモート デスクトップ サービスをリセットする方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 62e131840970ed417b58d207db81e0c1f52a449b
ms.lasthandoff: 03/25/2017


---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>クラシック デプロイ モデルで作成された Windows VM でリモート デスクトップ サービスまたはそのログイン パスワードをリセットする方法
> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 また、[Resource Manager のデプロイ モデルを使用して作成された VM でこれらの手順を実行](../../virtual-machines-windows-reset-rdp.md)することもできます。

Windows 仮想マシン (VM) に接続できない場合、ローカル管理者パスワードをリセットすることも、リモート デスクトップ サービスの構成をリセットすることもできます。 Azure ポータルまたは Azure PowerShell で VM アクセス拡張機能を使用して、パスワードをリセットできます。

## <a name="ways-to-reset-configuration-or-credentials"></a>構成または資格情報をリセットする方法
リモート デスクトップ サービスと資格情報は、ニーズに応じてさまざまな方法でリセットできます。

- [Azure Portal を使用したリセット](#azure-portal)
- [Azure PowerShell を使用したリセット](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure ポータル
[Azure ポータル](https://portal.azure.com)を使用してリモート デスクトップ サービスをリセットします。 ポータル メニューを展開するには、左上隅にある 3 本の棒をクリックして、**[仮想マシン (クラシック)]** をクリックします。

![Azure VM を参照する](./media/reset-rdp/Portal-Select-Classic-VM.png)

Windows 仮想マシンを選び、**[Reset Remote... (リモートのリセット...)]** を選びます。 リモート デスクトップの構成をリセットするための次のダイアログが表示されます。

![RDP 構成のリセット ページ](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

ローカル管理者アカウントのユーザー名とパスワードをリセットすることもできます。 VM から、**[サポート + トラブルシューティング]** > **[パスワードのリセット]** をクリックします。 パスワード リセット ブレードが表示されます。

![パスワード リセット ページ](./media/reset-rdp/Portal-PW-Reset-Windows.png)

新しいユーザー名とパスワードを入力して、 **[保存]**をクリックします。

## <a name="vmaccess-extension-and-powershell"></a>VMAccess 拡張機能と PowerShell
仮想マシンに VM エージェントがインストールされていることを確認します。 VM エージェントを使用するには、VMAccess 拡張機能を使用前にインストールしておく必要があります。 次のコマンドを使用して VM エージェントがインストールされていることを確認します。 ("myCloudService" と "myVM" は、それぞれ実際のクラウド サービスの名前と VM に置き換えます。 これらの名前を確認するには、パラメーターを指定せずに `Get-AzureVM` を実行します)。

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

**write-host** コマンドで **True** が表示される場合は、VM エージェントがインストールされています。 **False**が表示される場合は、Azure ブログの [VM エージェントと拡張機能に関する記事のパート 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) で手順とダウンロード用リンクをご確認ください。

ポータルで仮想マシンを作成した場合、 `$vm.GetInstance().ProvisionGuestAgent` を実行して **True**が返されるかどうかを確認します。 作成していない場合は、次のコマンドを使用して設定できます。

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

このコマンドは、次のステップで **Set-AzureVMExtension** コマンドを実行する場合、"IaaS VM アクセス拡張機能を設定する前に、VM オブジェクトでゲスト エージェントのプロビジョニングを有効にする必要がある" というエラーを防ぐことができます。

### <a name="reset-the-local-administrator-account-password"></a>**ローカル管理者アカウント パスワードのリセット**
現在のローカル管理者アカウント名と新しいパスワードでサインイン資格情報を作成してから、次のように `Set-AzureVMAccessExtension` を実行します。

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

現在のアカウントと異なる名前を入力すると、VMAccess 拡張機能によってローカル管理者アカウントの名前が変更されて、そのアカウントにパスワードが割り当てられます。さらに、リモート デスクトップのサインアウトが発行されます。 ローカル管理者アカウントが無効になっている場合は、VMAccess 拡張機能によって有効に設定されます。

これらのコマンドはリモート デスクトップ サービスの構成もリセットします。

### <a name="reset-the-remote-desktop-service-configuration"></a>**リモート デスクトップ サービスの構成のリセット**
リモート デスクトップ サービスの構成をリセットするには、次のコマンドを実行します。

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

VMAccess 拡張機能によって仮想マシンで次の 2 つのコマンドが実行されます。

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

このコマンドで、組み込みの Windows ファイアウォール グループが有効になり、TCP ポート 3389 を使用するリモート デスクトップの着信トラフィックが許可されます。

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

このコマンドは fDenyTSConnections レジストリ値を 0 に設定します。これでリモート デスクトップ接続が有効になります。

## <a name="next-steps"></a>次のステップ
Azure VM アクセス拡張機能が応答せず、パスワードをリセットできない場合は、オフラインの[ローカル Windows パスワードをリセット](../../virtual-machines-windows-reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)します。 この方法はより高度なプロセスであるため、問題のある VM の仮想ハード ディスクを別の VM に接続する必要があります。 最初に、この記事に記載されている手順を実行し、オフラインのパスワードをリセットする方法は最後の手段としてのみ実行してください。

[Azure VM 拡張機能とその機能](../../virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[RDP または SSH を使用した Azure Virtual Machines への接続](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Windows ベースの Azure Virtual Machines へのリモート デスクトップ接続に関するトラブルシューティング](../../virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


