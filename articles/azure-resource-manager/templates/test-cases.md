---
title: テスト ツールキットのテスト ケース
description: ARM テンプレート テスト ツールキットによって実行されるテストについて説明します。
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 451323058ad743d6e26fc8bcea27d1b44c76f543
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674044"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>ARM テンプレート テスト ツールキットの既定のテスト ケース

この記事では、[テンプレート テスト ツールキット](test-toolkit.md)で実行される既定のテストについて説明します。 テストの合格の例または不合格の例を示します。 各テストの名前が含まれます。

## <a name="use-correct-schema"></a>正しいスキーマを使用する

テスト名: **DeploymentTemplate Schema Is Correct** (DeploymentTemplate スキーマが正しい)

テンプレートでは、有効なスキーマの値を指定する必要があります。

次の例は、このテストで **合格** になります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

テンプレートの schema プロパティには、次のいずれかのスキーマが設定されている必要があります。

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>パラメーターが存在する必要がある

テスト名: **Parameters Property Must Exist** (parameters プロパティが存在する必要がある)

テンプレートには parameters 要素が必要です。 パラメーターは、さまざまな環境でテンプレートを再利用できるようにするために不可欠です。 異なる環境にデプロイするときに変更される値について、テンプレートにパラメーターを追加します。

次の例は、このテストに **合格します**。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>宣言されたパラメーターが使用されている必要がある

テスト名: **Parameters Must Be Referenced** (パラメーターは参照されている必要がある)

テンプレートの混乱を軽減するには、定義されていても使用されていないパラメーターを削除します。 このテストでは、テンプレートのどこでも使用されていないパラメーターが検索されます。 使用されていないパラメーターを削除すると、不要な値を指定する必要がないため、テンプレートのデプロイも容易になります。

## <a name="secure-parameters-cant-have-hardcoded-default"></a>セキュリティで保護されたパラメーターの既定値をハードコーディングしてはならない

テスト名: **Secure String Parameters Cannot Have Default** (セキュリティで保護された文字列パラメーターに既定値を設定してはならない)

テンプレートのセキュリティで保護されたパラメーターに、ハードコーディングされた既定値を指定してはいけません。 既定値は空の文字列でかまいません。

パスワードのような機密性の高い値を含むパラメーターでは、**SecureString** 型または **SecureObject** 型を使用します。 パラメーターでセキュリティ保護された型が使用されていると、そのパラメーターの値はログに記録されず、デプロイ履歴に格納されません。 このアクションにより、悪意のあるユーザーが機密値を検出できなくなります。

ただし、既定値を指定すると、テンプレートまたはデプロイ履歴にアクセスできるすべてのユーザーが、その値を検出できます。

次の例は、このテストで **不合格** になります。

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

次の例は、このテストで **合格** になります。

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>環境 URL をハードコーディングできない

テスト名: **DeploymentTemplate Must Not Contain Hardcoded Uri** (DeploymentTemplate にはハードコーディングされた Uri を含めることができない)

テンプレート内の環境 URL はハードコーディングしないでください。 代わりに、[環境関数](template-functions-deployment.md#environment)を使用し、展開中、これらの URL を動的に取得します。 ブロックされている URL ホストの一覧については、[テスト ケース](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)を参照してください。

URL がハードコーディングされているため、次の例ではこのテストに **不合格** となります。

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

[concat](template-functions-string.md#concat) または [uri](template-functions-string.md#uri) と共に使用されているときも、テストは **不合格** になります。

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

次の例は、このテストで **合格** になります。

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>場所にはパラメーターを使用する

テスト名: **Location Should Not Be Hardcoded** (場所をハードコーディングしてはいけない)

テンプレートには、location という名前のパラメーターが必要です。 このパラメーターを使用して、テンプレート内のリソースの場所を設定します。 メイン テンプレート (azuredeploy.json または mainTemplate.json という名前) では、このパラメーターを既定でリソース グループの場所に設定することができます。 リンクまたは入れ子になったテンプレートの場合、location パラメーターに既定の場所を設定しないでください。

テンプレートのユーザーは、使用できるリージョンが限られている場合があります。 リソースの場所をハード コーディングすると、ユーザーはそのリージョンでリソースを作成できなくなる可能性があります。 リソースの場所を `"[resourceGroup().location]"` に設定しても、ユーザーがブロックされる可能性があります。 リソース グループは、他のユーザーがアクセスできないリージョンに作成されている可能性があります。 それらのユーザーは、テンプレートの使用をブロックされます。

既定でリソース グループの場所になる location パラメーターを指定した場合、ユーザーは、便利なときは既定値を使用できますが、別の場所を指定することもできるようになります。

次の例は、リソースで場所が `resourceGroup().location` に設定されているため、このテストで **不合格** になります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

次の例では、location パラメーターが使用されていますが、location パラメーターの既定値がハードコーディングされた場所であるため、このテストで **不合格** になります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

代わりに、既定値はリソース グループの場所であっても、ユーザーが別の値を指定できるパラメーターを作成します。 次の例では、テンプレートがメイン テンプレートとして使用されている場合、このテストに **合格** します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

ただし、前の例をリンクされたテンプレートとして使用すると、テストに **失敗** します。 リンクされたテンプレートとして使用する場合は、既定値を削除してください。

## <a name="resources-should-have-location"></a>リソースには場所が必要である

テスト名: **Resources Should Have Location** (リソースには場所が必要である)

リソースの場所は、[テンプレート式](template-expressions.md)または `global` に設定する必要があります。 テンプレート式では、通常、前のテストで説明した場所パラメーターが使用されます。

場所が式でも  **でもないため、次の例ではこのテストに** 不合格`global`になります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

次の例は、このテストで **合格** になります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

次の例も、このテストで **合格** になります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>VM サイズでパラメーターを使用する

テスト名: **VM Size Should Be A Parameter** (VM サイズはパラメーターにする必要がある)

仮想マシンのサイズをハードコーディングしてはいけません。 テンプレートのユーザーがデプロイされた仮想マシンのサイズを変更できるように、パラメーターを指定します。

次の例は、このテストで **不合格** になります。

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

代わりに、パラメーターを指定します。

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

次に、そのパラメーターに VM サイズを設定します。

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>最小値と最大値が数値である

テスト名: **Min And Max Value Are Numbers** (最小値と最大値が数値である)

パラメーターの最小値と最大値を定義する場合は、それらを数値として指定します。

次の例は、このテストで **不合格** になります。

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

代わりに、値を数値として指定します。 次の例は、このテストに **合格します**。

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

また、最小値または最大値の一方だけを指定した場合も、この警告が表示されます。

## <a name="artifacts-parameter-defined-correctly"></a>成果物パラメーターが正しく定義されている

テスト名: **artifacts parameter** (成果物パラメーター)

`_artifactsLocation` と `_artifactsLocationSasToken` のパラメーターを指定する場合は、正しい既定値と型を使用します。 このテストに合格するには、次の条件が満たされている必要があります。

* 一方のパラメーターを指定する場合は、他も指定する必要があります
* `_artifactsLocation` は **string** である必要があります
* `_artifactsLocation` の既定値は、メイン テンプレートで指定されている必要があります
* `_artifactsLocation` の既定値を、入れ子になったテンプレートで指定することはできません 
* `_artifactsLocation` には、`"[deployment().properties.templateLink.uri]"` またはその既定値に対する生リポジトリ URL のいずれかが必要です
* `_artifactsLocationSasToken` は **secureString** である必要があります
* `_artifactsLocationSasToken` の既定値には空の文字列のみを指定できます
* `_artifactsLocationSasToken` の既定値を、入れ子になったテンプレートで指定することはできません 

## <a name="declared-variables-must-be-used"></a>宣言された変数が使用されている必要がある

テスト名: **Variables Must Be Referenced** (変数は参照されている必要がある)

テンプレートの混乱を軽減するには、定義されていても使用されていない変数を削除します。 このテストでは、テンプレートのどこでも使用されていない変数が検索されます。

## <a name="dynamic-variable-should-not-use-concat"></a>動的変数で concat を使用してはならない

テスト名: **Dynamic Variable References Should Not Use Concat** (動的変数の参照で concat を使用してはならない)

場合によっては、別の変数またはパラメーターの値に基づいて変数を動的に構築する必要があります。 値を設定するときは、[concat](template-functions-string.md#concat) 関数を使用しないでください。 代わりに、使用可能なオプションが含まれるオブジェクトを使用し、デプロイの間にオブジェクトからプロパティの 1 つを動的に取得します。

次の例は、このテストで **合格** になります。 **currentImage** 変数は、デプロイの間に動的に設定されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>最新のバージョンの API を使用する

テスト名: **apiVersions Should Be Recent** (apiVersions は最新でなければならない)

各リソースの API バージョンでは、最新バージョンを使用する必要があります。 テストでは、そのリソースの種類で使用できるバージョンに対して、使用されているバージョンが評価されます。

## <a name="use-hardcoded-api-version"></a>ハードコーディングされた API バージョンを使用する

テスト名: **Providers apiVersions Is Not Permitted** (プロバイダーの apiVersions を使用してはならない)

リソースの種類の API バージョンによって、使用できるプロパティが決まります。 テンプレートではハードコーディングされた API バージョンを指定します。 デプロイの間に決定された API バージョンを取得しないでください。 使用できるプロパティがわかりません。

次の例は、このテストで **不合格** になります。

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

次の例は、このテストで **合格** になります。

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>プロパティを空にすることはできない

テスト名: **Template Should Not Contain Blanks** (テンプレートに空白が含まれていてはならない)

プロパティを空の値にハードコーディングしないでください。 空の値には、null および空の文字列、オブジェクト、配列が含まれます。 プロパティを空の値に設定した場合は、テンプレートからそのプロパティを削除します。 ただし、パラメーターを使用するなどして、デプロイの間に空の値にプロパティを設定することはできます。

## <a name="use-resource-id-functions"></a>リソース ID 関数を使用する

テスト名: **IDs Should Be Derived From ResourceIDs** (ID は ResourceID から派生する必要がある)

リソース ID を指定するときは、リソース ID 関数のいずれかを使用します。 使用できる関数は次のとおりです。

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

concat 関数を使用してリソース ID を作成しないでください。 次の例は、このテストで **不合格** になります。

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

次の例は、このテストで **合格** になります。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>ResourceId 関数のパラメーターが正しい

テスト名: **ResourceIds should not contain** (ResourceId に含まれていてはならない)

リソース ID を生成するときは、省略可能なパラメーターに不要な関数を使用しないでください。 既定の [resourceId](template-functions-resource.md#resourceid) 関数では、現在のサブスクリプションとリソース グループが使用されます。 それらの値を指定する必要はありません。  

次の例は、現在のサブスクリプション ID とリソース グループ名を指定する必要がないため、このテストで **不合格** になります。

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

次の例は、このテストで **合格** になります。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

このテストは以下に適用されます。

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [reference](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

`reference` と `list*` は、`concat` を使用してリソース ID を作成すると、テストで **不合格** になります。

## <a name="dependson-best-practices"></a>dependsOn ベスト プラクティス

テスト名: **DependsOn Best Practices** (DependsOn ベスト プラクティス)

デプロイの依存関係を設定するときに、[if](template-functions-logical.md#if) 関数を使用して条件をテストしないでください。 あるリソースが[条件付きでデプロイされる](conditional-resource-deployment.md)リソースに依存している場合は、任意のリソースと同様に依存関係を設定します。 条件付きリソースがデプロイされていない場合、Azure Resource Manager によって必要な依存関係からそれが自動的に削除されます。

次の例は、このテストで **不合格** になります。

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

次の例は、このテストで **合格** になります。

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>入れ子になったデプロイまたはリンクされたデプロイではデバッグを使用できない

テスト名: **Deployment Resources Must Not Be Debug** (デプロイ リソースをデバッグしてはならない)

**Microsoft.Resources/deployments** リソースの種類で [入れ子になったテンプレートまたはリンクされたテンプレート](linked-templates.md)を定義すると、そのテンプレートのデバッグを有効にすることができます。 そのテンプレートをテストする必要があるときはデバッグしても問題ありませんが、運用環境でテンプレートを使用する準備ができたらオフにする必要があります。

## <a name="admin-user-names-cant-be-literal-value"></a>管理者ユーザー名をリテラル値にすることはできない

テスト名: **adminUsername Should Not Be A Literal** (adminUsername をリテラルにしてはならない)

管理者ユーザー名を設定するときは、リテラル値を使用しないでください。

次の例は、このテストで **不合格** になります。

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

代わりに、パラメーターを使用します。 次の例は、このテストに **合格します**。

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>最新の VM イメージを使用する

テスト名: **VM Images Should Use Latest Version** (VM イメージでは最新バージョンを使用しなければならない)

テンプレートにイメージを含む仮想マシンが含まれている場合は、イメージの最新バージョンが使用されていることを確認します。

## <a name="use-stable-vm-images"></a>安定した VM イメージを使用する

テスト名: **Virtual Machines Should Not Be Preview** (仮想マシンはプレビューにしない)

仮想マシンではプレビュー イメージを使用しないでください。

次の例は、このテストで **不合格** になります。

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

次の例は、このテストで **合格** になります。

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>ManagedIdentity 拡張機能を使用してはならない

テスト名: **ManagedIdentityExtension must not be used** (ManagedIdentityExtension を使用してはならない)

ManagedIdentity 拡張機能を仮想マシンに適用しないでください。 詳細については、「[仮想マシンのマネージド ID 拡張機能の使用を止めて Azure Instance Metadata Service の使用を開始する方法](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md)」を参照してください。

## <a name="outputs-cant-include-secrets"></a>出力にシークレットを含めることはできない

テスト名: **Outputs Must Not Contain Secrets** (出力にシークレットが含まれていてはならない)

outputs セクションには、シークレットが公開される可能性のある値を含めないでください。 テンプレートからの出力はデプロイ履歴に格納されるため、悪意のあるユーザーがその情報を見つける可能性があります。

次の例は、セキュリティで保護されたパラメーターが出力値に含まれるため、テストで **不合格** になります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

次の例は、出力で [list*](template-functions-resource.md#list) 関数が使用されているため、**不合格** になります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

- テスト ツールキットの実行については、「[ARM テンプレート テスト ツールキットを使用する](test-toolkit.md)」を参照してください。
- テスト ツールキットの使用に関して説明している Microsoft Learn モジュールについては、「[what-if と ARM テンプレート テスト ツールキットを使用して変更をプレビューし、Azure リソースを検証する](/learn/modules/arm-template-test/)」をご覧ください。