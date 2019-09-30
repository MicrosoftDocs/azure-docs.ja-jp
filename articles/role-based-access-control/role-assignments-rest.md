---
title: RBAC と REST API を使用して Azure リソースへのアクセスを管理する - Azure | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) と REST API を使用してユーザー、グループ、アプリケーションの Azure リソースへのアクセスを管理する方法について説明します。 具体的には、アクセス権の一覧表示、付与、削除などを取り上げます。
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996469"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>RBAC と REST API を使用して Azure リソースへのアクセスを管理する

[ロールベースのアクセス制御 (RBAC)](overview.md) は、Azure のリソースに対するアクセスを管理するための手法です。 この記事では、RBAC と REST API を使用してユーザー、グループ、アプリケーションのアクセス権を管理する方法を説明します。

## <a name="list-access"></a>アクセス権の表示

RBAC でアクセス権を一覧表示するには、ロールの割り当てを一覧表示します。 ロールの割り当てを一覧表示するには、いずれかの[ロールの割り当て - 一覧表示](/rest/api/authorization/roleassignments/list) REST API を使用します。 結果を絞り込むには、スコープと任意のフィルターを指定します。

1. 次の要求から開始します。

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. URI の *{scope}* を、ロールの割り当てを一覧表示するスコープに変更します。

    | Scope (スコープ) | 種類 |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | リソース |

    前の例で、microsoft.web は App Service インスタンスを参照するリソース プロバイダーです。 同様に、他の任意のリソース プロバイダーを使用してスコープを指定できます。 詳細については、「[Azure リソース プロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md)」およびサポートされている「[Azure Resource Manager のリソース プロバイダー操作](resource-provider-operations.md)」を参照してください。  
     
1. *{filter}* には、ロールの割り当て一覧をフィルター処理するために適用する条件を指定します。

    | filter | 説明 |
    | --- | --- |
    | `$filter=atScope()` | サブスコープのロールの割り当ては含めずに、指定スコープのみを対象にロールの割り当てを一覧表示します。 |
    | `$filter=principalId%20eq%20'{objectId}'` | 指定したユーザー、グループ、またはサービス プリンシパルを対象にロールの割り当てを一覧表示します。 |
    | `$filter=assignedTo('{objectId}')` | 指定したユーザーまたはサービス プリンシパルを対象にロールの割り当てを一覧表示します。 ユーザーが、ロール割り当てがあるグループのメンバーである場合は、そのロール割り当ても一覧表示されます。 このフィルターはグループにとって推移的です。つまり、ユーザーがあるグループのメンバーで、そのグループが、ロール割り当てのある別のグループのメンバーである場合は、そのロール割り当ても一覧表示されます。 このフィルターは、ユーザーまたはサービス プリンシパルのオブジェクト ID のみを受け入れます。 グループのオブジェクト ID を渡すことはできません。 |

## <a name="grant-access"></a>アクセス権の付与

RBAC でアクセス権を付与するには、ロールの割り当てを作成します。 ロールの割り当てを作成するには、[ロールの割り当て - 作成](/rest/api/authorization/roleassignments/create) REST API を使用し、セキュリティ プリンシパル、ロールの定義、スコープを指定します。 この API を呼び出すには、`Microsoft.Authorization/roleAssignments/write` 操作へのアクセス許可が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは [Owner](built-in-roles.md#owner) と [User Access Administrator](built-in-roles.md#user-access-administrator) だけです。

1. [ロールの定義 - 一覧表示](/rest/api/authorization/roledefinitions/list) REST API を使用するか、[組み込みロール](built-in-roles.md)を参照し、割り当てるロール定義の識別子を取得します。

1. GUID ツールを使用し、ロール割り当て識別子に使用する一意の識別子を生成します。 この識別子の形式は `00000000-0000-0000-0000-000000000000` になります。

1. 次の要求と本文から開始します。

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. URI の *{scope}* をロールの割り当てのスコープに変更します。

    | Scope (スコープ) | 種類 |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | リソース |

1. *{roleAssignmentName}* を、ロールの割り当ての GUID 識別子に置き換えます。

1. 要求本文内で、 *{scope}* をロールの割り当てのスコープに変更します。

    | Scope (スコープ) | 種類 |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | リソース |

1. *{roleDefinitionId}* を、ロールの定義の識別子に置き換えます。

1. *{principalId}* を、ロールが割り当てられるユーザー、グループ、またはサービス プリンシパルのオブジェクト識別子に置き換えます。

## <a name="remove-access"></a>アクセス権の削除

RBAC では、アクセス権を削除するにはロールの割り当てを削除する必要があります。 ロールの割り当てを削除するには、[ロールの割り当て - 削除](/rest/api/authorization/roleassignments/delete) REST API を使用します。 この API を呼び出すには、`Microsoft.Authorization/roleAssignments/delete` 操作へのアクセス許可が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは [Owner](built-in-roles.md#owner) と [User Access Administrator](built-in-roles.md#user-access-administrator) だけです。

1. ロールの割り当ての識別子 (GUID) を取得します。 この識別子は、ロールの割り当てを最初に作成するときに返されます。あるいは、ロールの割り当てを一覧表示することで取得できます。

1. 次の要求から開始します。

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. URI の *{scope}* を、ロールの割り当てを削除するためのスコープに変更します。

    | Scope (スコープ) | 種類 |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理グループ |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | リソース |

1. *{roleAssignmentName}* を、ロールの割り当ての GUID 識別子に置き換えます。

## <a name="next-steps"></a>次の手順

- [Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API リファレンス](/rest/api/azure/)
- [REST API を使用して Azure リソースのカスタム ロールを作成する](custom-roles-rest.md)
