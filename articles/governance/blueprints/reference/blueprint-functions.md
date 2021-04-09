---
title: Azure Blueprints の関数
description: Azure Blueprints の定義と割り当てに使用できる関数について説明します。
ms.date: 01/27/2021
ms.topic: reference
ms.openlocfilehash: 92cb906e87179073b7a69aa0bd4eab22c77087f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919276"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Azure Blueprints で使用する関数

Azure Blueprints では、ブループリントの定義をより動的にする関数が提供されます。 これらの関数は、ブループリントの定義とブループリント アーティファクトで使用するためのものです。 Azure Resource Manager テンプレート (ARM テンプレート) のアーティファクトでは、ブループリントのパラメーターによって動的な値を取得するだけでなく、Resource Manager の関数の使用も完全にサポートされています。

次の関数がサポートされています。

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

そのブループリント アーティファクトの出力が設定されたプロパティのオブジェクトが返されます。

> [!NOTE]
> `artifacts()` 関数は、ARM テンプレート内からは使用できません。 Azure PowerShell または REST API によってブループリントを [Blueprints-as-code](https://github.com/Azure/azure-blueprints/blob/master/README.md) の一部として管理する場合、関数は、ブループリント定義の JSON 内またはアーティファクト JSON 内のみで使用できます。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| artifactName |はい |string |ブループリント アーティファクトの名前。 |

### <a name="return-value"></a>戻り値

出力プロパティのオブジェクト。 **outputs** のプロパティは、参照されているブループリント アーティファクトの種類に依存します。 すべての種類は次の形式に従います。

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>ポリシー割り当てアーティファクト

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="arm-template-artifact"></a>ARM テンプレートのアーティファクト

返されるオブジェクトの **outputs** のプロパティは、ARM テンプレート内で定義されており、デプロイによって返されます。

#### <a name="role-assignment-artifact"></a>ロールの割り当てアーティファクト

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>例

次のサンプル出力プロパティを含む ID が _myTemplateArtifact_ の ARM テンプレート アーティファクト:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

_myTemplateArtifact_ サンプルからデータを取得する例を次にいくつか示します。

| 式 | Type | 値 |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "my string value" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "my value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

複数の文字列値を結合し、連結された文字列を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| string1 |はい |string |連結の最初の値。 |
| 残りの引数 |いいえ |string |連結する順番での追加の値 |

### <a name="return-value"></a>戻り値

連結された値の文字列。

### <a name="remarks"></a>解説

Azure Blueprint 関数は、文字列でのみ動作する点が、ARM テンプレート関数と異なります。

### <a name="example"></a>例

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

ブループリント パラメーターの値が返されます。 指定するパラメーター名が、ブループリント定義またはブループリント アーティファクトで定義されている必要があります。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| parameterName |はい |string |返されるパラメーターの名前。 |

### <a name="return-value"></a>戻り値

指定したブループリント パラメーターまたはブループリント アーティファクト パラメーターの値。

### <a name="remarks"></a>解説

Azure Blueprint 関数は、ブループリント パラメーターでのみ動作する点が、ARM テンプレート関数と異なります。

### <a name="example"></a>例

ブループリント定義でパラメーター _principalIds_ を定義します。

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

次に、ブループリント アーティファクトの `parameters()` に対する引数として _principalIds_ を使います。

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

現在のリソース グループを表すオブジェクトを返します。

### <a name="return-value"></a>戻り値

返されるオブジェクトの形式は次のとおりです。

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>解説

Azure Blueprint 関数は、ARM テンプレート関数と異なります。 `resourceGroup()` 関数は、サブスクリプション レベルのアーティファクトまたはブループリント定義では使用できません。 リソース グループ アーティファクトの一部であるブループリント アーティファクトでのみ使用できます。

`resourceGroup()` 関数の一般的な使用方法は、リソース グループ アーティファクトと同じ場所にリソースを作成することです。

### <a name="example"></a>例

リソース グループの場所を使用するには、ブループリント定義または割り当て中のいずれかで、別のアーティファクトに対する場所として設定し、ブループリント定義でリソース グループのプレースホルダー オブジェクトを宣言します。 次の例では、_NetworkingPlaceholder_ がリソース グループ プレースホルダーの名前です。

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

その後、リソース グループのプレースホルダー オブジェクトを対象とするブループリント アーティファクトのコンテキストで、`resourceGroup()` 関数を使います。 この例では、テンプレート アーティファクトは _NetworkingPlaceholder_ リソース グループにデプロイされ、_NetworkingPlaceholder_ リソース グループの場所が動的に設定されたパラメーター _resourceLocation_ がテンプレートに提供されます。 _NetworkingPlaceholder_ リソース グループの場所は、ブループリント定義で静的に定義されているか、または割り当ての間に動的に定義されています。 いずれの場合も、テンプレート アーティファクトでは、パラメーターとしてその情報が提供され、正しい場所にリソースをデプロイするために使用されます。

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

指定したリソース グループ アーティファクトを表すオブジェクトが返されます。 アーティファクトのコンテキストが必要な `resourceGroup()` とは異なり、この関数は、特定のリソース グループ プレースホルダーのプロパティを、そのリソース グループのコンテキスト内にないときに取得するために使用されます。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| placeholderName |はい |string |取得するリソース グループ アーティファクトのプレースホルダーの名前。 |

### <a name="return-value"></a>戻り値

返されるオブジェクトの形式は次のとおりです。

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>例

リソース グループの場所を使用するには、ブループリント定義または割り当て中のいずれかで、別のアーティファクトに対する場所として設定し、ブループリント定義でリソース グループのプレースホルダー オブジェクトを宣言します。 次の例では、_NetworkingPlaceholder_ がリソース グループ プレースホルダーの名前です。

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

その後、任意のブループリント アーティファクトのコンテキストから `resourceGroups()` 関数を使って、そのリソース グループ プレースホルダー オブジェクトへの参照を取得します。 この例では、テンプレート アーティファクトは _NetworkingPlaceholder_ リソース グループの外側にデプロイされ、_NetworkingPlaceholder_ リソース グループの場所が動的に設定されたパラメーター _artifactLocation_ がテンプレートに提供されます。 _NetworkingPlaceholder_ リソース グループの場所は、ブループリント定義で静的に定義されているか、または割り当ての間に動的に定義されています。 いずれの場合も、テンプレート アーティファクトでは、パラメーターとしてその情報が提供され、正しい場所にリソースをデプロイするために使用されます。

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

現在のブループリント割り当てのサブスクリプションの詳細が返されます。

### <a name="return-value"></a>戻り値

返されるオブジェクトの形式は次のとおりです。

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>例

サブスクリプションの表示名と `concat()` 関数を使って、テンプレート アーティファクトにパラメーター _resourceName_ として渡される名前付け規則を作成します。

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>次のステップ

- [ブループリントのライフサイクル](../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。