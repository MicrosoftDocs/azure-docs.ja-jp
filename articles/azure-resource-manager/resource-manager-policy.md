---
title: "Azure リソース ポリシー | Microsoft Docs"
description: "Azure Resource Manager のポリシーを使用して、デプロイ中に一貫性のあるリソース プロパティが設定されるようにする方法について説明します。 ポリシーは、サブスクリプションまたはリソース グループに適用できます。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 6d459e37b8b39f5d76c4ec86ebb7351c783b81fb
ms.openlocfilehash: 64cb4be184e02519a6c496f8639035201ebb60f8
ms.lasthandoff: 02/15/2017


---
# <a name="resource-policy-overview"></a>リソース ポリシーの概要
リソース ポリシーを使用すると、組織内のリソースの規則を確立することができます。 規則を定義することによって、コストを制御し、リソースをより簡単に管理することができます。 たとえば、特定の種類の仮想マシンのみが許可されるように指定したり、すべてのリソースに特定のタグが付けられることを要求したりすることができます。 ポリシーは、すべての子リソースが継承します。 したがって、リソース グループに適用されたポリシーは、そのリソース グループのすべてのリソースに適用されます。

ポリシーについて理解するための概念は、次の&2; つです。

* ポリシー定義 - いつポリシーが適用され、どのようなアクションが行われるかを記述します
* ポリシー割り当て - ポリシー定義をスコープ (サブスクリプションまたはリソース グループ) に適用します

このトピックでは、ポリシー定義を中心に説明します。 ポリシー割り当てについては、[ポリシーの割り当てと管理](resource-manager-policy-create-assign.md)に関するページを参照してください。

Azure には、いくつかの組み込みのポリシー定義が用意されているので、定義が必要なポリシーの数を減らすことができます。 組み込みのポリシー定義が目的のシナリオでうまく機能する場合は、スコープに割り当てる際にその定義を使用します。

リソースの作成と更新 (PUT 操作と PATCH 操作) を行うときに、ポリシーが評価されます。

> [!NOTE]
> 現時点では、タグ、種類、場所をサポートしていない、Microsoft.Resources/deployments のようなリソースの種類は、ポリシーによる評価の対象となりません。 このサポートは、今後追加される予定です。 下位互換性の問題を回避するためには、ポリシーの作成時に種類を明示的に指定する必要があります。 たとえば、種類が指定されていないタグ ポリシーは、すべての種類に適用されます。 そのような場合、タグをサポートしていない入れ子になったリソースがあり、デプロイ リソースの種類がポリシーの評価に追加されていると、テンプレートのデプロイに失敗する可能性があります。 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>RBAC との違いは何か。
ポリシーとロールベースのアクセス制御 (RBAC) には、いくつかの主要な違いがあります。 RBAC は、さまざまなスコープでの**ユーザー**の操作に焦点を当てています。 たとえば、目的のスコープでリソース グループの共同作成者ロールに追加されると、そのリソース グループに変更を加えられるようになります。 ポリシーは、デプロイ中の**リソース** プロパティに焦点を当てます。 たとえば、ポリシーを介して、プロビジョニング可能なリソースの種類を制御したり、リソースをプロビジョニングできる場所を制限したりできます。 RBAC とは異なり、ポリシーは既定で許可し、明示的に否認するシステムです。 

ポリシーを使用するには、RBAC で認証される必要があります。 具体的には、アカウントには次のものが必要です。

* ポリシーを定義するための `Microsoft.Authorization/policydefinitions/write` アクセス許可
* ポリシーを割り当てるための `Microsoft.Authorization/policyassignments/write` アクセス許可 

これらのアクセス許可は、**共同作成者**ロールには含まれていません。

## <a name="policy-definition-structure"></a>ポリシー定義の構造
ポリシー定義を作成するには、JSON を使用します。 ポリシー定義には、以下のものに対する要素が含まれています。

* parameters
* 表示名
* description
* ポリシー規則
  * 論理評価
  * 効果

次の例は、リソースがデプロイされる場所を制限するポリシーを示しています。

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
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

## <a name="parameters"></a>パラメーター
パラメーターを使用すると、ポリシー定義の数を減らすことで、ポリシーの管理を単純化できます。 リソース プロパティに対してポリシーを定義し (リソースをデプロイできる場所を制限するなど)、定義にパラメーターを含めます。 その後、そのポリシー定義を別のシナリオで再利用する場合は、ポリシーを割り当てるときに別の値を渡します (サブスクリプションに対する&1; 組の場所を指定するなど)。

パラメーターは、ポリシーの定義を作成するときに宣言します。

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

パラメーターの型は、文字列または配列のどちらも可能です。 メタデータ プロパティは、Azure Portal などでわかりやすい情報を表示するために使用されます。 

ポリシー規則では、次の構文でパラメーターを参照します。 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>表示名と説明

ポリシー定義を識別し、使用される際のコンテキストを指定するには、**displayName** と **description** を使用します。

## <a name="policy-rule"></a>ポリシー規則

ポリシー規則は、**If** と **Then** ブロックで構成されています。 **If** ブロックでは、いつポリシーが適用されるかを指定する、1 つ以上の条件を定義します。 これらの条件に論理演算子を適用して、ポリシーのシナリオを細かく定義することができます。 **Then** ブロックでは、**If** 条件が満たされる場合に生じる効果を定義します。

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>論理演算子
サポートされている論理演算子は、次のとおりです。

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

**not** 構文は、条件の結果を反転します。 **allOf** 構文 (**And** 論理演算に似ています) では、すべての条件が真である必要があります。 **anyOf** 構文 (**Or** 論理演算に似ています) では、1 つ以上の条件が真である必要があります。

論理演算子は、入れ子にすることができます。 次の例は、**And** 演算内で入れ子になっている **Not** 演算を示しています。 

```json
"if": {
  "allOf": [
    {
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
条件は、**フィールド**が特定の基準を満たすかどうかを評価します。 サポートされている条件は次のとおりです。

* `"equals": "value"`
* `"like": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

**like** 条件を使用する場合は、値にワイルドカード (*) を指定することができます。

### <a name="fields"></a>フィールド
条件は、フィールドを使用して構成されます。 フィールドは、リソースの状態の記述に使用されるリソース要求ペイロード内のプロパティを表します。  

次のフィールドがサポートされています。

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* プロパティのエイリアス

リソースの種類に固有のプロパティにアクセスするには、プロパティのエイリアスを使用します。 サポートされているエイリアスは次のとおりです。

* Microsoft.CDN/profiles/sku.name
* Microsoft.Compute/virtualMachines/imageOffer
* Microsoft.Compute/virtualMachines/imagePublisher
* Microsoft.Compute/virtualMachines/sku.name
* Microsoft.Compute/virtualMachines/imageSku 
* Microsoft.Compute/virtualMachines/imageVersion
* Microsoft.SQL/servers/databases/edition
* Microsoft.SQL/servers/databases/elasticPoolName
* Microsoft.SQL/servers/databases/requestedServiceObjectiveId
* Microsoft.SQL/servers/databases/requestedServiceObjectiveName
* Microsoft.SQL/servers/elasticPools/dtu
* Microsoft.SQL/servers/elasticPools/edition
* Microsoft.SQL/servers/version
* Microsoft.Storage/storageAccounts/accessTier
* Microsoft.Storage/storageAccounts/enableBlobEncryption
* Microsoft.Storage/storageAccounts/sku.name
* Microsoft.Web/serverFarms/sku.name

### <a name="effect"></a>効果
ポリシーでは、`deny`、`audit`、`append` の&3; 種類の効果がサポートされています。 

* **deny** は監査ログでイベントを生成し、要求は失敗します
* **audit** は監査ログで警告イベントを生成しますが、要求は失敗しません
* **append** は定義済みのフィールド セットを要求に追加します 

**append** の場合、次のように詳細を指定する必要があります。

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

値には文字列または JSON 形式オブジェクトを指定できます。 

## <a name="policy-examples"></a>ポリシーの例

以下のトピックに、ポリシーの例があります。

* タグ ポリシーの例については、「[Apply resource policies for tags (タグに関するリソース ポリシーを適用する)](resource-manager-policy-tags.md)」を参照してください。
* ストレージ ポリシーの例については、「[Apply resource policies to storage accounts (ストレージ アカウントにリソース ポリシーを適用する)](resource-manager-policy-storage.md)」を参照してください。
* 仮想マシン ポリシーの例については、[Linux VM へのリソース ポリシーの適用](../virtual-machines/virtual-machines-linux-policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)と [Windows VM へのリソース ポリシーの適用](../virtual-machines/virtual-machines-windows-policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)に関するページを参照してください。

### <a name="allowed-resource-locations"></a>許可されるリソースの場所
どの場所が許可されるかを指定するには、「[ポリシー定義の構造](#policy-definition-structure)」セクションの例を参照してください。 このポリシー定義を割り当てるには、リソース ID が `/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c` の組み込みポリシーを使用します。

### <a name="not-allowed-resource-locations"></a>許可されないリソースの場所
どの場所が許可されないかを指定するには、次のポリシー定義を使用します。

```json
{
  "properties": {
    "parameters": {
      "notAllowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that are not allowed when deploying resources",
          "strongType": "location",
          "displayName": "Not allowed locations"
        }
      }
    },
    "displayName": "Not allowed locations",
    "description": "This policy enables you to block locations that your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "field": "location",
        "in": "[parameters('notAllowedLocations')]"
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="allowed-resource-types"></a>許可されるリソースの種類
次の例は、種類が Microsoft.Resources、Microsoft.Compute、Microsoft.Storage、Microsoft.Network であるリソースのみのデプロイを許可するポリシーを示しています。 他はすべて拒否されます。

```json
{
  "if": {
    "not": {
      "anyOf": [
        {
          "field": "type",
          "like": "Microsoft.Resources/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Compute/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Storage/*"
        },
        {
          "field": "type",
          "like": "Microsoft.Network/*"
        }
      ]
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

### <a name="set-naming-convention"></a>命名規則の設定
次の例では、**like** の条件でサポートされているワイルドカードが使用されています。 この条件は、名前が指定のパターン (namePrefix\*nameSuffix) と一致する場合、要求を拒否するように指定しています。

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>次のステップ
* ポリシー規則を定義した後で、スコープに割り当てます。 ポリシー割り当てについては、[ポリシーの割り当てと管理](resource-manager-policy-create-assign.md)に関するページを参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。
* [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json) でポリシー スキーマが公開されています。 


