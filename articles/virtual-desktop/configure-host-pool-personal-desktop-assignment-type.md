---
title: Windows Virtual Desktop の個人用デスクトップの割り当ての種類 - Azure
description: Windows Virtual Desktop の個人用デスクトップ ホスト プールの自動割り当てまたは直接割り当てを構成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 446dae3c064f5f23d35cb12b2b24bdfea9e27012
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88007813"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>個人用デスクトップ ホスト プールの割り当ての種類を構成する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop (classic) を使用している場合は、[この記事](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md)を参照してください。

ニーズに合わせて、個人用デスクトップ ホスト プールの割り当ての種類を構成して Windows Virtual Desktop 環境を調整することができます。 このトピックでは、ユーザーの自動割り当てまたは直接割り当てを構成する方法について説明します。

>[!NOTE]
> この記事の手順は、プールされたホスト プールではなく個人用デスクトップ ホスト プールにのみ適用されます。これは、プールされたホスト プール内のユーザーは特定のセッション ホストに割り当てられないためです。

## <a name="prerequisites"></a>前提条件

この記事では、Windows Virtual Desktop PowerShell モジュールを既にダウンロードしてインストールしていることを前提としています。 まだ行っていない場合は、[PowerShell モジュールのセットアップ](powershell-module.md)に関する記事の手順に従ってください。

## <a name="configure-automatic-assignment"></a>自動割り当てを構成する

自動割り当ては、Windows Virtual Desktop 環境で作成された新しい個人用デスクトップ ホスト プールの既定の割り当ての種類です。 ユーザーを自動的に割り当てる場合、特定のセッション ホストは必要ありません。

ユーザーを自動的に割り当てるには、まずユーザーを個人用デスクトップ ホスト プールに割り当てて、ユーザーが自分のフィードでデスクトップを表示できるようにします。 割り当てられたユーザーが自分のフィードでデスクトップを起動すると、ホスト プールにまだ接続していない場合は、使用可能なセッション ホストが要求されます。これにより、割り当てプロセスが完了します。

ユーザーを VM に自動的に割り当てるようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

ユーザーを個人用デスクトップ ホスト プールに割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>直接割り当てを構成する

自動割り当てとは異なり、直接割り当てを使用する場合、ユーザーが個人用デスクトップに接続するには、ユーザーを個人用デスクトップ ホストプールと特定のセッション ホストの両方に割り当てる必要があります。 ユーザーがセッション ホストの割り当てなしでホスト プールにのみ割り当てられている場合、ユーザーはリソースにアクセスできません。

ユーザーをセッション ホストに直接割り当てる必要があるようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

ユーザーを個人用デスクトップ ホスト プールに割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

特定のセッション ホストにユーザーを割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

ユーザーを Azure portal のセッション ホストに直接割り当てるには、次の手順を実行します。

1. Azure Portal ( <https://portal.azure.com> ) にサインインします。
2. 検索バーに「**Windows Virtual Desktop**」と入力します。
3. **[サービス]** の下で **[Windows Virtual Desktop]** を選択します。
4. [Windows Virtual Desktop] ページで、ウィンドウの左側にあるメニューの **[ホスト プール]** を選択します。
5. 更新するホスト プールの名前を選択します。
6. 次に、ウィンドウの左側にあるメニューに移動し、 **[アプリケーション グループ]** を選択します。
7. 編集するデスクトップ アプリ グループの名前を選択し、ウィンドウの左側にあるメニューの **[割り当て]** を選択します。
8. **[+ 追加]** を選択し、このデスクトップ アプリ グループの公開先となるユーザーまたはユーザー グループを選択します。
9. **[Assign VM]\(VM の割り当て\)** を選択して、セッション ホストをユーザーに割り当てます。
10. ユーザーに割り当てるセッション ホストを選択し、 **[割り当てる]** を選択します。
11. 選択可能なユーザーの一覧から、セッション ホストを割り当てるユーザーを選択します。
12. 終了したら、 **[選択]** を選択します。

## <a name="next-steps"></a>次のステップ

個人用デスクトップ割り当ての種類を構成したので、次は Windows Virtual Desktop クライアントにサインインしてユーザー セッションの一部としてテストすることができます。 次の 2 つの手順では、任意のクライアントを使用してセッションに接続する方法を説明します。

- [Windows デスクトップ クライアントを使用して接続する](connect-windows-7-10.md)
- [Web クライアントに接続する](connect-web.md)
- [Android クライアントに接続する](connect-android.md)
- [iOS クライアントに接続する](connect-ios.md)
- [macOS クライアントに接続する](connect-macos.md)