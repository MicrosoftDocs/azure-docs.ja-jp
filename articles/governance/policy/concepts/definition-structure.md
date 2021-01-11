---
title: ポリシー定義の構造の詳細
description: ポリシー定義を使用し、組織の Azure リソースの規則を確立する方法について説明します。
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: ba6b8160eefb0a59bc8273989c27a3a8501a79b7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547802"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy の定義の構造

Azure Policy によってリソースの規則が確立されます。 ポリシー定義には、リソースのコンプライアンス[条件](#conditions) と、条件が満たされた場合に実行する効果が記述されます。 条件では、リソース プロパティ [フィールド](#fields)が必要な値と比較されます。 リソース プロパティ フィールドには、[エイリアス](#aliases)を使用することでアクセスします。 リソース プロパティ フィールドは、単一値フィールドまたは複数値の[配列](#understanding-the--alias)です。 条件の評価は、配列では異なります。
[条件](#conditions)の詳細を参照してください。

規則を定義することによって、コストを制御し、リソースをより簡単に管理することができます。 たとえば、特定の種類の仮想マシンのみを許可するように指定することができます。 また、リソースに特定のタグを付けることを必須にすることもできます。 ポリシー割り当ては、子リソースによって継承されます。 リソース グループにポリシー割り当てが適用されると、そのリソース グループ内のすべてのリソースに適用されます。

ポリシー定義のスキーマは [https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json) にあります

ポリシー定義を作成するには、JSON を使用します。 ポリシー定義には、以下のものに対する要素が含まれています。

- 表示名
- description
- mode
- metadata
- parameters
- ポリシー規則
  - 論理評価
  - 効果

たとえば、次の JSON は、リソースがデプロイされる場所を制限するポリシーを示しています。

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
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

Azure Policy の組み込みとパターンについては、「[Azure Policy のサンプル](../samples/index.md)」を参照してください。

## <a name="display-name-and-description"></a>表示名と説明

**displayName** と **description** を使用して、ポリシー定義を識別し、定義が使用される際のコンテキストを指定します。 **displayName** の最大長は _128_ 文字で、**description** の最大長は _512_ 文字です。

> [!NOTE]
> ポリシー定義の作成または更新時、JSON の範囲外のプロパティにより **ID**、**型**、**名前**が定義され、JSON ファイルでは不要となります。 SDK 経由でポリシー定義を取得すると、JSON の一部として **ID**、**型**、**名前**プロパティが返されますが、いずれもポリシー定義に関連する読み取り専用情報となります。

## <a name="type"></a>Type

**type** プロパティを設定することはできませんが、SDK によって返され、ポータルに表示される 3 つの値があります。

- `Builtin`:これらのポリシー定義は、Microsoft によって提供および管理されます。
- `Custom`:顧客によって作成されるすべてのポリシー定義にこの値があります。
- `Static`:Microsoft に**所有権**がある[規制に関するコンプライアンス ポリシー](./regulatory-compliance.md)を示します。 これらのポリシー定義のコンプライアンス結果は、Microsoft インフラストラクチャでのサード パーティの監査の結果です。 Azure portal では、この値は **Microsoft マネージド**として表示されることがあります。 詳細については、「[クラウドにおける共同責任](../../../security/fundamentals/shared-responsibility.md)」を参照してください。

## <a name="mode"></a>モード

**Mode** は、ポリシーが Azure Resource Manager のプロパティまたはリソース プロバイダーのプロパティのどちらをターゲットにしているかに応じて構成されます。

### <a name="resource-manager-modes"></a>Resource Manager のモード

**mode** によって、ポリシー定義に対して評価されるリソースの種類が決定されます。 サポートされているモードは次のとおりです。

- `all`: リソース グループ、サブスクリプション、およびすべてのリソースの種類を評価します
- `indexed`: タグと場所をサポートするリソースの種類のみを評価します

たとえば、リソース `Microsoft.Network/routeTables` では、タグと場所がサポートされ、両方のモードで評価されます。 ただし、リソース `Microsoft.Network/routeTables/routes` は、タグ付けできず、`Indexed` モードでは評価されません。

ほとんどの場合、**mode** は `all` に設定することをお勧めします。 ポータルを使用して作成されるポリシーの定義はすべて、`all` モードを使用します。 PowerShell または Azure CLI を使用する場合、**mode** パラメーターを手動で指定することができます。 ポリシー定義に **mode** 値が含まれていない場合、既定値として Azure PowerShell では `all` が、Azure CLI では `null` が使用されます。 `null` モードは、下位互換性をサポートするために `indexed` を使用するのと同じです。

タグまたは場所を適用するポリシーを作成する場合は、`indexed` を使用してください。 これは必須ではありませんが、それによって、タグまたは場所をサポートしていないリソースが、コンプライアンス結果に非準拠として表示されることを回避できます。 例外は**リソース グループ**と**サブスクリプション**です。 リソース グループまたはサブスクリプションに対して場所またはタグを適用するポリシー定義では、**mode**を `all` に設定し、明確に `Microsoft.Resources/subscriptions/resourceGroups` 型または `Microsoft.Resources/subscriptions` 型をターゲットにする必要があります。 例については、「[パターン: タグ - サンプル 1](../samples/pattern-tags.md)」を参照してください。 タグをサポートするリソースの一覧については、「[Azure リソースでのタグのサポート](../../../azure-resource-manager/management/tag-support.md)」を参照してください。

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes"></a>リソース プロバイダーのモード (プレビュー)

現在、プレビューの間は、次のリソース プロバイダー モードがサポートされています。

- [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) でアドミッション コントローラー規則を管理するための `Microsoft.ContainerService.Data`。 このリソース プロバイダー モードを使用する定義では、[EnforceRegoPolicy](./effects.md#enforceregopolicy) 効果を使用する**必要があります**。 このモデルは "_非推奨_" となっています。
- Azure 上で、または Azure を離れて Kubernetes クラスターを管理するための `Microsoft.Kubernetes.Data`。 このリソース プロバイダー モードを使用する定義では、効果 _audit_、_deny_、および _disabled_ を使用します。 [EnforceOPAConstraint](./effects.md#enforceopaconstraint) 効果の使用は "_非推奨_" になっています。
- [Azure Key Vault](../../../key-vault/general/overview.md) でコンテナーと証明書を管理するための `Microsoft.KeyVault.Data`。

> [!NOTE]
> プレビュー期間中のリソース プロバイダー モードでは、組み込みポリシー定義のみがサポートされ、イニシアティブはサポートされません。

## <a name="metadata"></a>Metadata

オプションの `metadata` プロパティには、ポリシー定義に関する情報が格納されています。 お客様は `metadata` で組織にとって有用なすべてのプロパティと値を定義できます。 ただし、Azure Policy と組み込みで使用される_一般的_なプロパティがいくつかあります。

### <a name="common-metadata-properties"></a>一般的なメタデータのプロパティ

- `version` (string):ポリシー定義の内容のバージョンに関する詳細を追跡します。
- `category` (string):ポリシー定義が表示される Azure portal 内のカテゴリを指定します。
- `preview` (boolean):ポリシー定義が _preview_ であるかどうかを示す true または false フラグです。
- `deprecated` (boolean):ポリシー定義が _deprecated_ とマークされているかどうかを示す true または false フラグです。

> [!NOTE]
> Azure Policy サービスは、`version`、`preview`、`deprecated` の各プロパティを使用して、組み込みのポリシー定義に対する変更のレベルや取り組み、状態を伝えます。 `version` の形式は `{Major}.{Minor}.{Patch}` です。 特定の状態 (_deprecated_、_preview_ など) は、`version` プロパティに追加されるほか、別のプロパティに **boolean** として追加されます。 Azure Policy が組み込みをバージョン管理する方法の詳細については、[組み込みのバージョン管理](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)に関する記事を参照してください。

## <a name="parameters"></a>パラメーター

パラメーターによって、ポリシー定義の数を減らし、ポリシーの管理を単純化できます。 1 つのフォームにあるフィールドのようなパラメーター `name`、`address``city``state` を考えてみてください。 これらのパラメーターは常に同じままですが、その値はフォームの個々の入力に基づいて変わります。
パラメーターは、ポリシーの作成時と同じように機能します。 ポリシー定義にパラメーターを含めることで、別の値を使用してさまざまなシナリオについてポリシーを再利用できます。

> [!NOTE]
> パラメーターは、既存の割り当て済み定義に追加できます。 新しいパラメーターには、**defaultValue** プロパティを含める必要があります。 これにより、ポリシーまたはイニシアティブの既存の割り当てが間接的に無効になることを防ぎます。

### <a name="parameter-properties"></a>パラメーターのプロパティ

パラメーターには、ポリシー定義内で使用される次のプロパティがあります。

- `name`:お使いのパラメーターの名前。 ポリシー規則内の `parameters` デプロイ関数によって使用されます。 詳しくは、[パラメーター値の使用](#using-a-parameter-value)に関するページをご覧ください。
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

ポリシー規則では、次の `parameters` 関数構文でパラメーターを参照します。

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

このサンプルでは、「[パラメーターのプロパティ](#parameter-properties)」に示されていた **allowedLocations** パラメーターを参照します。

### <a name="strongtype"></a>strongType

`metadata` プロパティの中で、**strongType** を使用して、Azure portal 内でオプションの複数選択リストを提供できます。 **strongType** には、サポートされる "_リソースの種類_" または許可される値を指定できます。 ある "_リソースの種類_" が **strongType** に対して有効かどうかを確認するには、[Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider) を使用します。 _リソースの種類_ **strongType** の形式は `<Resource Provider>/<Resource Type>` です。 たとえば、「 `Microsoft.Network/virtualNetworks/subnets` 」のように入力します。

**Get-AzResourceProvider** によって返されない一部の "_リソースの種類_" もサポートされています。 その種類には、次のようなものがあります。

- `Microsoft.RecoveryServices/vaults/backupPolicies`

"_リソースの種類_" でない **strongType** の許可される値には、次のものがあります。

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>定義の場所

イニシアティブまたはポリシーを作成する際には、定義の場所を指定する必要があります。 定義の場所は、管理グループまたはサブスクリプションにする必要があります。 この場所によって、イニシアティブまたはポリシー定義を割り当てることができるスコープが決まります。 リソースは、割り当て対象とする定義の場所のダイレクト メンバーか、その階層内の子である必要があります。

定義の場所が:

- **サブスクリプション**の場合 - そのサブスクリプション内のリソースだけを、ポリシーに割り当てることができます。
- **管理グループ**の場合 - 子管理グループと子サブスクリプション内のリソースだけを、ポリシーに割り当てることができます。 ポリシー定義を複数のサブスクリプションに適用する予定がある場合、その場所はサブスクリプションを含む管理グループである必要があります。

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

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

**less**、**lessOrEquals**、**greater**、**greaterOrEquals** では、プロパティの種類が条件の種類と一致しない場合、エラーがスローされます。 文字列比較は、`InvariantCultureIgnoreCase` を使用して行われます。

**like** 条件と **notLike** 条件を使用する場合は、値にワイルドカード (`*`) を指定できます。
値に複数のワイルドカード (`*`) を指定することはできません。

**match** 条件と **notMatch** 条件を使用する場合は、任意の数字と一致する `#`、任意の文字と一致する `?`、すべての文字と一致する `.` のほか、一致させる具体的な文字を指定することができます。 **match** と **notMatch** では大文字と小文字が区別されますが、_stringValue_ を評価するその他すべての条件では大文字と小文字が区別されません。 大文字と小文字が区別されない代替手段は、**matchInsensitively** と **notMatchInsensitively** で使用できます。

**\[\*\] エイリアス**配列フィールド値では、配列内の各要素は、要素間で論理**積**を使用して個別に評価されます。 詳細については、「[\[\*\] エイリアスの評価](../how-to/author-policies-for-arrays.md#evaluating-the--alias)」を参照してください。

### <a name="fields"></a>フィールド

条件は、フィールドを使用して構成されます。 フィールドでは、リソース要求ペイロード内のプロパティが照合され、リソースの状態が記述されます。

次のフィールドがサポートされています。

- `name`
- `fullName`
  - リソースのフル ネームを返します。 リソースのフル ネームは、親リソースの名前が付加されたリソース名です ("myServer/myDatabase" など)。
- `kind`
- `type`
- `location`
  - 場所に依存しないリソースに対しては **global** を使用します。
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
  - 例: `tags['''My.Apostrophe.Tag''']`。 **'My.Apostrophe.Tag'** はタグの名前です。
- プロパティのエイリアス: 一覧については、「[エイリアス](#aliases)」を参照してください。

> [!NOTE]
> `tags.<tagName>`、`tags[tagName]`、および`tags[tag.with.dots]` は、タグ フィールドを宣言する方法としてまだ受け付けられます。 ただし、推奨される式は上に示したものです。

#### <a name="use-tags-with-parameters"></a>パラメーターを含むタグを使用する

パラメーター値をタグ フィールドに渡すことができます。 タグ フィールドにパラメーターを渡すと、ポリシー割り当ての間のポリシー定義の柔軟性が向上します。

次の例では、`concat` を使用して、**tagName** パラメーターの値で指定されているタグのタグ フィールド参照が作成されています。 そのタグが存在しない場合は、**modify** 効果が使用され、`resourcegroup()` 参照関数を使用することにより監査対象のリソースの親リソース グループで設定されている同じ名前付きタグの値を使用してタグが追加されます。

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>値

条件は、**value** を使用して形成することもできます。 **value** では、[パラメーター](#parameters)、[サポートされるテンプレート関数](#policy-functions)、またはリテラルに対する条件をチェックします。 **value** は、サポートされる任意の[条件](#conditions)と組み合わせられます。

> [!WARNING]
> _テンプレート関数_ の結果がエラーの場合、ポリシーの評価は失敗します。 評価の失敗は、暗黙的な **deny** です。 詳細については、「[テンプレート エラーの回避](#avoiding-template-failures)」を参照してください。 新しいポリシーの定義をテストしたり検証したりしている間、失敗となった評価の影響が新しいリソースや更新されたリソースに波及するのを避けるには、[enforcementMode](./assignment-structure.md#enforcement-mode) として **DoNotEnforce** を使用してください。

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
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>テンプレート エラーの回避

**value** で_テンプレート関数_を使用することにより、入れ子になった多数の複雑な関数が可能になります。 _テンプレート関数_ の結果がエラーの場合、ポリシーの評価は失敗します。 評価の失敗は、暗黙的な **deny** です。 特定のシナリオでエラーが発生する **value** の例は、以下のとおりです。

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

上述のポリシー規則の例では、[substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) を使用して **name** の最初の 3 文字を **abc** と比較しています。 **name** が 3 文字よりも短い場合、`substring()` 関数はエラーとなります。 このエラーにより、ポリシーは **deny** となります。

3 文字よりも短い [name](../../../azure-resource-manager/templates/template-functions-logical.md#if) をエラーにせず、**name** の最初の 3 文字が **abc** と等しいかどうかを確認するには、代わりに **if()** 関数を使用します。

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

変更したポリシー規則では、3 文字未満の値の `if()` を取得する前に、**によって**name `substring()`の長さが確認されます。 **name** が短すぎる場合、代わりに "not starting with abc"\(abc で開始されていません\) が返され、**abc** と比較されます。 **abc** で開始しない短い名前のリソースも、ポリシー規則でエラーとなりますが、評価時にはエラーが発生しなくなります。

### <a name="count"></a>Count

条件式を満たすリソース ペイロード内の配列のメンバー数をカウントする条件は、**count** 式を使用して作成することができます。 通常のシナリオでは、条件を満たす配列メンバーの数が、"at least one of" (少なくとも 1 つ)、"exactly one of" (1 つだけ)、"all of" (すべて)、"none of" (ない) のそれぞれに該当するかどうかを確認します。 **count** では、ある条件式の各 [\[\*\] alias](#understanding-the--alias) 配列メンバーを評価して _true_ の結果を合計し、式の演算子と比較します。 **count** 式は 1 つの **policyRule** 定義に最大 3 回まで追加できます。

**count** 式の構造は次の通りです。

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

**count** では、次のプロパティを使用します。

- **count.field** (必須):配列へのパスを含みます。また、配列のエイリアスである必要があります。 配列が含まれていない場合、式の評価は _false_ となり、条件式は考慮されません。
- **count.where** (オプション):**count.field** の [\[\*\] alias](#understanding-the--alias) の各配列メンバーをそれぞれ評価するための条件式です。 このプロパティが指定されていない場合、"field" のパスを持つすべての配列メンバーの評価は _true_ になります。 このプロパティ内では、任意の[条件](../concepts/definition-structure.md#conditions)を使用できます。
  このプロパティ内では、[論理演算子](#logical-operators)を使用して複雑な評価要件を作成できます。
- **\<condition\>** (必須):値は、**count.where** 条件式を満たした項目数と比較されます。 数値の[条件](../concepts/definition-structure.md#conditions)を使用する必要があります。

#### <a name="count-examples"></a>カウントの例

例 1:配列が空かどうかをチェックします

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

例 2:1 つの配列メンバーだけが条件式を満たすかどうかをチェックします

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

例 3: 少なくとも 1 つの配列メンバーが条件式を満たすかどうかをチェックします

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

例 4: すべてのオブジェクト配列メンバーが条件式を満たすかどうかをチェックします

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

例 5:少なくとも 1 つの配列メンバーが、条件式内の複数のプロパティと一致するかどうかをチェックします

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>結果

Azure Policy では、次の種類の効果をサポートしています。

- **append** は定義済みのフィールド セットを要求に追加します。
- **Audit**: アクティビティ ログ内に警告イベントを生成しますが、要求は失敗しません。
- **AuditIfNotExists**: 関連するリソースが存在しない場合、アクティビティ ログに警告イベントを生成します
- **Deny** はアクティビティ ログでイベントを生成し、要求は失敗します
- **DeployIfNotExists**: 関連するリソースが存在しない場合、リソースをデプロイします
- **Disabled**: リソースがポリシー規則に準拠しているかどうかを評価しません。
- **EnforceOPAConstraint** (プレビュー): Azure 上の自己管理型 Kubernetes クラスター用に、Gatekeeper v3 を使用して Open Policy Agent アドミッション コントローラーを構成します (プレビュー)
- **EnforceRegoPolicy** (プレビュー): Azure Kubernetes Service で Gatekeeper v2 を使用して Open Policy Agent アドミッション コントローラーを構成します
- **Modify**: リソースで定義されているタグを追加、更新、または削除します。

各効果の詳細、評価の順序、プロパティ、例については、「[Azure Policy の効果について](effects.md)」を参照してください。

### <a name="policy-functions"></a>ポリシー関数

ポリシー規則では、次の関数およびユーザー定義関数を除くすべての [Resource Manager テンプレート関数](../../../azure-resource-manager/templates/template-functions.md)を使用できます。

- copyIndex()
- deployment()
- list*
- newGuid()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> これらの関数は、**deployIfNotExists** ポリシー定義のテンプレートのデプロイの `details.deployment.properties.template` の部分で引き続き使用できます。

次の関数は、ポリシー規則で使用できますが、Azure Resource Manager テンプレート (ARM テンプレート) での使用方法とは異なります。

- `utcNow()` - ARM テンプレートとは異なり、このプロパティは _defaultValue_ の外部で使用できます。
  - 現在の日時に設定されているユニバーサル ISO 8601 日時形式 'yyyy-MM-ddTHH:mm:ss.fffffffZ' の文字列が返されます。

次の関数は、ポリシー ルールでのみ使用できます。

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**: [必須] 文字列 - ユニバーサル ISO 8601 日時形式 'yyyy-MM-ddTHH:mm:ss.fffffffZ' の文字列
  - **numberOfDaysToAdd**: [必須] 整数 - 追加する日数
- `field(fieldName)`
  - **fieldName**: [必須] 文字列 - 取得する[フィールド](#fields)の名前
  - そのフィールドの値を、If 条件による評価の対象となっているリソースから返します。
  - `field` は、主に **AuditIfNotExists** と **DeployIfNotExists** で、評価されるリソースのフィールドを参照するために使用されます。 使用例については、「[DeployIfNotExists の例](effects.md#deployifnotexists-example)」をご覧ください。
- `requestContext().apiVersion`
  - ポリシーの評価をトリガーした要求の API バージョンを返します (例: `2019-09-01`)。
    この値は、PUT または PATCH 要求で、リソースの作成または更新時の評価に使用された API バージョンになります。 既存のリソースに対するコンプライアンスの評価中は、常に最新バージョンの API が使用されます。
  
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

- Visual Studio Code 用の Azure Policy 拡張機能 (推奨)

  [Visual Studio Code 用の Azure Policy 拡張機能](../how-to/extension-for-vscode.md)を使用してリソース プロパティのエイリアスの表示と検出を行う方法について説明します。

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Visual Studio Code 用の Azure Policy 拡張機能" border="false":::

- Azure Resource Graph

  `project` 演算子を使用して、リソースの **エイリアス**を表示します。

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

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
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>[*] エイリアスについて

利用できるいくつかのエイリアスには、通常の名前で表示されるバージョンと、それに **\[\*\]** が添付された別のバージョンがあります。 次に例を示します。

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

"通常" のエイリアスでは、フィールドは 1 つの値として表されます。 このフィールドは完全一致の比較シナリオ用で、値のセット全体を正確に定義する必要があります。それ以上でもそれ以下でもありません。

**\[\*\]** エイリアスにより、配列内の各要素の値および各要素の特定のプロパティとの比較が可能になります。 このアプローチでは、"全くない"、"1 つ以上が" または "すべてが" のシナリオで、要素のプロパティを比較できます。 もっと複雑なシナリオでは、[count](#count) 条件式を使用します。 例では、**ipRules\[\*\]** を使用して、すべての _action_ が _Deny_ であるかどうかが検証されますが、存在している規則の数または IP の _value_ は検証されません。
このサンプル規則では、**ipRules\[\*\].value** が **10.0.4.1** であるすべての一致がチェックされ、1 つ以上の一致が検索されない場合のみ、**effectType** が適用されます。

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

## <a name="next-steps"></a>次のステップ

- [イニシアチブ定義の構造](./initiative-definition-structure.md)を参照してください
- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Policy の効果について](effects.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
