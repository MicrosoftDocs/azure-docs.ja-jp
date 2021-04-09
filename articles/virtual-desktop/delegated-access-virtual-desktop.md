---
title: Windows Virtual Desktop における委任されたアクセス - Azure
description: Windows Virtual Desktop のデプロイで管理機能を委任する方法 (例を含む)。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2aa2c74704cf89c082d2837b39e82902efa0a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010057"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows Virtual Desktop における委任されたアクセス

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを使用しない Windows Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md)を参照してください。

Windows Virtual Desktop には委任されたアクセス モデルが用意され、これにより特定のユーザーにロールを割り当てることで、それらのユーザーが持つことのできるアクセス数を定義できます。 ロールの割り当てには、セキュリティ プリンシパル、ロールの定義、スコープの 3 つのコンポーネントがあります。 Windows Virtual Desktop の委任されたアクセス モデルは、Azure RBAC モデルに基づいています。 特定のロールの割り当てとそのコンポーネントの詳細については、「[Azure ロールベースのアクセス制御の概要](../role-based-access-control/built-in-roles.md)」を参照してください。

Windows Virtual Desktop の委任されたアクセスでは、ロール割り当ての要素ごとに次の値がサポートされています。

* セキュリティ プリンシパル
    * ユーザー
    * ユーザー グループ
    * サービス プリンシパル
* ロール定義
    * 組み込みのロール
    * カスタム ロール
* Scope
    * ホスト プール
    * アプリ グループ
    * Workspaces

## <a name="powershell-cmdlets-for-role-assignments"></a>ロール割り当てを目的とした PowerShell のコマンドレット

開始する前に、[PowerShell モジュールの設定](powershell-module.md)に関するページの手順に従って、Windows Virtual Desktop PowerShell モジュールを設定してください (まだ設定していない場合)。

Windows Virtual Desktop では、アプリ グループをユーザーまたはユーザー グループに公開するときに、Azure のロールベースのアクセス制御 (Azure RBAC) を使用します。 デスクトップ仮想化ユーザー ロールがユーザーまたはユーザー グループに割り当てられます。そのスコープはアプリ グループになります。 このロールは、アプリ グループに対する特別なデータ アクセスをユーザーに付与します。

次のコマンドレットを実行して、Azure Active Directory ユーザーをアプリ グループに追加します。

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

次のコマンドレットを実行して、Azure Active Directory ユーザー グループをアプリ グループに追加します。

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>次のステップ

各ロールで使用できる PowerShell コマンドレットのより詳細な一覧については、「[PowerShell リファレンス](/powershell/windows-virtual-desktop/overview)」を参照してください。

Azure RBAC でサポートされているロールの完全な一覧については、「[Azure 組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。

Windows Virtual Desktop 環境を設定する方法のガイドについては、「[Windows Virtual Desktop 環境](environment-setup.md)」を参照してください。
