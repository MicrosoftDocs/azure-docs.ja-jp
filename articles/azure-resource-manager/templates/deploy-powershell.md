---
title: PowerShell とテンプレートを使用してリソースをデプロイする
description: Azure Resource Manager と Azure PowerShell を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 64993b526b67430266a8b3e85e3bcc233a3e28a3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079521"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ

この記事では、Azure PowerShell と Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure にリソースをデプロイする方法について説明します。 Azure ソリューションのデプロイと管理の概念について詳しくない場合、「[テンプレートのデプロイの概要](overview.md)」をご覧ください。

## <a name="deployment-scope"></a>デプロイのスコープ

リソース グループ、サブスクリプション、管理グループ、またはテナントをデプロイのターゲットにすることができます。 多くの場合、リソース グループをデプロイの対象にします。 より大きなスコープでポリシーとロールの割り当てを適用するには、サブスクリプション、管理グループ、またはテナントのデプロイを使用します。 サブスクリプションにデプロイする際には、リソース グループを作成してそこにリソースをデプロイすることができます。

使用するコマンドは、デプロイのスコープに応じて異なります。

* **リソース グループ**にデプロイするには、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用します。

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* **サブスクリプション**にデプロイするには、New-AzResourceGroupDeployment を使用します。

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  サブスクリプション レベルでのデプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

* **管理グループ**にデプロイするには、[新しい AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment)を使用します。

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  管理グループ レベルでのデプロイの詳細については、「[管理グループ レベルでリソースを作成する](deploy-to-management-group.md)」を参照してください。

* **テナント**にデプロイするには、[新しい AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment) を使用します。

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  テナント レベルでのデプロイの詳細については、「[テナント レベルでリソースを作成する](deploy-to-tenant.md)」を参照してください。

この記事の例では、リソース グループ デプロイを使用します。

## <a name="prerequisites"></a>前提条件

デプロイするテンプレートが必要です。 デプロイするテンプレートがない場合は、Azure クイック スタート テンプレート リポジトリから[サンプル テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)を保存します。 この記事で使用されているローカル ファイル名は、**c:\MyTemplates\azuredeploy.json** です。

Azure Cloud Shell を使用してテンプレートをデプロイする場合を除き、Azure PowerShell をインストールして Azure に接続する必要があります。

- **ローカル コンピューターに Azure PowerShell コマンドレットをインストールします。** 詳細については、[Azure PowerShell の概要](/powershell/azure/get-started-azureps)に関するページを参照してください。
- **[Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** を使用して、Azure に接続します。 Azure サブスクリプションが複数ある場合は、[Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) を実行する必要が生じることもあります。 詳しくは、「[Use multiple Azure subscriptions (複数の Azure サブスクリプションを使用する)](/powershell/azure/manage-subscriptions-azureps)」をご覧ください。

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ

次の例では、リソース グループを作成し、ローカル コンピューターからテンプレートをデプロイします。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 末尾をピリオドにすることはできません。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -Name ExampleDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

デプロイが完了するまでに数分かかる場合があります。

## <a name="deployment-name"></a>デプロイ名

前の例では、デプロイに `ExampleDeployment` という名前を付けました。 デプロイの名前を指定しない場合は、テンプレート ファイルの名前が使用されます。 たとえば、`azuredeploy.json` という名前のテンプレートをデプロイするときにデプロイ名を指定しなかった場合、デプロイの名前は `azuredeploy` になります。

デプロイを実行するたびに、リソース グループのデプロイ履歴にデプロイ名のエントリが追加されます。 別のデプロイを実行するときに同じ名前を付けると、現在のデプロイによって前のエントリが置き換えられます。 デプロイ履歴に一意のエントリを保持する場合は、デプロイごとに一意の名前を付けます。

一意の名前を作成するために、ランダムな数値を割り当てることができます。

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

または、日付の値を追加します。

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

同じリソース グループに対して同じ名前のデプロイを同時に実行した場合は、最後のデプロイのみが完了します。 完了していない同じ名前のデプロイは、最後のデプロイによって置き換えられます。 たとえば、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを同時に実行した場合は、1 つのストレージ アカウントのみがデプロイされます。 結果のストレージ アカウントの名前は `storage2` になります。

ただし、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、その完了直後に、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを実行した場合は、ストレージ アカウントが 2 つになります。 1 つは `storage1` という名前に、もう 1 つは `storage2` という名前になります。 ただし、デプロイ履歴にはエントリが 1 つだけ存在します。

デプロイごとに一意の名前を指定すると、競合なしでそれらを同時に実行できます。 `storage1` という名前のストレージ アカウントをデプロイする `newStorage1` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage2` という名前の別のデプロイを同時に実行した場合は、2 つのストレージ アカウントがデプロイされ、デプロイ履歴には 2 つのエントリが存在します。

同時デプロイによる競合を回避し、デプロイ履歴に一意のエントリが確実に存在するようにするには、各デプロイに一意の名前を付けます。

## <a name="deploy-remote-template"></a>リモート テンプレートのデプロイ

ARM テンプレートをローカル コンピューターに格納する代わりに、外部の場所に格納することもできます。 ソース管理リポジトリ (GitHub など) にテンプレートを格納できます。 または、組織内の共有アクセス用の Azure ストレージ アカウントに格納することができます。

外部テンプレートをデプロイするには、**TemplateUri** パラメーターを使用します。 この例の URI を使用して、GitHub のサンプル テンプレートをデプロイします。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

前の例では、テンプレートにはパブリックにアクセスできる URI が必要になります。テンプレートに機密データを含めてはいけないため、この方法は多くの場合に利用できます。 機密データ (管理者パスワードなど) を指定する必要がある場合は、セキュリティで保護されたパラメーターとしてその値を渡します。 ただし、テンプレートを一般からアクセス可能にしない場合は、プライベートなストレージ コンテナーに格納することで保護できます。 Shared Access Signature (SAS) トークンを必要とするテンプレートをデプロイする方法については、[SAS トークンを使用したプライベート テンプレートのデプロイ](secure-template-with-sas-token.md)に関するページをご覧ください。 チュートリアルについては、「[チュートリアル:ARM テンプレートのデプロイで Azure Key Vault を統合する](template-tutorial-use-key-vault.md)」を参照してください。

## <a name="preview-changes"></a>変更のプレビュー

テンプレートをデプロイする前に、テンプレートが環境に与える変更をプレビューすることができます。 [what-if 操作](template-deploy-what-if.md)を使用して、テンプレートによって必要な変更が行われるかどうかを確認します。 また、what-if はテンプレートのエラーも検証します。

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

シェルにコードを貼り付けて、シェル内を右クリックし、 **[貼り付け]** を選択します。

## <a name="pass-parameter-values"></a>パラメーター値を渡す

パラメーター値を渡すには、インライン パラメーターまたはパラメーター ファイルのいずれかを使用できます。

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

ファイルからのパラメーター値の取得は、構成値を指定する必要がある場合に便利です。 たとえば、[Linux 仮想マシン用の cloud-init の値](../../virtual-machines/linux/using-cloud-init.md)を指定できます。

オブジェクトの配列を渡す必要がある場合、PowerShell でハッシュ テーブルを作成し、そのテーブルを配列に追加します。 デプロイの際に、その配列をパラメーターとして渡します。

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>パラメーター ファイル

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 パラメーター ファイルは、ローカル ファイルでも、アクセス可能な URI を持つ外部ファイルでもかまいません。

パラメーター ファイルの詳細については、「[Resource Manager パラメーター ファイルを作成する](parameter-files.md)」を参照してください。

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

## <a name="next-steps"></a>次のステップ

- エラーが発生したときに正常なデプロイにロールバックするには、「[エラー発生時に正常なデプロイにロールバックする](rollback-on-error.md)」を参照してください。
- リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
- テンプレートでパラメーターを定義する方法については、「[ARM テンプレートの構造と構文を理解する](template-syntax.md)」を参照してください。
- SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](secure-template-with-sas-token.md)」を参照してください。
