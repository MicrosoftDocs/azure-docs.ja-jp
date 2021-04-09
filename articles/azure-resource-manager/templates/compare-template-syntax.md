---
title: Azure Resource Manager テンプレートの構文を JSON と Bicep で比較する
description: JSON と Bicep で開発された Azure Resource Manager テンプレートを比較し、言語間での変換方法を示します。
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461796"
---
# <a name="comparing-json-and-bicep-for-templates"></a>テンプレートにおける JSON と Bicep の比較

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) に関する Bicep 構文と JSON 構文を比較します。 ほとんどの場合、Bicep 構文は、JSON の同等のものより詳細度が低くなります。

JSON を使用した ARM テンプレートの開発に慣れている場合は、次の例を使用して、Bicep の同等の構文について確認してください。

## <a name="expressions"></a>式

式を作成するには:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>パラメーター

パラメーターを既定値で宣言するには:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

パラメーター値を取得するには:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>変数

変数を宣言するには:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

変数の値を取得するには:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>文字列

文字列を連結するには:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>論理演算子

論理 **AND** を返すには:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

条件付きで値を設定するには:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>デプロイのスコープ

デプロイのターゲット範囲を設定するには:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>リソース

リソースを宣言するには:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

リソースを条件付きでデプロイするには:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

リソース プロパティを設定するには:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

テンプレート内のリソースのリソース ID を取得するには:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>ループ

配列またはカウント内の項目を繰り返すには:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>リソースの依存関係

リソース間の依存関係を定義するには:

Bicep の場合、依存関係の自動検出を利用するか、依存関係を手動で設定します。

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>参照リソース

テンプレート内のリソースからプロパティを取得するには:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

テンプレートにデプロイされていない既存のリソースからプロパティを取得するには:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>出力

テンプレート内のリソースからプロパティを出力するには:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>コードの再利用

ソリューションを複数のファイルに分割するには:

* Bicep の場合、[モジュール](bicep-tutorial-add-modules.md)を使用します。
* JSON の場合、[リンクされたテンプレート](linked-templates.md)を使用します。

## <a name="recommendations"></a>推奨事項

* 可能であれば、Bicep ファイルで [reference](template-functions-resource.md#reference) と [resourceId](template-functions-resource.md#resourceid) 関数を使用しないようにしてください。 同じ Bicep デプロイでリソースを参照する場合は、代わりにリソース識別子を使用します。 たとえば、リソース識別子として `stg` 使用して Bicep ファイルでリソースをデプロイした場合、プロパティ値を取得するには `stg.id` や `stg.properties.primaryEndpoints.blob` のような構文を使用します。 リソース識別子を使用することで、リソース間に暗黙的な依存関係が作成されます。 dependsOn プロパティを使用して依存関係を明示的に設定する必要はありません。
* リソースが Bicep ファイルにデプロイされていない場合でも、**既存** のキーワードを使用してリソースへのシンボリック参照を取得することができます。
* 識別子には一貫した大文字小文字の区別を使用してください。 使用する大文字小文字の種類がわからない場合は、キャメル形式を試してください。 たとえば、「 `param myCamelCasedParameter string` 」のように入力します。
* 説明がユーザーにとって重要な情報となる場合にのみ、説明をパラメーターに追加します。 情報のために `//` コメントを使用できます。

## <a name="next-steps"></a>次のステップ

* Bicep の詳細については、[Bicep のチュートリアル](./bicep-tutorial-create-first-bicep.md)を参照してください。
* 言語間でテンプレートを変換する方法については、「[JSON と Bicep 間での ARM テンプレートの変換](bicep-decompile.md)」を参照してください。
