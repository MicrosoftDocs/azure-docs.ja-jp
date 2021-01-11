---
title: Windows Virtual Desktop (クラシック) ユーザー用のフィードをカスタマイズする - Azure
description: PowerShell コマンドレットを使用して Windows Virtual Desktop (クラシック) ユーザー用のフィードをカスタマイズする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a8c1a5da4f4d46d98e01eeb0bf210567af19a95
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007235"
---
# <a name="customize-feed-for-windows-virtual-desktop-classic-users"></a>Windows Virtual Desktop (クラシック) ユーザー用のフィードをカスタマイズする

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../customize-feed-for-virtual-desktop-users.md)を参照してください。

フィードは、RemoteApp とリモート デスクトップのリソースがユーザーの認識可能な方法で表示されるようにカスタマイズすることができます。

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します。 その後、次のコマンドレットを実行して、ご自分のアカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp の表示名をカスタマイズする

公開された RemoteApp の表示名は、フレンドリ名を設定することで変更できます。 既定では、フレンドリ名は RemoteApp プログラムの名前と同じです。

アプリ グループの公開された Remoteapp の一覧を取得するには、次の PowerShell コマンドレットを実行します。

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Name と FriendlyName が強調表示された PowerShell コマンドレット Get-RDSRemoteApp のスクリーンショット。](../media/get-rdsremoteapp.png)

RemoteApp にフレンドリ名を割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Name と New FriendlyName が強調表示された PowerShell コマンドレット Set-RDSRemoteApp のスクリーンショット。](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>リモート デスクトップの表示名をカスタマイズする

公開されたリモート デスクトップの表示名は、フレンドリ名を設定することで変更できます。 ホスト プールとデスクトップ アプリ グループを、PowerShell を使用して手動で作成した場合、既定のフレンドリ名は「セッション デスクトップ」です。 ホスト プールとデスクトップ アプリ グループを、GitHub の Azure Resource Manager テンプレートまたは Azure Marketplace のサービスを使用して作成した場合、既定のフレンドリ名はホスト プール名と同じです。

リモート デスクトップのリソースを取得するには、次の PowerShell コマンドレットを実行します。

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Name と FriendlyName が強調表示された PowerShell コマンドレット Get-RDSRemoteApp のスクリーンショット。](../media/get-rdsremotedesktop.png)

リモート デスクトップのリソースにフレンドリ名を割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Name と New FriendlyName が強調表示された PowerShell コマンドレット Set-RDSRemoteApp のスクリーンショット。](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>次のステップ

これでフィードをユーザーに合わせてカスタマイズしたので、Windows Virtual Desktop クライアントにサインインして、それを試すことができます。これを行うには、Windows Virtual Desktop への接続方法に関するページに進みます。

 * [Windows 10 または Windows 7 から接続する](connect-windows-7-10-2019.md)
 * [Web ブラウザーから接続する](connect-web-2019.md)
