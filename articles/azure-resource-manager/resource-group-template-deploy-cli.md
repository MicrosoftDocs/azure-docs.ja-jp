---
title: Azure CLI とテンプレートを使用してリソースをデプロイする | Microsoft Docs
description: Azure Resource Manager と Azure CLI を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 11d5b174dc21392df89def8e91847e8a0dd12562
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206535"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ

この記事では、Azure CLI と Resource Manager テンプレートを使用して、Azure にリソースをデプロイする方法について説明します。 Azure ソリューションのデプロイと管理に関する概念に精通していない場合は、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Azure CLI がインストールされていない場合は、[Cloud Shell](#deploy-template-from-cloud-shell) を使用できます。

## <a name="deployment-scope"></a>デプロイの範囲

Azure サブスクリプション、またはサブスクリプション内のリソース グループのいずれかを、デプロイの対象として指定できます。 多くの場合、リソース グループをデプロイの対象にします。 サブスクリプション デプロイを使用するのは、サブスクリプション全体にポリシーとロールの割り当てを適用するときです。 また、リソース グループを作成し、それにリソースをデプロイする場合も、サブスクリプション デプロイを使用します。 デプロイの範囲に応じて、異なるコマンドを使用します。

**リソース グループ**にデプロイするには、[az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) を使用します。

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

**サブスクリプション**にデプロイするには、[az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create) を使用します。

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

現在、管理グループのデプロイは、REST API を介してのみサポートされています。 「[Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](resource-group-template-deploy-rest.md)」を参照してください。

この記事の例では、リソース グループ デプロイを使用します。 サブスクリプション デプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ

リソースを Azure にデプロイするときは、以下の手順に従います。

1. Azure アカウントへのサインイン
2. デプロイ済みのリソースのコンテナーとして機能するリソース グループを作成します。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 末尾をピリオドにすることはできません。
3. 作成するリソースを定義するテンプレートをリソース グループにデプロイする

テンプレートには、デプロイをカスタマイズできるパラメーターを含めることができます。 たとえば、特定の環境 (開発、テスト、運用など) 向けにカスタマイズした値を指定できます。 サンプル テンプレートでは、ストレージ アカウント SKU のパラメーターを定義します。 

次の例では、リソース グループを作成し、ローカル コンピューターからテンプレートをデプロイします。

```azurecli-interactive
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

## <a name="deploy-remote-template"></a>リモート テンプレートのデプロイ

Resource Manager テンプレートは、ローカル コンピューターに格納する代わりに、外部の場所に格納することもできます。 ソース管理リポジトリ (GitHub など) にテンプレートを格納できます。 または、組織内の共有アクセス用の Azure ストレージ アカウントに格納することができます。

外部テンプレートをデプロイするには、**template-uri** パラメーターを使用します。 この例の URI を使用して、GitHub のサンプル テンプレートをデプロイします。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

前の例では、テンプレートにはパブリックにアクセスできる URI が必要になります。テンプレートに機密データを含めてはいけないため、この方法は多くの場合に利用できます。 機密データ (管理者パスワードなど) を指定する必要がある場合は、セキュリティで保護されたパラメーターとしてその値を渡します。 ただし、テンプレートを一般からアクセス可能にしない場合は、プライベートなストレージ コンテナーに格納することで保護できます。 Shared Access Signature (SAS) トークンを必要とするテンプレートをデプロイする方法については、[SAS トークンを使用したプライベート テンプレートのデプロイ](resource-manager-cli-sas-token.md)に関するページをご覧ください。

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Cloud Shell で次のコマンドを使用します。

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>デプロイに失敗したときに再デプロイする

この機能は、"*エラー時のロールバック*" とも呼ばれます。 デプロイに失敗した場合、デプロイ履歴から以前に成功したデプロイを自動的に再デプロイすることができます。 再デプロイを指定するには、デプロイ コマンドで `--rollback-on-error` パラメーターを使用します。 この機能は、インフラストラクチャのデプロイに関して既知の正常な状態が存在し、その状態に戻したい場合に便利です。 いくつかの注意事項と制限があります。

- 再デプロイは、以前に実行されたときとまったく同じパラメーターで実行されます。 パラメーターを変更することはできません。
- 以前のデプロイは、[完全モード](./deployment-modes.md#complete-mode)を使用して実行されます。 以前のデプロイに含まれていなかったリソースはすべて削除され、すべてのリソースの構成は以前の状態に設定されます。 [デプロイ モード](./deployment-modes.md)を完全に理解しておいてください。
- 再デプロイではリソースのみが影響を受け、データの変更には影響ありません。
- この機能は、リソース グループのデプロイにおいてのみサポートされ、サブスクリプション レベルのデプロイではサポートされません。 サブスクリプション レベル デプロイについて詳しくは、「[サブスクリプション レベルでリソース グループとリソースを作成する](./deploy-to-subscription.md)」をご覧ください。

このオプションを使用するには、ご自身のデプロイを履歴で特定できるように、デプロイの名前は一意でなければなりません。 一意の名前が付いていないと、失敗した現在のデプロイによって、履歴にある以前の成功したデプロイが上書きされる可能性があります。 このオプションは、ルート レベルのデプロイでのみ使用できます。 入れ子になったテンプレートのデプロイを再デプロイに使用することはできません。

最後に成功したデプロイを再デプロイするには、`--rollback-on-error` パラメーターをフラグとして追加します。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

特定のデプロイを再デプロイするには、`--rollback-on-error` パラメーターを使用してデプロイの名前を指定します。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

指定したデプロイは成功している必要があります。

## <a name="parameters"></a>parameters

パラメーター値を渡すには、インライン パラメーターまたはパラメーター ファイルのいずれかを使用できます。 この記事の先の例では、インライン パラメーターを示しています。

### <a name="inline-parameters"></a>インライン パラメーター

インライン パラメーターを渡すには、`parameters` に値を指定します。 たとえば、Bash シェルで文字列と配列をテンプレートに渡すには、以下を使用します。

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

ファイルの内容を取得し、その内容をインライン パラメーターとして提供することもできます。

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

ファイルからのパラメーター値の取得は、構成値を指定する必要がある場合に便利です。 たとえば、[Linux 仮想マシン用の cloud-init の値](../virtual-machines/linux/using-cloud-init.md)を指定できます。

arrayContent.json 形式は次のようになります。

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>パラメーター ファイル

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 パラメーター ファイルはローカル ファイルである必要があります。 外部パラメーター ファイルは、Azure CLI ではサポートされていません。

パラメーター ファイルは次の形式にする必要があります。

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

parameters セクションに、テンプレートで定義したパラメーター (storageAccountType) に一致するパラメーター名が含まれていることに注目してください。 パラメーター ファイルに、このパラメーターの値が含まれます。 この値は、デプロイの際に、テンプレートに自動的に渡されます。 複数のパラメーター ファイルを作成し、シナリオに合う適切なパラメーター ファイルを渡すことができます。 

前の例をコピーし、`storage.parameters.json` という名前のファイルとして保存します。

ローカル パラメーター ファイルを渡すには、`@` を使用して storage.parameters.json という名前のローカル ファイルを指定します。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

### <a name="parameter-precedence"></a>パラメーターの優先順位

同じデプロイ操作で、インライン パラメーターとローカル パラメーター ファイルを使用することができます。 たとえば、一部の値をローカル パラメーター ファイルで指定し、その他の値をデプロイ中にインラインで追加します。 ローカル パラメーター ファイルとインラインの両方でパラメーターの値を指定すると、インラインの値が優先されます。

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters @demotemplate.parameters.json \
  --parameters exampleArray=@arrtest.json
```

## <a name="test-a-template-deployment"></a>テンプレートのデプロイをテストする

リソースを実際にデプロイすることなく、テンプレートとパラメーターの値をテストするには、[az group deployment validate](/cli/azure/group/deployment#az-group-deployment-validate) を使用します。 

```azurecli-interactive
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

エラーが検出された場合は、エラー メッセージが返されます。 たとえば、ストレージ アカウント SKU について間違った値を渡した場合は、次のエラーが返されます。

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

## <a name="next-steps"></a>次の手順

- この記事の例では、既定のサブスクリプションのリソース グループにリソースをデプロイしています。 別のサブスクリプションを使用するには、「[Manage multiple Azure subscriptions (複数の Azure サブスクリプションの管理)](/cli/azure/manage-azure-subscriptions-azure-cli)」を参照してください。
- リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
- テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
- 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
- SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-cli-sas-token.md)」を参照してください。
- 複数のリージョン間で、サービスを安全にロールアウトするには、[Azure Deployment Manager](deployment-manager-overview.md) に関する記事を参照してください。
