---
title: "テナント管理者によるアクセス権の昇格 | Microsoft Docs"
description: "このトピックでは、ロール ベースのアクセス制御 (RBAC) の組み込みのロールについて説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: b9eb9c7f59d0936364bad788ed3b6635b2453739
ms.openlocfilehash: 9bcad7aaf7f1bd8c51dbfa88381276a70a4def5c


---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>ロール ベースのアクセス制御を使用してテナント管理者としてアクセス権を昇格させる

テナント管理者は、ロール ベースのアクセス制御を使用して、通常よりも上位のアクセス許可を付与できるように、アクセス権を一時的に昇格させることができます。 テナント管理者は、必要に応じて、自分自身をユーザー アクセス管理者ロールに昇格させることができます。 このロールは、自分自身または他のユーザーに "/" スコープでロールを付与する権限をテナント管理者に与えます。

この機能は、組織内に存在するすべてのサブスクリプションをテナント管理者が確認できるようにするために重要です。 さらに、オートメーション アプリケーション (請求と監査など) がすべてのサブスクリプションにアクセスし、請求や資産管理の観点から見た組織の正確な状況を提供できるようにします。  

## <a name="how-to-use-elevateaccess-to-give-tenant-access"></a>elevateAccess を使用してテナントへのアクセス権を付与する方法

基本的なプロセスは、次の手順で動作します。

1. REST を使用して *elevateAccess*を呼び出します。これにより、ユーザー アクセス管理者ロールが "/" スコープで付与されます。

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. [ロールの割り当て](/rest/api/authorization/roleassignments)を作成し、任意の役割を任意のスコープで割り当てます。 次の例は、閲覧者ロールを "/" スコープで割り当てるためのプロパティを示しています。

    ```
    { "properties":{
    "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
    "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
    "scope": "/"
    },
    "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. ユーザー アクセス管理管理者である間は、ロールの割り当てを "/" スコープで削除することもできます。

4. 再び必要になるまで、ユーザー アクセス管理権限を取り消します。


## <a name="how-to-undo-the-elevateaccess-action"></a>elevateAccess アクションを取り消す方法

*elevateAccess* を呼び出すと、自分自身に対するロールの割り当てが作成されます。このため、これらの権限を取り消すには、割り当てを削除する必要があります。

1.  [GET roleDefinitions](/rest/api/authorization/roledefinitions#RoleDefinitions_Get) を呼び出します。roleName = User Access Administrator で、ユーザー アクセス管理者ロールの名前の GUID を判別します。 応答は次のようになります。

    ```
    {"value":[{"properties":{
    "roleName":"User Access Administrator",
    "type":"BuiltInRole",
    "description":"Lets you manage user access to Azure resources.",
    "assignableScopes":["/"],
    "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
    "createdOn":"0001-01-01T08:00:00.0000000Z",
    "updatedOn":"2016-05-31T23:14:04.6964687Z",
    "createdBy":null,
    "updatedBy":null},
    "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "type":"Microsoft.Authorization/roleDefinitions",
    "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
    "nextLink":null}
    ```

    *name* パラメーターの GUID を保存します (この例では **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**)。

2. [GET roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_Get) を呼び出します。principalId = 自分の ObjectId です。 これにより、テナント内のすべての割り当てが一覧表示されます。 スコープが "/" で、RoleDefinitionId が手順 1 で見つかったロール名 GUID で終わるものを探します。 ロールの割り当ては次のようになります。

    ```
    {"value":[{"properties":{
    "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "principalId":"{objectID}",
    "scope":"/",
    "createdOn":"2016-08-17T19:21:16.3422480Z",
    "updatedOn":"2016-08-17T19:21:16.3422480Z",
    "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
    "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
    "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
    "type":"Microsoft.Authorization/roleAssignments",
    "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
    "nextLink":null}
    ```

    もう一度、*name* パラメーターの GUID を保存します (この例では **e7dd75bc-06f6-4e71-9014-ee96a929d099**)。

3. 最後に、[DELETE roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById) を呼び出します。roleAssignmentId = 手順 2 で見つかった GUID の名前です。

## <a name="next-steps"></a>次のステップ

- 「[REST API を使用したロールベースの Access Control の管理](role-based-access-control-manage-access-rest.md)」を参照する

- Azure Portal で[アクセスの割り当ての管理](role-based-access-control-manage-assignments.md)を行う



<!--HONumber=Feb17_HO2-->


