---
title: Azure RBAC のスコープについて
description: Azure ロールベースのアクセス制御 (Azure RBAC) のスコープと、リソースのスコープを決定する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: deee42c46c9b08bb265c972695b9319413d4fcb1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555911"
---
# <a name="understand-scope-for-azure-rbac"></a>Azure RBAC のスコープについて

"*スコープ*" は、アクセスが適用されるリソースのセットです。 ロールを割り当てる際は、セキュリティ プリンシパルに実際に必要なアクセス権だけを付与できるよう、スコープについて理解することが重要です。 スコープを制限することで、セキュリティ プリンシパルが侵害された場合にリスクに晒されるリソースを制限できます。

## <a name="scope-levels"></a>スコープ レベル

Azure では、4 つのレベル ([管理グループ](../governance/management-groups/overview.md)、サブスクリプション、[リソース グループ](../azure-resource-manager/management/overview.md#resource-groups)、リソース) でスコープを指定できます。 スコープは親子関係で構造化されています。 階層のレベルごとに、スコープがより限定的になります。 これらのスコープレベルのいずれかで、ロールを割り当てることができます。 選択するレベルで、ロールの適用範囲が決まります。 上位レベルのロールのアクセス許可が下位レベルに継承されます。 

![ロール割り当てのスコープ](./media/scope-overview/rbac-scope-no-label.png)

管理グループはサブスクリプションより上位レベルのスコープですが、管理グループではより複雑な階層がサポートされます。 次の図は、定義できる管理グループとサブスクリプションの階層の例を示しています。 管理グループの詳細については、「[Azure 管理グループとは](../governance/management-groups/overview.md)」を参照してください。

![管理グループとサブスクリプションの階層](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>スコープの形式

コマンド ラインを使用してロールを割り当てる場合は、スコープを指定する必要があります。 コマンド ライン ツールでは、スコープはロールの割り当ての正確なスコープを識別する文字列で、長い場合があります。 Azure portal では、通常、このスコープは "*リソース ID*" として表示されます。

スコープは、スラッシュ (/) 文字で区切られた一連の識別子で構成されます。 この文字列は、次の階層を表すと考えることができます。ここで、プレースホルダー (`{}`) のないテキストは固定識別子です。

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` は、使用するサブスクリプションの ID (GUID) です。
- `{resourcesGroupName}` は、包含リソース グループの名前です。
- `{providerName}` は、リソースを処理する[リソース プロバイダー](../azure-resource-manager/management/azure-services-resource-providers.md)の名前です。`{resourceType}` と `{resourceSubType*}` は、そのリソース プロバイダー内のレベルをさらに識別します。
- `{resourceName}` は、特定のリソースを識別する文字列の最後の部分です。

管理グループは、サブスクリプションより 1 つ上のレベルであり、最も広範な (最も限定的でない) スコープを持ちます。 このレベルでのロールの割り当ては、管理グループ内のサブスクリプションに適用されます。 管理グループのスコープは、次の形式になります。

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>スコープの例

> [!div class="mx-tableFixed"]
> | Scope | 例 |
> | --- | --- |
> | 管理グループ | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | サブスクリプション | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Resource group | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | リソース | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>リソースのスコープを決定する方法

管理グループ、サブスクリプション、またはリソース グループのスコープを特定するのは非常に簡単です。 必要なのは、名前とサブスクリプション ID を把握しておくことだけです。 ただし、リソースのスコープを決定するにはもう少し作業が必要です。 リソースのスコープを決定するいくつかの方法を次に示します。

- Azure portal で、リソースを開き、プロパティを確認します。 リソースには、スコープを決定できる場所である **リソース ID** が表示されます。 たとえば、ストレージ アカウントのリソース ID は次のようになります。

    ![Azure portal のストレージ アカウントのリソース ID](./media/scope-overview/scope-resource-id.png)

- もう 1 つの方法として、Azure portal を使用してリソース スコープで一時的にロールを割り当て、[Azure PowerShell](role-assignments-list-powershell.md) または [Azure CLI](role-assignments-list-cli.md) を使用してロールの割り当てを一覧表示する方法もあります。 出力では、スコープはプロパティとして一覧表示されます。

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>次のステップ

- [Azure ロールを割り当てる手順](role-assignments-steps.md)
- [Azure サービスのリソース プロバイダー](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 管理グループとは](../governance/management-groups/overview.md)
