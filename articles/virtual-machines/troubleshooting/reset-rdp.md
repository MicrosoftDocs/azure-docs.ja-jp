---
title: Windows VM でリモート デスクトップ サービスまたはその管理者パスワードをリセットする | Microsoft Docs
description: Azure portal または Azure PowerShell を使用して、Windows VM でアカウント パスワードまたはリモート デスクトップ サービスをリセットする方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 580ec443dc087f270e30856c336a5699bbf1ae71
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058438"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Windows VM でリモート デスクトップ サービスまたはその管理者パスワードをリセットする
Windows 仮想マシン (VM) に接続できない場合は、ご自分のローカル管理者パスワードをリセットすることも、(Windows ドメイン コントローラーでサポートされていない) リモート デスクトップ サービスの構成をリセットすることもできます。 パスワードをリセットするには、Azure portal を使用するか、Azure PowerShell で VM アクセス拡張機能を使用します。 VM にサインインした後、そのローカル管理者のパスワードをリセットします。  
PowerShell を使用する場合は、[最新の PowerShell モジュールのインストールと構成](/powershell/azure/overview)が完了しており、Azure サブスクリプションにサインインしていることを確認します。 また、[クラシック デプロイ モデルを使用して作成された VM でこれらの手順を実行](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp)することもできます。

リモート デスクトップ サービスと資格情報は、以下の方法でリセットできます。

- [Azure portal を使用してリセットする](#reset-by-using-the-azure-portal)

- [VMAccess 拡張機能と PowerShell を使用してリセットする](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Azure portal を使用してリセットする

最初に [Azure portal](https://portal.azure.com) にサインインしてから、左側のメニューで **[仮想マシン]** を選択します。 

### <a name="reset-the-local-administrator-account-password"></a>**ローカル管理者アカウント パスワードのリセット**

1. Windows VM を選択して、 **[サポート + トラブルシューティング]** の **[パスワードのリセット]** を選択します。 **[パスワードのリセット]** ウィンドウが表示されます。

2. **[パスワードのリセット]** を選択し、ユーザー名とパスワードを入力して、 **[更新]** を選択します。 

3. VM への接続を再度試してみます。

### <a name="reset-the-remote-desktop-services-configuration"></a>**リモート デスクトップ サービスの構成のリセット**

このプロセスでは、VM 上でリモート デスクトップ サービスを有効にし、既定の RDP ポート 3389 のファイアウォール規則を作成します。

1. Windows VM を選択して、 **[サポート + トラブルシューティング]** の **[パスワードのリセット]** を選択します。 **[パスワードのリセット]** ウィンドウが表示されます。 

2. **[構成のみのリセット]** を選択して **[更新]** を選択します。 

3. VM への接続を再度試してみます。

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>VMAccess 拡張機能と PowerShell を使用してリセットする

まず、[最新の PowerShell モジュールのインストールと構成](/powershell/azure/overview)が完了しており、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure サブスクリプションにサインインしていることを確認します。

### <a name="reset-the-local-administrator-account-password"></a>**ローカル管理者アカウント パスワードのリセット**

- [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell コマンドレットを使用して、管理者パスワードまたはユーザー名をリセットします。 バージョン 1 は非推奨のため、`typeHandlerVersion` 設定は 2.0 以上である必要があります。 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > VM で現在のローカル管理者アカウントと異なる名前を入力すると、VMAccess 拡張機能によってローカル管理者アカウントの名前が追加されて、指定したパスワードがそのアカウントに割り当てられます。 VM のローカル管理者アカウントが存在する場合は、VMAccess 拡張機能によってパスワードがリセットされます。 アカウントが無効になっている場合は、VMAccess 拡張機能によって有効にされます。

### <a name="reset-the-remote-desktop-services-configuration"></a>**リモート デスクトップ サービスの構成のリセット**

1. [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell コマンドレットを使用して、VM へのリモート アクセスをリセットします。 次の例では、`myResourceGroup` リソース グループの `myVM` という名前の VM で、`myVMAccess` という名前のアクセス拡張機能をリセットします。

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > 同時に VM に存在する VM アクセス エージェント数は 1 つのみです。 VM アクセス エージェントのプロパティを設定するには、`-ForceRerun` オプションを使用します。 `-ForceRerun` を使用する際は、以前のコマンドで使用したのと同じ名前を VM アクセス エージェントに使用してください。

1. それでも仮想マシンにリモート接続できない場合は、[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。 Windows ドメイン コントローラーへの接続を失った場合は、ドメイン コントローラーのバックアップから復元する必要があります。

## <a name="next-steps"></a>次の手順

- Azure VM アクセス拡張機能が応答せず、パスワードをリセットできない場合は、[オフラインのローカル Windows パスワードをリセット](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)できます。 この方法はより高度であり、問題のある VM の仮想ハード ディスクを別の VM に接続する必要があります。 最初に、この記事に記載されている手順を実行し、これらの手順でうまくいかない場合にのみ、オフラインのパスワードをリセットする方法をお試しください。

- [Azure VM 拡張機能とその機能について学習します](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

- [RDP または SSH を使用して Azure Virtual Machines に接続します](https://msdn.microsoft.com/library/azure/dn535788.aspx)。

- [Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティングを行います](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

