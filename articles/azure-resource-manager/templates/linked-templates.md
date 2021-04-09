---
title: デプロイ用のテンプレートをリンクする
description: Azure Resource Manager テンプレート (ARM テンプレート) でリンクされたテンプレートを使用して、モジュール式のテンプレート ソリューションを作成する方法について説明します。 パラメーターの値を渡す方法、パラメーター ファイルを指定する方法、および URL を動的に作成する方法を示します。
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 6076cbae43e420ac354b5c9d7d101a9c541c078d
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889180"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Azure リソース デプロイ時のリンクされたテンプレートおよび入れ子になったテンプレートの使用

複雑なソリューションをデプロイするには、Azure Resource Manager テンプレート (ARM テンプレート) を複数の関連するテンプレートに分割し、メイン テンプレートを使用してそれらをまとめてデプロイできます。 関連するテンプレートは、独立したファイルでも、メイン テンプレート内に埋め込まれるテンプレート構文でもかまいません。 この記事では、メイン テンプレートからリンクを介して参照される独立したテンプレート ファイルに対して、**リンクされたテンプレート** という用語を使用します。 メイン テンプレートに埋め込まれたテンプレート構文に対して、**入れ子になったテンプレート** という用語を使用します。

中小規模のソリューションの場合、テンプレートを 1 つにするとわかりやすく、保守も簡単になります。 すべてのリソースと値を 1 つのファイルで参照できます。 高度なシナリオの場合、リンクされたテンプレートを使用することで、対象となるコンポーネントにソリューションを分割することができます。 これらのテンプレートは、他のシナリオで簡単に再利用できます。

チュートリアルについては、「[チュートリアル: リンク済みテンプレートをデプロイする](./deployment-tutorial-linked-template.md)」を参照してください。

> [!NOTE]
> リンクされたテンプレートまたは入れ子になったテンプレートには、デプロイ モードを[増分](deployment-modes.md)にのみ設定できます。 ただし、メイン テンプレートは完全モードでデプロイできます。 メイン テンプレートを完全モードでデプロイし、リンクされたテンプレートまたは入れ子になったテンプレートが同じリソースグループを対象とする場合、リンクされたテンプレートまたは入れ子になったテンプレートにデプロイされたリソースは、完全モード デプロイの評価に含まれます。 メイン テンプレートおよびリンクされたまたは入れ子になったテンプレートにデプロイされたリソースの組み合わされたコレクションは、リソース グループ内の既存のリソースと比較されます。 この組み合わされたコレクションに含まれていないリソースはすべて削除されます。
>
> リンクまたは入れ子になったテンプレートが別のリソース グループを対象としている場合、そのデプロイは増分モードを使用します。
>

## <a name="nested-template"></a>入れ子になったテンプレート

テンプレートを入れ子にするには、メイン テンプレートに[デプロイ リソース](/azure/templates/microsoft.resources/deployments)を追加します。 `template` プロパティに、テンプレートの構文を指定します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

次の例では、入れ子になったテンプレートを使用して、ストレージ アカウントがデプロイされます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>入れ子になったテンプレートでの式の評価のスコープ

入れ子になったテンプレートを使用する場合、テンプレート式の評価のスコープを親テンプレートにするか入れ子にするかを指定できます。 スコープによって、[resourceGroup](template-functions-resource.md#resourcegroup) や [subscription](template-functions-resource.md#subscription) などのパラメーター、変数、および関数がどのように解決されるかが決まります。

スコープは、`expressionEvaluationOptions` プロパティを使用して設定します。 既定では、`expressionEvaluationOptions` プロパティは `outer` に設定されます。これは、親テンプレート スコープを使用することを意味します。 値を `inner` に設定すると、入れ子になったテンプレートのスコープ内で式が評価されます。

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate1",
  "properties": {
    "expressionEvaluationOptions": {
      "scope": "inner"
    },
  ...
```

> [!NOTE]
>
> スコープを `outer` に設定した場合は、入れ子になったテンプレートの outputs セクションで、入れ子になったテンプレートでデプロイしているリソースに対する `reference` 関数を使用することはできません。 入れ子になったテンプレートでデプロイされたリソースの値を返すには、`inner` スコープを使用するか、入れ子になったテンプレートをリンクされたテンプレートに変換します。

次のテンプレートは、テンプレート式がスコープに従ってどのように解決されるかを示しています。 これには、親テンプレートと入れ子になったテンプレートの両方に定義されている `exampleVar` という名前の変数が含まれています。 それは、変数の値を返します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

`exampleVar` の値は、`expressionEvaluationOptions` の `scope` プロパティの値によって変わります。 次の表に、両方のスコープの結果を示します。

| 評価のスコープ | 出力 |
| ----- | ------ |
| inner | 入れ子になったテンプレートから |
| outer (既定値) | 親テンプレートから |

次の例では、SQL サーバーをデプロイし、パスワードに使用するキー コンテナー シークレットを取得します。 キー コンテナー ID は動的に作成され (outer テンプレート `parameters` の `adminPassword.reference.keyVault` を参照)、パラメーターとして入れ子になったテンプレートに渡されるため、スコープは `inner` に設定されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

入れ子になったテンプレートで、セキュリティで保護されたパラメーター値を使用する場合は注意してください。 スコープを外部に設定した場合、セキュリティで保護された値は、デプロイ履歴にプレーン テキストとして保存されます。 デプロイ履歴でテンプレートを確認するユーザーは、セキュリティで保護された値を見ることができます。 代わりに、内部スコープを使用するか、セキュリティで保護された値を必要とするリソースを親テンプレートに追加してください。

次の抜粋は、セキュリティで保護された値と、セキュリティで保護されていない値を示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPasswordOrKey": {
      "type": "securestring",
      "metadata": {
        "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
      }
    }
  },
  ...
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      "name": "mainTemplate",
      "properties": {
        ...
        "osProfile": {
          "computerName": "mainTemplate",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in parent template
        }
      }
    },
    {
      "name": "outer",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "outer"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "outer",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "outer",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // No, not secure because resource is in nested template with outer scope
                }
              }
            }
          ]
        }
      }
    },
    {
      "name": "inner",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "parameters": {
          "adminPasswordOrKey": {
              "value": "[parameters('adminPasswordOrKey')]"
          },
          "adminUsername": {
              "value": "[parameters('adminUsername')]"
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": {
              "type": "string",
              "metadata": {
                "description": "Username for the Virtual Machine."
              }
            },
            "adminPasswordOrKey": {
              "type": "securestring",
              "metadata": {
                "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "inner",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "inner",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in nested template and scope is inner
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="linked-template"></a>リンク済みテンプレート

テンプレートをリンクするには、メイン テンプレートに[デプロイ リソース](/azure/templates/microsoft.resources/deployments)を追加します。 `templateLink` プロパティに、含めるテンプレートの URI を指定します。 次の例では、ストレージ アカウントにあるテンプレートにリンクしています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

リンク済みテンプレートを参照する場合、`uri` の値は、ローカル ファイル、またはローカル ネットワークでのみ使用できるファイルにはしないでください。 Azure Resource Manager は、テンプレートにアクセスできる必要があります。 HTTP または HTTPS としてダウンロードできる URI 値を指定してください。

HTTP または HTTPS を含むパラメーターを使用して、テンプレートを参照することができます。 たとえば、一般的なパターンでは、`_artifactsLocation` パラメーターを使用します。 次のような式を使用して、リンク済みテンプレートを設定できます。

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

GitHub のテンプレートにリンクしている場合は、生の URL を使用します。 このリンクの形式は、`https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json` です。 生のリンクを取得するには **[Raw]\(生\)** を選択します。

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="生の URL の選択":::

### <a name="parameters-for-linked-template"></a>リンクされたテンプレートのパラメーター

リンクされたテンプレートのパラメーターには、外部ファイルまたはインラインを指定できます。 外部パラメーター ファイルを指定する場合は、`parametersLink` プロパティを使用します。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

パラメーター値をインラインで渡すには、`parameters` プロパティを使用します。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parameters": {
        "storageAccountName": {
          "value": "[parameters('storageAccountName')]"
        }
      }
    }
  }
]
```

インライン パラメーターとパラメーター ファイルへのリンクの両方を使用することはできません。 `parametersLink` と `parameters` の両方が指定された場合、デプロイは失敗します。

### <a name="use-relative-path-for-linked-templates"></a>リンクされたテンプレートの相対パスを使用する

`Microsoft.Resources/deployments` の `relativePath` プロパティを使用すると、リンクされたテンプレートを簡単に作成できます。 このプロパティを使用すると、リモートのリンクされたテンプレートを、親を基準とした相対的な場所にデプロイできます。 この機能を使用するには、すべてのテンプレート ファイルをステージングし、GitHub や Azure ストレージ アカウントなどのリモート URI で利用できるようにする必要があります。 Azure PowerShell または Azure CLI から URI を使用してメイン テンプレートを呼び出す場合、子のデプロイ URI は親と relativePath の組み合わせになります。

> [!NOTE]
> templateSpec を作成するとき、Azure PowerShell または Azure CLI を使用して、`relativePath` プロパティで参照されるテンプレートを templateSpec リソースにパッケージ化します。 ファイルをステージングする必要はありません。 詳細については、「[リンクされたテンプレートを使用してテンプレート スペックを作成する](./template-specs.md#create-a-template-spec-with-linked-templates)」を参照してください。

次のようなフォルダー構造があるとします。

![リソース マネージャーのリンクされたテンプレートの相対パス](./media/linked-templates/resource-manager-linked-templates-relative-path.png)

次のテンプレートは、*mainTemplate.json* で、前の図で示された *nestedChild.json* をデプロイする方法を示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "childLinked",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "children/nestedChild.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

次のデプロイでは、前のテンプレートのリンクされたテンプレートの URI は、 **https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/children/nestedChild.json** です。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

---

Azure ストレージ アカウントに保存されている相対パスを使用してリンクされたテンプレートをデプロイするには、TemplateUri パラメーターと共に `QueryString`/`query-string` パラメーターを使用して、使用する SAS トークンを指定します。 このパラメーターは、Azure CLI バージョン 2.18 以降および Azure PowerShell バージョン 5.4 以降でのみサポートされています。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

---

QueryString の先頭に "?" がないことを確認してください。 デプロイの URI をアセンブルすると、これが 1 つ追加されます。

## <a name="template-specs"></a>テンプレート スペック

リンクされたテンプレートをアクセス可能なエンドポイントで維持する代わりに、メイン テンプレートとそのリンクされたテンプレートをデプロイできる単一エンティティにパッケージ化した[テンプレート スペック](template-specs.md)を作成できます。 テンプレート スペックは、Azure サブスクリプションのリソースです。 これにより、テンプレートを組織内のユーザーと安全に共有することが容易になります。 テンプレート スペックへのアクセス権を付与するには、Azure ロールベースのアクセス制御 (Azure RBAC) を使用します。現在、この機能はプレビュー段階にあります。

詳細については、次を参照してください。

- [チュートリアル:リンクされたテンプレートを使用してテンプレート スペックを作成する](./template-specs-create-linked.md)」を参照してください。
- [チュートリアル:テンプレート スペックをリンクされたテンプレートとしてデプロイする](./template-specs-deploy-linked-template.md)」を参照してください。

## <a name="dependencies"></a>依存関係

他のリソースの種類と同様に、リンクされたテンプレート間の依存関係を設定できます。 あるリンクされたテンプレート内のリソースを、2 番目のリンクされたテンプレート内のリソースの前にデプロイする必要がある場合は、1 番目に依存する 2 番目のテンプレートを設定します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>contentVersion

`templateLink` または `parametersLink` プロパティには `contentVersion` プロパティを指定する必要はありません。 `contentVersion` を指定しない場合、テンプレートの現在のバージョンがデプロイされます。 コンテンツのバージョン値を指定する場合、リンクされているテンプレートのバージョンと一致している必要があります。それ以外の場合、デプロイはエラーで失敗します。

## <a name="using-variables-to-link-templates"></a>変数を使用したテンプレートのリンク

前の例では、URL の値をハード コーディングしてテンプレートをリンクする方法について説明しました。 この方法は簡単なテンプレートには適していますが、モジュール構造の大規模な一連のテンプレートにはあまり適していません。 その場合は、メイン テンプレートのベース URL を格納する静的変数を作成し、リンクされたテンプレートの URL をそのベース URL から動的に作成することができます。 この方法の利点は、テンプレートを簡単に移動またはフォークできることです。これは、メイン テンプレート内の静的変数のみ変更すれば済むためです。 メイン テンプレート内の静的変数を変更するだけで、正しい URI が、メイン テンプレートから、分解されたテンプレート全体に渡されます。

次の例では、ベース URL を使用して、リンクされたテンプレート (`sharedTemplateUrl` と `vmTemplateUrl`) の 2 つの URL を作成する方法を示しています。

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

[deployment()](template-functions-deployment.md#deployment) を使用して、現在のテンプレートのベース URL を取得したり、同じ場所にある他のテンプレートの URL を取得したりすることもできます。 この方法は、テンプレートの場所が変更された場合や、テンプレート ファイルのハード コーディング URL を回避する必要がある場合に便利です。 `templateLink` プロパティは、URL を含むリモート テンプレートにリンクした場合にのみ返されます。 ローカル テンプレートを使用している場合、そのプロパティは使用できません。

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

最終的には、`templateLink` プロパティの `uri` プロパティで変数を使用します。

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>copy の使用

入れ子になったテンプレートを使用してリソースの複数のインスタンスを作成するには、`Microsoft.Resources/deployments` リソースのレベルで `copy` 要素を追加します。 または、スコープが `inner` の場合は、入れ子になったテンプレート内にコピーを追加できます。

次のテンプレート例では、`copy` を入れ子になったテンプレートと共に使用する方法を示します。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy": {
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "expressionEvaluationOptions": {
        "scope": "inner"
      },
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "StorageV2"
            // Copy works here when scope is inner
            // But, when scope is default or outer, you get an error
            // "copy": {
            //   "name": "storagecopy",
            //   "count": 2
            // }
          }
        ]
      }
    }
  }
]
```

## <a name="get-values-from-linked-template"></a>リンク済みテンプレートから値を取得する

リンクされたテンプレートから出力値を取得するには、`"[reference('deploymentName').outputs.propertyName.value]"` のような構文でプロパティ値を取得します。

リンクされたテンプレートから出力プロパティを取得する場合、プロパティ名にダッシュを含めることはできません。

次の例では、リンクされたテンプレートを参照して、出力値を取得する方法を示します。 リンクされたテンプレートは、単純なメッセージを返します。 最初に、リンクされたテンプレートを示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

メイン テンプレートは、リンクされたテンプレートを展開し、返される値を取得します。 展開リソースを名前で参照し、リンクされたテンプレートによって返されるプロパティの名前を使用していることに注意してください。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

次の例では、パブリック IP アドレスをデプロイし、そのパブリック IP の Azure リソースのリソース ID を返すテンプレートを示します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

ロード バランサーのデプロイ時に、前のテンプレートからのパブリック IP アドレスを使用するには、テンプレートにリンクし、`Microsoft.Resources/deployments` リソースで依存関係を宣言します。 ロード バランサーのパブリック IP アドレスは、リンクされているテンプレートからの出力値に設定されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>デプロイ履歴

Resource Manager では、各テンプレートはデプロイ履歴内で個別のデプロイとして処理されます。 3 つのリンクされたテンプレートまたは入れ子になったテンプレートがあるメイン テンプレートは、デプロイ履歴に次のように表示されます。

![デプロイ履歴](./media/linked-templates/deployment-history.png)

履歴内のこれらの個別のエントリを使用して、展開後に出力値を取得できます。 次のテンプレートは、パブリック IP アドレスを作成し、IP アドレスを出力します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

次のテンプレートは、前のテンプレートにリンクします。 3 つのパブリック IP アドレスが作成されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

展開後、次の PowerShell スクリプトを使用して、出力値を取得できます。

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Bash シェルの Azure CLI スクリプトでは次のようになります。

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>外部テンプレートのセキュリティ保護

リンクされたテンプレートは外部から利用可能でなければなりませんが、一般公開する必要はありません。 ストレージ アカウント所有者のみがアクセス可能なプライベート ストレージ アカウントに、テンプレートを追加できます。 次に、デプロイ時にアクセスできるように、Shared Access Signature (SAS) トークンを作成します。 リンクされたテンプレートの URI に SAS トークンを追加します。 トークンがセキュリティで保護された文字列として渡された場合でも、SAS トークンを含むリンクされたテンプレートの URI が、デプロイ操作中にログに記録されます。 公開を制限するには、トークンの有効期限を設定します。

パラメーター ファイルは SAS トークンを使ったアクセスに制限することができます。

現時点では、 [Azure Storage ファイアウォール](../../storage/common/storage-network-security.md)の内側にあるストレージ アカウントのテンプレートにリンクすることはできません。

> [!IMPORTANT]
> SAS トークンを使用してリンクされたテンプレートをセキュリティで保護する代わりに、[テンプレート スペック](template-specs.md)を作成することを検討してください。テンプレート スペックでは、メイン テンプレートとそのリンクされたテンプレートが、Azure サブスクリプションのリソースとして安全に格納されます。 Azure RBAC を使用して、テンプレートをデプロイする必要があるユーザーにアクセス権を付与します。

次の例は、テンプレートにリンクするときに、SAS トークンを渡す方法を示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "securestring" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

PowerShell では、コンテナーのトークンを取得し、次のコマンドを使ってテンプレートを展開します。 `containerSasToken` パラメーターはテンプレートで定義されていることに注意してください。 `New-AzResourceGroupDeployment` コマンド内のパラメーターではありません。

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Bash シェルの Azure CLI では、コンテナーのトークンを取得し、次のコードを使用してテンプレートをデプロイします。

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>サンプル テンプレート

次の例は、リンク済みテンプレートの一般的な使い方を示します。

|Main template  |リンク済みテンプレート |説明  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[リンク済みテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | リンク済みテンプレートから文字列を返します。 |
|[パブリック IP アドレスを使用する Azure Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[リンク済みテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |リンク済みテンプレートからパブリック IP アドレスを返し、ロード バランサーでその値を設定します。 |
|[複数の IP アドレス](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [リンク済みテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |リンク済みテンプレートにいくつかのパブリック IP アドレスを作成します。  |

## <a name="next-steps"></a>次のステップ

* チュートリアルについては、「[チュートリアル:リンク済みテンプレートをデプロイする](./deployment-tutorial-linked-template.md)」を参照してください。
* リソースのデプロイの順序の定義については、「[ARM テンプレートでのリソース デプロイ順序の定義](define-resource-dependency.md)」を参照してください。
* 定義するリソースは 1 つですが、そこに多数のインスタンスを作成する方法については、「[ARM テンプレートでのリソースの反復処理](copy-resources.md)」を参照してください。
* ストレージ アカウントにテンプレートを設定し、SAS トークンを生成する手順については、「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」または「[ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)」を参照してください。
