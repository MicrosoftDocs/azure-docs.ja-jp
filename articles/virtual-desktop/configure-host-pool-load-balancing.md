---
title: Windows Virtual Desktop の負荷分散を構成する - Azure
description: Windows Virtual Desktop 環境での負荷分散方法を構成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 07eae73a36bf4051925547fa375f46963a162881
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010108"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Windows Virtual Desktop の負荷分散方法を構成する

ホスト プールの負荷分散方法を構成することで、Windows Virtual Desktop 環境をニーズに合うように調整できます。

>[!NOTE]
> これは、ユーザーにホスト プール内のセッション ホストに対して常に 1 対 1 のマッピングがあるため、永続的なデスクトップ ホスト プールには適用されません。

## <a name="prerequisites"></a>前提条件

この記事では、[Windows Virtual Desktop PowerShell モジュールの設定](powershell-module.md)に関するページの手順に従って PowerShell モジュールをダウンロードしてインストールし、Azure アカウントにサインインしていることを前提としています。

## <a name="configure-breadth-first-load-balancing"></a>幅優先の負荷分散を構成する

幅優先の負荷分散は、新しい非永続的ホスト プール用の既定の構成です。 幅優先の負荷分散では、新しいユーザー セッションがホスト プール内のすべての使用可能なセッション ホストに分散されます。 幅優先の負荷分散を構成するときに、ホスト プール内のセッション ホストあたりのセッションの上限を設定できます。

セッションの上限の調整なしで幅優先の負荷分散を実行するようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

その後、幅優先の負荷分散方法を設定したことを確認するには、次のコマンドレットを実行します。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

幅優先の負荷分散を実行し、新しいセッションの上限を使用するようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>深さ優先の負荷分散を構成する

深さ優先の負荷分散では、新しいユーザー セッションが接続数が最も多いが、そのセッションの上限しきい値に達していない利用可能なセッション ホストに分散されます。 深さ優先の負荷分散を構成するときに、ホスト プール内のセッション ホストあたりのセッションの上限を設定する必要があります。

深さ優先の負荷分散を実行するようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

設定が更新されたことを確認するには、次のコマンドレットを実行します。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Azure portal を使用して負荷分散を構成する

また、Azure portal を使用して、負荷分散を構成することもできます。

負荷分散を構成するには:

1. Azure portal (https://portal.azure.com) にサインインします。
2. [サービス] 下で **[Windows Virtual Desktop]** を探して選択します。
3. Windows Virtual Desktop のページで、 **[ホスト プール]** を選択します。
4. 編集するホスト プールの名前を選択します。
5. **[プロパティ]** を選択します。
6. **セッション上限**をフィールドに入力して、ドロップダウン メニューでこのホスト プールに使用する**負荷分散アルゴリズム**を選択します。
7. **[保存]** を選択します。 これにより、新しい負荷分散の設定が適用されます。