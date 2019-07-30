---
title: Azure リソースのカスタム ロール | Microsoft Docs
description: Azure リソースの詳細なアクセス管理を行うために、ロールベースのアクセス制御 (RBAC) を使用してカスタム ロールを作成する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b628086a67f1d76357fda4f753350b6411b8f15
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273717"
---
# <a name="custom-roles-for-azure-resources"></a>Azure リソースのカスタム ロール

[Azure リソースの組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 組み込みロールと同様、カスタム ロールは、ユーザー、グループ、サービス プリンシパルに対して、サブスクリプション、リソース グループ、リソースのスコープで割り当てることができます。

カスタム ロールは Azure Active Directory (Azure AD) ディレクトリに保存され、サブスクリプション間で共有することができます。 各ディレクトリは、最大 **5,000** 個のカスタム ロールを持つことができます (Azure Government、Azure Germany、Azure China 21Vianet などの特殊なクラウドの場合、カスタム ロールの上限は 2,000 個です)。カスタム ロールは、Azure PowerShell、Azure CLI、REST API で作成することができます。

## <a name="custom-role-example"></a>カスタム ロールの例

JSON 形式で表示されるカスタム ロールの例を次に示します。 このカスタム ロールは、仮想マシンの監視と再起動に使用できます。

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
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
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

カスタム ロールを作成すると、Azure portal にオレンジ色のリソース アイコンと共に表示されます。

![カスタム ロールのアイコン](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>カスタム ロールの作成手順

1. カスタム ロールを作成する方法を決定する

    カスタム ロールは、[Azure PowerShell](custom-roles-powershell.md)、[Azure CLI](custom-roles-cli.md)、または [REST API](custom-roles-rest.md) を使用して作成できます。

1. 必要なアクセス許可を決定する

    カスタム ロールを作成する場合、アクセス許可を定義するために使用可能なリソース プロバイダーの操作を把握しておく必要があります。 操作の一覧を確認するには、「[Azure Resource Manager のリソース プロバイダー操作](resource-provider-operations.md)」を参照してください。 操作を[ロール定義](role-definitions.md)の `Actions` または `NotActions` プロパティに追加します。 データ操作をする場合は、それらを `DataActions` または `NotDataActions` プロパティに追加します。

1. カスタム ロールを作成する

    通常は、まず、既存の組み込みロールを使用し、必要に応じてそれを変更します。 次に、[New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) または [az role definition create](/cli/azure/role/definition#az-role-definition-create) コマンドを使用して、カスタム ロールを作成します。 カスタム ロールを作成するには、[所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、すべての `AssignableScopes` に対する `Microsoft.Authorization/roleDefinitions/write` アクセス許可が必要になります。

1. カスタム ロールをテストする

    カスタム ロールを作成したら、それをテストして期待どおりに動作することを確認する必要があります。 後で調整する必要がある場合は、カスタム ロールを更新できます。

カスタム ロールの作成方法に関するステップバイステップのチュートリアルが必要な場合は、「[チュートリアル:Azure PowerShell を使用してカスタム ロールを作成する」か「チュートリアル:](tutorial-custom-role-powershell.md)」か「[チュートリアル:Azure CLI を使用してカスタム ロールを作成する](tutorial-custom-role-cli.md)」を参照してください。

## <a name="custom-role-properties"></a>カスタム ロールのプロパティ

カスタム ロールには、次のプロパティがあります。

| プロパティ | 必須 | Type | 説明 |
| --- | --- | --- | --- |
| `Name` | はい | string | カスタム ロールの表示名。 ロールの定義は、サブスクリプション レベルのリソースですが、同じ Azure AD ディレクトリを共有する複数のサブスクリプションで使用できます。 この表示名は、Azure AD ディレクトリ範囲で一意である必要があります。 英字、数字、スペース、特殊文字を含めることができます。 最大文字数は 128 文字です。 |
| `Id` | はい | string | カスタム ロールの一意の ID。 Azure PowerShell と Azure CLI では、新しいロールを作成するときに自動的にこの ID が生成されます。 |
| `IsCustom` | はい | string | これがカスタム ロールであるかどうかを示します。 カスタム ロールの場合は `true` に設定します。 |
| `Description` | はい | string | カスタム ロールの説明。 英字、数字、スペース、特殊文字を含めることができます。 最大文字数は 1024 文字です。 |
| `Actions` | はい | String[] | ロールで実行できる管理操作を指定する文字列の配列。 詳細については、「[Actions](role-definitions.md#actions)」を参照してください。 |
| `NotActions` | いいえ | String[] | 許可された `Actions` から除外される管理操作を指定する文字列の配列。 詳細については、「[notActions](role-definitions.md#notactions)」を参照してください。 |
| `DataActions` | いいえ | String[] | 対象のオブジェクト内のデータに対して、ロールで実行できるデータ操作を指定する文字列の配列。 詳細については、「[DataActions](role-definitions.md#dataactions)」を参照してください。 |
| `NotDataActions` | いいえ | String[] | 許可された `DataActions` から除外されるデータ操作を指定する文字列の配列。 詳細については、「[NotDataActions](role-definitions.md#notdataactions)」を参照してください。 |
| `AssignableScopes` | はい | String[] | 割り当てにカスタム ロールを使用できるスコープを指定する文字列の配列。 カスタム ロールでは、現在のところルート スコープ (`"/"`) または管理グループ スコープに `AssignableScopes` を設定することはできません。 詳細については、[AssignableScopes](role-definitions.md#assignablescopes) および [Azure 管理グループでリソースを整理する方法](../governance/management-groups/index.md#custom-rbac-role-definition-and-assignment)に関するページを参照してください。 |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>カスタム ロールを作成、削除、更新、または表示できるユーザー

組み込みロールと同じように、`AssignableScopes` プロパティでは、割り当てにロールを使用できるスコープを指定します。 カスタム ロールの `AssignableScopes` プロパティでは、カスタム ロールを作成、削除、更新、または表示できるユーザーも制御されます。

| タスク | Operation | 説明 |
| --- | --- | --- |
| カスタム ロールの作成/削除 | `Microsoft.Authorization/ roleDefinitions/write` | カスタム ロールのすべての `AssignableScopes` に対してこの操作が許可されているユーザーは、これらのスコープで使用するカスタム ロールを作成 (または削除) できます。 たとえば、サブスクリプション、リソース グループ、リソースの[所有者](built-in-roles.md#owner)と[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)です。 |
| カスタム ロールの更新 | `Microsoft.Authorization/ roleDefinitions/write` | カスタム ロールのすべての `AssignableScopes` に対してこの操作が許可されているユーザーは、これらのスコープ内のカスタム ロールを更新できます。 たとえば、サブスクリプション、リソース グループ、リソースの[所有者](built-in-roles.md#owner)と[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)です。 |
| カスタム ロールの表示 | `Microsoft.Authorization/ roleDefinitions/read` | あるスコープでこの操作を許可されたユーザーは、そのスコープで割り当て可能なカスタム ロールを表示できます。 すべての組み込みロールでは、カスタム ロールを割り当てることができます。 |

## <a name="next-steps"></a>次の手順
- [Azure PowerShell を使用して Azure リソースのカスタム ロールを作成する](custom-roles-powershell.md)
- [Azure CLI を使用して Azure リソースのカスタム ロールを作成する](custom-roles-cli.md)
- [Azure リソースのロール定義の概要](role-definitions.md)
- [Azure リソースの RBAC のトラブルシューティング](troubleshooting.md)
