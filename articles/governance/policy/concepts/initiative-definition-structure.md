---
title: イニシアチブ定義の構造の詳細
description: ポリシー イニシアチブ定義を使用し、組織の Azure リソースのデプロイのポリシー定義をグループ化する方法について説明します。
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: edd3f25dd528d1a718c9287c9f30988b87fb73e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587221"
---
# <a name="azure-policy-initiative-definition-structure"></a>Azure Policy イニシアチブ定義の構造

イニシアティブを使用すると、複数の関連するポリシー定義をグループ化できます。グループを単一の項目として操作するので、割り当てと管理が単純になります。 たとえば、関連するタグ付けポリシー定義を 1 つのイニシアティブとしてグループ化できます。 それぞれのポリシーを個別に割り当てるのではなく、イニシアティブを適用することになります。

ポリシー イニシアチブ定義を作成するには、JSON を使用します。 ポリシー イニシアチブ定義には、以下のものに対する要素が含まれています。

- 表示名
- description
- metadata
- parameters
- ポリシー定義
- ポリシー グループ (このプロパティは[規制コンプライアンス (プレビュー) 機能の一部](./regulatory-compliance.md))

次の例は、2 つのタグ (`costCenter` および `productName`) を扱うためのイニシアティブの作成方法を示しています。 2 つの組み込みポリシーを使用して、既定のタグの値を適用しています。

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
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
    }
}
```

Azure Policy の組み込みとパターンについては、「[Azure Policy のサンプル](../samples/index.md)」を参照してください。

## <a name="metadata"></a>Metadata

オプションの `metadata` プロパティには、イニシアチブ ポリシー定義に関する情報が格納されています。
お客様は `metadata` で組織にとって有用なすべてのプロパティと値を定義できます。 ただし、Azure Policy と組み込みで使用される _一般的_ なプロパティがいくつかあります。

### <a name="common-metadata-properties"></a>一般的なメタデータのプロパティ

- `version` (string):ポリシー イニシアチブ定義の内容のバージョンに関する詳細を追跡します。
- `category` (string):ポリシー定義が表示される Azure portal 内のカテゴリを指定します。

  > [!NOTE]
  > [規制コンプライアンス](./regulatory-compliance.md) イニシアチブの場合、`category` は **規制コンプライアンス** である必要があります。

- `preview` (boolean):イニシアチブ ポリシー定義が _preview_ であるかどうかを示す true または false フラグです。
- `deprecated` (boolean):イニシアチブ ポリシー定義が _deprecated_ とマークされているかどうかを示す true または false フラグです。

> [!NOTE]
> Azure Policy サービスは、`version`、`preview`、`deprecated` の各プロパティを使用して、組み込みのポリシー定義に対する変更のレベルや取り組み、状態を伝えます。 `version` の形式は `{Major}.{Minor}.{Patch}` です。 特定の状態 (_deprecated_、_preview_ など) は、`version` プロパティに追加されるほか、別のプロパティに **boolean** として追加されます。 Azure Policy が組み込みをバージョン管理する方法の詳細については、[組み込みのバージョン管理](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)に関する記事を参照してください。

## <a name="parameters"></a>パラメーター

パラメーターによって、ポリシー定義の数を減らし、ポリシーの管理を単純化できます。 1 つのフォームにあるフィールドのようなパラメーター `name`、`address``city``state` を考えてみてください。 これらのパラメーターは常に同じままですが、その値はフォームの個々の入力に基づいて変わります。
パラメーターは、ポリシー イニシアチブの作成時と同じように機能します。 ポリシー イニシアチブ定義にパラメーターを含めることにより、含まれているポリシーでそのパラメーターを再利用できます。

> [!NOTE]
> イニシアティブをいったん割り当てると、イニシアティブ レベルのパラメーターを変更することはできません。 このため、パラメーターを定義するときに **defaultValue** を設定することをお勧めします。

### <a name="parameter-properties"></a>パラメーターのプロパティ

パラメーターには、ポリシー イニシアチブ定義内で使用される次のプロパティがあります。

- `name`:お使いのパラメーターの名前。 ポリシー規則内の `parameters` デプロイ関数によって使用されます。 詳しくは、[パラメーター値の使用](#passing-a-parameter-value-to-a-policy-definition)に関するページをご覧ください。
- `type`:パラメーターが **string**、**array**、**object**、**boolean**、**integer**、**float** **datetime** のどれであるかを決定します。
- `metadata`:Azure portal によって主に使用されるサブプロパティを定義して、ユーザー フレンドリな情報を表示します。
  - `description`:パラメーターが何に使用されるかの説明。 許可される値の例を提示するために使用できます。
  - `displayName`:ポータル内に表示されるパラメーターのフレンドリ名。
  - `strongType`:(省略可能) ポータル経由でポリシー定義を割り当てるときに使用されます。 コンテキスト対応の一覧を提供します。 詳しくは、[strongType](#strongtype) に関するページをご覧ください。
- `defaultValue`:(省略可能) 値が指定されていない場合、割り当ての中でパラメーターの値を設定します。
- `allowedValues`:(省略可能) 割り当て中にパラメーターが許可する値の配列を指定します。

たとえば、ポリシー イニシアチブ定義を定義して、含まれるさまざまなポリシー定義内のリソースの場所を制限することができます。 そのポリシー イニシアチブ定義のパラメーターは、**allowedLocations** にすることができます。 このパラメーターは、含まれているポリシー定義ごとに使用でき、ポリシー イニシアチブの割り当て時に定義されます。

```json
"parameters": {
    "init_allowedLocations": {
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

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>パラメーター値をポリシー定義に渡す

イニシアチブ定義の [policyDefinitions](#policy-definitions) 配列に含まれるポリシー定義に、どのイニシアチブ パラメーターを渡すかを宣言します。 パラメーター名は同じにすることができますが、ポリシー定義とは異なる名前を使用すると、コードの読みやすさが上がります。

たとえば、以前に定義した **init_allowedLocations** イニシアチブ パラメーターは、次のように、いくつかの含まれるポリシー定義とそのパラメーター **sql_locations** と **vm_locations** に渡すことができます。

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

このサンプルでは、「[パラメーターのプロパティ](#parameter-properties)」に示されていた **init_allowedLocations** パラメーターを参照します。

### <a name="strongtype"></a>strongType

`metadata` プロパティの中で、**strongType** を使用して、Azure portal 内でオプションの複数選択リストを提供できます。 **strongType** には、サポートされる "_リソースの種類_" または許可される値を指定できます。 ある "_リソースの種類_" が **strongType** に対して有効かどうかを確認するには、[Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider) を使用します。

**Get-AzResourceProvider** によって返されない一部の "リソースの種類" もサポートされています。 "リソースの種類" は次のとおりです。

- `Microsoft.RecoveryServices/vaults/backupPolicies`

"リソースの種類" でない **strongType** の許可される値には、次のものがあります。

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>ポリシーの定義

イニシアチブ定義の `policyDefinitions` 部分は、イニシアチブに含まれる既存のポリシー定義の _配列_ です。 「[パラメーター値をポリシー定義に渡す](#passing-a-parameter-value-to-a-policy-definition)」で説明したように、このプロパティで[イニシアチブ パラメーター](#parameters)がポリシー定義に渡されます。

### <a name="policy-definition-properties"></a>ポリシー定義のプロパティ

ポリシー定義を表す各 _配列_ 要素には、次のプロパティがあります。

- `policyDefinitionId` (string):含めるカスタムまたは組み込みのポリシー定義の ID。
- `policyDefinitionReferenceId` (string):含まれているポリシー定義の短い名前。
- `parameters`:(省略可能) ポリシー定義内のプロパティとして、含まれているポリシー定義にイニシアチブ パラメーターを渡すための名前と値のペア。 詳しくは、「[パラメーター](#parameters)」をご覧ください。
- `groupNames` (string の配列):(省略可能) ポリシー定義がメンバーとなっているグループ。 詳しくは、[ポリシー グループ](#policy-definition-groups)に関するページをご覧ください。

次に示すのは、それぞれが同じイニシアチブ パラメーターに渡された 2 つのポリシー定義が含まれている `policyDefinitions` の例です。

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definition-groups"></a>ポリシー定義グループ

イニシアチブ定義のポリシー定義グループをグループ化して分類できます。 Azure Policy の [規制コンプライアンス](./regulatory-compliance.md) (プレビュー) 機能では、このプロパティを使用して定義が **コントロール** と **コンプライアンス ドメイン** にグループ化されます。 これらの情報は、 `policyDefinitionGroups` _配列_ プロパティで定義されます。 追加のグループ化の詳細については、Microsoft によって作成された **policyMetadata** オブジェクトを参照してください。 詳細については、[メタデータ オブジェクト](#metadata-objects)に関するページを参照してください。

### <a name="policy-definition-groups-parameters"></a>ポリシー定義グループのパラメーター

`policyDefinitionGroups` の各 _配列_ 要素は、次のプロパティを両方持つ必要があります。

- `name` (string) \[必要\]:**グループ** の短い名前。 規制コンプライアンスでは、**コントロール**。 このプロパティの値は `policyDefinitions` で `groupNames` により使用されます。
- `category` (string):グループが属している階層。 規制コンプライアンスでは、コントロールの **コンプライアンス ドメイン**。
- `displayName` (string):**グループ** または **コントロール** のフレンドリ名。 ポータルで使用されます。
- `description` (string):**グループ** または **コントロール** の対象の説明です。
- `additionalMetadataId` (string):[policyMetadata](#metadata-objects) オブジェクトの場所。 **コントロール** および **コンプライアンス ドメイン** に関する追加情報が含まれています。

  > [!NOTE]
  > ユーザーは、既存の [policyMetadata](#metadata-objects) オブジェクトをポイントすることがあります。 しかし、これらのオブジェクトは _読み取り専用_ であり、Microsoft によってのみ作成されます。

NIST の組み込みのイニシアチブ定義の `policyDefinitionGroups` プロパティの例は次のようになります。

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>メタデータ オブジェクト

Microsoft によって作成された規制コンプライアンスの組み込みには、各コントロールに関する追加情報が含まれています。
この情報については以下のとおりです。

- 規制コンプライアンス イニシアチブの **コントロール** の概要について Azure portal に表示されます。
- REST API 経由で入手できます。 `Microsoft.PolicyInsights` リソース プロバイダーおよび [policyMetadata 操作グループ](/rest/api/policy/policymetadata/getresource)を参照してください。
- Azure CLI 経由で入手できます。 [az policy metadata](/cli/azure/policy/metadata) コマンドを参照してください。

> [!IMPORTANT]
> 規制コンプライアンスのためのメタデータ オブジェクトは _読み取り専用_ であるため、顧客が作成することはできません。

ポリシー グループのメタデータには、`properties` ノードに次の情報が含まれています。

- `metadataId`:グループ化に関連する **コントロール ID**。
- `category` (必須):**コントロール** が含まれる **コンプライアンス ドメイン**。
- `title` (必須):**コントロール ID** のフレンドリ名です。
- `owner` (必須):Azure でのコントロールの責任者を識別します。_顧客_、_Microsoft_、_共有_。
- `description`:コントロールに関する追加情報。
- `requirements`:コントロールの実装の責任についての詳細。
- `additionalContentUrl`:コントロールの詳細へのリンク。 このプロパティは、通常、コンプライアンス標準におけるこのコントロールについて説明するドキュメントのセクションへのリンクです。

**policyMetadata** オブジェクトの例を次に示します。 このサンプル メタデータは _NIST SP 800-53 R4 AC-1_ コントロールに属しています。

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>次のステップ

- [定義の構造](./definition-structure.md)を確認します。
- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Policy の効果について](effects.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
