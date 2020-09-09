---
title: Azure ロールの定義について - Azure RBAC
description: Azure リソースの詳細なアクセス管理を行うための Azure ロールベースのアクセス制御 (RBAC) の Azure ロール定義について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 7c6f9203385c47da9803fb05358889d00d77d3e5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511638"
---
# <a name="understand-azure-role-definitions"></a>Azure ロールの定義について

Azure ロールのしくみを理解しようとしている場合、または独自の [Azure カスタム ロール](custom-roles.md)を作成している場合は、ロールの定義方法を理解すると便利です。 この記事では、ロール定義の詳細について説明し、いくつかの例を示します。

## <a name="role-definition"></a>ロール定義

*ロール定義*はアクセス許可のコレクションです。 単に*ロール*と呼ばれることもあります。 ロール定義には、実行できる操作 (読み取り、書き込み、削除など) が登録されています。 許可された操作から除外されている操作または基となるデータに関連する操作も一覧表示できます。

次に示すのは、Azure PowerShell を使用して表示される場合のロール定義のプロパティの例です。

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

次に示すのは、Azure portal、Azure CLI、または REST API を使用して表示される場合のロール定義のプロパティの例です

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

次の表で、ロールのプロパティについて説明します。

| プロパティ | 説明 |
| --- | --- |
| `Name`</br>`roleName` | ロールの表示名です。 |
| `Id`</br>`name` | ロールの一意の ID です。 |
| `IsCustom`</br>`roleType` | これがカスタム ロールであるかどうかを示します。 カスタム ロールの場合は `true` または `CustomRole` に設定します。 組み込みロールの場合は `false` または `BuiltInRole` に設定します。 |
| `Description`</br>`description` | ロールの説明です。 |
| `Actions`</br>`actions` | ロールで実行できる管理操作を指定する文字列の配列。 |
| `NotActions`</br>`notActions` | 許可された `Actions` から除外される管理操作を指定する文字列の配列。 |
| `DataActions`</br>`dataActions` | 対象のオブジェクト内のデータに対して、ロールで実行できるデータ操作を指定する文字列の配列。 |
| `NotDataActions`</br>`notDataActions` | 許可された `DataActions` から除外されるデータ操作を指定する文字列の配列。 |
| `AssignableScopes`</br>`assignableScopes` | 割り当てにロールを使用できるスコープを指定する文字列の配列。 |

### <a name="operations-format"></a>操作の形式

操作は、次の形式の文字列で指定します。

- `{Company}.{ProviderName}/{resourceType}/{action}`

操作文字列の `{action}` 部分には、リソースの種類に対して実行できる操作の種類を指定します。 たとえば、`{action}` には次の部分文字列が表示されます。

| アクションの部分文字列    | 説明         |
| ------------------- | ------------------- |
| `*` | ワイルドカード文字は、文字列と一致するすべての操作に対するアクセスを許可します。 |
| `read` | 読み取り操作 (GET) を有効にします。 |
| `write` | 書き込み操作 (PUT または PATCH) を有効にします。 |
| `action` | 仮想マシンの再起動 (POST) などのカスタム操作を有効にします。 |
| `delete` | 削除操作 (DELETE) を有効にします。 |

### <a name="role-definition-example"></a>ロールの定義の例

Azure PowerShell と Azure CLI に表示される[共同作成者](built-in-roles.md#contributor)ロールの定義を次に示します。 `Actions` 以下のワイルドカード (`*`) 操作は、このロールに割り当てられたプリンシパルがすべてのアクションを実行できること、つまりすべてを管理できることを示します。 これには、今後、Azure が新しいリソースの種類を追加するときに定義されるアクションも含まれます。 `NotActions` 以下の操作は `Actions` から引かれます。 [共同作成者](built-in-roles.md#contributor)ロールの場合、`NotActions` は、リソースに対するアクセスを管理するこのロールの機能を削除し、Azure Blueprint の割り当ても管理します。

Azure PowerShell に表示される共同作成者ロール:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Azure CLI に表示される共同作成者ロール:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>管理操作とデータ操作

管理操作のロールベースのアクセス制御は、ロール定義の `Actions` プロパティと `NotActions` プロパティで指定されています。 Azure での管理操作の例をいくつか示します。

- ストレージ アカウントに対するアクセスを管理する
- BLOB コンテナーの作成、更新、または削除
- リソース グループとそのすべてのリソースを削除する

コンテナーの認証方法が "アクセス キー" ではなく "Azure AD ユーザー アカウント" に設定されている場合、管理アクセスはデータに継承されません。 この分離により、ワイルドカード (`*`) を使用したロールはデータに無制限にアクセスできなくなります。 たとえば、ユーザーがサブスクリプションで[閲覧者](built-in-roles.md#reader)ロールを持っている場合、ストレージ アカウントは表示できますが、既定では基になるデータを表示することはできません。

以前は、ロールベースのアクセス制御はデータ操作には使用されませんでした。 データ操作のアクセス許可はリソース プロバイダーによって異なります。 管理操作に使用する同じロールベースのアクセス制御許可モデルがデータ操作に拡張されました。

データ操作をサポートするために、新しいデータ プロパティがロール定義に追加されました。 データ操作は `DataActions` プロパティおよび `NotDataActions` プロパティで指定されます。 これらのデータ プロパティを追加することによって、管理とデータの分離が維持されます。 このことによって、ワイルドカード (`*`) を含む現在のロール割り当てが突然データにアクセスする動作が防止されます。 `DataActions` および `NotDataActions` で指定できるデータ操作の一部を次に示します。

- コンテナーの BLOB の一覧の読み取り
- コンテナーのストレージ BLOB の書き込み
- キュー内のメッセージの削除

以下は、[ストレージ BLOB データ閲覧者](built-in-roles.md#storage-blob-data-reader)ロール定義で、`Actions` プロパティと `DataActions` プロパティ両方の操作が含まれています。 このロールでは、BLOB コンテナーおよび基になる BLOB データを読み取ることができます。

Azure PowerShell に表示される Storage BLOB データ リーダー ロール:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Azure CLI に表示される Storage BLOB データ リーダー ロール:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

`DataActions` プロパティと `NotDataActions` プロパティに追加できるのはデータ操作のみです。 リソース プロバイダーはどの操作がデータ操作かを指定します。そのためには `isDataAction` プロパティを `true` に設定します。 `isDataAction` が `true` である操作の一覧を確認するには、「[リソース プロバイダー操作](resource-provider-operations.md)」を参照してください。 データ操作のないロールは、ロール定義内に `DataActions` プロパティと `NotDataActions` プロパティを含める必要はありません。

すべての管理操作 API 呼び出しの許可は Azure Resource Manager によって処理されます。 データ操作 API 呼び出しの許可はリソース プロバイダーまたは Azure Resource Manager のいずれかによって処理されます。

### <a name="data-operations-example"></a>データ操作例

管理操作とデータ操作の動作方法の理解を深めるために、具体例を考えてみましょう。 Alice はサブスクリプション スコープで[所有者](built-in-roles.md#owner)ロールに割り当てられています。 Bob はストレージ アカウント スコープで[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールに割り当てられています。 この例を次の図に示します。

![ロールベースのアクセス制御が管理操作とデータ操作の両方をサポートするように拡張されます](./media/role-definitions/rbac-management-data.png)

Alice の[所有者](built-in-roles.md#owner)ロールおよび Bob の[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールは次のアクションを実行できます。

所有者

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

ストレージ BLOB データ共同作成者

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Alice にはサブスクリプション スコープにワイルドカード (`*`) アクションがあるため、Alice のアクセス許可は継承され、すべての管理アクションを実行できます。 Alice は、コンテナーの読み取り、書き込み、および削除を行うことができます。 ただし、Alice は追加の手順を経ずにデータ操作を実行することはできません。 たとえば、既定では、Alice はコンテナー内の BLOB を読み取ることができません。 BLOB を読み取るには、Alice はストレージ アクセス キーを取得し、それを使用して BLOB にアクセスする必要があります。

Bob のアクセス許可は[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールで指定された `Actions` および `DataActions` のみに制限されます。 Bob はロールに基づいて、管理操作とデータ操作の両方を実行できます。 たとえば、Bob は指定されたストレージ アカウントのコンテナーを読み取り、書き込み、および削除でき、また BLOB も読み取り、書き込み、および削除できます。

ストレージの管理とデータ プレーンのセキュリティの詳細については、「[Azure Storage セキュリティ ガイド](../storage/blobs/security-recommendations.md)」を参照してください。

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>データ操作のために Azure ロールの使用をサポートしているツール

データ操作を表示し、操作するには、正しいバージョンのツールまたは SDK が必要です。

| ツール  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 以降 |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 以降 |
| [Azure for .NET](/dotnet/azure/) | 2.8.0 プレビュー以降 |
| [Azure SDK for Go](/azure/go/azure-sdk-go-install) | 15.0.0 以降 |
| [Azure for Java](/java/azure/) | 1.9.0 以降 |
| [Azure for Python](/azure/python/) | 0.40.0 以降 |
| [Azure SDK for Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 以降 |

REST API でデータ操作を確認して使用するには、次のバージョン以降に **api-version** パラメーターを設定する必要があります。

- 2018-07-01

## <a name="actions"></a>Actions

`Actions` アクセス許可では、ロールで実行できる管理操作を指定します。 このプロパティに文字列で指定された一連の操作によって、Azure リソース プロバイダーのセキュリティ保護可能な操作が識別されます。 `Actions` で使用できる管理操作の例をいくつか示します。

> [!div class="mx-tableFixed"]
> | 操作文字列    | 説明         |
> | ------------------- | ------------------- |
> | `*/read` | すべての Azure リソース プロバイダーの全リソースの種類を対象に読み取り操作のアクセス権を付与します。|
> | `Microsoft.Compute/*` | Microsoft.Compute リソース プロバイダーのすべてのリソースの種類を対象にすべての操作のアクセス権を付与します。|
> | `Microsoft.Network/*/read` | Microsoft.Network リソース プロバイダーの全リソース タイプを対象に読み取り操作のアクセス権を付与します。|
> | `Microsoft.Compute/virtualMachines/*` | Virtual Machines とその子リソース タイプを対象にすべての操作のアクセス権を付与します。|
> | `microsoft.web/sites/restart/Action` | Web アプリを再起動するためのアクセス権を付与します。|

## <a name="notactions"></a>NotActions

`NotActions` アクセス許可には、許可された `Actions` から除外される管理操作を指定します。 制限対象の操作を除外する方が、許可する操作セットを容易に定義できる場合は、`NotActions` アクセス許可を使用します。 ロール (有効なアクセス許可) によって付与されたアクセスは、`Actions` 操作から `NotActions` 操作を引くことによって計算されます。

> [!NOTE]
> `NotActions` で特定の操作を除外したロールをユーザーに割り当てたうえで、同じユーザーにその操作へのアクセス権を付与する別のロールを割り当てた場合、ユーザーはその操作の実行が許可されます。 `NotActions` は拒否ルールとは異なり、特定の操作を除外する必要があるときに、許可の対象となる一連の操作を指定しやすくすることを目的としたものに過ぎません。
>

## <a name="dataactions"></a>DataActions

`DataActions` アクセス許可では、対象のオブジェクト内のデータに対して、ロールで実行できるデータ操作を指定します。 たとえば、ユーザーがあるストレージ アカウントへの BLOB データの読み取りアクセス許可を持っている場合、そのユーザーはそのストレージ アカウント内の BLOB を読み取ることができます。 次に `DataActions` で使用できるデータ操作の例を示します。

> [!div class="mx-tableFixed"]
> | 操作文字列    | 説明         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | BLOB または BLOB の一覧を返します。 |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | BLOB の書き込みの結果を返します。 |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | メッセージを返します。 |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | メッセージまたはメッセージの書き込みまたは削除の結果を返します。 |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions` アクセス許可では、許可された `DataActions` から除外されるデータ操作を指定します。 ロール (有効なアクセス許可) によって付与されたアクセスは、`DataActions` 操作から `NotDataActions` 操作を引くことによって計算されます。 各リソース プロバイダーは、それぞれの API セットを提供し、データ操作をサポートします。

> [!NOTE]
> `NotDataActions` であるデータ操作を除外したロールをユーザーに割り当てたうえで、同じユーザーにそのデータ操作へのアクセス権を付与する別のロールを割り当てた場合、ユーザーはそのデータ操作の実行が許可されます。 `NotDataActions` は拒否ルールとは異なり、特定のデータ操作を除外する必要があるときに、許可の対象となる一連のデータ操作を指定しやすくすることを目的としたものに過ぎません。
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes` プロパティは、このロール定義を使用可能なスコープ (管理グループ、サブスクリプション、またはリソースグループ) を指定します。 ロールを必要とする管理グループ、サブスクリプション、またはリソース グループのみで、その割り当てを利用できるようにすることができます。 少なくとも 1 つの管理グループ、サブスクリプション、またはリソース グループを使用する必要があります。

組み込みロールでは `AssignableScopes` がルート スコープ (`"/"`) に設定されています。 ルート スコープは、すべてのスコープでそのロールを割り当て可能であることを示します。 有効な AssignableScopes の例を次に示します。

> [!div class="mx-tableFixed"]
> | 割り当てにロールを使用できる | 例 |
> |----------|---------|
> | 1 つのサブスクリプション | `"/subscriptions/{subscriptionId1}"` |
> | 2 つのサブスクリプション | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Network resource group (ネットワーク リソース グループ) | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | 1 つの管理グループ | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | 管理グループとサブスクリプション | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | すべてのスコープ (組み込みロールにのみ適用) | `"/"` |

カスタム ロールの `AssignableScopes` の詳細については、[Azure カスタム ロール](custom-roles.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure 組み込みロール](built-in-roles.md)
* [Azure カスタム ロール](custom-roles.md)
* [Azure Resource Manager のリソース プロバイダー操作](resource-provider-operations.md)
