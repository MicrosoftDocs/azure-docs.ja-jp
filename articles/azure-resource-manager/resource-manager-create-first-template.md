---
title: "初めての Azure Resource Manager テンプレートを作成する | Microsoft Docs"
description: "Azure Resource Manager テンプレートを初めて作成する際の手順を詳細に説明したガイドです。 ストレージ アカウントのテンプレート リファレンスを使ってテンプレートを作成する方法を紹介します。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/21/2017
ms.topic: get-started-article
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f861f2ba0376f00ff21e03cbb81a04a266f38630
ms.lasthandoff: 03/22/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>初めての Azure Resource Manager テンプレートを作成する
このトピックでは、Azure Resource Manager テンプレートを初めて作成する際の手順を説明します。 Resource Manager テンプレートとは、ソリューションに対してデプロイが必要なリソースを定義した JSON ファイルのことをいいます。 Azure ソリューションのデプロイと管理に関する概念について理解を深めるには、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。 既にリソースがあり、そのリソースのテンプレートを取得するには、「[既存のリソースから Azure Resource Manager テンプレートをエクスポートする](resource-manager-export-template.md)」を参照してください。

テンプレートの作成と編集には、JSON エディターが必要です。 軽量なオープンソースのクロスプラットフォーム コード エディターとしては、[Visual Studio Code](https://code.visualstudio.com/) があります。 VS Code は、拡張機能により Resource Manager テンプレートの作成と編集をサポートしています。 このトピックは、VS Code を使用することを前提として書かれています。もっとも、別の JSON エディター (Visual Studio など) があれば、そちらを使っても問題ありません。

## <a name="get-vs-code-and-extension"></a>VS Code と拡張機能の入手
1. 必要があれば、[https://code.visualstudio.com/](https://code.visualstudio.com/) から VS Code をインストールします。

2. Quick Open (Ctrl + P) にアクセスして以下のコードを実行し、拡張機能 [Azure Resource Manager ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)をインストールします。 

   ```
   ext install msazurermtools.azurerm-vscode-tools
   ```

3. 拡張機能を有効にするように求められたら、VS Code を再起動します。

## <a name="create-blank-template"></a>空のテンプレートの作成

まずは、テンプレートのごく基本的なセクションのみが入った空のテンプレートを作成しましょう。

1. ファイルを作成します。 

2. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [  ],
     "outputs": {  }
   }
   ```

3. このファイルを **azuredeploy.json** という名前で保存します。 

## <a name="add-storage-account"></a>ストレージ アカウントの追加
1. デプロイの対象となるストレージ アカウントを定義するため、テンプレートの **resources** セクションにストレージ アカウントを追加します。 ストレージ アカウントに利用可能な値は、[ストレージ アカウントのテンプレート リファレンス](/azure/templates/microsoft.storage/storageaccounts)を参照してください。 表示されているストレージ アカウントの JSON をコピーします。 

3. コピーした JSON をテンプレートの **resources** セクションに貼り付けます。次の例では、その結果を示しています。 

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [
       {
         "name": "string",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-05-01",
         "sku": {
           "name": "string"
         },
         "kind": "string",
         "location": "string",
         "tags": {},
         "properties": {
           "customDomain": {
             "name": "string",
             "useSubDomain": boolean
           },
           "encryption": {
             "services": {
               "blob": {
                 "enabled": boolean
               }
             },
             "keySource": "Microsoft.Storage"
           },
           "accessTier": "string"
         }
       }
     ],
     "outputs": {  }
   }
   ```

  上の例ではプレースホルダーが多数使われていますが、実際のストレージ アカウントでは必要のないプロパティも一部存在します。

## <a name="set-values-for-storage-account"></a>ストレージ アカウントの値の設定

ここまでで、ストレージ アカウントの値を設定する準備ができました。 

1. JSON のコピー元となった[ストレージ アカウントのテンプレート リファレンス](/azure/templates/microsoft.storage/storageaccounts)をもう一度参照してください。 プロパティと、それに使用可能な値を説明したテーブルがいくつかあります。 

2. **properties** 要素にある **customDomain**、**encryption**、**accessTier** の&3; つはいずれも、必ずしも必要ないことがわかります。 シナリオによってはこの&3; つの値が重要になることもありますが、例を簡潔にするため、今回は削除します。

   ```json
   "resources": [
     {
       "name": "string",
       "type": "Microsoft.Storage/storageAccounts",
       "apiVersion": "2016-05-01",
       "sku": {
         "name": "string"
       },
       "kind": "string",
       "location": "string",
       "tags": {},
       "properties": {
       }
     }
   ],
   ```

3. **kind** 要素には現在、プレース ホルダーの値 ("string") が設定されています。 VS Code では、テンプレートで使用する値の特定に役立つ機能が多数用意されており、 この値についても、有効でないことを示すメッセージが表示されます。 "string" にカーソルを合わせると、**kind** で有効な値が `Storage` または `BlobStorage` であることがわかります。 

   ![VS Code による有効な値の表示](./media/resource-manager-create-first-template/vs-code-show-values.png)

   使用可能な値を確認するには、二重引用符に囲まれた文字を削除してから、**Ctrl + Space** を押します。 表示されたオプションから **Storage** を選択します。
  
   ![IntelliSense の表示](./media/resource-manager-create-first-template/intellisense.png)

   VS Code を使用していない場合には、ストレージ アカウントのテンプレート リファレンス ページを参照してください。 説明のところに、有効な値として上の&2; つが書かれています。 要素を **Storage** に設定します。

   ```json
   "kind": "Storage",
   ```

ここまでの作業が終わった時点で、テンプレートは以下のようになります。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>テンプレート関数の追加

テンプレートの構文を簡略化したり、テンプレートのデプロイ中にのみ利用可能な値を取得したりするには、テンプレートの中で関数を使用します。 テンプレート関数の一覧については、「[Azure Resource Manager のテンプレート関数](resource-group-template-functions.md)」を参照してください。

ストレージ アカウントをリソース グループと同じ場所にデプロイするために、**location** プロパティを次のように設定します。

```json
"location": "[resourceGroup().location]",
```

ここでも、VS Code により使用可能な関数が表示されます。 

![関数の表示](./media/resource-manager-create-first-template/show-functions.png)

関数は角かっこで囲まれていることに注目してください。 この [resourceGroup](resource-group-template-functions.md#resourcegroup) 関数は、`location` というプロパティを備えたオブジェクトを返すものです。 リソース グループは、ソリューションに関連するあらゆるリソースを保持しています。 location プロパティには "Central US" のような値をハードコーディングすることもできますが、そうすると、テンプレートのデプロイ先を変更する場合に、手作業が必要になります。 `resourceGroup` 関数を使用すると、このテンプレートのデプロイ先を別の場所にある別のリソース グループに簡単に変更することができます。

ここまでの作業が終わった時点で、テンプレートは以下のようになります。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-parameters-and-variables"></a>パラメーターと変数の追加
テンプレートに設定する値はあと&2; つだけです。具体的には、**name** と **sku.name** です。 この&2; つのプロパティには、デプロイ時に値をカスタマイズできるパラメーターを追加します。 

ストレージ アカウント名には制約がいくつもあるため、設定の難易度が高くなっています。 名前の長さは 3 ～ 24 文字で、使用できるのは数字と小文字のみです。また、一意になっている必要があります。 ここでは、制約に抵触しない一意の値を当て推量で設定するのではなく、[uniqueString](resource-group-template-functions.md#uniquestring) 関数を使ってハッシュ値を生成することにしましょう。 プレフィックスを追加してこのハッシュ値に意味を持たせると、ハッシュ値がストレージ アカウントを示すものであることがデプロイ後にわかりやすくなります。 

1. 名前付け規則に合致するプレフィックスを渡すには、テンプレートの **parameters** セクションに移動します。 テンプレートに、ストレージ アカウント名のプレフィックスを受け取るパラメーターを追加します。

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     }
   },
   ```

  `uniqueString` により返される文字が 13 文字であり、名前の文字数が 24 文字を超えることができないことから、プレフィックスは最大 11 文字とします。 デプロイ中にこのパラメーターに値を渡さなかった場合には、既定の値が使用されます。

2. テンプレートの **variables** セクションに移動します。 プレフィックスと一意の文字列から名前を作成するには、次の変数を追加します。

   ```json
   "variables": {
     "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
   },
   ```

3. **resources** セクションで、ストレージ アカウント名としてその変数を設定します。

   ```json
   "name": "[variables('storageName')]",
   ```

3. ストレージ アカウントの別の SKU に渡す処理を有効にするには、**parameters** セクションに移動します。 ストレージ名のプレフィックスのパラメーターの後に、使用できる SKU の値と既定値を指定したパラメーターを追加します。 使用できる値は、テンプレートのリファレンス ページまたは VS Code で確認できます。 次の例では、有効な SKU の値をすべて入れてあります。 これに対して、使用できる値をこのテンプレートを使ってデプロイする SKU の種類だけに限定することもできます。

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     },
     "storageSKU": {
       "type": "string",
       "allowedValues": [
         "Standard_LRS",
         "Standard_ZRS",
         "Standard_GRS",
         "Standard_RAGRS",
         "Premium_LRS"
       ],
       "defaultValue": "Standard_LRS",
       "metadata": {
         "description": "The type of replication to use for the storage account."
       }
     }
   },
   ```

3. SKU のプロパティを、パラメーターの値を使用するように変更します。

   ```json
   "sku": {
     "name": "[parameters('storageSKU')]"
   },
   ```    

4. ファイルを保存します。

ここまでの作業が終わった時点で、テンプレートは以下のようになります。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>次のステップ
* テンプレートが完成し、サブスクリプションにデプロイする準備が整いました。 デプロイ方法については、「[Azure にリソースをデプロイする](resource-manager-quickstart-deploy.md)」を参照してください。
* テンプレートの構造の詳細については、「 [Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。

