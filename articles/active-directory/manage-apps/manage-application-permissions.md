---
title: ユーザーと管理者のアクセス許可を管理する - Azure Active Directory | Microsoft Docs
description: Azure AD でアプリケーションのアクセス許可を確認および管理する方法について説明します。 たとえば、アプリケーションに付与されているすべてのアクセス許可を取り消します。
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd2bc7b9ccc9efe0c6fdf8de02665160667cb8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256934"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Azure Active Directory で、過剰な特権が与えられているか、または疑わしいアプリケーションに対してアクションを実行する

アプリケーションのアクセス許可を確認および管理する方法について説明します。 この記事では、シナリオに従って、アプリケーションをセキュリティで保護するために実行できるさまざまなアクションについて説明します。 これらのアクションは、ユーザーまたは管理者の同意によって Azure Active Directory (Azure AD) テナントに追加されたすべてのアプリケーションに適用されます。

アプリケーションに同意する行為の詳細については、「[Azure Active Directory 同意フレームワーク](../develop/consent-framework.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

次のアクションを実行するには、グローバル管理者、アプリケーション管理者、またはクラウド アプリケーション管理者としてサインインする必要があります。

アプリケーションへのアクセスを制限するには、ユーザーの割り当てが必要で、その後、アプリケーションにユーザーまたはグループを割り当てる必要があります。  詳細については、[ユーザーとグループの割り当て方法](./assign-user-or-group-access-portal.md)に関するページを参照してください。

アクションを実行するためのコンテキスト PowerShell スクリプトを取得するには、Azure AD ポータルにアクセスします。
 
1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
3. アクセスを制限するアプリケーションを選択します。
4. **[アクセス許可]** を選択します。 コマンド バーで **[アクセス許可の確認]** を選択します。

![[アクセス許可の確認] ウィンドウのスクリーンショット。](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>アプリケーションへのアクセスを制御する

**[ユーザー割り当て]** の設定を有効にすることによってアプリケーションへのアクセスを制限することをお勧めします。

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
3. アクセスを制限するアプリケーションを選択します。
4. **[プロパティ]** を選択し、 **[User requirement required] (ユーザー要件が必須)** を **[はい]** に設定します。
5. **[ユーザーとグループ]** を選択し、アプリケーションに割り当てられている不要なユーザーを削除します。
6. アプリケーションにユーザーまたはグループを割り当てます。

必要に応じて、PowerShell を使用して、アプリケーションに割り当てられているすべてのユーザーを削除できます。

## <a name="revoke-all-permissions-for-an-application"></a>アプリケーションのすべてのアクセス許可を取り消す

PowerShell スクリプトを使用すると、このアプリケーションに付与されているすべてのアクセス許可が取り消されます。

> [!NOTE]
> 現在の付与されているアクセス許可を取り消しても、ユーザーのアプリケーションへの再同意は停止されません。 ユーザーの同意をブロックしたい場合は、[ユーザーがアプリケーションに同意する方法の構成](configure-user-consent.md)に関するページを参照してください。

必要に応じて、アプリケーションを無効にして、ユーザーがアプリにアクセスしたり、アプリケーションがデータにアクセスしたりできないようにすることができます。

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
3. アクセスを制限するアプリケーションを選択します。
4. **[プロパティ]** を選択し、 **[ユーザーのサインインが有効になっていますか?]** を **[いいえ]** に設定します。

## <a name="investigate-a-suspicious-application"></a>疑わしいアプリケーションを調査する

**[ユーザー割り当て]** の設定を有効にすることによってアプリケーションへのアクセスを制限することをお勧めします。 その後、ユーザーと管理者がアプリケーションに付与したアクセス許可を確認します。

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
3. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
5. アクセスを制限するアプリケーションを選択します。
6. **[プロパティ]** を選択し、 **[User requirement required] (ユーザー要件が必須)** を **[はい]** に設定します。
7. **[アクセス許可]** を選択し、管理者とユーザーによって同意されたアクセス許可を確認します。

必要に応じて、PowerShell を使用して、次のことを実行できます。

- 割り当てられているすべてのユーザーを削除して、アプリケーションにサインインできないようにします。
- アプリケーションにアクセスできるユーザーの更新トークンを無効にします。
- アプリケーションのすべてのアクセス許可を取り消します。

または、アプリケーションを無効にして、ユーザーのアクセスをブロックし、アプリケーションのデータへのアクセスを停止します。


## <a name="disable-a-malicious-application"></a>悪意のあるアプリケーションを無効にする 

アプリケーションを無効にして、ユーザーのアクセスをブロックし、アプリケーションがデータにアクセスできないようにすることをお勧めします。 代わりにアプリケーションを削除した場合、ユーザーはアプリケーションに再同意し、データへのアクセス権を付与できるようになります。

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
3. アクセスを制限するアプリケーションを選択します。
4. **[プロパティ]** を選択し、オブジェクト ID をコピーします。

### <a name="powershell-commands"></a>PowerShell コマンド


サービス プリンシパル オブジェクト ID を取得します。

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。
3. アクセスを制限するアプリケーションを選択します。
4. **[プロパティ]** を選択し、オブジェクト ID をコピーします。

```powershell
$sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
$sp.ObjectId
```
アプリケーションに割り当てられているすべてのユーザーを削除します。
 ```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectId of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

# Remove all users and groups assigned to the application
$assignments | ForEach-Object {
    if ($_.PrincipalType -eq "User") {
        Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    } elseif ($_.PrincipalType -eq "Group") {
        Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    }
}
 ```

アプリケーションに付与されているアクセス許可を取り消します。

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```
更新トークンを無効にします。
```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```
## <a name="next-steps"></a>次のステップ
- [アプリケーションの同意の管理と同意要求の評価](manage-consent-requests.md)
- [ユーザーの同意の構成](configure-user-consent.md)
- [管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)
