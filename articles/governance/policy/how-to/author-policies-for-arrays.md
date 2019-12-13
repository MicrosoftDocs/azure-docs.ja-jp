---
title: リソースの配列プロパティのポリシーを作成する
description: Azure Policy 定義ルールを使用して、配列パラメーターおよび配列の言語式を処理し、[*] エイリアスを評価し、要素を付加する方法について説明します。
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 915f50945e0c2520fbda09c4db1b581c9381073b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873099"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Azure リソースの配列プロパティのポリシーを作成する

通常、Azure Resource Manager のプロパティは文字列とブール値として定義されます。 一対多のリレーションシップが存在するときには、複雑なプロパティは代わりに配列として定義されます。 Azure Policy では、配列は以下の複数の方法で使用されます。

- 複数のオプションを提供する、[定義パラメーター](../concepts/definition-structure.md#parameters)の型
- **in** または **notIn** の条件を使用した[ポリシー ルール](../concepts/definition-structure.md#policy-rule)の一部
- [\[\*\] エイリアス](../concepts/definition-structure.md#understanding-the--alias)を評価して以下を評価するポリシー ルールの一部。
  - **None**、**Any**、または **All** などのシナリオ
  - **count** を使用した複雑なシナリオ
- 既存の配列を置き換えたり追加する [append 効果](../concepts/effects.md#append)において

この記事では、Azure Policy によるそれぞれの使用について説明し、複数の定義例を紹介します。

## <a name="parameter-arrays"></a>パラメーター配列

### <a name="define-a-parameter-array"></a>パラメーター配列を定義する

配列としてパラメーターを定義すると、複数の値が必要なときにポリシーを柔軟に使用することができます。
このポリシー定義により、パラメーター **allowedLocations** が表す単一の場所がどこでも可能になり、既定では _eastus2_ になります。

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

**type** が _string_ だったので、ポリシーの割り当て時には 1 つの値しか設定できません。 このポリシーが割り当てられると、スコープ内のリソースは、単一の Azure リージョン内でのみ許可されます。 ほとんどのポリシー定義は、_eastus2_、_eastus_、_westus2_ を許可するなど、承認済みのオプションの一覧を許可する必要があります。

複数のオプションを許可するポリシー定義を作成するには、_array_ **type** を使用します。 同じポリシーは、次のように書き換えることができます。

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> ポリシー定義が保存されると、パラメーターの **type** プロパティは変更できません。

この新しいパラメーター定義は、ポリシーの割り当て中に複数の値を取ります。 配列プロパティ **allowedValues** が定義されている場合、割り当て中に利用できる値は、事前に定義された選択肢の一覧にさらに制限されます。 **allowedValues** の使用は省略可能です。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>割り当て中にパラメーター配列に値を渡す

Azure portal からポリシーを割り当てるときに、**type** _array_ のパラメーターは単一のテキストボックスとして表示されます。 ヒントには「値を区切るには ; を使用してください (例: ロンドン;New York)」と示されます。 _eastus2_、_eastus_、および _westus2_ の許可されている場所の値をパラメーターに渡すには、次の文字列を使用します。

`eastus2;eastus;westus2`

パラメーター値の形式は、Azure CLI、Azure PowerShell、または REST API を使用する場合で異なります。 値は、パラメーターの名前も含んだ JSON 文字列で渡されます。

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

各 SDK でこの文字列を使用するには、次のコマンドを使用します。

- Azure CLI:コマンド [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) とパラメーター **params**
- Azure PowerShell:コマンドレット [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) とパラメーター **PolicyParameter**
- REST API:要求本文の一部としての _PUT_ [作成](/rest/api/resources/policyassignments/create)操作において、**properties.parameters** プロパティの値として

## <a name="policy-rules-and-arrays"></a>ポリシー ルールと配列

### <a name="array-conditions"></a>配列条件

パラメーターの _array_
**type** を一緒に使用できるポリシー ルール[条件](../concepts/definition-structure.md#conditions)は、`in` と `notIn` に限られます。 条件 `equals` を持つ次のポリシー定義を例として取り上げます。

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Azure portal からこのポリシー定義を作成しようとすると、次のエラー メッセージのようなエラーが発生します。

- 「The policy '{GUID}' could not be parameterized because of validation errors.(ポリシー「{GUID}」は検証エラーのためにパラメーター化できませんでした。) Please check if policy parameters are properly defined.(ポリシー パラメーターが正しく定義されていることを確認してください。) The inner exception 'Evaluation result of language expression '[parameters('allowedLocations')]' is type 'Array', expected type is 'String'.'.(内部例外「言語式「[parameters('allowedLocations')]」の評価結果は型「Array」ですが、予想された型は「String」です。)」

条件 `equals` の予想される **type** は _string_ です。 **allowedLocations** は **type** _array_ と定義されるので、ポリシー エンジンは、言語式を評価してエラーをスローします。 `in` および `notIn` の条件では、ポリシー エンジンは言語式で **type** _array_ を予想します。 このエラー メッセージを解決するには、`equals` を `in` または `notIn` のどちらかに変更します。

### <a name="evaluating-the--alias"></a>[*] エイリアスの評価

名前に **\[\*\]** が付けられたエイリアスは、**type** が _array_ であることを示します。 配列全体の値を評価する代わりに、 **\[\*\]** は配列の各要素を評価できるようにします。 この項目ごとの評価が役立つ標準的なシナリオは次の 3 つです。なし、任意、すべて。 複雑なシナリオでは、[count](../concepts/definition-structure.md#count) を使用します。

**if** ルールが true として評価された場合にのみ、ポリシー エンジンは **then** 内の **effect** をトリガーします。
この事は、 **\[\*\]** が配列の各個々の要素を評価する方法というコンテキストで理解するために重要です。

下のシナリオ テーブルのポリシー ルール例:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

下のシナリオ テーブルの **IpRules** 配列は次のようになります。

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

下の条件例ごとに、`<field>` を `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"` に置き換えます。

以下の結果は、条件と、上記のポリシー ルール例および既存の値の配列とを組み合わせたものです。

|条件 |結果 |説明 |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |なし |1 つの配列要素が false (127.0.0.1 != 127.0.0.1) として、もう 1 つが true (127.0.0.1 != 192.168.1.1) として評価されるので、**notEquals** 条件が _false_ になり、効果はトリガーされません。 |
|`{<field>,"notEquals":"10.0.4.1"}` |ポリシー効果 |両方の配列要素が true (10.0.4.1 != 127.0.0.1 および 10.0.4.1 != 192.168.1.1) として評価されるので、**notEquals** 条件が _true_ になり、効果はトリガーされます。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |ポリシー効果 |1 つの配列要素が true (127.0.0.1 == 127.0.0.1) として、もう 1 つが false (127.0.0.1 == 192.168.1.1) として評価されるので、**Equals** 条件が _false_ になります。 論理演算子は true (**not** _false_) として評価されるので、効果はトリガーされます。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |ポリシー効果 |両方の配列要素が false (10.0.4.1 == 127.0.0.1 および 10.0.4.1 == 192.168.1.1) として評価されるので、**Equals** 条件は _false_ になります。 論理演算子は true (**not** _false_) として評価されるので、効果はトリガーされます。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |ポリシー効果 |1 つの配列要素が false (127.0.0.1 != 127.0.0.1) として、もう 1 つが true (127.0.0.1 != 192.168.1.1) として評価されるので、**notEquals** 条件は _false_ になります。 論理演算子は true (**not** _false_) として評価されるので、効果はトリガーされます。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |なし |両方の配列要素が true (10.0.4.1 != 127.0.0.1 および 10.0.4.1 != 192.168.1.1) として評価されるので、**notEquals** 条件は _true_ になります。 論理演算子は false (**not** _true_) として評価されるので、効果はトリガーされません。 |
|`{<field>,"Equals":"127.0.0.1"}` |なし |1 つの配列要素が true (127.0.0.1 == 127.0.0.1) として、もう 1 つが false (127.0.0.1 == 192.168.1.1) として評価されるので、**Equals** 条件が _false_ になり、効果はトリガーされません。 |
|`{<field>,"Equals":"10.0.4.1"}` |なし |両方の配列要素が false (10.0.4.1 == 127.0.0.1 および 10.0.4.1 == 192.168.1.1) として評価されるので、**Equals** 条件が _false_ になり、効果はトリガーされません。 |

## <a name="the-append-effect-and-arrays"></a>append 効果と配列

[append 効果](../concepts/effects.md#append)の動作は、**details.field** が **\[\*\]** エイリアスであるかどうかに応じて異なります。

- **\[\*\]** エイリアスでない場合、append は配列全体を **value** プロパティに置き換えます
- **\[\*\]** エイリアスである場合、append は **value** プロパティを既存の配列に追加するか、新しい配列を作成します

詳細については、「[Append の例](../concepts/effects.md#append-examples)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [準拠していないリソースを修復する](remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。