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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0e0c83d411242be38992dd763dea72eda70ffbf4
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006460"
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

    | Scope (スコープ) | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | サブスクリプション |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | リソース グループ |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. *{filter}* には、ロールの割り当て一覧をフィルター処理するために適用する条件を指定します。

    | filter | 説明 |
    | --- | --- |
    | `$filter=atScope()` | サブスコープのロールの割り当ては含めずに、指定スコープのみを対象にロールの割り当てを一覧表示します。 |
    | `$filter=principalId%20eq%20'{objectId}'` | 特定のユーザー、グループ、またはサービス プリンシパルを対象にロールの割り当てを一覧表示します。 |
    | `$filter=assignedTo('{objectId}')` | グループから継承されたものも含め、特定のユーザーを対象にロールの割り当てを一覧表示します。 |

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
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. URI の *{scope}* をロールの割り当てのスコープに変更します。

    | Scope (スコープ) | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | サブスクリプション |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | リソース グループ |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. *{roleAssignmentName}* を、ロールの割り当ての GUID 識別子に置き換えます。

1. 要求本文の *{subscriptionId}* を、実際のサブスクリプション ID に置き換えます。

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

    | Scope (スコープ) | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | サブスクリプション |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | リソース グループ |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. *{roleAssignmentName}* を、ロールの割り当ての GUID 識別子に置き換えます。

## <a name="next-steps"></a>次の手順

- [Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API リファレンス](/rest/api/azure/)
- [REST API を使用して Azure リソースのカスタム ロールを作成する](custom-roles-rest.md)