---
title: REST API を使用して Azure 拒否割り当てを一覧表示する - Azure RBAC
description: REST API と Azure ロールベースのアクセス制御 (RBAC) を使用して、ユーザー、グループ、およびアプリケーションの Azure 拒否割り当てを一覧表示する方法について説明します。
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2f835c270930734bf9963a7c7c3168b873eddaf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84791913"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>REST API を使用して Azure 拒否割り当てを一覧表示する

[Azure 拒否割り当て](deny-assignments.md)を使用すると、ロールの割り当てでアクセスを許可されている場合であっても、指定した Azure リソース アクションをユーザーが実行できなくなります。 この記事では、REST API を使用して拒否割り当てを一覧表示する方法を説明します。

> [!NOTE]
> 独自の拒否割り当てを直接作成することはできません。 拒否割り当てが作成されるしくみの詳細については、[Azure 拒否割り当て](deny-assignments.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

拒否割り当てに関する情報を取得するのに必要なものは次のとおりです:

- ほとんどの [Azure 組み込みロール](built-in-roles.md)に含まれている `Microsoft.Authorization/denyAssignments/read` アクセス許可。

## <a name="list-a-single-deny-assignment"></a>1 つの拒否割り当てを一覧表示する

1. 次の要求から開始します。

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. URI の *{scope}* を、拒否割り当てを一覧表示するスコープに変更します。

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | サブスクリプション |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | リソース |

1. *{deny-assignment-id}* を、取得する拒否割り当て識別子に置き換えます。

## <a name="list-multiple-deny-assignments"></a>複数の拒否割り当てを一覧表示する

1. 次のいずれかの要求から開始します。

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    省略可能なパラメーターを使用します。

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. URI の *{scope}* を、拒否割り当てを一覧表示するスコープに変更します。

    > [!div class="mx-tableFixed"]
    > | Scope | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | サブスクリプション |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | リソース |

1. *{filter}* には、拒否割り当て一覧をフィルター処理するために適用する条件を指定します。

    > [!div class="mx-tableFixed"]
    > | Assert | 説明 |
    > | --- | --- |
    > | (フィルターなし) | 指定されたスコープおよびその上下の拒否割り当てをすべて一覧表示します。 |
    > | `$filter=atScope()` | 指定されたスコープ以上の拒否割り当てのみを一覧表示します。 サブスコープの拒否割り当ては含まれません。 |
    > | `$filter=assignedTo('{objectId}')` | 指定したユーザーまたはサービス プリンシパルを対象に拒否割り当てを一覧表示します。<br/>ユーザーが、拒否割り当てがあるグループのメンバーである場合は、その拒否割り当ても一覧表示されます。 このフィルターはグループにとって推移的です。つまり、ユーザーがあるグループのメンバーで、そのグループが、拒否割り当てのある別のグループのメンバーである場合は、その拒否割り当ても一覧表示されます。<br/>このフィルターは、ユーザーまたはサービス プリンシパルのオブジェクト ID のみを受け入れます。 グループのオブジェクト ID を渡すことはできません。 |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 指定したユーザーまたはサービス プリンシパルを対象に、指定したスコープで拒否割り当てを一覧表示します。 |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 指定された名前の拒否割り当てを一覧表示します。 |
    > | `$filter=principalId+eq+'{objectId}'` | 指定したユーザー、グループ、またはサービス プリンシパルを対象に拒否割り当てを一覧表示します。 |

## <a name="list-deny-assignments-at-the-root-scope-"></a>ルート スコープ (/) の拒否割り当てを一覧表示する

1. 「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](elevate-access-global-admin.md)」で説明されているように、アクセス権限を昇格させます。

1. 次の要求を使用します。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. *{filter}* には、拒否割り当て一覧をフィルター処理するために適用する条件を指定します。 フィルターが必要です。

    > [!div class="mx-tableFixed"]
    > | Assert | 説明 |
    > | --- | --- |
    > | `$filter=atScope()` | 指定されたルート スコープの拒否割り当てのみを一覧表示します。 サブスコープの拒否割り当ては含まれません。 |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | 指定された名前の拒否割り当てを一覧表示します。 |

1. 昇格されたアクセス権を削除します。

## <a name="next-steps"></a>次のステップ

- [Azure 拒否割り当てについて](deny-assignments.md)
- [Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](elevate-access-global-admin.md)
- [Azure REST API リファレンス](/rest/api/azure/)
