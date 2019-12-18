---
title: チュートリアル:カスタム ポリシー定義の作成
description: このチュートリアルでは、Azure リソースに対してカスタム ビジネス ルールを適用するための Azure Policy のカスタム ポリシー定義を作成します。
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: 51899491d7a75dc41bdab94d17769393ab4a6659
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74885451"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>チュートリアル:カスタム ポリシー定義の作成

カスタム ポリシー定義を使用すると、顧客が Azure を使用するための独自のルールを定義できます。 これらのルールは通常、次のものを適用します。

- セキュリティの実行
- コスト管理
- 組織固有のルール (名前付けや場所など)

カスタム ポリシーを作成するうえでのビジネス ドライバーがどのようなものであっても、新しいカスタム ポリシーを定義する手順は同じです。

カスタム ポリシーを作成する前に、[ポリシーのサンプル](../samples/index.md)を見て、ニーズに一致するポリシーが既に存在するかどうかを確認してください。

カスタム ポリシーの作成手順は次のとおりです。

> [!div class="checklist"]
> - ビジネス要件を特定する
> - 各要件を Azure リソースのプロパティにマップする
> - プロパティをエイリアスにマップする
> - 使用する効果を決定する
> - ポリシー定義を作成する

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="identify-requirements"></a>要件を特定する

ポリシー定義を作成する前に、ポリシーの意図を理解しておくことが重要です。 このチュートリアルでは、一般的な大企業のセキュリティ要件を目標として利用して、必要な手順を説明します。

- 各ストレージ アカウントで HTTPS が有効になっていること
- 各ストレージ アカウントで HTTP が無効になっていること

ご自分の要件では、リソースの "適切な状態" と "不適切な状態" の両方を明確に特定する必要があります。

リソースの想定される状態は定義してありますが、非準拠リソースをどうするかはまだ定義していません。 Azure Policy ではさまざまな[効果](../concepts/effects.md)がサポートされます。 このチュートリアルで定義するビジネス要件では、ビジネス ルールに準拠しないリソースが作成されないようにします。 この目標を達成するには、[Deny](../concepts/effects.md#deny) 効果を使用します。 また、特定の割り当てに対するポリシーを一時停止するオプションも必要です。 そのため、[Disabled](../concepts/effects.md#disabled) 効果を使用し、その効果をポリシー定義の[パラメーター](../concepts/definition-structure.md#parameters)にします。

## <a name="determine-resource-properties"></a>リソースのプロパティを判別する

ビジネス要件に基づいて、Azure Policy を使用して監査する Azure リソースは、ストレージ アカウントです。 ただし、ポリシー定義で使用するプロパティはわかりません。 Azure Policy では、リソースの JSON 表現に対して評価を行います。そのため、そのリソースで使用可能なプロパティを把握する必要があります。

Azure リソースのプロパティを判別する方法はたくさんあります。 このチュートリアルでは、それぞれについて見ていきます。

- VS Code 用 Azure Policy 拡張機能
- Resource Manager テンプレート
  - 既存のリソースのエクスポート
  - 作成エクスペリエンス
  - クイック スタート テンプレート (GitHub)
  - テンプレートのリファレンス ドキュメント
- Azure リソース エクスプローラー

### <a name="view-resources-in-vs-code-extension"></a>VS Code 拡張機能でリソースを表示する

[VS Code 拡張機能](../how-to/extension-for-vscode.md#search-for-and-view-resources)を使用すると、環境内のリソースを参照し、各リソースの Resource Manager プロパティを表示できます。

### <a name="resource-manager-templates"></a>Resource Manager テンプレート

管理しようとしているプロパティを含む [Resource Manager テンプレート](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md)を確認する方法はいくつかあります。

#### <a name="existing-resource-in-the-portal"></a>ポータルにおける既存のリソース

プロパティを見つける最も簡単な方法は、同じ種類の既存リソースを確認することです。 適用する設定を使用して既に構成されているリソースには、比較対象の値もあります。
その特定のリソースについて、Azure portal の ( **[設定]** にある) **[テンプレートのエクスポート]** ページを確認します。

![既存のリソースのテンプレート ページをエクスポートする](../media/create-custom-policy-definition/export-template.png)

これにより、ストレージ アカウントの場合、次の例のようなテンプレートが表示されます。

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

**properties** の下にある **supportsHttpsTrafficOnly** という値は **false** に設定されています。 このようなプロパティが、探していたプロパティです。 また、このリソースの**種類**は **Microsoft.Storage/storageAccounts** です。 これにより、ポリシーをこの種類のリソースのみに制限できます。

#### <a name="create-a-resource-in-the-portal"></a>ポータルでリソースを作成する

ポータルを使用するもう 1 つの方法は、リソースの作成エクスペリエンスです。 ポータルを使用してストレージ アカウントを作成する際、 **[詳細]** タブに **[安全な転送が必須]** オプションがあります。 このプロパティには、"_無効_" と "_有効_" のオプションがあります。 情報アイコンには追加のテキストがあり、このオプションが目的のプロパティであることを確認できます。 ただし、ポータルのこの画面ではプロパティ名はわかりません。

**[確認と作成]** タブのページの下部に、 **[Automation のテンプレートをダウンロードする]** リンクがあります。 このリンクを選択すると、構成したリソースを作成するテンプレートが開きます。 この場合は、2 つの重要な情報が表示されます。

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

この情報でわかるのは、プロパティの種類と、探しているプロパティが **supportsHttpsTrafficOnly** であるということです。

#### <a name="quickstart-templates-on-github"></a>GitHub 上のクイック スタート テンプレート

GitHub 上の [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates)には、さまざまなリソース用に構築された数百もの Resource Manager テンプレートがあります。 これらのテンプレートは、探しているリソースのプロパティを見つけるのに役立ちます。 探しているプロパティであるように見えて、別のものを制御しているプロパティである場合もあります。

#### <a name="resource-reference-docs"></a>リソースのリファレンス ドキュメント

**supportsHttpsTrafficOnly** が適切なプロパティであるかどうかを検証するには、Resource Manager テンプレート リファレンスで、ストレージ プロバイダーの[ストレージ アカウント リソース](/azure/templates/microsoft.storage/2018-07-01/storageaccounts)について確認します。
プロパティ オブジェクトには、有効なパラメーターのリストがあります。 [[StorageAccountPropertiesCreateParameters-object]](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) リンクを選択すると、許容されるプロパティの表が表示されます。 **supportsHttpsTrafficOnly** が存在し、その説明は探しているものと一致していてビジネス要件を満たします。

### <a name="azure-resource-explorer"></a>Azure リソース エクスプローラー

Azure リソースを探すもう 1 つの方法は、[Azure Resource Explorer](https://resources.azure.com) (プレビュー) を使用することです。 このツールはお使いのサブスクリプションのコンテキストを使用するため、Web サイトで Azure 資格情報を使用して認証する必要があります。 認証されると、プロバイダー、サブスクリプション、リソース グループ、リソースごとに参照できます。

ストレージ アカウントのリソースを見つけて、プロパティを確認します。 ここでも、**supportsHttpsTrafficOnly** プロパティを確認できます。 **[ドキュメント]** タブを選択すると、プロパティの説明が前にリファレンス ドキュメントで見たものと一致することを確認できます。

## <a name="find-the-property-alias"></a>プロパティのエイリアスを見つける

リソースのプロパティを特定しましたが、このプロパティを[エイリアス](../concepts/definition-structure.md#aliases)にマップする必要があります。

Azure リソースのエイリアスを判別する方法はいくつかあります。 このチュートリアルでは、それぞれについて見ていきます。

- VS Code 用 Azure Policy 拡張機能
- Azure CLI
- Azure PowerShell
- Azure Resource Graph

### <a name="get-aliases-in-vs-code-extension"></a>VS Code 拡張機能でエイリアスを取得する

VS Code 拡張機能の Azure Policy 拡張機能を使用すると、リソースを簡単に参照し、[エイリアスを検出](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties)できます。

### <a name="azure-cli"></a>Azure CLI

Azure CLI では、`az provider` コマンド グループを使用してリソースのエイリアスを探します。 これまでに Azure リソースについて取得した詳細に基づいて、**Microsoft.Storage** 名前空間でフィルター処理します。

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

その結果から、**supportsHttpsTrafficOnly** という名前のストレージ アカウントでサポートされているエイリアスを確認します。 このエイリアスが存在するということは、目的のビジネス要件を適用するためのポリシーを作成できるということです。

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell では、`Get-AzPolicyAlias` コマンドレットを使用してリソースのエイリアスを探します。 これまでに Azure リソースについて取得した詳細に基づいて、**Microsoft.Storage** 名前空間でフィルター処理します。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Azure CLI と同様に、その結果から **supportsHttpsTrafficOnly** という名前のストレージ アカウントでサポートされているエイリアスを確認します。

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Azure Resource Graph](../../resource-graph/overview.md) は、Azure リソースのプロパティを探すもう 1 つの手段となるサービスです。 Resource Graph を使用して単一のストレージ アカウントを探すサンプル クエリを次に示します。

```kusto
Resources
| where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1"
```

これらの結果は、Resource Manager テンプレートや Azure Resource Explorer を使用した場合と似ています。 しかし、Azure Resource Graph の結果には、_エイリアス_配列を_プロジェクションする_ことで、"[エイリアス](../concepts/definition-structure.md#aliases)" の詳細を含めることもできます。

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

エイリアスに関するストレージ アカウントからの出力の例を次に示します。

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

Azure Resource Graph は [Cloud Shell](https://shell.azure.com) を介して使用することもでき、リソースのプロパティをすばやく簡単に探すことができます。

## <a name="determine-the-effect-to-use"></a>使用する効果を決定する

非準拠リソースをどう処理するかを決定することは、最初に何を評価するかを決定することと同じくらい重要です。 非準拠リソースに対して考えられる応答はそれぞれ、[効果](../concepts/effects.md)と呼ばれます。 効果は、非準拠リソースがログ記録されるか、ブロックされるか、追加されるデータがあるか、リソースを準拠状態に戻すために関連付けられているデプロイがあるかを制御します。

この例では、非準拠リソースを Azure 環境で作成したくないため、必要な効果は Deny です。 Audit は、ポリシーの効果を Deny に設定する前にポリシーの影響を判別するのに最適な効果です。 割り当てごとにより簡単に効果を変更できるようにする方法の 1 つは、効果をパラメーター化することです。 詳細な方法については、「[パラメーター](#parameters)」を参照してください。

## <a name="compose-the-definition"></a>定義を作成する

これで、管理しようとしているもののプロパティの詳細とエイリアスがわかりました。 次は、ポリシー ルール自体を作成します。 ポリシー言語に慣れていない場合は、[ポリシー定義の構造](../concepts/definition-structure.md)に関するページでポリシー定義の構成方法を確認してください。 ポリシー定義の空のテンプレートは次のようになります。

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadata

最初の 3 つのコンポーネントは、ポリシー メタデータです。 ルールを作成する目的がわかっているため、これらのコンポーネントに値を指定するのは簡単です。 [Mode](../concepts/definition-structure.md#mode) は、主にタグとリソースの場所に関するコンポーネントです。 タグをサポートするリソースに評価を制限する必要はないため、**mode** には _all_ という値を使用します。

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>parameters

評価の変更のためにパラメーターは使用していませんが、トラブルシューティング用にパラメーターを使用して**効果**を変更できるようにしたいと思います。 **effectType** パラメーターを定義し、それを **Deny** と **Disabled** のみに制限します。 これら 2 つのオプションは、目的のビジネス要件に一致しています。 完成した parameters ブロックの例を次に示します。

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>ポリシー規則

[ポリシー ルール](../concepts/definition-structure.md#policy-rule)の作成は、カスタム ポリシー定義を構築する際の最後の手順です。 次のテストのために、2 つのステートメントを特定しました。

- ストレージ アカウントの**種類**は **Microsoft.Storage/storageAccounts**
- ストレージ アカウントの **supportsHttpsTrafficOnly** は **true** ではない

これらのステートメントが両方とも true である必要があるので、**allOf** [論理演算子](../concepts/definition-structure.md#logical-operators)を使用します。 静的に宣言する代わりに、**effectType** パラメーターを effect に渡します。 完成したルールは次の例のようになります。

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>完成した定義

ポリシーの 3 つの部分をすべて定義して、完成した定義を次に示します。

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

この完成した定義を使用して、新しいポリシーを作成できます。 ポータルと各 SDK (Azure CLI、Azure PowerShell、REST API) で定義の受け入れ方は異なるので、それぞれのコマンドを参照して適切な使用方法を確認してください。 次に、パラメーター化した効果を使用して適切なリソースに割り当て、ストレージ アカウントのセキュリティを管理します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルのリソースに対する作業が完了した場合は、次の手順を使用して、ここで作成した割り当てまたは定義をすべて削除してください。

1. Azure Policy ページの左側にある **[作成]** の下の **[定義]** (または割り当てを削除する場合は **[割り当て]** ) を選択します。

1. 削除する新しいイニシアティブまたはポリシー定義 (または割り当て) を見つけます。

1. 行を右クリックするか、定義 (または割り当て) の末尾にある省略記号を選択し、 **[定義の削除]** (または **[割り当ての削除]** ) を選択します。

## <a name="review"></a>レビュー

このチュートリアルでは、以下のタスクを完了しました。

> [!div class="checklist"]
> - ビジネス要件を特定する
> - 各要件を Azure リソースのプロパティにマップする
> - プロパティをエイリアスにマップする
> - 使用する効果を決定する
> - ポリシー定義を作成する

## <a name="next-steps"></a>次の手順

次に、カスタム ポリシー定義を使用し、ポリシーを作成して割り当てます。

> [!div class="nextstepaction"]
> [ポリシー定義を作成して割り当てる](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)