---
title: デプロイ シーケンス順序について
description: ブループリント定義が経過するライフサイクルと各ステージの詳細について説明します。
author: dcthegeek
ms.author: dacoulte
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b05a7ce260e8cc1da4ac8a0c186694ae097a3b1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721297"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Azure ブループリントでのデプロイ シーケンスについて

Azure Blueprints では、ブループリント定義の割り当てを処理するときに、**シーケンス順序**を使用してリソースの作成順序を決定します。 この記事では、次の概念について説明します。

- 使用される既定のシーケンス順序
- 順序をカスタマイズする方法
- カスタマイズされた順序を処理する方法

JSON の例では、次の変数を独自の値で置き換える必要があります。

- `{YourMG}` - 実際の管理グループの名前に置き換えます

## <a name="default-sequencing-order"></a>既定のシーケンス順序

ブループリント定義に成果物をデプロイする順序を指定するディレクティブが含まれていない場合、またはそのディレクティブが null の場合は、次の順序が使用されます。

- サブスクリプション レベルの**ロールの割り当て**成果物が、成果物の名前で並べ替えられます
- サブスクリプション レベルの**ポリシーの割り当て**成果物が、成果物の名前で並べ替えられます
- サブスクリプション レベルの **Azure Resource Manager テンプレート**成果物が、成果物の名前で並べ替えられます
- **リソース グループ**成果物 (子成果物を含む) が、プレースホルダーの名前で並べ替えられます

各**リソース グループ**内では、そのリソース グループ内に作成される成果物に対して次のようなシーケンス順序が適用されます。

- リソース グループの子の**ロールの割り当て**成果物が、成果物の名前で並べ替えられます
- リソース グループの子の**ポリシー割り当て**成果物が、成果物の名前で並べ替えられます
- リソース グループの子の**Azure Resource Manager テンプレート**成果物が、成果物の名前で並べ替えられます

> [!NOTE]
> [artifacts()](../reference/blueprint-functions.md#artifacts) の使用により、参照される成果物の暗黙的な依存関係が作成されます。

## <a name="customizing-the-sequencing-order"></a>シーケンス順序のカスタマイズ

大規模なブループリント定義を作成するときは、リソースを特定の順序で作成することが必要になる場合があります。 このシナリオの最も一般的な使用パターンは、ブループリント定義にいくつかの Azure Resource Manager テンプレートが含まれている場合です。 ブループリントでは、シーケンス順序を定義することで、このパターンを処理します。

この順序は、JSON 内で `dependsOn` プロパティを定義することで実現します。 このプロパティは、リソース グループ用のブループリント定義、および成果物オブジェクトによってサポートされています。 `dependsOn` は、特定の成果物が作成される前に作成する必要がある成果物の名前で構成される文字列配列です。

### <a name="example---ordered-resource-group"></a>例 - 順序指定されたリソース グループ

このブループリント定義の例には、`dependsOn` の値を宣言することでカスタムのシーケンス順序を定義されたリソース グループと、標準のリソース グループの両方が含まれています。 この場合、**assignPolicyTags** という名前の成果物が、**ordered-rg** リソース グループの前に処理されます。
**standard-rg** は、既定のシーケンス順序ごとに処理されます。

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>例 - カスタムの順序で並べられた成果物

この例は、Azure Resource Manager テンプレートに依存するポリシー成果物です。 既定の順序では、Azure Resource Manager テンプレートの前に、ポリシー成果物が作成されます。 この順序では、ポリシー成果物は Azure Resource Manager テンプレートが作成されるまで待機します。

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>例 - リソース グループに依存するサブスクリプション レベルのテンプレート成果物

この例は、サブスクリプション レベルでデプロイされた Resource Manager テンプレートを対象とし、リソース グループに依存します。 既定の順序付けでは、サブスクリプション レベルの成果物は、任意のリソース グループとそのリソース グループの子成果物の前に作成されます。 リソース グループは、次のようにブループリント定義で定義されます。

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

**wait-for-me** リソース グループに依存するサブスクリプション レベルのテンプレート成果物は、次のように定義されます。

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/subtemplateWaitForRG",
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "name": "subtemplateWaitForRG"
}
```

## <a name="processing-the-customized-sequence"></a>カスタマイズされたシーケンスの処理

作成プロセスでは、トポロジカル ソートを使用して、ブループリント成果物の依存関係グラフが作成されます。 この確認により、リソース グループと成果物の各レベルの依存関係がサポートされます。

成果物の依存関係が既定の順序を変更しないと宣言されている場合、変更は加えられません。 たとえば、サブスクリプション レベルのポリシーに依存するリソース グループです。 もう 1 つの例は、リソース グループ 'standard rg' 子ロールの割り当てに依存しているリソース グループ ' standard rg' 子ポリシーの割り当てです。 どちらの場合も、`dependsOn` によって既定のシーケンス順序が変更されることはなく、何の変更も加えられません。

## <a name="next-steps"></a>次の手順

- [ブループリントのライフサイクル](lifecycle.md)を参照する。
- [静的および動的パラメーター](parameters.md)の使用方法を理解する。
- [ブループリントのリソース ロック](resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。