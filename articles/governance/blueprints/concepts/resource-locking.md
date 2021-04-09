---
title: リソース ロックについて
description: ブループリントを割り当てるときにリソースを保護するための Azure Blueprints 内のロック オプションについて説明します。
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: b2004ad294ae0eec1b4f2fc6f49308efd32d652e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920192"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Azure Blueprint でのリソース ロックについて

一貫性のある環境を大規模に作成することが本当に価値があるのは、一貫性を維持するためのメカニズムがある場合だけです。 この記事では、Azure Blueprint でのリソース ロックのしくみについて説明します。 リソース ロックと "_拒否割り当て_" の適用の例については、[新しいリソースの保護](../tutorials/protect-new-resources.md)に関するチュートリアルを参照してください。

> [!NOTE]
> Azure Blueprints によってデプロイされたリソース ロックは、ブループリントの割り当てによってデプロイされたリソースにのみ適用されます。 既に存在するリソース グループ内のリソースなどの既存のリソースには、ロックは追加されません。

## <a name="locking-modes-and-states"></a>ロック モードと状態

ブループリント割り当てに適用されるロック モードには、次に示す 3 つのオプションがあります。**ロックしない**、**読み取り専用**、**削除しない**。 ロック モードは、ブループリント割り当て中のアーティファクトのデプロイにおいて構成されます。 別のロック モードを設定するには、ブループリント割り当てを更新します。
ただし、Azure Blueprints の外部でロック モードを変更することはできません。

ブループリント割り当て内のアーティファクトによって作成されたリソースは、次の 4 つのいずれかの状態になります。**ロックなし**、**読み取り専用**、**編集/削除できません**、または **削除不可**。 各種のアーティファクトは、**ロックなし** の状態にすることができます。 次の表を使用することで、リソースの状態を特定することができます。

|モード|アーティファクトのリソースの種類|State|説明|
|-|-|-|-|
|ロックしない|*|ロックなし|リソースは Azure Blueprints によって保護されません。 この状態は、ブループリント割り当ての外部から **読み取り専用** または **削除しない** のリソース グループ アーティファクトに追加されるリソースに対しても使用されます。|
|[読み取り専用]|Resource group|編集/削除できません|リソース グループは読み取り専用であり、リソース グループ上のタグを変更することはできません。 このリソース グループに対しては、**ロックなし** リソースの追加、移動、変更、削除を行うことができます。|
|[読み取り専用]|非リソース グループ|[読み取り専用]|いかなる方法でもリソースを変更することはできません。 変更することも削除することもできません。|
|削除しない|*|削除不可|リソースを変更することはできますが、削除することはできません。 このリソース グループに対しては、**ロックなし** リソースの追加、移動、変更、削除を行うことができます。|

## <a name="overriding-locking-states"></a>ロック状態をオーバーライドする

通常、サブスクリプションに対する適切な [Azure ロールベースのアクセス制御](../../../role-based-access-control/overview.md) (Azure RBAC) を持つユーザー ('所有者' ロールのユーザーなど) が、リソースの変更や削除を許可されることはありえます。 ただし、デプロイされた割り当ての一部として Azure Blueprints によってロックが適用されている場合は、このアクセスは許可されません。 **読み取り専用** オプションまたは **削除しない** オプションを使用して割り当てが設定されている場合、サブスクリプション所有者であっても、ブロックされたアクションを保護されたリソースに対して実行することはできません。

このセキュリティ対策により、定義済みのブループリントの一貫性と作成目的で設計された環境が、偶発的またはプログラムによる削除や変更から保護されます。

### <a name="assign-at-management-group"></a>管理グループに割り当てる

サブスクリプションの所有者がブループリントの割り当てを削除できないようにするための唯一のオプションは、ブループリントを管理グループに割り当てることです。 このシナリオでは、管理グループの **所有者** だけが、ブループリント割り当てを削除するために必要なアクセス許可を持っています。

サブスクリプションではなく管理グループにブループリントを割り当てる場合、REST API 呼び出しは、次のように変更されます。

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

`{assignmentMG}` によって定義される管理グループは、管理グループ階層内にあるか、ブループリント定義が保存されている管理グループと同じである必要があります。

ブループリント割り当ての要求本文は、次のようになります。

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

この要求本文と、サブスクリプションに割り当てられるものの重要な違いは `properties.scope` プロパティです。 この必須プロパティは、ブループリント割り当てが適用されるサブスクリプションに設定される必要があります。 このサブスクリプションは、ブループリント割り当てが格納される管理グループ階層の直接の子である必要があります。

> [!NOTE]
> 管理グループの範囲に割り当てられたブループリントは、サブスクリプション レベルのブループリント割り当てとしても機能します。 唯一の違いは、サブスクリプションの所有者が割り当ておよび関連付けられているロックを削除できないようにするために、ブループリント割り当てが格納されることです。

## <a name="removing-locking-states"></a>ロック状態を削除する

割り当てによって保護されたリソースを変更または削除する必要が生じた場合、2 つの方法でそれを行うことができます。

- ブループリント割り当てを更新して、ロック モードを **ロックなし** にする
- ブループリント割り当てを削除する

割り当てが削除されると、Azure Blueprints によって作成されたロックが削除されます。 ただし、リソース自体は維持されるので、通常の方法を使用して削除する必要があります。

## <a name="how-blueprint-locks-work"></a>ブループリントのロックのしくみ

ブループリントの割り当て時には、**読み取り専用** オプションまたは **削除しない** オプションがその割り当てによって選択された場合、Azure RBAC [拒否割り当て](../../../role-based-access-control/deny-assignments.md)の拒否アクションがアーティファクト リソースに適用されます。 この拒否アクションは、ブループリント割り当てのマネージド ID によって追加され、アーティファクト リソースから削除するには、同じマネージド ID を使用する必要があります。 このセキュリティ対策により、ロック メカニズムが強制されて、Azure Blueprints 外からブループリントのロックを解除できなくなります。

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="リソースグループのアクセス制御 (I A M) ページと [拒否割り当て] タブのスクリーンショット。" border="false":::

各モードの[拒否割り当てプロパティ](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties)は、次のようになります。

|モード |Permissions.Actions |Permissions.NotActions |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|[読み取り専用] |**\** _ |_ *\*/read **<br />** Microsoft.Authorization/locks/delete **<br />** Microsoft.Network/virtualNetwork/subnets/join/action** |SystemDefined (Everyone) |**excludedPrincipals** におけるブループリント割り当てとユーザー定義 |リソース グループ - _true_;リソース - _false_ |
|削除しない |**\*/delete** | **Microsoft.Authorization/locks/delete**<br />**Microsoft.Network/virtualNetwork/subnets/join/action** |SystemDefined (Everyone) |**excludedPrincipals** におけるブループリント割り当てとユーザー定義 |リソース グループ - _true_;リソース - _false_ |

> [!IMPORTANT]
> Azure Resource Manager では、ロール割り当ての詳細が最大 30 分間キャッシュされます。 そのため、ブループリント リソースに対する拒否割り当ての拒否アクションは、すぐには完全に反映されない場合があります。 その間は、ブルー プリントのロックで保護しようとしたリソースが削除される可能性もあります。

## <a name="exclude-a-principal-from-a-deny-assignment"></a>拒否割り当てからプリンシパルを除外する

一部のデザインまたはセキュリティ シナリオでは、ブルー プリント割り当てによって作成される[割り当て拒否](../../../role-based-access-control/deny-assignments.md)からのプリンシパルの除外が必要な場合があります。 この手順は、[割り当てを作成する](/rest/api/blueprints/assignments/createorupdate)ときに、REST API 内で **locks** プロパティの **excludedPrincipals** 配列に最大 5 つの値を追加することで実行されます。 次の割り当て定義は、**excludedPrincipals** を含む要求本文の例です。

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>拒否割り当てからアクションを除外する

ブループリント割り当ての[拒否割り当て](../../../role-based-access-control/deny-assignments.md)で[プリンシパルを除外する](#exclude-a-principal-from-a-deny-assignment)場合と同様に、特定の [Azure リソース プロバイダーの操作](../../../role-based-access-control/resource-provider-operations.md)を除外することができます。 **properties.locks** ブロック内の **excludedPrincipals** と同じ場所に **excludedActions** を追加できます。

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

**excludedPrincipals** は明示的である必要がありますが、**excludedActions** エントリでは、リソースプロバイダー操作のワイルドカード検索に `*` を使用できます。

## <a name="next-steps"></a>次のステップ

- [新しいリソースの保護](../tutorials/protect-new-resources.md)に関するチュートリアルに従います。
- [ブループリントのライフサイクル](./lifecycle.md)を参照する。
- [静的および動的パラメーター](./parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](./sequencing-order.md)のカスタマイズを参照する。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。
