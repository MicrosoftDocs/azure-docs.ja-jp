---
title: パラメーター ファイルを作成する
description: Azure Resource Manager テンプレートのデプロイ中に値を渡すためのパラメーター ファイルを作成します
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 2b6d942b21594fa608127bb8f403e72295671005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89276662"
---
# <a name="create-resource-manager-parameter-file"></a>Resource Manager パラメーター ファイルを作成する

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 この記事では、パラメーター ファイルを作成する方法について説明します。

## <a name="parameter-file"></a>パラメーター ファイル

パラメーター ファイルの形式は次のとおりです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

パラメーターの値は、パラメーター ファイルにプレーンテキストとして格納されていることに注意してください。 この方法は、リソースの SKU の指定など、機密性の高くない値に適しています。 パスワードなどの機密性の高い値には適していません。 機密性の高い値をパラメーターとして渡す必要がある場合は、キー コンテナーに値を格納し、パラメーター ファイルでそのキーコンテナーを参照します。 機密性の高い値はデプロイ中に安全に取得されます。

次のパラメーター ファイルには、プレーンテキスト値と、キー コンテナーに格納されている値が含まれています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

キー コンテナーの値の使用に関する詳細は、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](key-vault-parameter.md)」を参照してください。

## <a name="define-parameter-values"></a>パラメーター値を定義する

パラメーター値を定義する方法を理解するには、デプロイしているテンプレートを開きます。 テンプレートのパラメーター セクションを確認します。 次の例は、テンプレートのパラメーターを示しています。

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

最初に確認する詳細事項は、各パラメーターの名前です。 パラメーター ファイル内の値は、名前と一致している必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

パラメーターの型を確認します。 パラメーター ファイル内の値は、同じ型にする必要があります。 このテンプレートでは、両方のパラメーターを文字列として指定できます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

次に、既定値を探します。 パラメーターに既定値がある場合は、値を指定できますが、指定しなくてもかまいません。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

最後に、使用可能な値および最大長などの制約を確認します。 これらによって、パラメーターに指定できる値の範囲がわかります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

パラメーター ファイルには、テンプレートで定義されているパラメーターの値のみを含めることができます。 パラメーター ファイルに、テンプレートのパラメーターと一致しない余分なパラメーターが含まれている場合はエラーが発生します。

## <a name="parameter-type-formats"></a>パラメーターの型の形式

次の例は、さまざまなパラメーターの型の形式を示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="deploy-template-with-parameter-file"></a>パラメーター ファイルを使用したテンプレートのデプロイ

Azure CLI でローカル パラメーター ファイルを渡すには、@ とパラメーター ファイルの名前を使用します。

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

詳細については、[ARM テンプレートと Azure CLI でのリソースのデプロイ](./deploy-cli.md#parameters)に関するページを参照してください。

Azure PowerShell でローカル パラメーター ファイルを渡すには、`TemplateParameterFile` パラメーターを使用します。

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

詳細については、[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](./deploy-powershell.md#pass-parameter-values)に関するページを参照してください。

> [!NOTE]
> ポータルで、カスタム テンプレート ブレードでパラメーター ファイルを使用することはできません。

## <a name="file-name"></a>ファイル名

パラメーター ファイルに名前を付けるための一般的な規則は、テンプレート名に **.parameters** を追加することです。 たとえば、テンプレートの名前が **azuredeploy.json** の場合、パラメーター ファイルには **azuredeploy.parameters.json** という名前を付けます。 この名前付け規則により、テンプレートとパラメーターの関連がわかります。

異なる環境にデプロイする場合は、複数のパラメーター ファイルを作成します。 パラメーター ファイルに名前を付けるときは、その用途を識別する方法を追加します。 たとえば、 **azuredeploy.parameters-dev.json** や **azuredeploy.parameters-prod.json** を使用します。

## <a name="parameter-precedence"></a>パラメーターの優先順位

同じデプロイ操作で、インライン パラメーターとローカル パラメーター ファイルを使用することができます。 たとえば、一部の値をローカル パラメーター ファイルで指定し、その他の値をデプロイ中にインラインで追加します。 ローカル パラメーター ファイルとインラインの両方でパラメーターの値を指定すると、インラインの値が優先されます。

ファイルへの URI を提供することにより、外部パラメーター ファイルを使用することができます。 外部パラメーター ファイルを使用する場合、他の値をインラインまたはローカル ファイルから渡すことはできません。 すべてのインライン パラメーターは無視されます。 すべてのパラメーター値を外部ファイル内で指定します。

## <a name="parameter-name-conflicts"></a>パラメーター名の競合

PowerShell コマンドのパラメーターのいずれかと名前が同じであるパラメーターがテンプレートに含まれている場合、PowerShell ではテンプレート内のパラメーター名の後ろに **FromTemplate** という文字を付加します。 たとえば、テンプレート内の **ResourceGroupName** という名前のパラメーターは、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) コマンドレットの **ResourceGroupName** パラメーターと競合します。 **ResourceGroupNameFromTemplate** の値を指定するように求められます。 デプロイ コマンドに使用されていないパラメーター名を使用すると、このような混乱を回避できます。


## <a name="next-steps"></a>次のステップ

- テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートのパラメーター](template-parameters.md)」を参照してください。
- キー コンテナーの値の使用に関する詳細は、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](key-vault-parameter.md)」を参照してください。
- パラメーターの詳細については、「[Azure Resource Manager テンプレートのパラメーター](template-parameters.md)」を参照してください。
