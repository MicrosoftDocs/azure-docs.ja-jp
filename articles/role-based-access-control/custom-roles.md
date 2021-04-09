---
title: Azure カスタム ロール - Azure RBAC
description: Azure リソースの詳細なアクセス管理を行うために、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して Azure カスタム ロールを作成する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 79aaeee942a6d46243ee1c72d5904484b8698ebe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97617325"
---
# <a name="azure-custom-roles"></a>Azure カスタム ロール

> [!IMPORTANT]
> `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure の組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 組み込みロールと同様に、カスタム ロールは、ユーザー、グループ、サービス プリンシパルに対して、管理グループ、サブスクリプション、およびリソース グループのスコープで割り当てることができます。

カスタム ロールは、同じ Azure AD ディレクトリを信頼するサブスクリプション間で共有できます。 カスタム ロールの数は、ディレクトリあたり **5,000** 個という制限があります (Azure Germany と Azure China 21Vianet の場合、制限は 2,000 カスタム ロールです)。カスタム ロールは、Azure portal、Azure PowerShell、Azure CLI、または REST API を使用して作成できます。

## <a name="steps-to-create-a-custom-role"></a>カスタム ロールの作成手順

カスタム ロールを作成する基本的な手順について説明します。

1. 必要なアクセス許可を決定する。

    カスタム ロールを作成する場合、アクセス許可を定義するために使用可能な操作を把握しておく必要があります。 通常は、まず、既存の組み込みロールを使用し、必要に応じてそれを変更します。 操作を[ロール定義](role-definitions.md)の `Actions` または `NotActions` プロパティに追加します。 データ操作をする場合は、それらを `DataActions` または `NotDataActions` プロパティに追加します。

    詳細については、次のセクション「[必要なアクセス許可を特定する方法](#how-to-determine-the-permissions-you-need)」をご覧ください。

1. カスタム ロールを作成する方法を決定する。

    カスタム ロールは、[Azure portal](custom-roles-portal.md)、[Azure PowerShell](custom-roles-powershell.md)、[Azure CLI](custom-roles-cli.md)、または [REST API](custom-roles-rest.md) を使用して作成できます。

1. カスタム ロールを作成する。

    最も簡単な方法は、Azure portal を使用することです。 Azure portal を使用してカスタム ロールを作成する手順については、「[Azure portal を使用して Azure カスタム ロールを作成または更新する](custom-roles-portal.md)」を参照してください。

1. カスタム ロールをテストする。

    カスタム ロールを作成したら、それをテストして期待どおりに動作することを確認する必要があります。 後で調整する必要がある場合は、カスタム ロールを更新できます。

## <a name="how-to-determine-the-permissions-you-need"></a>必要なアクセス許可を特定する方法

Azure には、カスタム ロールに含めることができる何千ものアクセス許可があります。 カスタム ロールに追加するアクセス許可を決定するのに役立ついくつかの方法を示します。

- 既存の[組み込みロール](built-in-roles.md)を確認します。

    既存のロールを変更したり、複数のロールで使用されるアクセス許可を結合したりすることができます。

- アクセス権を付与する Azure サービスを一覧表示します。

- [Azure サービスにマップされるリソース プロバイダー](../azure-resource-manager/management/azure-services-resource-providers.md)を決定します。

    Azure サービスでは、[リソース プロバイダー](../azure-resource-manager/management/overview.md)によって機能とアクセス許可が公開されます。 たとえば、Microsoft.Compute リソース プロバイダーは、仮想マシンのリソースを提供し、Microsoft.Billing リソース プロバイダーはサブスクリプションと課金のリソースを提供します。 リソース プロバイダーを把握することで、カスタム ロールに必要なアクセス許可を絞り込み特定できます。

    Azure portal を使用してカスタム ロールを作成する場合は、キーワードを検索してリソース プロバイダーを特定することもできます。 この検索機能については、「[Azure portal を使用して Azure カスタム ロールを作成または更新する](custom-roles-portal.md#step-4-permissions)」で説明しています。

    ![リソース プロバイダーが含まれた [アクセス許可の追加] ペイン](./media/custom-roles-portal/add-permissions-provider.png)

- [使用可能なアクセス許可](resource-provider-operations.md)を検索して、含めるアクセス許可を見つけます。

    Azure portal を使用してカスタム ロールを作成する場合は、キーワードを使用してアクセス許可を検索できます。 たとえば、"*仮想マシン*" や "*請求*" のアクセス許可を検索できます。 すべてのアクセス許可を CSV ファイルとしてダウンロードし、このファイルを検索することもできます。 この検索機能については、「[Azure portal を使用して Azure カスタム ロールを作成または更新する](custom-roles-portal.md#step-4-permissions)」で説明しています。

    ![[アクセス許可の追加] の一覧](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>カスタム ロールの例

Azure PowerShell を使用して JSON 形式で表示されるカスタム ロールの例を次に示します。 このカスタム ロールは、仮想マシンの監視と再起動に使用できます。

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
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Azure CLI を使用して表示される同じカスタム ロールを次に示します。

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
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
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

カスタム ロールを作成すると、Azure portal にオレンジ色のリソース アイコンと共に表示されます。

![カスタム ロールのアイコン](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>カスタム ロールのプロパティ

次の表で、カスタム ロールのプロパティについて説明します。

| プロパティ | 必須 | Type | 説明 |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | はい | String | カスタム ロールの表示名。 ロールの定義は、管理グループまたはサブスクリプション レベルのリソースですが、同じ Azure AD ディレクトリを共有する複数のサブスクリプションで使用できます。 この表示名は、Azure AD ディレクトリ範囲で一意である必要があります。 英字、数字、スペース、特殊文字を含めることができます。 最大文字数は 128 文字です。 |
| `Id`</br>`name` | はい | String | カスタム ロールの一意の ID。 Azure PowerShell と Azure CLI では、新しいロールを作成するときに自動的にこの ID が生成されます。 |
| `IsCustom`</br>`roleType` | はい | String | これがカスタム ロールであるかどうかを示します。 カスタム ロールの場合は `true` または `CustomRole` に設定します。 組み込みロールの場合は `false` または `BuiltInRole` に設定します。 |
| `Description`</br>`description` | はい | String | カスタム ロールの説明。 英字、数字、スペース、特殊文字を含めることができます。 最大文字数は 1024 文字です。 |
| `Actions`</br>`actions` | はい | String[] | ロールで実行できる管理操作を指定する文字列の配列。 詳細については、「[Actions](role-definitions.md#actions)」を参照してください。 |
| `NotActions`</br>`notActions` | いいえ | String[] | 許可された `Actions` から除外される管理操作を指定する文字列の配列。 詳細については、「[notActions](role-definitions.md#notactions)」を参照してください。 |
| `DataActions`</br>`dataActions` | いいえ | String[] | 対象のオブジェクト内のデータに対して、ロールで実行できるデータ操作を指定する文字列の配列。 `DataActions` が含まれるカスタム ロールを作成する場合、そのロールは管理グループのスコープで割り当てることができません。 詳細については、「[DataActions](role-definitions.md#dataactions)」を参照してください。 |
| `NotDataActions`</br>`notDataActions` | いいえ | String[] | 許可された `DataActions` から除外されるデータ操作を指定する文字列の配列。 詳細については、「[NotDataActions](role-definitions.md#notdataactions)」を参照してください。 |
| `AssignableScopes`</br>`assignableScopes` | はい | String[] | 割り当てにカスタム ロールを使用できるスコープを指定する文字列の配列。 カスタム ロールの `AssignableScopes` に定義できる管理グループは 1 つだけです。 `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。 詳細については、「[AssignableScopes](role-definitions.md#assignablescopes)」を参照してください。 |

## <a name="wildcard-permissions"></a>ワイルドカードのアクセス許可

`Actions`、`NotActions`、`DataActions`、および `NotDataActions` では、アクセス許可の定義でワイルドカード (`*`) をサポートしています。 ワイルドカード (`*`) を使用すると、指定されたアクション文字列に一致するものすべてにアクセス許可が拡張されます。 たとえば、Azure Cost Management とエクスポートに関連するすべてのアクセス許可を追加したいとします。 次のすべてのアクション文字列を追加できます。

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

これらの文字列をすべて追加する代わりに、ワイルドカードの文字列を追加するだけで済みます。 たとえば、次のワイルドカードの文字列は、前の 5 つの文字列に相当します。 これには、今後追加される可能性があるエクスポートのアクセス許可も含まれます。

```
Microsoft.CostManagement/exports/*
```

また、1 つの文字列に複数のワイルドカードを含めることもできます。 たとえば、次の文字列は、Cost Management のすべてのクエリ アクセス許可を表します。

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>カスタム ロールを作成、削除、更新、または表示できるユーザー

組み込みロールと同じように、`AssignableScopes` プロパティでは、割り当てにロールを使用できるスコープを指定します。 カスタム ロールの `AssignableScopes` プロパティでは、カスタム ロールを作成、削除、更新、または表示できるユーザーも制御されます。

| タスク | Operation | 説明 |
| --- | --- | --- |
| カスタム ロールの作成/削除 | `Microsoft.Authorization/ roleDefinitions/write` | カスタム ロールのすべての `AssignableScopes` に対してこの操作が許可されているユーザーは、これらのスコープで使用するカスタム ロールを作成 (または削除) できます。 たとえば、管理グループ、サブスクリプション、リソース グループの[所有者](built-in-roles.md#owner)と[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)です。 |
| カスタム ロールの更新 | `Microsoft.Authorization/ roleDefinitions/write` | カスタム ロールのすべての `AssignableScopes` に対してこの操作が許可されているユーザーは、これらのスコープ内のカスタム ロールを更新できます。 たとえば、管理グループ、サブスクリプション、リソース グループの[所有者](built-in-roles.md#owner)と[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)です。 |
| カスタム ロールの表示 | `Microsoft.Authorization/ roleDefinitions/read` | あるスコープでこの操作を許可されたユーザーは、そのスコープで割り当て可能なカスタム ロールを表示できます。 すべての組み込みロールでは、カスタム ロールを割り当てることができます。 |

## <a name="custom-role-limits"></a>カスタム ロールの制限事項

次の一覧では、カスタム ロールの制限事項について説明します。

- 各ディレクトリは、最大 **5,000** 個のカスタム ロールを持つことができます
- Azure Germany と Azure China 21Vianet では、各ディレクトリに対して最大 2,000 のカスタム ロールを持つことができます。
- ルート スコープ (`"/"`) には `AssignableScopes` を設定できません。
- `AssignableScopes` にワイルドカード (`*`) を使用することはできません。 このワイルドカード制限は、ロール定義を更新することで、ユーザーがスコープにアクセスできないようにするのに役立ちます。
- カスタム ロールの `AssignableScopes` に定義できる管理グループは 1 つだけです。 `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。
- `DataActions` が含まれるカスタム ロールを管理グループのスコープで割り当てることはできません。
- ロールの定義の割り当て可能なスコープに管理グループが存在するかどうかは、Azure Resource Manager では確認されません。

カスタム ロールと管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)」をご覧ください。

## <a name="input-and-output-formats"></a>入力形式と出力形式

コマンド ラインを使用してカスタム ロールを作成するには、通常、JSON を使用してカスタム ロールに対して必要なプロパティを指定します。 使用するツールによっては、入力と出力の形式が若干異なります。 このセクションでは、ツールに応じて入力と出力の形式を示します。

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してカスタム ロールを作成するには、次の入力を指定する必要があります。

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Azure PowerShell を使用してカスタム ロールを更新するには、次の入力を指定する必要があります。 `Id` プロパティが追加されていることに注意してください。 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Azure PowerShell と [ConvertTo-Json](/powershell/module/microsoft.powershell.utility/convertto-json) コマンドを使用してカスタム ロールを一覧表示する場合の出力の例を次に示します。 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してカスタム ロールを作成または更新するには、次の入力を指定する必要があります。 この形式は、Azure PowerShell を使用してカスタム ロールを作成する場合と同じ形式です。

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Azure CLI を使用してカスタム ロールを一覧表示する場合の出力の例を次に示します。

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>REST API

REST API を使用してカスタム ロールを作成または更新するには、次の入力を指定する必要があります。 この形式は、Azure portal を使用してカスタム ロールを作成したときに生成される形式と同じです。

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

REST API を使用してカスタム ロールを一覧表示する場合の出力の例を次に示します。

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure PowerShell を使用して Azure カスタム ロールを作成する](tutorial-custom-role-powershell.md)
- [チュートリアル:Azure CLI を使用して Azure カスタム ロールを作成する](tutorial-custom-role-cli.md)
- [Azure ロールの定義について](role-definitions.md)
- [Azure RBAC のトラブルシューティング](troubleshooting.md)
