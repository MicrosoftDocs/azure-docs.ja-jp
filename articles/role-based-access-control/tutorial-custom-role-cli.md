---
title: チュートリアル:Azure CLI を使用して Azure カスタム ロールを作成する - Azure RBAC
description: このチュートリアルでは、Azure CLI と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して Azure カスタム ロールを作成します。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.custom: devx-track-azurecli
ms.openlocfilehash: 319bca74c8e781e5dc5022e9fb901b2edca24a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87485645"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-cli"></a>チュートリアル:Azure CLI を使用して Azure カスタム ロールを作成する

[Azure の組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 このチュートリアルでは、Azure CLI を使用して、Reader Support Tickets というカスタム ロールを作成します。 このカスタム ロールが割り当てられたユーザーは、サブスクリプションの管理プレーンにすべてを表示することができ、サポート チケットを開くこともできます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * カスタム ロールの作成
> * カスタム ロールの一覧表示
> * カスタム ロールの更新
> * カスタム ロールの削除

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の要件があります。

- [所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、カスタム ロールを作成するためのアクセス許可
- [Azure Cloud Shell](../cloud-shell/overview.md) または [Azure CLI](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>Azure CLI へのサインイン

[Azure CLI](/cli/azure/authenticate-azure-cli) にサインインします。

## <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、JSON テンプレートをベースに必要な変更を加えて、新しいロールを作成するのが最も簡単です。

1. [Microsoft.Support リソース プロバイダー](resource-provider-operations.md#microsoftsupport)の操作一覧を確認します。 アクセス許可の作成に使用できる操作を知るための参考にしてください。

    | Operation | 説明 |
    | --- | --- |
    | Microsoft.Support/register/action | サポート リソース プロバイダーに登録します。 |
    | Microsoft.Support/supportTickets/read | サポート チケットの詳細 (ステータス、重大度、連絡先の詳細、やり取りなど) を取得するか、サブスクリプション全体のサポート チケットの一覧を取得します。 |
    | Microsoft.Support/supportTickets/write | サポート チケットを作成または更新します。 技術、課金、クォータ、またはサブスクリプション管理に関する問題のサポート チケットを作成できます。 既存のサポート チケットの重大度、連絡先の詳細、やり取りを更新できます。 |
    
1. **ReaderSupportRole.json** という名前の新しいファイルを作成します。

1. ReaderSupportRole.json をエディターで開き、次の JSON を追加します。

    各種のプロパティについては、[Azure カスタム ロール](custom-roles.md)に関するページを参照してください。

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. `Actions` プロパティに次の操作を追加します。 これらのアクションによって、ユーザーはサブスクリプションに含まれるあらゆるものを閲覧したり、サポート チケットを作成したりすることができます。

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. [az account list](/cli/azure/account#az-account-list) コマンドを使用して、サブスクリプションの ID を取得します。

    ```azurecli
    az account list --output table
    ```

1. `AssignableScopes` の `{subscriptionId1}` は、実際のサブスクリプション ID で置き換えてください。

    明示的にサブスクリプション ID を追加してください。それ以外の場合、サブスクリプションにロールをインポートできなくなります。

1. `Name` プロパティと `Description` プロパティを、それぞれ "Reader Support Tickets" と "View everything in the subscription and also open support tickets." に変更します。

    編集後の JSON ファイルは次のようになります。

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. 新しいカスタム ロールを作成するには、[az role definition create](/cli/azure/role/definition#az-role-definition-create) コマンドを使用して、JSON ロール定義ファイルを指定します。

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    新しいカスタム ロールが利用できる状態となり、組み込みロールと同じように、ユーザー、グループ、またはサービス プリンシパルに割り当てることができます。

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示

- すべてのカスタム ロールを一覧表示するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) コマンドに `--custom-role-only` パラメーターを組み合わせて使用します。

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    カスタム ロールは、Azure portal で確認することもできます。

    ![Azure Portal にインポートされたカスタム ロールのスクリーンショット](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>カスタム ロールの更新

カスタム ロールを更新するには、JSON ファイルを更新してカスタム ロールを更新します。

1. ReaderSupportRole.json ファイルを開きます。

1. `Actions` に、リソース グループのデプロイを作成および管理するための操作を追加します (`"Microsoft.Resources/deployments/*"`)。 前の操作の後に必ずコンマを追加してください。

    更新後の JSON ファイルは次のようになります。

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. カスタム ロールを更新するには、[az role definition update](/cli/azure/role/definition#az-role-definition-update) コマンドを使用して、更新済みの JSON ファイルを指定します。

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>カスタム ロールの削除

- [az role definition delete](/cli/azure/role/definition#az-role-definition-delete) コマンドを使用し、ロール名またはロール ID を指定してカスタム ロールを削除します。

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI を使用して Azure カスタム ロールを作成または更新する](custom-roles-cli.md)
