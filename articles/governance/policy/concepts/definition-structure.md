---
title: ポリシー定義の構造の詳細
description: Azure Policy でリソース ポリシー定義を使用して、ポリシーが適用されるタイミングとその効果を示すことで、組織でのリソースの規則を確立する方法について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 77bf284734428e9257b46d85296796e4051ace26
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494838"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy の定義の構造

リソース ポリシーの定義は、Azure Policy でソースに対する規則を確立するために使用されます。 各定義には、リソースのコンプライアンスと、リソースが準拠していない場合にどのような効果を適用するかが記述されます。
規則を定義することによって、コストを制御し、リソースをより簡単に管理することができます。 たとえば、特定の種類の仮想マシンのみを許可するように指定することができます。 また、すべてのリソースに特定のタグが指定されていることを必須にすることができます。 ポリシーは、すべての子リソースが継承します。 リソース グループにポリシーが適用された場合、ポリシーは、そのリソース グループ内のすべてのリソースに適用されます。

Azure Policy で使用されるスキーマについては、[https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json) を参照してください。

ポリシー定義を作成するには、JSON を使用します。 ポリシー定義には、以下のものに対する要素が含まれています。

- モード
- parameters
- 表示名
- description
- ポリシー規則
  - 論理評価
  - 効果

たとえば、次の JSON は、リソースがデプロイされる場所を制限するポリシーを示しています。

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Azure Policy のサンプルはすべて「[Azure Policy のサンプル](../samples/index.md)」にあります。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Mode

**Mode** は、ポリシーが Azure Resource Manager のプロパティまたはリソース プロバイダーのプロパティのどちらをターゲットにしているかどうかに応じて構成されます。

### <a name="resource-manager-modes"></a>Resource Manager のモード

**mode** では、ポリシーに対して評価されるリソースの種類を決定します。 サポートされているモードは次のとおりです。

- `all`: リソース グループとすべてのリソースの種類を評価します
- `indexed`: タグと場所をサポートするリソースの種類のみを評価します

ほとんどの場合、**mode** は `all` に設定することをお勧めします。 ポータルを使用して作成されるポリシーの定義はすべて、`all` モードを使用します。 PowerShell または Azure CLI を使用する場合、**mode** パラメーターを手動で指定することができます。 ポリシー定義に **mode** 値が含まれていない場合、既定値として Azure PowerShell では `all` が、Azure CLI では `null` が使用されます。 `null` モードは、下位互換性をサポートするために `indexed` を使用するのと同じです。

タグまたは場所を適用するポリシーを作成する場合は、`indexed` を使用してください。 これは必須ではありませんが、それによって、タグまたは場所をサポートしていないリソースが、コンプライアンス結果に非準拠として表示されることを回避できます。 例外は**リソース グループ**です。 リソース グループに対して場所またはタグを適用するポリシーでは、**mode** を `all` に設定し、明確に `Microsoft.Resources/subscriptions/resourceGroups` 型をターゲットにする必要があります。 例については、[リソース グループのタグを適用する](../samples/enforce-tag-rg.md)ことに関する記事を参照してください。 タグをサポートするリソースの一覧については、「[Azure リソースでのタグのサポート](../../../azure-resource-manager/tag-support.md)」を参照してください。

### <a name="resource-provider-modes"></a>リソース プロバイダーのモード

現在サポートされている唯一のリソース プロバイダーのモードは、[Azure Kubernetes Service](../../../aks/intro-kubernetes.md) のアドミッション コントローラー規則を管理するための `Microsoft.ContainerService.Data` です。

> [!NOTE]
> [Kubernetes 用の Azure Policy](rego-for-aks.md) はパブリック プレビューで、組み込みのポリシー定義のみをサポートします。

## <a name="parameters"></a>parameters

パラメーターによって、ポリシー定義の数を減らし、ポリシーの管理を単純化できます。 1 つのフォームにあるフィールドのようなパラメーター `name`、`address``city``state` を考えてみてください。 これらのパラメーターは常に同じままですが、その値はフォームの個々の入力に基づいて変わります。
パラメーターは、ポリシーの作成時と同じように機能します。 ポリシー定義にパラメーターを含めることで、別の値を使用してさまざまなシナリオについてポリシーを再利用できます。

> [!NOTE]
> パラメーターは、既存の割り当て済み定義に追加できます。 新しいパラメーターには、**defaultValue** プロパティを含める必要があります。 これにより、ポリシーまたはイニシアティブの既存の割り当てが間接的に無効になることを防ぎます。

### <a name="parameter-properties"></a>パラメーターのプロパティ

パラメーターには、ポリシー定義内で使用される次のプロパティがあります。

- **name**:お使いのパラメーターの名前。 ポリシー規則内の `parameters` デプロイ関数によって使用されます。 詳しくは、[パラメーター値の使用](#using-a-parameter-value)に関するページをご覧ください。
- `type`:パラメーターが **string**、**array**、**object**、**boolean**、**integer**、**float** **datetime** のどれであるかを決定します。
- `metadata`:Azure portal によって主に使用されるサブプロパティを定義して、ユーザー フレンドリな情報を表示します。
  - `description`:パラメーターが何に使用されるかの説明。 許可される値の例を提示するために使用できます。
  - `displayName`:ポータル内に表示されるパラメーターのフレンドリ名。
  - `strongType`:(省略可能) ポータル経由でポリシー定義を割り当てるときに使用されます。 コンテキスト対応の一覧を提供します。 詳しくは、[strongType](#strongtype) に関するページをご覧ください。
  - `assignPermissions`:(省略可能) ポリシーの割り当て中に Azure portal にロールの割り当てを作成させるには、_true_ に設定します。 このプロパティは、割り当てスコープ外でアクセス許可を割り当てたい場合に便利です。 ロールの割り当ては、ポリシーのロール定義ごと (またはイニシアチブのすべてのポリシーのロール定義ごとに) 1 つあります。 パラメーター値は、有効なリソースまたはスコープである必要があります。
- `defaultValue`:(省略可能) 値が指定されていない場合、割り当ての中でパラメーターの値を設定します。
  割り当てられている既存のポリシー定義を更新するときは、必須です。
- `allowedValues`:(省略可能) 割り当て中にパラメーターが許可する値の配列を指定します。

たとえば、リソースをデプロイできる場所を制限するためのポリシー定義を定めることができます。 そのポリシー定義のパラメーターは、**allowedLocations** にすることができます。 このパラメーターは、許可される値を制限するために、ポリシー定義の割り当てごとに使用されます。 **strongType** の使用によって、ポータル経由で割り当てを完了したときに、拡張されたエクスペリエンスが提供されます。

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>パラメーター値の使用

ポリシー規則では、次に示す `parameters` 関数とデプロイ値の構文でパラメーターを参照します。

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

このサンプルでは、「[パラメーターのプロパティ](#parameter-properties)」に示されていた **allowedLocations** パラメーターを参照します。

### <a name="strongtype"></a>strongType

`metadata` プロパティの中で、**strongType** を使用して、Azure portal 内でオプションの複数選択リストを提供できます。 現時点で **strongType** で使用できる値には、以下が含まれます。

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>定義の場所

イニシアティブまたはポリシーを作成する際には、定義の場所を指定する必要があります。 定義の場所は、管理グループまたはサブスクリプションにする必要があります。 この場所によって、イニシアティブまたはポリシー定義を割り当てることができるスコープが決まります。 リソースは、割り当て対象とする定義の場所のダイレクト メンバーか、その階層内の子である必要があります。

定義の場所が:

- **サブスクリプション**の場合 - そのサブスクリプション内のリソースだけを、ポリシーに割り当てることができます。
- **管理グループ**の場合 - 子管理グループと子サブスクリプション内のリソースだけを、ポリシーに割り当てることができます。 ポリシー定義を複数のサブスクリプションに適用する場合、場所は、それらのサブスクリプションを含む管理グループである必要があります。

## <a name="display-name-and-description"></a>表示名と説明

**displayName** と **description** を使用して、ポリシー定義を識別し、定義が使用される際のコンテキストを指定します。 **displayName** の最大長は _128_ 文字で、**description** の最大長は _512_ 文字です。

## <a name="policy-rule"></a>ポリシー規則

ポリシー規則は、**If** と **Then** ブロックで構成されています。 **If** ブロックでは、いつポリシーが適用されるかを指定する、1 つ以上の条件を定義します。 これらの条件に論理演算子を適用して、ポリシーのシナリオを細かく定義することができます。

**Then** ブロックでは、**If** 条件が満たされる場合に生じる効果を定義します。

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>論理演算子

サポートされている論理演算子は、次のとおりです。

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**not** 構文は、条件の結果を反転します。 **allOf** 構文 (**And** 論理演算に似ています) では、すべての条件が真である必要があります。 **anyOf** 構文 (**Or** 論理演算に似ています) では、1 つ以上の条件が真である必要があります。

論理演算子は、入れ子にすることができます。 次の例は、**allOf** 演算内で入れ子になっている **not** 演算を示しています。

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>条件

条件では、**field** または **value** アクセサーが特定の基準を満たすかどうかを評価します。 サポートされている条件は次のとおりです。

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

**like** 条件と **notLike** 条件を使用する場合は、値にワイルドカード (`*`) を指定できます。
値に複数のワイルドカード (`*`) を指定することはできません。

**match** 条件と **notMatch** 条件を使用する場合は、任意の数字と一致する `#`、任意の文字と一致する `?`、すべての文字と一致する `.` のほか、一致させる具体的な文字を指定することができます。
**match** と **notMatch** は、大文字と小文字が区別されます。 大文字と小文字が区別されない代替手段は、**matchInsensitively** と **notMatchInsensitively** で使用できます。 例については、「[複数の名前パターンを許可する](../samples/allow-multiple-name-patterns.md)」を参照してください。

### <a name="fields"></a>フィールド

条件は、フィールドを使用して構成されます。 フィールドでは、リソース要求ペイロード内のプロパティが照合され、リソースの状態が記述されます。

次のフィールドがサポートされています。

- `name`
- `fullName`
  - リソースのフル ネームを返します。 リソースのフル ネームは、親リソースの名前が付加されたリソース名です ("myServer/myDatabase" など)。
- `kind`
- `type`
- `location`
  - 場所に依存しないリソースに対しては **global** を使用します。 例については、「[サンプル - 許可される場所](../samples/allowed-locations.md)」を参照してください。
- `identity.type`
  - リソースで有効になっている[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) の種類を返します。
- `tags`
- `tags['<tagName>']`
  - この角かっこ構文では、ハイフン、ピリオド、スペースなどの区切り記号を含むタグ名がサポートされます。
  - **\<tagName\>** は、条件を検証するタグの名前です。
  - 例: `tags['Acct.CostCenter']` (**Acct.CostCenter** がタグの名前)。
- `tags['''<tagName>''']`
  - この角かっこ構文では、2 個のアポストロフィでエスケープすることにより、アポストロフィが含まれるタグ名がサポートされます。
  - **'\<tagName\>'** は、条件を検証するタグの名前です。
  - 例: `tags['''My.Apostrophe.Tag''']` ( **'\<tagName\>'** がタグの名前)。
- プロパティのエイリアス: 一覧については、「[エイリアス](#aliases)」を参照してください。

> [!NOTE]
> `tags.<tagName>`、`tags[tagName]`、および`tags[tag.with.dots]` は、タグ フィールドを宣言する方法としてまだ受け付けられます。 ただし、推奨される式は上に示したものです。

#### <a name="use-tags-with-parameters"></a>パラメーターを含むタグを使用する

パラメーター値をタグ フィールドに渡すことができます。 タグ フィールドにパラメーターを渡すと、ポリシー割り当ての間のポリシー定義の柔軟性が向上します。

次の例では、`concat` を使用して、**tagName** パラメーターの値で指定されているタグのタグ フィールド参照が作成されています。 そのタグが存在しない場合、**append** 効果が使用され、`resourcegroup()` 参照関数を使用することにより監査対象のリソースの親リソース グループで設定されている同じ名前付きタグの値を使用してタグが追加されます。

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>値

条件は、**value** を使用して形成することもできます。 **value** では、[パラメーター](#parameters)、[サポートされるテンプレート関数](#policy-functions)、またはリテラルに対する条件をチェックします。
**value** は、サポートされる任意の[条件](#conditions)と組み合わせられます。

> [!WARNING]
> _テンプレート関数_の結果がエラーの場合、ポリシーの評価は失敗します。 評価の失敗は、暗黙的な **deny** です。 詳細については、「[テンプレート エラーの回避](#avoiding-template-failures)」を参照してください。

#### <a name="value-examples"></a>値の例

このポリシー規則の例では、**value** を使用して `resourceGroup()` 関数の結果と `*netrg` の **like** 条件に対して返された **name** プロパティを比較します。 規則では、名前が `*netrg` で終わる任意のリソース グループ内で `Microsoft.Network/*` の **type** ではないリソースをすべて拒否します。

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

このポリシー規則の例では、**value** を使用して、複数の入れ子になった関数の結果が `true` と **equals** になるかをチェックします。 規則では、3 つ以上のタグを持たないリソースをすべて拒否します。

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>テンプレート エラーの回避

**value** で_テンプレート関数_を使用することにより、入れ子になった多数の複雑な関数が可能になります。 _テンプレート関数_の結果がエラーの場合、ポリシーの評価は失敗します。 評価の失敗は、暗黙的な **deny** です。 特定のシナリオでエラーが発生する **value** の例は、以下のとおりです。

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

上述のポリシー規則の例では、[substring()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) を使用して **name** の最初の 3 文字を **abc** と比較しています。 **name** が 3 文字よりも短い場合、`substring()` 関数はエラーとなります。 このエラーにより、ポリシーは **deny** となります。

3 文字よりも短い **name** をエラーにせず、**name** の最初の 3 文字が **abc** と等しいかどうかを確認するには、代わりに [if()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) 関数を使用します。

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

変更したポリシー規則では、3 文字未満の値の `substring()` を取得する前に、`if()` によって **name** の長さが確認されます。 **name** が短すぎる場合、代わりに "not starting with abc"\(abc で開始されていません\) が返され、**abc** と比較されます。 **abc** で開始しない短い名前のリソースも、ポリシー規則でエラーとなりますが、評価時にはエラーが発生しなくなります。

### <a name="effect"></a>効果

Azure Policy では、次の種類の効果をサポートしています。

- **Deny** はアクティビティ ログでイベントを生成し、要求は失敗します
- **Audit**: アクティビティ ログ内に警告イベントを生成しますが、要求は失敗しません。
- **append** は定義済みのフィールド セットを要求に追加します。
- **AuditIfNotExists**: リソースが存在しない場合に監査を有効にします。
- **DeployIfNotExists**: リソースが存在しない場合にリソースをデプロイします。
- **Disabled**: リソースがポリシー規則に準拠しているかどうかを評価しません。
- **EnforceRegoPolicy**: Azure Kubernetes Service の Open Policy Agent アドミッション コントローラーを構成します (プレビュー)

**append** の場合、次のように詳細を指定する必要があります。

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

値には文字列または JSON 形式オブジェクトを指定できます。

**AuditIfNotExists** と **DeployIfNotExists** では、関連するリソースの存在が評価された後、規則が適用されます。 リソースが規則と一致しない場合に効果が実装されます。 たとえば、すべての仮想ネットワークを対象に Network Watcher のデプロイを要求することができます。 詳細については、「[拡張機能が存在しない場合の監査](../samples/audit-ext-not-exist.md)」の例を参照してください。

**DeployIfNotExists** 効果を使用する場合は、ポリシー規則の **details** 部分に **roleDefinitionId** プロパティが指定されている必要があります。 詳細については、[修復 - ポリシー定義を構成する](../how-to/remediate-resources.md#configure-policy-definition)を参照してください。

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

各効果の詳細、評価の順序、プロパティ、例については、「[Azure Policy の効果について](effects.md)」を参照してください。

### <a name="policy-functions"></a>ポリシー関数

ポリシー規則では、次の関数およびユーザー定義関数を除くすべての [Resource Manager テンプレート関数](../../../azure-resource-manager/resource-group-template-functions.md)を使用できます。

- copyIndex()
- deployment()
- list*
- newGuid()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

ポリシー規則では、次の関数をすべて使用できますが、Azure Resource Manager テンプレートでの使用方法とは異なります。

- addDays(dateTime, numberOfDaysToAdd)
  - **dateTime**: [必須] 文字列 - ユニバーサル ISO 8601 日時形式 'yyyy-MM-ddTHH:mm:ss.fffffffZ' の文字列
  - **numberOfDaysToAdd**: [必須] 整数 - 追加する日数
- utcNow() - Resource Manager テンプレートとは異なり、これは defaultValue 外で使用できます。
  - 現在の日時に設定されているユニバーサル ISO 8601 日時形式 'yyyy-MM-ddTHH:mm:ss.fffffffZ' の文字列が返されます。

さらに、`field` 関数もポリシー規則で使用できます。 `field` は、主に **AuditIfNotExists** と **DeployIfNotExists** で、評価されるリソースのフィールドを参照するために使用されます。 使用例については、「[DeployIfNotExists の例](effects.md#deployifnotexists-example)」をご覧ください。

#### <a name="policy-function-example"></a>ポリシー関数の例

このポリシー規則の例では、`resourceGroup` リソース関数を使用して **name** プロパティを取得します。ここでは、`concat` 配列およびオブジェクト関数と組み合わせて、リソース グループ名で始まるリソース名を指定する `like` 条件を作成します。

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>エイリアス

リソースの種類に固有のプロパティにアクセスするには、プロパティのエイリアスを使用します。 エイリアスを使用すると、リソースのプロパティで許可される値または条件を制限できます。 各エイリアスは、特定のリソースの種類について異なる API バージョンのパスにマップされます。 ポリシーの評価時に、ポリシー エンジンはその API バージョンのプロパティ パスを取得します。

エイリアスの一覧は常に拡大しています。 Azure Policy で現在サポートされているエイリアスを見つけるには、次のいずれかの方法を使用します。

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>[*] エイリアスについて

利用できるいくつかのエイリアスには、'normal' という名前で表示されるバージョンと、それに **[\*]** が添付された別のバージョンがあります。 例:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

"通常" のエイリアスでは、フィールドは 1 つの値として表されます。 このフィールドは完全一致の比較シナリオ用で、値のセット全体を正確に定義する必要があります。それ以上でもそれ以下でもありません。

配列内の各要素の値および各要素の特定のプロパティの比較は、 **[\*]** エイリアスで可能です。 このアプローチでは、"全くない"、"1 つ以上が" または "すべてが" のシナリオで、要素のプロパティを比較できます。 例では、**ipRules[\*]** を使用して、すべての _action_ が _Deny_ であるかどうかが検証されますが、存在している規則の数または IP _value_ は検証されません。 このサンプル規則では、**ipRules[\*].value** が **10.0.4.1** であるすべての一致がチェックされ、1 つ以上の一致が検索されない場合のみ、**effectType** が適用されます。

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

詳細については、[[\*] エイリアスの評価](../how-to/author-policies-for-arrays.md#evaluating-the--alias) に関する説明を参照してください。

## <a name="initiatives"></a>イニシアティブ

イニシアティブを使用すると、複数の関連するポリシー定義をグループ化できます。グループを単一の項目として操作するので、割り当てと管理が単純になります。 たとえば、関連するタグ付けポリシー定義を 1 つのイニシアティブとしてグループ化できます。 それぞれのポリシーを個別に割り当てるのではなく、イニシアティブを適用することになります。

次の例は、2 つのタグ (`costCenter` および `productName`) を扱うためのイニシアティブの作成方法を示しています。 2 つの組み込みポリシーを使用して、既定のタグの値を適用しています。

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>次の手順

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Policy の効果について](effects.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/getting-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。