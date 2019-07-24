---
title: REST API を使用して Azure リソースのカスタム ロールを作成する - Azure | Microsoft Docs
description: REST API を使用して、ロールベースのアクセス制御 (RBAC) による Azure リソースのカスタム ロールを作成する方法について説明します。 これには、カスタム ロールを一覧表示、作成、更新、削除する方法が含まれます。
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cec75f757789be4f962cf2b0fbf6b9443a4453cc
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588196"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>REST API を使用して Azure リソースのカスタム ロールを作成する

[Azure リソースの組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 この記事では、REST API を使用して、カスタム ロールを作成して管理する方法について説明します。

## <a name="list-roles"></a>ロールの一覧表示

すべてのロールを一覧表示する、または表示名を使って単一のロールに関する情報を取得するには、[ロールの定義 - 一覧表示](/rest/api/authorization/roledefinitions/list) REST API を使用します。 この API を呼び出すには、指定されたスコープにおける `Microsoft.Authorization/roleDefinitions/read` 操作のアクセス許可が必要です。 この操作のアクセス権は、いくつかの[組み込みロール](built-in-roles.md)に付与されています。

1. 次の要求から開始します。

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. URI 内の *{scope}* は、ロールを一覧表示するスコープに置き換えます。

    | Scope (スコープ) | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | サブスクリプション |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | リソース グループ |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | リソース |

1. *{filter}* には、ロールの一覧をフィルター処理するために適用する条件を指定します。

    | filter | 説明 |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | 指定したスコープとそのすべての子スコープでの割り当てに使用できるロールを一覧表示します。 |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | ロールの完全な表示名の URL エンコード形式を使用します。 次に例を示します。`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>ロールに関する情報を取得する

ロール定義識別子を使ってロールに関する情報を取得するには、[ロールの定義 - 取得](/rest/api/authorization/roledefinitions/get) REST API を使用します。 この API を呼び出すには、指定されたスコープにおける `Microsoft.Authorization/roleDefinitions/read` 操作のアクセス許可が必要です。 この操作のアクセス権は、いくつかの[組み込みロール](built-in-roles.md)に付与されています。

単一のロールに関する情報をその表示名を使って取得する方法については、前の「 [ロールの一覧表示](custom-roles-rest.md#list-roles)」セクションを参照してください。

1. [ロールの定義 - 一覧表示](/rest/api/authorization/roledefinitions/list) REST API を使用して、ロールの GUID 識別子を取得します。 組み込みロールの場合は、[組み込みロール](built-in-roles.md)から識別子を取得することもできます。

1. 次の要求から開始します。

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI 内の *{scope}* は、ロールを一覧表示するスコープに置き換えます。

    | Scope (スコープ) | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | サブスクリプション |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | リソース グループ |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | リソース |

1. *{roleDefinitionId}* は、ロールの定義の GUID 識別子に置き換えます。

## <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、[ロールの定義 - 作成または更新](/rest/api/authorization/roledefinitions/createorupdate) REST API を使用します。 この API を呼び出すには、すべての `assignableScopes` において `Microsoft.Authorization/roleDefinitions/write` 操作へのアクセス許可が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは [Owner](built-in-roles.md#owner) と [User Access Administrator](built-in-roles.md#user-access-administrator) だけです。 

1. カスタム ロールのアクセス許可の作成に使用可能な[リソース プロバイダー操作](resource-provider-operations.md)の一覧を確認します。

1. GUID ツールを使用し、カスタム ロール識別子に使用する一意の識別子を生成します。 この識別子の形式は `00000000-0000-0000-0000-000000000000` になります。

1. 次の要求と本文から開始します。

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. URI 内の *{scope}* は、カスタム ロールの 1 つ目の `assignableScopes` に置き換えます。

    | Scope (スコープ) | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | サブスクリプション |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | リソース グループ |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | リソース |

1. *{roleDefinitionId}* は、カスタム ロールの GUID 識別子に置き換えます。

1. 要求本文内にある `assignableScopes` プロパティの *{roleDefinitionId}* は、GUID 識別子に置き換えます。

1. *{subscriptionId}* は、実際のサブスクリプション ID に置き換えます。

1. `actions` プロパティに、ロールで実行できる操作を追加します。

1. `notActions` プロパティに、許可された `actions` から除外される操作を追加します。

1. `roleName` および `description` プロパティに、一意なロール名と説明を指定します。 プロパティの詳細については、「[カスタム ロール](custom-roles.md)」を参照してください。

    要求本文の例を次に示します。

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>カスタム ロールの更新

カスタム ロールを更新するには、[ロールの定義 - 作成または更新](/rest/api/authorization/roledefinitions/createorupdate) REST API を使用します。 この API を呼び出すには、すべての `assignableScopes` において `Microsoft.Authorization/roleDefinitions/write` 操作へのアクセス許可が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは [Owner](built-in-roles.md#owner) と [User Access Administrator](built-in-roles.md#user-access-administrator) だけです。 

1. カスタム ロールに関する情報を取得するには、[ロールの定義 - 一覧表示](/rest/api/authorization/roledefinitions/list)または[ロールの定義 - 取得](/rest/api/authorization/roledefinitions/get) REST API を使用します。 詳細については、このページの「[ロールの一覧表示](custom-roles-rest.md#list-roles)」セクションを参照してください。

1. 次の要求から開始します。

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI 内の *{scope}* は、カスタム ロールの 1 つ目の `assignableScopes` に置き換えます。

    | Scope (スコープ) | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | サブスクリプション |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | リソース グループ |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | リソース |

1. *{roleDefinitionId}* は、カスタム ロールの GUID 識別子に置き換えます。

1. カスタム ロールに関する情報に基づいて、次の形式の要求本文を作成します。

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. カスタム ロールに加えようとしている変更を反映するように要求本文を更新します。

    新しい診断設定アクションを追加した要求本文の例を次に示します。

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>カスタム ロールの削除

カスタム ロールを削除するには、[ロールの定義 - 削除](/rest/api/authorization/roledefinitions/delete) REST API を使用します。 この API を呼び出すには、すべての `assignableScopes` において `Microsoft.Authorization/roleDefinitions/delete` 操作へのアクセス許可が必要です。 組み込みロールのうち、この操作のアクセス権が付与されているのは [Owner](built-in-roles.md#owner) と [User Access Administrator](built-in-roles.md#user-access-administrator) だけです。 

1. カスタム ロールの GUID 識別子を取得するには、[ロールの定義 - 一覧表示](/rest/api/authorization/roledefinitions/list)または[ロールの定義 - 取得](/rest/api/authorization/roledefinitions/get) REST API を使用します。 詳細については、このページの「[ロールの一覧表示](custom-roles-rest.md#list-roles)」セクションを参照してください。

1. 次の要求から開始します。

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI 内の *{scope}* は、カスタム ロールを削除するスコープに置き換えます。

    | Scope (スコープ) | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | サブスクリプション |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | リソース グループ |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | リソース |

1. *{roleDefinitionId}* は、カスタム ロールの GUID 識別子に置き換えます。

## <a name="next-steps"></a>次の手順

- [Azure リソースのカスタム ロール](custom-roles.md)
- [RBAC と REST API を使用して Azure リソースへのアクセスを管理する](role-assignments-rest.md)
- [Azure REST API リファレンス](/rest/api/azure/)