---
title: Windows Virtual Desktop の負荷分散を構成する - Azure
description: Windows Virtual Desktop 環境での負荷分散方法を構成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2c57ac10fbd318dd4bbb2dc86457e186dd824834
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91951657"
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

深さ優先の負荷分散では、新しいユーザー セッションが接続数が最も多いが、そのセッションの上限しきい値に達していない利用可能なセッション ホストに分散されます。

>[!IMPORTANT]
>深さ優先の負荷分散を構成するときに、ホスト プール内のセッション ホストあたりのセッションの上限を設定する必要があります。

深さ優先の負荷分散を実行するようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

>[!NOTE]
> 深さ優先の負荷分散アルゴリズムを使用すると、セッション ホストの上限 (`-MaxSessionLimit`) に基づいてセッションがセッション ホストに分散されます。 このパラメーターの既定値は `999999` です。これは、この変数に設定できる最大の数値でもあります。 このパラメーターは、深さ優先の負荷分散アルゴリズムを使用する場合に必要です。 最適なユーザー エクスペリエンスを実現するには、セッション ホストの上限パラメーターを実際の環境に最も適した数に変更してください。

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
6. **セッション上限** をフィールドに入力して、ドロップダウン メニューでこのホスト プールに使用する **負荷分散アルゴリズム** を選択します。
7. **[保存]** を選択します。 これにより、新しい負荷分散の設定が適用されます。