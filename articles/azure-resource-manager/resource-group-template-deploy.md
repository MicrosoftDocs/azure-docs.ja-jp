---
title: PowerShell とテンプレートを使用してリソースをデプロイする | Microsoft Docs
description: Azure Resource Manager と Azure PowerShell を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 2ef5cc702bd5035c958a8feb9b6f5051781cd3cc
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649796"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ

Azure PowerShell と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。 Azure ソリューションのデプロイと管理の概念について詳しくは、「[Azure Resource Manager の概要](resource-group-overview.md)」をご覧ください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>デプロイのスコープ

Azure サブスクリプション、またはサブスクリプション内のリソース グループのいずれかを、デプロイの対象として指定できます。 多くの場合、リソース グループをデプロイの対象にします。 サブスクリプション デプロイを使用するのは、サブスクリプション全体にポリシーとロールの割り当てを適用するときです。 また、リソース グループを作成し、それにリソースをデプロイする場合も、サブスクリプション デプロイを使用します。 使用するコマンドは、デプロイのスコープに応じて異なります。

**リソース グループ**にデプロイするには、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用します。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

**サブスクリプション**にデプロイするには、[New-AzDeployment](/powershell/module/az.resources/new-azdeployment) を使用します。

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

この記事の例では、リソース グループ デプロイを使用します。 サブスクリプション デプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

デプロイするテンプレートが必要です。 デプロイするテンプレートがない場合は、Azure クイック スタート テンプレート リポジトリから[サンプル テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)を保存します。 この記事で使用されているローカル ファイル名は、**c:\MyTemplates\azuredeploy.json** です。

Azure Cloud Shell を使用してテンプレートをデプロイする場合以外は、Azure PowerShell をインストールして Azure に接続する必要があります。

- **ローカル コンピューターに Azure PowerShell コマンドレットをインストールします。** 詳細については、[Azure PowerShell の概要](/powershell/azure/get-started-azureps)に関するページを参照してください。
- **[Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** を使用して、Azure に接続します。 Azure サブスクリプションが複数ある場合は、[Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) を実行する必要が生じることもあります。 詳しくは、「[Use multiple Azure subscriptions (複数の Azure サブスクリプションを使用する)](/powershell/azure/manage-subscriptions-azureps)」をご覧ください。

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ

次の例では、リソース グループを作成し、ローカル コンピューターからテンプレートをデプロイします。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 末尾をピリオドにすることはできません。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

デプロイが完了するまでに数分かかる場合があります。

## <a name="deploy-remote-template"></a>リモート テンプレートのデプロイ

Resource Manager テンプレートは、ローカル コンピューターに格納する代わりに、外部の場所に格納することもできます。 ソース管理リポジトリ (GitHub など) にテンプレートを格納できます。 または、組織内の共有アクセス用の Azure ストレージ アカウントに格納することができます。

外部テンプレートをデプロイするには、**TemplateUri** パラメーターを使用します。 この例の URI を使用して、GitHub のサンプル テンプレートをデプロイします。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

前の例では、テンプレートにはパブリックにアクセスできる URI が必要になります。テンプレートに機密データを含めてはいけないため、この方法は多くの場合に利用できます。 機密データ (管理者パスワードなど) を指定する必要がある場合は、セキュリティで保護されたパラメーターとしてその値を渡します。 ただし、テンプレートを一般からアクセス可能にしない場合は、プライベートなストレージ コンテナーに格納することで保護できます。 Shared Access Signature (SAS) トークンを必要とするテンプレートをデプロイする方法については、[SAS トークンを使用したプライベート テンプレートのデプロイ](resource-manager-powershell-sas-token.md)に関するページをご覧ください。 チュートリアルについては、「[チュートリアル:Resource Manager テンプレートのデプロイで Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)」を参照してください。

## <a name="deploy-from-azure-cloud-shell"></a>Azure Cloud Shell からデプロイする

[Azure Cloud Shell](https://shell.azure.com) を使用して、テンプレートをデプロイできます。 外部テンプレートをデプロイするには、テンプレートの URI を指定します。 ローカル テンプレートをデプロイするには、最初に Cloud Shell のストレージ アカウントにテンプレートを読み込む必要があります。 ファイルをシェルにアップロードするには、シェル ウィンドウから **[ファイルのアップロード/ダウンロード]** メニュー アイコンを選択します。

Cloud Shell を開くには、[https://shell.azure.com](https://shell.azure.com) を参照するか、または次のコード セクションの **[使ってみる]** を選択します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

シェルにコードを貼り付けて、シェル内を右クリックし、**[貼り付け]** を選択します。

## <a name="redeploy-when-deployment-fails"></a>デプロイに失敗したときに再デプロイする

この機能は、"*エラー時のロールバック*" とも呼ばれます。 デプロイに失敗した場合、デプロイ履歴から以前に成功したデプロイを自動的に再デプロイすることができます。 再デプロイを指定するには、デプロイ コマンド内で `-RollbackToLastDeployment` または `-RollBackDeploymentName` パラメーターのいずれかを使用します。 この機能は、インフラストラクチャのデプロイに対して既知の正常な状態を取得してあり、それに戻したい場合に便利です。 いくつかの注意事項と制限があります。

- 再デプロイは、以前に実行されたときとまったく同じパラメーターで実行されます。 パラメーターを変更することはできません。
- 以前のデプロイは、[完全モード](./deployment-modes.md#complete-mode)を使用して実行されます。 以前のデプロイに含まれていなかったリソースはすべて削除され、すべてのリソースの構成は以前の状態に設定されます。 [デプロイ モード](./deployment-modes.md)を完全に理解しておいてください。
- 再デプロイではリソースのみが影響を受け、データの変更への影響はありません。
- この機能は、リソース グループのデプロイにおいてのみサポートされ、サブスクリプション レベルのデプロイではサポートされません。 サブスクリプション レベル デプロイについて詳しくは、「[サブスクリプション レベルでリソース グループとリソースを作成する](./deploy-to-subscription.md)」をご覧ください。

このオプションを使用するには、ご自身のデプロイを履歴で特定できるように、デプロイの名前は一意でなければなりません。 一意の名前が付いていないと、失敗した現在のデプロイによって、履歴にある以前の成功したデプロイが上書きされる可能性があります。 このオプションは、ルート レベルのデプロイでのみ使用できます。 入れ子になったテンプレートのデプロイを再デプロイに使用することはできません。

最後に成功したデプロイを再デプロイするには、`-RollbackToLastDeployment` パラメーターをフラグとして追加します。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

特定のデプロイを再デプロイするには、`-RollBackDeploymentName` パラメーターを使用してデプロイの名前を指定します。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

指定したデプロイは成功している必要があります。

## <a name="pass-parameter-values"></a>パラメーター値を渡す

パラメーター値を渡すには、インライン パラメーターまたはパラメーター ファイルのいずれかを使用できます。 この記事の先の例では、インライン パラメーターを示しています。

### <a name="inline-parameters"></a>インライン パラメーター

インライン パラメーターを渡すには、`New-AzResourceGroupDeployment` コマンドでパラメーターの名前を指定します。 たとえば、文字列と配列をテンプレートに渡すには、以下を使用します。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

ファイルの内容を取得し、その内容をインライン パラメーターとして提供することもできます。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

ファイルからのパラメーター値の取得は、構成値を指定する必要がある場合に便利です。 たとえば、[Linux 仮想マシン用の cloud-init の値](../virtual-machines/linux/using-cloud-init.md)を指定できます。

### <a name="parameter-files"></a>パラメーター ファイル

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 パラメーター ファイルは、ローカル ファイルでも、アクセス可能な URI を持つ外部ファイルでもかまいません。

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

ローカル パラメーター ファイルを渡すには、**TemplateParameterFile** パラメーターを使用します。

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

外部パラメーター ファイルを渡すには、**TemplateParameterUri** パラメーターを使用します。

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>パラメーターの優先順位

同じデプロイ操作で、インライン パラメーターとローカル パラメーター ファイルを使用することができます。 たとえば、一部の値をローカル パラメーター ファイルで指定し、その他の値をデプロイ中にインラインで追加します。 ローカル パラメーター ファイルとインラインの両方でパラメーターの値を指定すると、インラインの値が優先されます。

ただし、外部パラメーター ファイルを使用する場合、他の値をインラインまたはローカル ファイルから渡すことはできません。 **TemplateParameterUri** パラメーターでパラメーター ファイルを指定すると、すべてのインライン パラメーターが無視されます。 すべてのパラメーター値を外部ファイル内で指定します。 パラメーター ファイルに含めることができない機密性の高い値がテンプレートに含まれている場合は、その値をキー コンテナーに追加するか、すべてのパラメーター値をインラインで動的に指定してください。

### <a name="parameter-name-conflicts"></a>パラメーター名の競合

PowerShell コマンドのパラメーターのいずれかと名前が同じであるパラメーターがテンプレートに含まれている場合、PowerShell ではテンプレート内のパラメーター名の後ろに **FromTemplate** という文字を付加します。 たとえば、テンプレート内の **ResourceGroupName** という名前のパラメーターは、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) コマンドレットの **ResourceGroupName** パラメーターと競合します。 **ResourceGroupNameFromTemplate** の値を指定するように求められます。 一般的に、このような混乱を防ぐために、デプロイ処理に使用したパラメーターと同じ名前をパラメーターに付けないことが推奨されます。

## <a name="test-template-deployments"></a>テンプレートのデプロイをテストする

リソースを実際にデプロイすることなく、テンプレートとパラメーターの値をテストするには、[Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment) を使用します。 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

エラーが検出されなかった場合は、コマンドが応答なしで終了します。 エラーが検出された場合は、エラー メッセージが返されます。 たとえば、ストレージ アカウント SKU について間違った値を渡した場合は、次のエラーが返されます。

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

テンプレートに構文エラーがある場合は、テンプレートを解析できなかったことを示すエラー メッセージが返されます。 このメッセージには、解析エラーの行番号と位置が表示されます。

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>次の手順

- 複数のリージョン間で、サービスを安全にロールアウトするには、[Azure Deployment Manager](deployment-manager-overview.md) に関する記事を参照してください。
- リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
- テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
- SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-powershell-sas-token.md)」を参照してください。
