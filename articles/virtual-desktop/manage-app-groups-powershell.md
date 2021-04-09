---
title: Windows Virtual Desktop のアプリ グループを管理する PowerShell - Azure
description: PowerShell を使用して Windows Virtual Desktop アプリ グループを管理する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a6f24dea00a174aa0276a9b30add0854c3694056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008646"
---
# <a name="manage-app-groups-using-powershell"></a>PowerShell を使用してアプリ グループを管理する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを使用しない Windows Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/manage-app-groups-2019.md)を参照してください。

Windows Virtual Desktop の新しいホスト プール向けに作成される既定のアプリ グループには、完全なデスクトップも公開されています。 加えて、ホスト プールには RemoteApp アプリケーション グループ (複数可) を作成することができます。 このチュートリアルに沿って作業すれば、RemoteApp アプリ グループを作成して、独自の **[スタート]** メニュー アプリを公開することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * RemoteApp グループを作成する。
> * RemoteApp プログラムへのアクセスを許可する。

## <a name="prerequisites"></a>前提条件

この記事では、[PowerShell モジュールの設定](powershell-module.md)に関する記事に記載されている手順に従って PowerShell モジュールを設定し、Azure アカウントにサインインしていることを前提としています。

## <a name="create-a-remoteapp-group"></a>RemoteApp グループを作成する

PowerShell を使用して RemoteApp グループを作成するには:

1. 次の PowerShell コマンドレットを実行して、新しい空の RemoteApp アプリ グループを作成します。

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (省略可) アプリ グループが作成されたことを確認したければ、次のコマンドレットを実行すると、ホスト プールのすべてのアプリ グループが一覧表示されます。

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. 次のコマンドレットを実行して、ホスト プールの仮想マシン イメージにある **[スタート]** メニュー アプリの一覧を取得します。 **FilePath**、**IconPath**、**IconIndex** など、公開したいアプリケーションの重要な情報の値を書き留めます。

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   出力には、すべての [スタート] メニュー項目が次のような形式で表示されます。

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. 次のコマンドレットを実行して、`AppAlias` に基づくアプリケーションをインストールします。 `AppAlias` は、手順 3. の出力を実行すると利用できるようになります。

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. (省略可) 次のコマンドレットを実行して、手順 1. で作成したアプリケーション グループに新しい RemoteApp プログラムを発行します。

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. アプリが公開されたことを確認するために、次のコマンドレットを実行します。

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. このアプリ グループに発行するアプリケーションごとに手順 1. から手順 5. を繰り返します。
8. 次のコマンドレットを実行して、アプリ グループ内の RemoteApp プログラムへのアクセスをユーザーに許可します。

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>次のステップ

Microsoft のチュートリアルからこのハウツー ガイドに移動してきた場合は、[サービスの更新プログラムを検証するためのホスト プールの作成](create-validation-host-pool.md)に関する記事を参照してください。 運用環境に展開する前に、検証ホスト プールを使用してサービスの更新プログラムを監視できます。