---
title: パターン:ポリシー定義を使用してリソースをデプロイする
description: この Azure Policy パターンでは、deployIfNotExists ポリシー定義を使用してリソースをデプロイする方法の例を示します。
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 0a9eec54954b8963f38b3f19a0d0cabffe1092e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89649981"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure Policy パターン: リソースをデプロイする

[deployIfNotExists](../concepts/effects.md#deployifnotexists) 効果を使用すると、準拠していないリソースを作成または更新する場合に、[Azure Resource Manager テンプレート](../../../azure-resource-manager/templates/overview.md) (ARM テンプレート) をデプロイすることができます。 このアプローチは、[deny](../concepts/effects.md#deny) 効果を使用するよりも好ましい場合があります。リソースの作成を継続できる一方で、準拠したものになるように確実にリソースを変更することができるからです。

## <a name="sample-policy-definition"></a>ポリシー定義の例

このポリシー定義では、**field** 演算子を使用して、作成または更新するリソースの `type` を評価します。 このリソースが _Microsoft.Network/virtualNetworks_ である場合、ポリシーは新しいリソースまたは更新するリソースの場所で Network Watcher を検索します。 一致する Network Watcher が見つからない場合は、ARM テンプレートがデプロイされ、不足しているリソースが作成されます。

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>説明

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

**properties.policyRule.then.details** ブロックでは、作成または更新するリソースに関連して **properties.policyRule.if** ブロック内で検索する対象を Azure Policy に指示します。 この例では、リソース グループ **networkWatcherRG** 内に、**field** の `location` が新しいリソースまたは更新するリソースの場所と等しい Network Watcher が存在する必要があります。 `field()` 関数を使用することで、**existenceCondition** は、新しいリソースまたは更新するリソースのプロパティ (具体的には `location` プロパティ) にアクセスできるようになります。

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

**properties.policyRule.then.details** ブロック内の **roleDefinitionIds** "_配列_" プロパティは、含まれる Resource Manager テンプレートをデプロイするためにマネージド ID が必要とする権限をポリシー定義に伝えます。 このプロパティは、テンプレートのデプロイに必要なアクセス許可を持つロールが含まれるように設定する必要がありますが、"最小限の特権の原則" の概念を使用して必要な操作しか実行できないようにする必要があります。

#### <a name="deployment-template"></a>デプロイ テンプレート

ポリシー定義の **deployment** の部分には、次の 3 つの主要コンポーネントを定義する **properties** ブロックがあります。

- **mode** - このプロパティでは、テンプレートの [デプロイ モード](../../../azure-resource-manager/templates/deployment-modes.md)を設定します。

- **template** - このプロパティにはテンプレート自体が含まれます。 この例では、**location** テンプレート パラメーターにより、新しい Network Watcher リソースの場所が設定されます。

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parameters** - このプロパティでは、**template** に提供されるパラメーターを定義します。 パラメーター名は **template** で定義されているものと一致している必要があります。 この例では、パラメーターには、一致するように **location** という名前が付けられています。 **location** の値は、再び `field()` 関数を使用して、評価対象のリソースの値を取得します。これは **policyRule.if** ブロック内の仮想ネットワークに当たります。

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>次のステップ

- その他の[パターンと組み込みの定義](./index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。