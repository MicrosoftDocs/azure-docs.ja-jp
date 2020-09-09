---
title: 出力値の複数のインスタンスを定義する
description: デプロイから値を返すときに、Azure Resource Manager テンプレートで copy 操作を使用して、複数回、反復処理を行います。
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 50c4b4b8f301ad88d3dfde98ace1aed4431693db
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583431"
---
# <a name="output-iteration-in-arm-templates"></a>ARM テンプレートでの出力の反復処理

この記事では、Azure Resource Manager (ARM) テンプレートで 1 つの出力に対して複数の値を作成する方法について説明します。 テンプレートの outputs セクションに **copy** 要素を追加することにより、デプロイ時に多数の項目を動的に返すことができます。

[resources](copy-resources.md)、[resource 内の properties](copy-properties.md)、および[variables](copy-variables.md) でも、copy を使用できます。

## <a name="syntax"></a>構文

この copy 要素には、次の一般的な形式があります。

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

**count** プロパティには、出力値で必要な反復回数を指定します。

**input** プロパティは、繰り返すプロパティを指定します。 **input** プロパティの値から構築される要素の配列を作成します。 それは、1 つのプロパティ (文字列など) にすることも、複数のプロパティを持つオブジェクトにすることもできます。

## <a name="copy-limits"></a>コピー制限

count は 800 を超えることはできません。

count は負の数値にすることはできません。 Azure CLI、PowerShell、または REST API の最新バージョンを使用してテンプレートをデプロイする場合、ゼロを指定できます。 具体的には、次のものを使用する必要があります。

* Azure PowerShell **2.6** 以降
* Azure CLI **2.0.74** 以降
* REST API バージョン **2019-05-10** 以降
* [[Linked deployments]\(リンクされたデプロイ\)](linked-templates.md) には、デプロイ リソースの種類に API バージョン **2019-05-10** 以降を使用する必要があります

以前のバージョンの PowerShell、CLI、および REST API では、count の 0 をサポートしていません。

## <a name="outputs-iteration"></a>出力の反復

次の例では、可変数のストレージ アカウントが作成され、各ストレージ アカウントのエンドポイントが返されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

前のテンプレートは、次の値を持つ配列を返します。

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

次の例では、新しいストレージ アカウントから 3 つのプロパティが返されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

前の例は、次の値を持つ配列を返します。

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>次のステップ

* チュートリアルを実行するには、[チュートリアル: ARM テンプレートを使用した複数のリソース インスタンスの作成](template-tutorial-create-multiple-instances.md)に関するページを参照してください。
* copy 要素のその他の使用方法については、以下を参照してください。
  * [ARM テンプレートでのリソースの反復処理](copy-resources.md)
  * [ARM テンプレートでのプロパティの反復処理](copy-properties.md)
  * [ARM テンプレートでの変数の反復処理](copy-variables.md)
* テンプレートのセクションについては、[ARM テンプレートの作成](template-syntax.md)に関する記事を参照してください。
* テンプレートをデプロイする方法については、[ARM テンプレートを使用したアプリケーションのデプロイ](deploy-powershell.md)に関する記事を参照してください。

