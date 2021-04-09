---
title: PowerShell とテンプレートを使用してリソースをデプロイする
description: Azure Resource Manager と Azure PowerShell を使用してリソースを Azure にデプロイします。 リソースは、Resource Manager テンプレートまたは Bicep ファイルに定義されています。
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 784f17566ce4fb19a7ec5e3fd4a504d7c25f90fe
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521630"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ

この記事では、Azure PowerShell と Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルを使用して Azure にリソースをデプロイする方法について説明します。 Azure ソリューションのデプロイと管理の概念を熟知していない場合は、[テンプレートのデプロイの概要](overview.md)または [Bicep の概要](bicep-overview.md)に関するページをご覧ください。

Bicep ファイルをデプロイするには、[Azure PowerShell バージョン 5.6.0 以降](/powershell/azure/install-az-ps)が必要です。

## <a name="prerequisites"></a>前提条件

デプロイするテンプレートが必要です。 デプロイするテンプレートがない場合は、Azure クイック スタート テンプレート リポジトリから[サンプル テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)を保存します。 この記事で使用されているローカル ファイル名は、_C:\MyTemplates\azuredeploy.json_ です。

Azure PowerShell をインストールし、Azure に接続する必要があります。

- **ローカル コンピューターに Azure PowerShell コマンドレットをインストールします。** 詳細については、[Azure PowerShell の概要](/powershell/azure/get-started-azureps)に関するページを参照してください。
- **[Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** を使用して、Azure に接続します。 Azure サブスクリプションが複数ある場合は、[Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) を実行する必要が生じることもあります。 詳しくは、「[Use multiple Azure subscriptions (複数の Azure サブスクリプションを使用する)](/powershell/azure/manage-subscriptions-azureps)」をご覧ください。

PowerShell がインストールされていない場合は、Azure Cloud Shell を使用できます。 詳細については、「[Azure Cloud Shell から ARM テンプレートをデプロイする](deploy-cloud-shell.md)」を参照してください。

## <a name="deployment-scope"></a>デプロイのスコープ

リソース グループ、サブスクリプション、管理グループ、またはテナントをデプロイのターゲットにすることができます。 使用するコマンドは、デプロイのスコープに応じて異なります。

- **リソース グループ** にデプロイするには、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用します。

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template-or-bicep>
  ```

- **サブスクリプション** にデプロイするには、`New-AzDeployment` コマンドレットの別名である [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment) を使用します。

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  サブスクリプション レベルでのデプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

- **管理グループ** にデプロイするには、[新しい AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment)を使用します。

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  管理グループ レベルでのデプロイの詳細については、「[管理グループ レベルでリソースを作成する](deploy-to-management-group.md)」を参照してください。

- **テナント** にデプロイするには、[新しい AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment) を使用します。

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template-or-bicep>
  ```

  テナント レベルでのデプロイの詳細については、「[テナント レベルでリソースを作成する](deploy-to-tenant.md)」を参照してください。

各スコープに対して、テンプレートをデプロイするユーザーにはリソースを作成するためのアクセス許可が必要です。

## <a name="deployment-name"></a>デプロイ名

ARM テンプレートをデプロイするときに、デプロイに名前を付けることができます。 この名前は、デプロイ履歴からデプロイを取得するのに役立ちます。 デプロイの名前を指定しない場合は、テンプレート ファイルの名前が使用されます。 たとえば、`azuredeploy.json` という名前のテンプレートをデプロイするときにデプロイ名を指定しなかった場合、デプロイの名前は `azuredeploy` になります。

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

ただし、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、その完了直後に、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを実行した場合は、ストレージ アカウントは 2 つになります。 1 つは `storage1` という名前に、もう 1 つは `storage2` という名前になります。 ただし、デプロイ履歴にはエントリが 1 つだけ存在します。

デプロイごとに一意の名前を指定すると、競合なしでそれらを同時に実行できます。 `storage1` という名前のストレージ アカウントをデプロイする `newStorage1` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage2` という名前の別のデプロイを同時に実行した場合は、2 つのストレージ アカウントがデプロイされ、デプロイ履歴には 2 つのエントリが存在します。

同時デプロイによる競合を回避し、デプロイ履歴に一意のエントリが確実に存在するようにするには、各デプロイに一意の名前を付けます。

## <a name="deploy-local-template-or-bicep-file"></a>ローカル テンプレートまたは Bicep ファイルをデプロイする

ローカル コンピューターから、または外部に格納されているテンプレートを使用して、テンプレートをデプロイできます。 ここでは、ローカル テンプレートのデプロイについて説明します。

存在しないリソース グループにデプロイする場合、リソース グループを作成する必要があります。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 名前の末尾をピリオドにすることはできません。

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

ローカル テンプレートまたは Bicep ファイルをデプロイするには、デプロイ コマンドで `-TemplateFile` パラメーターを使用します。 次の例では、テンプレートから取得したパラメーター値を設定する方法も示しています。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep>
```

デプロイが完了するまでに数分かかる場合があります。

## <a name="deploy-remote-template"></a>リモート テンプレートのデプロイ

> [!NOTE]
> 現在、Azure PowerShell ではリモート Bicep ファイルのデプロイはサポートされていません。 リモート Bicep ファイルをデプロイするには、まず CLI Bicep を使用して Bicep ファイルを JSON テンプレートにコンパイルします。

ARM テンプレートをローカル コンピューターに格納する代わりに、外部の場所に格納することもできます。 ソース管理リポジトリ (GitHub など) にテンプレートを格納できます。 または、組織内の共有アクセス用の Azure ストレージ アカウントに格納することができます。

存在しないリソース グループにデプロイする場合、リソース グループを作成する必要があります。 リソース グループ名には、英数字、ピリオド、アンダースコア、ハイフン、かっこのみを含めることができます。 最大長は 90 文字です。 名前の末尾をピリオドにすることはできません。

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

外部テンプレートをデプロイするには、`-TemplateUri` パラメーターを使用します。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name remoteTemplateDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

前の例では、テンプレートにはパブリックにアクセスできる URI が必要になります。テンプレートに機密データを含めてはいけないため、この方法は多くの場合に利用できます。 機密データ (管理者パスワードなど) を指定する必要がある場合は、セキュリティで保護されたパラメーターとしてその値を渡します。 ただし、テンプレートへのアクセスを管理する場合は、[テンプレート スペック](#deploy-template-spec)を使用することを検討してください。

離れた場所にあり、リンクされているテンプレートを、ストレージ アカウントに格納されている相対パスでデプロイするには、`QueryString` を使用して SAS トークンを指定します。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

詳細については、「[リンクされたテンプレートの相対パスを使用する](./linked-templates.md#linked-template)」を参照してください。

## <a name="deploy-template-spec"></a>テンプレート スペックのデプロイ

> [!NOTE]
> 現在、Azure PowerShell では、Bicep ファイルを指定してテンプレート スペックを作成することはサポートされていません。 ただし、[Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) リソースを使用して Bicep ファイルを作成し、テンプレート スペックをデプロイできます。こちらに[例](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep)があります。
ローカルまたはリモートのテンプレートをデプロイする代わりに、[テンプレート スペック](template-specs.md)を作成できます。テンプレート スペックは、ARM テンプレートが含まれる Azure サブスクリプションのリソースです。 これにより、組織内のユーザーとテンプレートを安全に共有することが容易になります。 テンプレート スペックへのアクセス権を付与するには、Azure ロールベースのアクセス制御 (Azure RBAC) を使用します。現在、この機能はプレビュー段階にあります。

次の例では、テンプレート スペックの作成とデプロイの方法を示します。

まず、ARM テンプレートを指定して、テンプレート スペックを作成します。

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0 `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateJsonFile ./mainTemplate.json
```

次に、テンプレート スペックの ID を取得して、デプロイします。

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

詳細については、「[Azure Resource Manager テンプレート スペック (プレビュー)](template-specs.md)」を参照してください。

## <a name="preview-changes"></a>変更のプレビュー

テンプレートをデプロイする前に、テンプレートが環境に与える変更をプレビューすることができます。 [what-if 操作](template-deploy-what-if.md)を使用して、テンプレートによって必要な変更が行われるかどうかを確認します。 What-if はまた、テンプレートのエラーも検証します。

## <a name="pass-parameter-values"></a>パラメーター値を渡す

パラメーター値を渡すには、インライン パラメーターまたはパラメーター ファイルのいずれかを使用できます。

### <a name="inline-parameters"></a>インライン パラメーター

インライン パラメーターを渡すには、`New-AzResourceGroupDeployment` コマンドでパラメーターの名前を指定します。 たとえば、文字列と配列をテンプレートに渡すには、以下を使用します。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

ファイルの内容を取得し、その内容をインライン パラメーターとして提供することもできます。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-template-or-bicep> `
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
  -TemplateFile <path-to-template-or-bicep> `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>パラメーター ファイル

スクリプト内のインライン値としてパラメーターを渡すよりも、パラメーター値を含む JSON ファイルを使用するほうが簡単な場合もあります。 パラメーター ファイルは、ローカル ファイルでも、アクセス可能な URI を持つ外部ファイルでもかまいません。 ARM テンプレートと Bicep ファイルのどちらでも、JSON パラメーター ファイルが使用されます。

パラメーター ファイルの詳細については、「[Resource Manager パラメーター ファイルを作成する](parameter-files.md)」を参照してください。

ローカル パラメーター ファイルを渡すには、`TemplateParameterFile` パラメーターを使用します。

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

外部パラメーター ファイルを渡すには、`TemplateParameterUri` パラメーターを使用します。

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>次のステップ

- エラーが発生したときに正常なデプロイにロールバックするには、「[エラー発生時に正常なデプロイにロールバックする](rollback-on-error.md)」を参照してください。
- リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
- テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
- SAS トークンを必要とするテンプレートをデプロイする方法については、「[SAS トークンを使用してプライベート ARM テンプレートをデプロイする](secure-template-with-sas-token.md)」を参照してください。
