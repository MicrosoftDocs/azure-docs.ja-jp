---
title: チュートリアル:タグ ガバナンスを管理する
description: このチュートリアルでは、新しいリソースと既存のリソースに対してタグ ガバナンス モデルを作成して適用するために、Azure Policy の Modify 効果を使用します。
ms.date: 10/05/2020
ms.topic: tutorial
ms.openlocfilehash: 9efeb27151cd3a32741f1bdb6d1d90d3304c5874
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91876279"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>チュートリアル:Azure Policy を使用してタグ ガバナンスを管理する

[タグ](../../../azure-resource-manager/management/tag-resources.md)は、Azure リソースを分類してまとめるうえで重要な要素です。 [タグ管理のベスト プラクティス](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)に従う際に、タグは Azure Policy を使用してビジネス ポリシーを適用したり、[Cost Management を使用してコストを追跡](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources)したりするための基礎にすることができます。
タグを使用する方法や理由にかかわらず、Azure リソースのタグをすばやく追加、変更、および削除できることが重要です。 Azure リソースでタグ付けがサポートされているかどうかを確認するには、[タグのサポート](../../../azure-resource-manager/management/tag-support.md)に関するページをご覧ください。

Azure Policy の [Modify](../concepts/effects.md#modify) 効果は、現在のリソース ガバナンスの段階に関係なく、タグのガバナンスを支援するように設計されています。 **Modify** は、次の場合に役立ちます。

- クラウドを初めて使用し、タグ ガバナンスがない
- タグ ガバナンスのない数千のリソースが既にある
- 変更が必要な分類が既にある

このチュートリアルでは、次のタスクを実行します。

> [!div class="checklist"]
> - ビジネス要件を特定する
> - 各要件をポリシー定義にマップする
> - タグ ポリシーをイニシアチブにグループ化する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="identify-requirements"></a>要件を特定する

ガバナンス管理の適切な実装と同様に、要件はビジネス ニーズに基づくものであり、技術的制御を作成する前に十分に理解しておく必要があります。 このシナリオのチュートリアルでは、次のようなビジネス要件があります。

- すべてのリソースで必須の 2 つのタグ:_CostCenter_ と _Env_
- _CostCenter_ は、すべてのコンテナーと個々のリソースに存在している必要があります
  - リソースは、それらが存在するコンテナーから継承されますが、個別に上書きされる可能性があります
- _Env_ は、すべてのコンテナーと個々のリソースに存在している必要があります
  - リソースでは、コンテナーの名前付けスキームによって環境が決定され、上書きされない可能性があります
  - コンテナー内のすべてのリソースは同じ環境に含まれています

## <a name="configure-the-costcenter-tag"></a>CostCenter タグを構成する

Azure Policy によって管理される Azure 環境に固有の観点では、_CostCenter_ タグ要件で次の結果が求められます。

- _CostCenter_ タグが欠落しているリソース グループを拒否する
- 欠落している場合は、親リソース グループから _CostCenter_ タグを追加するようにリソースを変更する

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>CostCenter タグが欠落しているリソース グループを拒否する

リソース グループの _CostCenter_ はリソース グループの名前では判断できないため、リソース グループを作成するには要求でタグが定義されている必要があります。 [Deny](../concepts/effects.md#deny) 効果を使用する次のポリシー規則では、_CostCenter_ タグがないリソース グループを作成または更新できないようにします。

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> このポリシー規則はリソース グループを対象とするため、ポリシー定義の _モード_ は、'Indexed' ではなく 'All' にする必要があります。

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>欠落している場合は CostCenter タグを継承するようにリソースを変更する

2 つ目の _CostCenter_ が必要なのは、欠落しているときに親リソース グループからタグを継承するリソースの場合です。 リソースでタグが既に定義されている場合は、それが親リソース グループのものとは異なる場合でも、そのままにしておく必要があります。 次のポリシー規則では、[Modify](../concepts/effects.md#modify) を使用します。

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

このポリシー規則では、**addOrReplace** ではなく、**add** 操作を使用します。これは、既存のリソースを [修復する](../how-to/remediate-resources.md)ときにタグ値が存在する場合は、それを変更しないためです。 また、`[resourcegroup()]` テンプレート関数を使用して、親リソース グループからタグ値を取得します。

> [!NOTE]
> このポリシー規則は、タグをサポートするリソースを対象とするため、ポリシー定義の _モード_ は 'Indexed' である必要があります。 また、この構成では、必ず、このポリシーでリソース グループをスキップするようにします。

## <a name="configure-the-env-tag"></a>Env タグを構成する

Azure Policy によって管理される Azure 環境に固有の観点では、_Env_ タグ要件で次の結果が求められます。

- リソース グループの名前付けスキームに基づいて、リソース グループの _Env_ タグを変更する
- リソース グループ内のすべてのリソースの _Env_ タグを、親リソース グループと同じものに変更する

### <a name="modify-resource-groups-env-tag-based-on-name"></a>名前に基づいてリソース グループの Env タグを変更する

Azure 環境内に存在する環境ごとに、[Modify](../concepts/effects.md#modify) ポリシーが必要になります。 それぞれの Modify ポリシーは、このポリシー定義のようになります。

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        },
        {
            "field": "tags['Env']",
            "notEquals": "Production"
        }

    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> このポリシー規則はリソース グループを対象とするため、ポリシー定義の _モード_ は、'Indexed' ではなく 'All' にする必要があります。

このポリシーは、`prd-` の実稼働リソースに使われるサンプルの名前付けスキームを使用するリソース グループのみと一致します。 この例では 1 つの **like** ではなく、複数の **match** 条件を使用して、より複雑な名前付けスキームを実現できます。

### <a name="modify-resources-to-inherit-the-env-tag"></a>Env タグを継承するようにリソースを変更する

ビジネス要件では、すべてのリソースに対して、親リソース グループにある _Env_ タグを含めることが求められます。 このタグは上書きできないため、**addOrReplace** 操作を、[Modify](../concepts/effects.md#modify) 効果と共に使用します。 サンプルの Modify ポリシーは、次の規則のようになります。

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> このポリシー規則は、タグをサポートするリソースを対象とするため、ポリシー定義の _モード_ は 'Indexed' である必要があります。 また、この構成では、必ず、このポリシーでリソース グループをスキップするようにします。

このポリシー規則では、_Env_ タグの親リソース グループ値がないか、_Env_ タグが欠落しているリソースを検索します。 一致するリソースでは _Env_ タグが親リソース グループの値に設定されます。これは、タグがリソースに既に存在していても値が異なる場合でも同様です。

## <a name="assign-the-initiative-and-remediate-resources"></a>イニシアチブを割り当てて、リソースを修復する

上記のタグ ポリシーが作成されたら、それらをタグ ガバナンスの 1 つのイニシアチブに結合し、管理グループまたはサブスクリプションに割り当てます。 その後、イニシアチブとそれに含まれるポリシーによって、既存のリソースのコンプライアンスが評価され、ポリシー規則の **if** プロパティに一致する新規または更新されたリソースの要求が変更されます。 しかし、ポリシーでは、定義済みのタグの変更によって、既存の準拠していないリソースが自動的に更新されることはありません。

[deployIfNotExists](../concepts/effects.md#deployifnotexists) ポリシーと同様に、**Modify** ポリシーでは修復タスクを使用して、既存の準拠していないリソースを変更します。 [リソースを修復する方法](../how-to/remediate-resources.md)の手順に従って、準拠していない **Modify** リソースを識別し、定義した分類に対するタグを修正します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルのリソースに対する作業が完了した場合は、次の手順を使用して、ここで作成した割り当てまたは定義をすべて削除してください。

1. Azure Policy ページの左側にある **[作成]** の下の **[定義]** (または割り当てを削除する場合は **[割り当て]** ) を選択します。

1. 削除する新しいイニシアティブまたはポリシー定義 (または割り当て) を見つけます。

1. 行を右クリックするか、定義 (または割り当て) の末尾にある省略記号を選択し、 **[定義の削除]** (または **[割り当ての削除]** ) を選択します。

## <a name="review"></a>確認

このチュートリアルでは、以下のタスクについて学習しました。

> [!div class="checklist"]
> - ビジネス要件を特定する
> - 各要件をポリシー定義にマップする
> - タグ ポリシーをイニシアチブにグループ化する

## <a name="next-steps"></a>次のステップ

ポリシー定義の構造の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Policy の定義の構造](../concepts/definition-structure.md)