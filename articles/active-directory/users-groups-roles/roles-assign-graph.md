---
title: Microsoft Graph API を使用してカスタム管理者ロールの割り当てと削除を行う - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で Graph API を使用して Azure AD 管理者ロールを割り当てたり削除したりする
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 898f444e868a469aed5358f49f48f5bcbfab4450
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707440"
---
# <a name="assign-custom-admin-roles-using-graph-api-in-azure-active-directory"></a>Azure Active Directory で Graph API を使用してカスタム管理者ロールを割り当てる 

Microsoft Graph API を使って、ユーザー アカウントにロールを割り当てる方法を自動化することができます。 この記事では、roleAssignments に対する POST、GET、DELETE の各操作について説明します。

## <a name="required-permissions"></a>必要なアクセス許可

ロールの割り当てまたは削除を行うには、グローバル管理者アカウントまたは Privileged Identity 管理者を使って Azure AD テナントに接続します。

## <a name="post-operations-on-roleassignment"></a>RoleAssignment での POST 操作

ユーザーとロール定義間でロールの割り当てを作成するための HTTP 要求。

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

Body

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Response

``` HTTP
HTTP/1.1 201 Created
```

プリンシパルまたはロールの定義が存在しないロールの割り当てを作成するための HTTP 要求

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

Body

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Response

``` HTTP
HTTP/1.1 404 Not Found
```

組み込みロール定義での単一リソース スコープ付きロール割り当てを作成するための HTTP 要求。

> [!NOTE] 
> 組み込みロールは現在、制限があります。ここでは、"/" 組織全体のスコープまたは "/AU/*" スコープにのみ範囲指定できます。 単一リソースの範囲指定は、組み込みロールには機能しませんが、カスタム ロールには機能します。

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

Body

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/ab2e1023-bddc-4038-9ac1-ad4843e7e539"]
}
```

Response

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>RoleAssignment での GET 操作

特定のプリンシパルに対するロールの割り当てを取得するための HTTP 要求

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=principalId eq ‘<object-id-of-principal>’
```

Response

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

特定のロールの定義に対するロールの割り当てを取得するための HTTP 要求。

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Response

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

ID によるロールの割り当てを取得するための HTTP 要求。

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

Response

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
}
```

## <a name="delete-operations-on-roleassignment"></a>RoleAssignment での DELETE 操作

ユーザーとロール定義間でロールの割り当てを削除するための HTTP 要求。

DELETE

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

Response
``` HTTP
HTTP/1.1 204 No Content
```

既に存在していないロールの割り当てを削除するための HTTP 要求

DELETE

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

Response

``` HTTP
HTTP/1.1 404 Not Found
```

ユーザーとロール定義間でロールの割り当てを削除するための HTTP 要求

DELETE

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

Response

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>次の手順

* [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
* ロールと管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](directory-assign-admin-roles.md)に関するページを参照してください。
* 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
