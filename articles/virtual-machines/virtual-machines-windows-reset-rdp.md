---
title: "Windows VM でパスワードまたはリモート デスクトップの構成をリセットする | Microsoft Docs"
description: "Azure Portal または Azure PowerShell を使用して、Windows VM でアカウントのパスワードまたはリモート デスクトップ サービスをリセットする方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 2673c95d2e312d427454585d46ac790cb126fea6
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Windows VM でリモート デスクトップ サービスまたはそのログイン パスワードをリセットする方法
Windows 仮想マシン (VM) に接続できない場合、ローカル管理者パスワードをリセットすることも、リモート デスクトップ サービスの構成をリセットすることもできます。 Azure ポータルまたは Azure PowerShell で VM アクセス拡張機能を使用して、パスワードをリセットできます。 PowerShell を使用する場合は、[最新の PowerShell モジュールのインストールと構成](/powershell/azureps-cmdlets-docs)が完了しており、Azure サブスクリプションにサインインしていることを確認します。 また、[クラシック デプロイ モデルを使用して作成された VM でこれらの手順を実行](windows/classic/reset-rdp.md)することもできます。

## <a name="ways-to-reset-configuration-or-credentials"></a>構成または資格情報をリセットする方法
リモート デスクトップ サービスと資格情報は、ニーズに応じてさまざまな方法でリセットできます。

- [Azure Portal を使用したリセット](#azure-portal)
- [Azure PowerShell を使用したリセット](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure ポータル
ポータル メニューを展開するには、左上隅にある 3 本の棒をクリックして、**[仮想マシン]** をクリックします。

![Azure VM を参照する](./media/virtual-machines-windows-reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**ローカル管理者アカウント パスワードのリセット**

Windows 仮想マシンを選び、**[サポート + トラブルシューティング]** > **[パスワードのリセット]** をクリックします。 パスワード リセット ブレードが表示されます。

![パスワード リセット ページ](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

ユーザー名と新しいパスワードを入力し、**[更新]** をクリックします。 VM への接続を再度試してみます。

> [!NOTE] 
> - パスワードを変更してポータルで操作を完了してから、VM でこの変更が有効になるまで 3 ～ 5 分かかる場合があります。 変更が有効にならない場合は VM を再起動します。
> - VMAccess 拡張機能は組み込みのローカル管理者アカウントに対してのみ動作し、その他のローカル ID やドメイン ID では動作しません。
> - 対象となるコンピューターがドメイン コントローラーの場合は、拡張機能によってドメイン管理者アカウントがリセットされたりアカウント名が変更されます。


### <a name="reset-the-remote-desktop-service-configuration"></a>**リモート デスクトップ サービスの構成のリセット**

Windows 仮想マシンを選び、**[サポート + トラブルシューティング]** > **[パスワードのリセット]** をクリックします。 パスワード リセット ブレードが次のように表示されます。

![RDP 構成のリセット](./media/virtual-machines-windows-reset-rdp/Portal-RM-RDP-Reset.png)

ドロップダウン メニューから **[構成のみのリセット]** を選択し、**[更新]** をクリックします。 VM への接続を再度試してみます。


## <a name="vmaccess-extension-and-powershell"></a>VMAccess 拡張機能と PowerShell
[最新の PowerShell モジュールのインストールと構成](/powershell/azureps-cmdlets-docs)が完了しており、`Login-AzureRmAccount` コマンドレットを使用して Azure サブスクリプションにサインインしていることを確認します。

### <a name="reset-the-local-administrator-account-password"></a>**ローカル管理者アカウント パスワードのリセット**
[Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell コマンドレットを使用して、管理者パスワードまたはユーザー名をリセットします。 次のようにアカウントの資格情報を作成します。

```powershell
$cred=Get-Credential
```

> [!NOTE] 
> VM で現在のローカル管理者アカウントと異なる名前を入力すると、VMAccess 拡張機能によってローカル管理者アカウントの名前が変更されて、指定したパスワードがそのアカウントに割り当てられ、リモート デスクトップのログオフ イベントが発行されます。 VM のローカル管理者アカウントが無効になっている場合は、VMAccess 拡張機能によって有効化されます。

次の例では、`myResourceGroup` という名前のリソース グループの `myVM` という名前の VM を指定した資格情報に更新します。

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

### <a name="reset-the-remote-desktop-service-configuration"></a>**リモート デスクトップ サービスの構成のリセット**
[Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell コマンドレットを使用して、VM へのリモート アクセスをリセットします。 次の例では、`myResourceGroup` リソース グループの `myVM` という名前の VM で、`myVMAccess` という名前のアクセス拡張機能をリセットします。

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0 -ForceRerun
```

> [!TIP]
> 同時に VM に存在する VM アクセス エージェント数は 1 つのみです。 VM アクセス エージェントのプロパティを問題なく設定するには、`-ForceRerun` オプションを使用します。 `-ForceRerun` を使用するときは、以前のコマンドで使用した同じ名前を VM アクセス エージェントに使用する必要があります。

それでも仮想マシンにリモート接続できない場合は、 [Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページの手順をご覧ください。


## <a name="next-steps"></a>次のステップ
Azure VM アクセス拡張機能が応答せず、パスワードをリセットできない場合は、オフラインの[ローカル Windows パスワードをリセット](virtual-machines-windows-reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)します。 この方法はより高度なプロセスであるため、問題のある VM の仮想ハード ディスクを別の VM に接続する必要があります。 最初に、この記事に記載されている手順を実行し、オフラインのパスワードをリセットする方法は最後の手段としてのみ実行してください。

[Azure VM 拡張機能とその機能](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[RDP または SSH を使用した Azure Virtual Machines への接続](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Windows ベースの Azure Virtual Machines へのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


