---
title: Azure AD ロールベースのアクセス制御にカスタム ロール定義を作成する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory リソースにリソース スコープを使用してカスタム Azure AD ロールを作成します。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1166839608c709db9aa052d6d0db5221fa15354
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880747"
---
# <a name="create-a-custom-role-and-assign-at-resource-scope-in-azure-active-directory"></a>カスタム ロールを作成し、Azure Active Directory のリソース スコープに割り当てます。

この記事では、Azure Active Directory (Azure AD) で新しいカスタム ロールを作成する方法について説明します。 カスタム ロールは、Azure AD [概要] ページの [[ロールと管理者]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) タブで作成できます。 ロールを割り当てることができるのは、ディレクトリ レベルのスコープまたはアプリ登録リソースのスコープだけです。

詳しくは、カスタム ロールの基本事項について[カスタム ロールの概要](roles-custom-overview.md)に関するページをご覧ください。

## <a name="using-the-azure-ad-portal"></a>Azure AD ポータルを使用する

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>新しいカスタム ロールを作成してアプリ登録を管理するためのアクセス権を付与する

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して  [Azure AD 管理センター](https://aad.portal.azure.com) にサインインします。
1.  **[Azure Active Directory]**  >  **[ロールと管理者]**  > **[新しいカスタム ロール]** を選択します。

   ![[ロールと管理者] ページでロールを作成または編集する](./media/roles-create-custom/new-custom-role.png)

1. **[基本]** タブにロールの名前と説明を指定し、 **[次へ]** をクリックします。

   ![[基本] タブでカスタム ロールの名前と説明を指定する](./media/roles-create-custom/basics-tab.png)

1. **[アクセス許可]** タブで、アプリ登録の基本的なプロパティと資格情報のプロパティを管理するために必要な権限を選択します。 各アクセス許可について詳しくは、「[Azure Active Directory でのアプリケーション登録のサブタイプとアクセス許可](./roles-custom-available-permissions.md)」をご覧ください。
   1. 最初に、検索バーに「credentials」と入力し、`microsoft.directory/applications/credentials/update` アクセス許可を選択します。

      ![[アクセス許可] タブでカスタム ロールのアクセス許可を選択する](./media/roles-create-custom/permissions-tab.png)

   1. 次に、検索バーに「basic」と入力し、`microsoft.directory/applications/basic/update` アクセス許可を選択し、 **[次へ]** をクリックします。
1. **[確認と作成]** タブでアクセス許可を確認し、 **[作成]** を選択します。

カスタム ロールが、割り当て可能なロールの一覧に表示されます。

## <a name="assign-a-role-scoped-to-a-resource"></a>ロールをスコープ指定してリソースに割り当てる

組み込みのロールと同様に、カスタム ロールを組織全体のスコープで割り当てて、すべてのアプリ登録に対するアクセス権を付与することができます。 ただし、カスタム ロールはリソース スコープで割り当てることもできます。 これにより、2 つ目のカスタムロールを作成せずに、1 つのアプリの資格情報と基本プロパティを更新できるアクセス許可を割り当て先に付与できます。

1. まだサインインしていない場合は、Azure AD 組織のアプリケーション開発者のアクセス許可を使用して  [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[アプリの登録]** を選択します。
1. 付与しようとするアクセス権によって管理されるアプリ登録を選択します。 Azure AD 組織内のアプリ登録の完全な一覧を表示するには、 **[すべてのアプリケーション]** を選択する必要がある場合があります。

    ![ロールの割り当てのリソース スコープとしてアプリ登録を選択する](./media/roles-create-custom/appreg-all-apps.png)

1. [アプリの登録] で、 **[ロールと管理者]** を選択します。 まだ 1 つも作成していない場合は、[前の手順](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)の指示を参照してください。

1. ロールを選択して、 **[割り当て]** ページを開きます。
1. **[割り当ての追加]** を選択してユーザーを追加します。 このユーザーには、選択したアプリ登録以外のアプリ登録に対するアクセス許可は付与されません。

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell を使用してカスタム ロールを作成する

### <a name="prepare-powershell"></a>PowerShell を準備する

最初に、[Azure AD Preview PowerShell モジュールをダウンロード](https://www.powershellgallery.com/packages/AzureADPreview)する必要があります。

Azure AD PowerShell モジュールをインストールするには、次のコマンドを使用します。

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

モジュールを使用する準備ができているかどうかを確認するには、次のコマンドを使用します。

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>カスタム ロールを作成する

次の PowerShell スクリプトを実行して、新しいロールを作成します。

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell を使用してカスタム ロールを割り当てる

次の PowerShell スクリプトを使用して、ロールを割り当てます。

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>Microsoft Graph API を使用してカスタム ロールを作成する

1. ロール定義を作成します。

    カスタム ロール定義を作成するための HTTP 要求。

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. ロールの割り当てを作成します。

    カスタム ロール定義を作成するための HTTP 要求。

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Body

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="next-steps"></a>次の手順

- [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
- ロールと管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](directory-assign-admin-roles.md)に関するページを参照してください。
- 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
