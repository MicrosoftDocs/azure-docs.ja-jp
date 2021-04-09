---
title: Windows Virtual Desktop (クラシック) の個人用デスクトップの割り当ての種類 - Azure
description: Windows Virtual Desktop (クラシック) の個人用デスクトップ ホスト プールの割り当ての種類を構成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 669010d2de90498c98fc685fe931b084a11cd104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008510"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type-for-windows-virtual-desktop-classic"></a>Windows Virtual Desktop (クラシック) の個人用デスクトップ ホスト プールの割り当ての種類を構成する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../configure-host-pool-personal-desktop-assignment-type.md)を参照してください。

ニーズに合わせて、個人用デスクトップ ホスト プールの割り当ての種類を構成して Windows Virtual Desktop 環境を調整することができます。 このトピックでは、ユーザーの自動割り当てまたは直接割り当てを構成する方法について説明します。

>[!NOTE]
> この記事の手順は、プールされたホスト プールではなく個人用デスクトップ ホスト プールにのみ適用されます。これは、プールされたホスト プール内のユーザーは特定のセッション ホストに割り当てられないためです。

## <a name="configure-automatic-assignment"></a>自動割り当てを構成する

自動割り当ては、Windows Virtual Desktop 環境で作成された新しい個人用デスクトップ ホスト プールの既定の割り当ての種類です。 ユーザーを自動的に割り当てる場合、特定のセッション ホストは必要ありません。

ユーザーを自動的に割り当てるには、まずユーザーを個人用デスクトップ ホスト プールに割り当てて、ユーザーが自分のフィードでデスクトップを表示できるようにします。 割り当てられたユーザーが自分のフィードでデスクトップを起動すると、ホスト プールにまだ接続していない場合は、使用可能なセッション ホストが要求されます。これにより、割り当てプロセスが完了します。

作業を開始する前に、[Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します (まだの場合のみ)。

> [!NOTE]
> これらの手順を実行する前に、Windows Virtual Desktop PowerShell モジュール バージョン 1.0.1534.2001 以降がインストールされていることを確認してください。

その後、次のコマンドレットを実行して、ご自分のアカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

ユーザーを VM に自動的に割り当てるようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

ユーザーを個人用デスクトップ ホスト プールに割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>直接割り当てを構成する

自動割り当てとは異なり、直接割り当てを使用する場合、ユーザーが個人用デスクトップに接続するには、ユーザーを個人用デスクトップ ホストプールと特定のセッション ホストの両方に割り当てる必要があります。 ユーザーがセッション ホストの割り当てなしでホスト プールにのみ割り当てられている場合、ユーザーはリソースにアクセスできません。

ユーザーをセッション ホストに直接割り当てる必要があるようにホスト プールを構成するには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

ユーザーを個人用デスクトップ ホスト プールに割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

特定のセッション ホストにユーザーを割り当てるには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>ユーザー割り当てを削除する

ユーザーが個人用デスクトップを必要としなくなった場合や、ユーザーが退職した場合、または他のユーザーのためにデスクトップを再利用したい場合、ユーザー割り当てを削除することができます。

現在、セッション ホストを完全に削除するのが、個人用デスクトップのユーザー割り当てを削除する唯一の方法となります。 セッション ホストを削除するには、次のコマンドレットを実行します。

```powershell
Remove-RdsSessionHost
```

そのセッション ホストを再び個人用デスクトップのホスト プールに追加する必要が生じた場合は、そのマシンから Windows Virtual Desktop をアンインストールしてから、「[PowerShell を使用してホスト プールを作成する](create-host-pools-powershell-2019.md)」の手順に従ってセッション ホストを再登録します。

## <a name="next-steps"></a>次のステップ

個人用デスクトップ割り当ての種類を構成したので、次は Windows Virtual Desktop クライアントにサインインしてユーザー セッションの一部としてテストすることができます。 次の 2 つの手順では、任意のクライアントを使用してセッションに接続する方法を説明します。

- [Windows デスクトップ クライアントを使用して接続する](connect-windows-7-10-2019.md)
- [Web クライアントに接続する](connect-web-2019.md)
