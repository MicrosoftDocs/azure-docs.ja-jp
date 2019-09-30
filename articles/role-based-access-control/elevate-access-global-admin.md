---
title: Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権を昇格させる | Microsoft Docs
description: Azure portal または REST API を使用し、Azure Active Directory ですべてのサブスクリプションと管理グループを管理する目的で、全体管理者のアクセス権を昇格させる方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 984284fa185d4d8454b1689a62ca9e08c342e33b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195108"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる

Azure Active Directory (Azure AD) の全体管理者には、自分のディレクトリにおいて、すべてのサブスクリプションと管理グループにアクセスする権限が与えられていない場合があります。 この記事では、すべてのサブスクリプションと管理グループにアクセスできるよう、権限を昇格させる方法について説明します。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>アクセス権を昇格が必要な理由

全体管理者であれば、次のような作業を行う場合が考えられます。

- ユーザーが Azure のサブスクリプションか管理グループにアクセスできなくなったとき、アクセス権を回復する
- 別のユーザーまたは自分に Azure のサブスクリプションまたは管理グループへのアクセス権を付与する
- 組織内にある Azure のすべてのサブスクリプションと管理グループを表示する
- オートメーション アプリ (請求書作成アプリや監査アプリなど) から Azure のすべてのサブスクリプションと管理グループへのアクセスを許可する

## <a name="how-does-elevate-access-work"></a>アクセス権の昇格のしくみ

Azure AD と Azure リソースは互いに依存することなくセキュリティで保護されます。 つまり、Azure AD のロール割り当てによって Azure リソースにアクセス権が付与されることはなく、Azure のロール割り当てによって Azure AD にアクセス権が付与されることはありません。 ただし、Azure AD の[全体管理者](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions)であれば、自分のディレクトリにあるすべての Azure サブスクリプションと管理グループに対するアクセス許可を自分に割り当てることができます。 仮想マシンやストレージ アカウントなど、Azure リソースへのアクセス許可が与えられていない場合、この機能を使用します。このようなリソースに対するアクセス権を得るには、全体管理者の特権を使用することをお勧めします。

アクセス権を昇格させると、Azure のルート範囲 (`/`) で[ユーザー アクセスの管理者](built-in-roles.md#user-access-administrator)ロールが割り当てられます。 これにより、すべてのリソースを表示したり、ディレクトリにあるあらゆるサブスクリプションまたは管理グループでアクセス権を割り当てたりできます。 ユーザー アクセス管理者ロールの割り当ては PowerShell を使用して削除できます。

昇格したこのアクセス権は、ルート範囲で必要な変更を行ったら削除してください。

![アクセス権を昇格する](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure ポータル

Azure portal を使用して全体管理者のアクセス権を昇格するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) または [Azure Active Directory 管理センター](https://aad.portal.azure.com)に全体管理者としてサインインします。

1. ナビゲーション リストで **[Azure Active Directory]** 、 **[プロパティ]** の順にクリックします。

   ![Azure AD のプロパティ - スクリーンショット](./media/elevate-access-global-admin/aad-properties.png)

1. **[Azure リソースのアクセス管理]** の下で **[はい]** に切り替えます。

   ![Azure リソースのアクセス管理 - スクリーンショット](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   **[はい]** に切り替えると、Azure RBAC のルート範囲 (/) でユーザー アクセス管理者ロールが割り当てられます。 この割り当てにより、この Azure AD ディレクトリに関連付けられているすべての Azure サブスクリプションと管理グループでロールを割り当てるアクセス許可が付与されます。 この切り替えは、Azure AD で全体管理者ロールが割り当てられたユーザーのみ利用できます。

   **[いいえ]** に切り替えると、Azure RBAC のユーザー アクセス管理者ロールがユーザー アカウントから削除されます。 この Azure AD ディレクトリに関連付けられているすべての Azure サブスクリプションと管理グループでロールを割り当てることができなくなります。 自分にアクセス権が割り当てられている Azure サブスクリプションと管理グループのみを表示し、管理できます。

1. **[保存]** をクリックして設定を保存します。

   この設定はグローバル プロパティではなく、現在サインインしているユーザーのみに適用されます。 全体管理者ロールのすべてのメンバーを対象にアクセスを昇格させることはできません。

1. サインアウトし、もう一度サインインするとアクセス権が更新されます。

    これで自分のディレクトリにあるすべてのサブスクリプションと管理グループにアクセスできます。 ルート スコープでユーザー アクセス管理者ロールが割り当てられていることがわかります。

   ![サブスクリプション ロール割り当てとルート スコープ - スクリーンショット](./media/elevate-access-global-admin/iam-root.png)

1. 昇格させたアクセス権で必要な変更を加えます。

    ロールの割り当て方法については、「[RBAC と Azure portal を使用してアクセスを管理する](role-assignments-portal.md)」を参照してください。 Azure AD Privileged Identity Management (PIM) を使用している場合、「[PIM で管理する Azure リソースを検出する](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md)」または「[PIM で Azure リソース ロールを割り当てる](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)」を参照してください。

1. 完了したら、 **[Azure リソースのアクセス管理]** を **[いいえ]** に戻します。 これはユーザー別の設定であるため、アクセスの昇格に使用したユーザーでサインインする必要があります。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-the-root-scope-"></a>ルート スコープ (/) のロールの割り当てを一覧表示する

ルート スコープ (`/`) のユーザーについてユーザー アクセス管理者ロールの割り当てを一覧表示するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) コマンドを使用します。

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>ルート スコープ (/) のロールの割り当てを削除する

ルート スコープ (`/`) のユーザーについてユーザー アクセス管理者ロールの割り当てを削除するには、次の手順を行います。

1. 昇格させたアクセス権を削除できるユーザーでサインインします。 これはアクセス権を昇格させたときと同じユーザーかルート スコープでアクセス権が昇格されている別の全体管理者になります。


1. ユーザー アクセス管理者ロールの割り当てを削除するには、[Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) コマンドを使用します。

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>全体管理者のアクセス権を昇格する

REST API を使用して全体管理者のアクセス権を昇格するには、以下の基本的な手順を実行します。

1. REST を使用して `elevateAccess` を呼び出します。これにより、ユーザー アクセス管理者ロールがルート スコープ (`/`) で付与されます。

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. [ロールの割り当て](/rest/api/authorization/roleassignments)を作成し、任意の役割を任意のスコープで割り当てます。 次の例は、{roleDefinitionID} ロールをルート スコープ (`/`) で割り当てるためのプロパティを示しています。

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. ユーザー アクセス管理者である間は、ルート スコープ (`/`) でロールの割り当てを削除することもできます。

1. 再び必要になるまで、ユーザー アクセス管理者特権を削除します。

### <a name="list-role-assignments-at-the-root-scope-"></a>ルート スコープ (/) のロールの割り当てを一覧表示する

ルート スコープ (`/`) でのユーザーに対するロールの割り当ての一覧を表示することができます。

- [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) を呼び出します。`{objectIdOfUser}` は、ロールの割り当てを取得するユーザーのオブジェクト ID です。

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>ルート スコープ (/) の拒否割り当てを一覧表示する

ルート スコープ (`/`) でのユーザーに対するすべての拒否割り当てを一覧表示することができます。

- Call GET denyAssignments を呼び出します。`{objectIdOfUser}` は、拒否割り当てを取得するユーザーのオブジェクト ID です。

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>昇格されたアクセス権を削除する

`elevateAccess` を呼び出すと、自分自身に対するロールの割り当てが作成されます。このため、これらの権限を取り消すには、割り当てを削除する必要があります。

1. [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) を呼び出します。この `roleName` はユーザー アクセス管理者であり、ユーザー アクセス管理者ロールの名前 ID を判別します。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    この場合の `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` では、`name` パラメーターの ID を省略できます。

2. また、ディレクトリ スコープでディレクトリ管理者のロール割り当ての一覧を表示する必要もあります。 アクセス権の昇格呼び出しを行ったディレクトリ管理者の `principalId` について、ディレクトリ スコープの割り当て一覧を表示します。 これにより、objectid についてディレクトリの割り当ての一覧が表示されます。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >ディレクトリ管理者の割り当ては多くありません。前のクエリが返す割り当ての数が多すぎる場合は、ディレクトリ スコープ レベルのみで、すべての割り当てに対してクエリを実行し、結果をフィルター処理することもできます。`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
   1. 前の呼び出しは、ロール割り当ての一覧を返します。 スコープが `"/"` で、`roleDefinitionId` が手順 1 で見つかったロール名 ID で終了し、`principalId` がディレクトリ管理者の objectId と一致する、ロール割り当てを検索します。 
    
      サンプル ロールの割り当て

       ```json
       {
         "value": [
           {
             "properties": {
               "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
               "principalId": "{objectID}",
               "scope": "/",
               "createdOn": "2016-08-17T19:21:16.3422480Z",
               "updatedOn": "2016-08-17T19:21:16.3422480Z",
               "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
               "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
             },
             "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
             "type": "Microsoft.Authorization/roleAssignments",
             "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
           }
         ],
         "nextLink": null
       }
       ```
        
      もう一度、`name` パラメーターの ID を保存します (この例では e7dd75bc-06f6-4e71-9014-ee96a929d099)。

   1. 最後に、ロール割り当て ID を使用して、`elevateAccess` によって追加された割り当てを削除します。

      ```http
      DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
      ```

## <a name="next-steps"></a>次の手順

- [Azure での各種ロールについて](rbac-and-directory-admin-roles.md)
- [RBAC と REST API を使用して Azure リソースへのアクセスを管理する](role-assignments-rest.md)
