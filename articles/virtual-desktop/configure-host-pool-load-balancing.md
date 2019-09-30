---
title: Windows Virtual Desktop プレビューの負荷分散方法を構成する - Azure
description: Windows Virtual Desktop 環境での負荷分散方法を構成する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: e1f1ea10dc68e501cfac7ef0cf0383ce78e8f380
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163763"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>Windows Virtual Desktop プレビューの負荷分散方法を構成する

ホスト プールの負荷分散方法を構成することで、Windows Virtual Desktop プレビュー環境をニーズに合うように調整できます。

>[!NOTE]
> これは、ユーザーにホスト プール内のセッション ホストに対して常に 1 対 1 のマッピングがあるため、永続的なデスクトップ ホスト プールには適用されません。

## <a name="configure-breadth-first-load-balancing"></a>幅優先の負荷分散を構成する

幅優先の負荷分散は、新しい非永続的ホスト プール用の既定の構成です。 幅優先の負荷分散では、新しいユーザー セッションがホスト プール内のすべての使用可能なセッション ホストに分散されます。 幅優先の負荷分散を構成するときに、ホスト プール内のセッション ホストあたりのセッションの上限を設定できます。

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)します。 その後、次のコマンドレットを実行して、ご自分のアカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

セッションの上限の調整なしで幅優先の負荷分散を実行するようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

幅優先の負荷分散を実行し、新しいセッションの上限を使用するようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>深さ優先の負荷分散を構成する

深さ優先の負荷分散では、新しいユーザー セッションが接続数が最も多いが、そのセッションの上限しきい値に達していない利用可能なセッション ホストに分散されます。 深さ優先の負荷分散を構成するときに、ホスト プール内のセッション ホストあたりのセッションの上限を設定する**必要があります**。

深さ優先の負荷分散を実行するようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
