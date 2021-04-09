---
title: テンプレート内のパラメーター
description: Azure Resource Manager テンプレート (ARM テンプレート) と Bicep ファイルでパラメーターを定義する方法について説明します。
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: a8608e4733960d7f5ba7e5f548c47f16a2b244bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123396"
---
# <a name="parameters-in-arm-templates"></a>ARM テンプレートのパラメーター

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) と Bicep ファイルでパラメーターを定義および使用する方法について説明します。 パラメーターに異なる値を指定することにより、さまざまな環境でテンプレートを再利用できます。

Resource Manager は、デプロイ操作を開始する前にパラメーター値を解決します。 テンプレートでパラメーターが使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

各パラメーターは、いずれかの[データ型](data-types.md)に設定する必要があります。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="minimal-declaration"></a>最小限の宣言

少なくとも、すべてのパラメーターには名前と型が必要です。 Bicep では、パラメーターに、同じスコープ内の変数、リソース、出力、またはその他のパラメーターと同じ名前を付けることはできません。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

---

## <a name="secure-parameters"></a>セキュリティで保護されたパラメーター

文字列またはオブジェクトのパラメーターをセキュリティで保護されたものとマークすることができます。 セキュリティで保護されたパラメーターの値はデプロイ履歴に保存されず、ログに記録されません。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

---

## <a name="allowed-values"></a>使用できる値

パラメーターに使用できる値を定義できます。 使用できる値は配列で指定します。 使用できる値の 1 つではない値がパラメーターに渡された場合、検証時にデプロイは失敗します。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

---

## <a name="default-value"></a>既定値

パラメーターの既定値を指定できます。 既定値は、デプロイ時に値が指定されない場合に使用されます。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoParam string = 'Contoso'
```

---

パラメーターの他のプロパティと共に既定値を指定するには、次の構文を使用します。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

---

既定値を指定した式を使用できます。 パラメーター セクションでは、[reference](template-functions-resource.md#reference) 関数も、いずれの [list](template-functions-resource.md#list) 関数も使用できません。 これらの関数は、リソースのランタイム状態を取得します。パラメーターが解決されるとき、デプロイの前に実行することはできません。

他のパラメーター プロパティと共に式を使用することはできません。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

別のパラメーター値を使用して既定値を作成できます。 次のテンプレートを使用すると、サイト名からホスト プラン名が作成されます。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

---

## <a name="length-constraints"></a>長さの制限

文字列と配列のパラメーターの最小長と最大長を指定できます。 一方または両方の制約を設定できます。 文字列の場合、長さは文字数を示します。 配列の場合、長さは配列内の項目数を示します。

次の例では、2 つのパラメーターを宣言しています。 1 つ目のパラメーターは、文字数が 3-24 である必要があるストレージ アカウント名用です。 もう 1 つのパラメーターは、項目数が 1-5 個である必要がある配列です。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

---

## <a name="integer-constraints"></a>整数の制約

整数パラメーターの最小値と最大値を設定できます。 一方または両方の制約を設定できます。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(1)
@maxValue(12)
param month int
```

---

## <a name="description"></a>説明

テンプレートのユーザーが指定する値を理解できるように、パラメーターに説明を追加することができます。 ポータルからテンプレートをデプロイする場合、説明で指定したテキストがそのパラメーターのヒントとして自動的に使用されます。 パラメーター名から推測できる情報よりもテキストからわかる情報の方が多い場合にのみ、説明を追加します。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

---

## <a name="use-parameter"></a>パラメーターを使用する

JSON テンプレートでは、[parameters](template-functions-deployment.md#parameters) 関数を使用してパラメーターの値を参照します。 Bicep では、パラメーター名を使用します。 次の例では、Key Vault 名のパラメーター値を使用します。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

---

## <a name="objects-as-parameters"></a>パラメーターとしてのオブジェクト

関連する値は 1 つのオブジェクトとして渡すことにより整理しやすくなります。 この方法により、テンプレート内のパラメータ数も減少します。

次の例は、オブジェクトであるパラメーターを示しています。 既定値は、オブジェクトの予想されるプロパティを示しています。 これらのプロパティは、デプロイするリソースを定義するときに使用されます。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
      "type": "object",
      "defaultValue": {
        "name": "VNet1",
        "location": "eastus",
        "addressPrefixes": [
          {
            "name": "firstPrefix",
            "addressPrefix": "10.0.0.0/22"
          }
        ],
        "subnets": [
          {
            "name": "firstSubnet",
            "addressPrefix": "10.0.0.0/24"
          },
          {
            "name": "secondSubnet",
            "addressPrefix": "10.0.1.0/24"
          }
        ]
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

---

## <a name="example-templates"></a>サンプル テンプレート

次の例は、パラメーターを使用するためのシナリオを示しています。

|Template  |説明  |
|---------|---------|
|[既定値のための関数を含むパラメーター](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | パラメーターの既定値を定義する際のテンプレート関数の使用方法を説明します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |
|[パラメーター オブジェクト](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | パラメーターのオブジェクトの使用方法を示します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |

## <a name="next-steps"></a>次のステップ

* パラメーターに使用できるプロパティの詳細については、「[ARM テンプレートの構造と構文について](template-syntax.md)」をご覧ください。
* パラメーター値をファイルとして渡す方法については、「[Resource Manager パラメーター ファイルの作成](parameter-files.md)」を参照してください。
* パラメーターの作成に関する推奨事項については、[ベスト プラクティス - パラメーター](template-best-practices.md#parameters)に関する記事をご覧ください。
