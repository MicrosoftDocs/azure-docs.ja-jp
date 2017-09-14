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
ms.date: 08/02/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 0ee2624f45a1de0c23cae4538a38ae3e302eedd3
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---
# <a name="resource-policy-overview"></a>リソース ポリシーの概要
リソース ポリシーを使用すると、組織内のリソースの規則を確立することができます。 規則を定義することによって、コストを制御し、リソースをより簡単に管理することができます。 たとえば、特定の種類の仮想マシンのみを許可するように指定することができます。 また、すべてのリソースに特定のタグが指定されていることを必須にすることができます。 ポリシーは、すべての子リソースが継承します。 したがって、リソース グループに適用されたポリシーは、そのリソース グループのすべてのリソースに適用されます。

ポリシーについて理解するための概念は、次の 2 つです。

* ポリシー定義 - いつポリシーが適用され、どのようなアクションが行われるかを記述します
* ポリシー割り当て - ポリシー定義をスコープ (サブスクリプションまたはリソース グループ) に適用します

このトピックでは、ポリシー定義を中心に説明します。 ポリシー割り当ての詳細については、「[Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md)」(Azure Portal によるリソース ポリシーの割り当てと管理) または「[Assign and manage policies through script](resource-manager-policy-create-assign.md)」(リソース ポリシーの割り当てと管理) を参照してください。

リソースの作成と更新 (PUT 操作と PATCH 操作) を行うときに、ポリシーが評価されます。

> [!NOTE]
> 現時点では、タグ、種類、場所をサポートしていない、Microsoft.Resources/deployments のようなリソースの種類は、ポリシーによる評価の対象となりません。 このサポートは、今後追加される予定です。 下位互換性の問題を回避するためには、ポリシーの作成時に種類を明示的に指定する必要があります。 たとえば、種類が指定されていないタグ ポリシーは、すべての種類に適用されます。 そのような場合、タグをサポートしていない入れ子になったリソースがあり、デプロイ リソースの種類がポリシーの評価に追加されていると、テンプレートのデプロイに失敗する可能性があります。 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>RBAC との違いは何か。
ポリシーとロールベースのアクセス制御 (RBAC) には、いくつかの主要な違いがあります。 RBAC は、さまざまなスコープでの**ユーザー**の操作に焦点を当てています。 たとえば、目的のスコープでリソース グループの共同作成者ロールに追加されると、そのリソース グループに変更を加えられるようになります。 ポリシーは、デプロイ中の**リソース** プロパティに焦点を当てます。 たとえば、ポリシーを使用して、プロビジョニングできるリソースの種類を制御できます。 また、リソースをプロビジョニングできる場所を制限できます。 RBAC とは異なり、ポリシーは既定で許可し、明示的に否認するシステムです。 

ポリシーを使用するには、RBAC で認証される必要があります。 具体的には、アカウントには次のものが必要です。

* ポリシーを定義するための `Microsoft.Authorization/policydefinitions/write` アクセス許可
* ポリシーを割り当てるための `Microsoft.Authorization/policyassignments/write` アクセス許可 

これらのアクセス許可は、**共同作成者**ロールには含まれていません。

## <a name="built-in-policies"></a>組み込みのポリシー

Azure には、いくつかの組み込みのポリシー定義が用意されているので、定義が必要なポリシーの数を減らすことができます。 ポリシーの定義に進む前に、必要な定義が組み込みのポリシーによって既に用意されているかどうかを検討してください。 次に示す組み込みのポリシー定義があります。

* 許可される場所
* 許可されるリソースの種類
* 許可されるストレージ アカウントの SKU
* 許可される仮想マシンの SKU
* タグと既定値の適用
* タグと値の使用
* 許可されないリソースの種類
* SQL Server バージョン 12.0 が必要
* ストレージ アカウントの暗号化が必要

[ポータル](resource-manager-policy-portal.md)、[PowerShell](resource-manager-policy-create-assign.md#powershell)、または [Azure CLI](resource-manager-policy-create-assign.md#azure-cli) を使用して、これらのポリシーを割り当てることができます。

## <a name="policy-definition-structure"></a>ポリシー定義の構造
ポリシー定義を作成するには、JSON を使用します。 ポリシー定義には、以下のものに対する要素が含まれています。

* パラメーター
* 表示名
* 説明
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
パラメーターを使用すると、ポリシー定義の数を減らすことで、ポリシーの管理を単純化できます。 リソース プロパティに対してポリシーを定義し (リソースをデプロイできる場所を制限するなど)、定義にパラメーターを含めます。 その後、そのポリシー定義を別のシナリオで再利用する場合は、ポリシーを割り当てるときに別の値を渡します (サブスクリプションに対する 1 組の場所を指定するなど)。

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

論理演算子は、入れ子にすることができます。 次の例は、**allOf** 演算内で入れ子になっている **not** 演算を示しています。 

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
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

**like** 条件を使用する場合は、値にワイルドカード (*) を指定することができます。

**match** 条件を使うときは、任意の数字を表す `#` や任意の文字を表す `?` のほか、具体的な文字を指定することができます。 具体例については、「[名前とテキスト用のリソース ポリシーを適用する](resource-manager-policy-naming-convention.md)」を参照してください。

### <a name="fields"></a>フィールド
条件は、フィールドを使用して構成されます。 フィールドは、リソースの状態の記述に使用されるリソース要求ペイロード内のプロパティを表します。  

次のフィールドがサポートされています。

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* プロパティのエイリアス: 一覧については、「[エイリアス](#aliases)」を参照してください。

### <a name="effect"></a>効果
ポリシーでは、`deny`、`audit`、`append` の 3 種類の効果がサポートされています。 

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

## <a name="aliases"></a>エイリアス

リソースの種類に固有のプロパティにアクセスするには、プロパティのエイリアスを使用します。 エイリアスを使用すると、リソースのプロパティに使用できる値または条件を制限できます。 各エイリアスは、特定のリソースの種類について異なる API バージョンのパスにマップされます。 ポリシーの評価時に、ポリシー エンジンはその API バージョンのプロパティ パスを取得します。

**Microsoft.Cache/Redis**

| エイリアス | 説明 |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | 非 ssl Redis サーバー ポート (6379) が有効かどうかを設定します。 |
| Microsoft.Cache/Redis/shardCount | Premium クラスター キャッシュに作成するシャードの数を設定します。  |
| Microsoft.Cache/Redis/sku.capacity | デプロイする Redis キャッシュのサイズを設定します。  |
| Microsoft.Cache/Redis/sku.family | 使用する SKU ファミリを設定します。 |
| Microsoft.Cache/Redis/sku.name | デプロイする Redis キャッシュの種類を設定します。 |

**Microsoft.Cdn/profiles**

| エイリアス | 説明 |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | 価格レベルの名前を設定します。 |

**Microsoft.Compute/disks**

| エイリアス | 説明 |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージを設定します。 |
| Microsoft.Compute/imagePublisher | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージの発行元を設定します。 |
| Microsoft.Compute/imageSku | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージの SKU を設定します。 |
| Microsoft.Compute/imageVersion | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージのバージョンを設定します。 |


**Microsoft.Compute/virtualMachines**

| エイリアス | Description |
| ----- | ----------- |
| Microsoft.Compute/imageId | 仮想マシンの作成に使用されるイメージの識別子を設定します。 |
| Microsoft.Compute/imageOffer | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージを設定します。 |
| Microsoft.Compute/imagePublisher | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージの発行元を設定します。 |
| Microsoft.Compute/imageSku | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージの SKU を設定します。 |
| Microsoft.Compute/imageVersion | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージのバージョンを設定します。 |
| Microsoft.Compute/licenseType | イメージまたはディスクがオンプレミスでライセンスされることを設定します。 この値は、Windows Server オペレーティング システムを含むイメージでのみ使用されます。  |
| Microsoft.Compute/virtualMachines/imageOffer | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージを設定します。 |
| Microsoft.Compute/virtualMachines/imagePublisher | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージの発行元を設定します。 |
| Microsoft.Compute/virtualMachines/imageSku | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージの SKU を設定します。 |
| Microsoft.Compute/virtualMachines/imageVersion | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージのバージョンを設定します。 |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Vhd URI を設定します。 |
| Microsoft.Compute/virtualMachines/sku.name | 仮想マシンのサイズを設定します。 |

**Microsoft.Compute/virtualMachines/extensions**

| エイリアス | 説明 |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | 拡張機能の発行元の名前を設定します。 |
| Microsoft.Compute/virtualMachines/extensions/type | 拡張機能の種類を設定します。 |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | 拡張機能のバージョンを設定します。 |

**Microsoft.Compute/virtualMachineScaleSets**

| エイリアス | Description |
| ----- | ----------- |
| Microsoft.Compute/imageId | 仮想マシンの作成に使用されるイメージの識別子を設定します。 |
| Microsoft.Compute/imageOffer | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージを設定します。 |
| Microsoft.Compute/imagePublisher | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージの発行元を設定します。 |
| Microsoft.Compute/imageSku | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージの SKU を設定します。 |
| Microsoft.Compute/imageVersion | 仮想マシンを作成するために使用されるプラットフォーム イメージまたはマーケットプレース イメージのバージョンを設定します。 |
| Microsoft.Compute/licenseType | イメージまたはディスクがオンプレミスでライセンスされることを設定します。 この値は、Windows Server オペレーティング システムを含むイメージでのみ使用されます。 |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | スケール セット内のすべての仮想マシンのコンピューター名プレフィックスを設定します。 |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | ユーザー イメージの BLOB URI を設定します。 |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | スケール セットのオペレーティング システム ディスクを保存するために使用されるコンテナーの URI を設定します。 |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | スケール セット内の仮想マシンのサイズを設定します。 |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | スケール セット内の仮想マシンのレベルを設定します。 |
  
**Microsoft.Network/applicationGateways**

| エイリアス | 説明 |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | ゲートウェイのサイズを設定します。 |

**Microsoft.Network/virtualNetworkGateways**

| エイリアス | 説明 |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | この仮想ネットワーク ゲートウェイの種類を設定します。 |
| Microsoft.Network/virtualNetworkGateways/sku.name | ゲートウェイの SKU 名を設定します。 |

**Microsoft.Sql/servers**

| エイリアス | 説明 |
| ----- | ----------- |
| Microsoft.Sql/servers/version | サーバーのバージョンを設定します。 |

**Microsoft.Sql/databases**

| エイリアス | 説明 |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | データベースのエディションを設定します。 |
| Microsoft.Sql/servers/databases/elasticPoolName | データベースが所属するエラスティック プールの名前を設定します。 |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | データベースの構成済みのサービス レベルの目標 ID を設定します。 |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | データベースの構成済みのサービス レベルの目標の名前を設定します。  |

**Microsoft.Sql/elasticpools**

| エイリアス | 説明 |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | データベースのエラスティック プールの共有 DTU の合計を設定します。 |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | エラスティック プールのエディションを設定します。 |

**Microsoft.Storage/storageAccounts**

| エイリアス | 説明 |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | 課金のために使用されるアクセス レベルを設定します。 |
| Microsoft.Storage/storageAccounts/accountType | SKU 名を設定します。 |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | BLOB ストレージ サービスに格納されるときに、サービスがデータを暗号化するかどうかを設定します。 |
| Microsoft.Storage/storageAccounts/enableFileEncryption | ファイル ストレージ サービスに格納されるときに、サービスがデータを暗号化するかどうかを設定します。 |
| Microsoft.Storage/storageAccounts/sku.name | SKU 名を設定します。 |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | ストレージ サービスへの https トラフィックのみを許可するように設定します。 |


## <a name="policy-examples"></a>ポリシーの例

以下のトピックに、ポリシーの例があります。

* タグ ポリシーの例については、「[Apply resource policies for tags (タグに関するリソース ポリシーを適用する)](resource-manager-policy-tags.md)」を参照してください。
* 名前付けとテキストのパターンの例については、「[名前とテキスト用のリソース ポリシーを適用する](resource-manager-policy-naming-convention.md)」を参照してください。
* ストレージ ポリシーの例については、「[Apply resource policies to storage accounts (ストレージ アカウントにリソース ポリシーを適用する)](resource-manager-policy-storage.md)」を参照してください。
* 仮想マシン ポリシーの例については、[Linux VM へのリソース ポリシーの適用](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)と [Windows VM へのリソース ポリシーの適用](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
* ポリシー規則を定義した後で、スコープに割り当てます。 ポータルでポリシーを割り当てる方法については、「[Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md)」(Azure Portal によるリソース ポリシーの割り当てと管理) を参照してください。 REST API、PowerShell、Azure CLI でポリシーを割り当てる方法については、「[Assign and manage policies through script](resource-manager-policy-create-assign.md)」(スクリプトによるポリシーの割り当てと管理) を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。
* [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json) でポリシー スキーマが公開されています。 


