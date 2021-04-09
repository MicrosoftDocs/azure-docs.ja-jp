---
title: Windows Virtual Desktop (classic) における委任されたアクセス - Azure
description: Windows Virtual Desktop (classic) のデプロイで管理機能を委任する方法 (例を含む)。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0152dc5102fd3f77418448234cab1234d25b97c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008153"
---
# <a name="delegated-access-in-windows-virtual-desktop-classic"></a>Windows Virtual Desktop (classic) における委任されたアクセス

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトがサポートされていない Windows Virtual Desktop (classic) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../delegated-access-virtual-desktop.md)を参照してください。

Windows Virtual Desktop には委任されたアクセス モデルが用意され、これにより特定のユーザーにロールを割り当てることで、それらのユーザーが持つことのできるアクセス数を定義できます。 ロールの割り当てには、セキュリティ プリンシパル、ロールの定義、スコープの 3 つのコンポーネントがあります。 Windows Virtual Desktop の委任されたアクセス モデルは、Azure RBAC モデルに基づいています。 特定のロールの割り当てとそのコンポーネントの詳細については、「[Azure ロールベースのアクセス制御の概要](../../role-based-access-control/built-in-roles.md)」を参照してください。

Windows Virtual Desktop の委任されたアクセスでは、ロール割り当ての要素ごとに次の値がサポートされています。

* セキュリティ プリンシパル
    * ユーザー
    * サービス プリンシパル
* ロール定義
    * 組み込みのロール
* Scope
    * テナント グループ
    * テナント
    * ホスト プール
    * アプリ グループ

## <a name="built-in-roles"></a>組み込みのロール

Windows Virtual Desktop の委任されたアクセスには、ユーザーおよびサービス プリンシパルに割り当てることのできるいくつかの組み込みロールの定義があります。

* RDS 所有者は、リソースへのアクセスを含め、すべてを管理できます。
* RDS 共同作成者は、すべてを管理できますが、リソースにはアクセスできません。
* RDS 閲覧者は、すべてを閲覧できますが、変更を加えることはできません。
* RDS オペレーターは、診断アクティビティを表示できます。

## <a name="powershell-cmdlets-for-role-assignments"></a>ロール割り当てを目的とした PowerShell のコマンドレット

次のコマンドレットを実行して、ロールの割り当てを作成、表示、削除できます。

* **Get-RdsRoleAssignment** はロール割り当ての一覧を表示します。
* **New-RdsRoleAssignment** は新しいロール割り当てを作成します。
* **Remove-RdsRoleAssignment** はロールの割り当てを削除します。

### <a name="accepted-parameters"></a>使用できるパラメーター

基本的な 3 つのコマンドレットを次のパラメーターで変更できます。

* **AadTenantId**: サービス プリンシパルがメンバーとなる Azure Active Directory テナント ID を指定します。
* **AppGroupName**: リモート デスクトップのアプリ グループの名前です。
* **Diagnostics**: 診断のスコープを示します (**Infrastructure** または **Tenant** パラメーターのいずれかと組み合わせる必要があります)。
* **HostPoolName**: リモート デスクトップのホスト プールの名前です。
* **Infrastructure**: インフラストラクチャのスコープを示します。
* **RoleDefinitionName**: ユーザー、グループ、またはアプリに割り当てられているリモート デスクトップ サービスのロールベース アクセス制御のロールの名前 (リモート デスクトップ サービスの所有者、リモート デスクトップ サービスの閲覧者など)。
* **ServerPrincipleName**: Azure Active Directory アプリケーションの名前。
* **SignInName**: ユーザーの電子メール アドレスまたはユーザー プリンシパル名。
* **TenantName**: リモート デスクトップ テナントの名前。

## <a name="next-steps"></a>次のステップ

各ロールで使用できる PowerShell コマンドレットのより詳細な一覧については、「[PowerShell リファレンス](/powershell/windows-virtual-desktop/overview)」を参照してください。

Windows Virtual Desktop 環境を設定する方法のガイドについては、「[Windows Virtual Desktop 環境](environment-setup-2019.md)」を参照してください。
