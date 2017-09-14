---
title: "Azure CLI とテンプレートを使用してリソースをデプロイする | Microsoft Docs"
description: "Azure Resource Manager と Azure CLI を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 4f1d5f4cc48470f8906edb28628006dd1996bd3a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ

このトピックでは、Azure CLI 2.0 と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。 Azure ソリューションのデプロイと管理に関する概念に精通していない場合は、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。  

デプロイする Resource Manager テンプレートとして、コンピューター上のローカル ファイル、または GitHub などのリポジトリに配置した外部ファイルを使用できます。 この記事でデプロイするテンプレートは、「[サンプル テンプレート](#sample-template)」セクションから、または [GitHub のストレージ アカウントのテンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json)として入手できます。

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Azure CLI がインストールされていない場合は、[Cloud Shell](#deploy-template-from-cloud-shell) を使用できます。

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ

リソースを Azure にデプロイするときは、以下の手順に従います。

1. Azure アカウントへのログイン
2. デプロイ済みのリソースのコンテナーとして機能するリソース グループを作成します。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 末尾をピリオドにすることはできません。
3. 作成するリソースを定義するテンプレートをリソース グループにデプロイする

テンプレートには、デプロイをカスタマイズできるパラメーターを含めることができます。 たとえば、特定の環境 (開発、テスト、運用など) 向けにカスタマイズした値を指定できます。 サンプル テンプレートでは、ストレージ アカウント SKU のパラメーターを定義します。 

次の例では、リソース グループを作成し、ローカル コンピューターからテンプレートをデプロイします。

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

デプロイが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のような結果を含むメッセージが表示されます。

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>外部テンプレートのデプロイ

Resource Manager テンプレートは、ローカル コンピューターに格納する代わりに、外部の場所に格納することもできます。 ソース管理リポジトリ (GitHub など) にテンプレートを格納できます。 または、組織内の共有アクセス用の Azure ストレージ アカウントに格納することができます。

外部テンプレートをデプロイするには、**template-uri** パラメーターを使用します。 この例の URI を使用して、GitHub のサンプル テンプレートをデプロイします。
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

前の例では、テンプレートにはパブリックにアクセスできる URI が必要になります。テンプレートに機密データを含めてはいけないため、この方法は多くの場合に利用できます。 機密データ (管理者パスワードなど) を指定する必要がある場合は、セキュリティで保護されたパラメーターとしてその値を渡します。 ただし、テンプレートを一般からアクセス可能にしない場合は、プライベートなストレージ コンテナーに格納することで保護できます。 Shared Access Signature (SAS) トークンを必要とするテンプレートをデプロイする方法については、[SAS トークンを使用したプライベート テンプレートのデプロイ](resource-manager-cli-sas-token.md)に関するページをご覧ください。

## <a name="deploy-template-from-cloud-shell"></a>Cloud Shell からのテンプレートのデプロイ

[Cloud Shell](../cloud-shell/overview.md) を使用して、テンプレートをデプロイするための Azure CLI コマンドを実行できます。 ただし、最初に Cloud Shell のファイル共有にテンプレートを読み込む必要があります。 Cloud Shell の使用経験がない場合は、その設定について [Azure Cloud Shell の概要](../cloud-shell/overview.md)に関するページを参照してください。

1. [Azure ポータル](https://portal.azure.com)にログインします。   

2. Cloud Shell リソース グループを選択します。 名前のパターンは `cloud-shell-storage-<region>` です。

   ![リソース グループの選択](./media/resource-group-template-deploy-cli/select-cs-resource-group.png)

3. Cloud Shell のストレージ アカウントを選択します。

   ![ストレージ アカウントを選択する](./media/resource-group-template-deploy-cli/select-storage.png)

4. **[ファイル]** を選択します。

   ![Select files](./media/resource-group-template-deploy-cli/select-files.png)

5. Cloud Shell のファイル共有を選択します。 名前のパターンは `cs-<user>-<domain>-com-<uniqueGuid>` です。

   ![ファイル共有を選択する](./media/resource-group-template-deploy-cli/select-file-share.png)

6. **[ディレクトリの追加]** を選択します。

   ![[ディレクトリの追加]](./media/resource-group-template-deploy-cli/select-add-directory.png)

7. **templates** という名前を付け、**[OK]** を選択します。

   ![ディレクトリに名前を付ける](./media/resource-group-template-deploy-cli/name-templates.png)

8. 新しいディレクトリを選択します。

   ![新しいディレクトリを選択する](./media/resource-group-template-deploy-cli/select-templates.png)

9. **[アップロード]**を選択します。

   ![[アップロード] を選択する](./media/resource-group-template-deploy-cli/select-upload.png)

10. テンプレートを見つけてアップロードします。

   ![ファイルをアップロードする](./media/resource-group-template-deploy-cli/upload-files.png)

11. プロンプトを開きます。

   ![Cloud Shell を開く](./media/resource-group-template-deploy-cli/start-cloud-shell.png)

12. Cloud Shell で次のコマンドを入力します。

   ```azurecli
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageAccountType=Standard_GRS
   ```

## <a name="parameter-files"></a>パラメーター ファイル

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 パラメーター ファイルは次の形式にする必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

parameters セクションに、テンプレートで定義したパラメーター (storageAccountType) に一致するパラメーター名が含まれていることに注目してください。 パラメーター ファイルに、このパラメーターの値が含まれます。 この値は、デプロイの際に、テンプレートに自動的に渡されます。 さまざまなデプロイ シナリオに合わせて複数のパラメーター ファイルを作成し、適切なパラメーター ファイルを渡すことができます。 

前の例をコピーし、`storage.parameters.json` という名前のファイルとして保存します。

ローカル パラメーター ファイルを渡すには、`@` を使用して storage.parameters.json という名前のローカル ファイルを指定します。

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>テンプレートのデプロイをテストする

リソースを実際にデプロイすることなく、テンプレートとパラメーターの値をテストするには、[az group deployment validate](/cli/azure/group/deployment#validate) を使用します。 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

エラーが検出されなかった場合は、テスト デプロイに関する情報が返されます。 具体的には、**error** 値が null であることに注目してください。

```azurecli
{
  "error": null,
  "properties": {
      ...
```

エラーが検出された場合は、エラー メッセージが返されます。 たとえば、ストレージ アカウント SKU について間違った値を渡そうとした場合は、次のエラーが返されます。

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

テンプレートに構文エラーがある場合は、テンプレートを解析できなかったことを示すエラー メッセージが返されます。 このメッセージには、解析エラーの行番号と位置が表示されます。

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

完全モードを使用するには、`mode` パラメーターを使用します。

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

## <a name="sample-template"></a>サンプル テンプレート

次のテンプレートをこのトピックの例として使用します。 このテンプレートをコピーして、storage.json という名前のファイルとして保存します。 このテンプレートを作成する方法にについては、「[初めての Azure Resource Manager テンプレートを作成する](resource-manager-create-first-template.md)」を参照してください。  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>次のステップ
* この記事の例では、既定のサブスクリプションのリソース グループにリソースをデプロイしています。 別のサブスクリプションを使用するには、「[Manage multiple Azure subscriptions (複数の Azure サブスクリプションの管理)](/cli/azure/manage-azure-subscriptions-azure-cli)」を参照してください。
* テンプレートのデプロイ用の完全なサンプル スクリプトについては、[Resource Manager テンプレートのデプロイ用のスクリプト](resource-manager-samples-cli-deploy.md)に関するページを参照してください。
* テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-cli-sas-token.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。

