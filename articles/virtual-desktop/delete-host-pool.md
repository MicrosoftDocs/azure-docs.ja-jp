---
title: Windows Virtual Desktop のホスト プールを削除する - Azure
description: Windows Virtual Desktop でホスト プールを削除する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfc9858bea468389d8ce90677f048e5d1fd3bb82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88007592"
---
# <a name="delete-a-host-pool"></a>ホスト プールを削除する

Windows Virtual Desktop で作成されたホスト プールはすべて、セッション ホストとアプリケーション グループに接続されます。 ホスト プールを削除するには、それに関連付けられているアプリケーション グループとセッション ホストを削除する必要があります。 アプリケーション グループの削除は非常に単純ですが、セッション ホストの削除は複雑です。 セッション ホストを削除する場合は、アクティブなユーザー セッションがないことを確認する必要があります。 ユーザーがデータを失うことを回避するには、セッション ホスト上のユーザー セッションをすべてログオフする必要があります。

## <a name="delete-a-host-pool-with-powershell"></a>PowerShell を使用してホスト プールを削除する

PowerShell を使用してホスト プールを削除するには、最初にホスト プール内のアプリケーション グループをすべて削除する必要があります。 アプリケーション グループをすべて削除するには、次の PowerShell コマンドレットを実行します。

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

次に、このコマンドレットを実行して、ホスト プールを削除します。

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

このコマンドレットでは、ホスト プールのセッション ホスト上にある既存のユーザー セッションがすべて削除されます。 また、ホスト プールからセッション ホストの登録が解除されます。 関連する仮想マシン (VM) は引き続き、サブスクリプション内に存在します。

## <a name="delete-a-host-pool-with-the-azure-portal"></a>Azure portal を使用してホスト プールを削除する

Azure portal を使用してホスト プールを削除する方法:

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[Windows Virtual Desktop]** を検索して選択します。

3. ページの左側にあるメニューで **[ホスト プール]** を選択し、削除するホスト プールの名前を選択します。

4. ページの左側にあるメニューで、 **[アプリケーション グループ]** を選択します。

5. 削除するホスト プール内のアプリケーション グループをすべて選択し、 **[削除]** を選択します。

6. アプリケーション グループを削除したら、ページの左側にあるメニューに移動し、 **[概要]** を選択します。

7. **[削除]** を選択します。

8. 削除するホスト プール内にセッション ホストがある場合は、続行するためのアクセス許可を求めるメッセージが表示されます。 **[はい]** を選択します。

9. Azure portal からすべてのセッション ホストが削除され、ホスト プールが削除されます。 セッション ホストに関連する VM は削除されず、サブスクリプション内に残ります。

## <a name="next-steps"></a>次の手順

ホスト プールを作成する方法については、次の記事を参照してください。

- [Azure portal を使用してホスト プールを作成する](create-host-pools-azure-marketplace.md)
- [PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)

ホスト プールを構成する方法については、次の記事を参照してください。

- [ホスト プールのリモート デスクトップ プロトコル プロパティをカスタマイズする](customize-rdp-properties.md)
- [Windows Virtual Desktop の負荷分散方法を構成する](configure-host-pool-load-balancing.md)
- [個人用デスクトップ ホスト プールの割り当ての種類を構成する](configure-host-pool-personal-desktop-assignment-type.md)