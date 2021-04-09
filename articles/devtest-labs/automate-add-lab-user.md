---
title: Azure DevTest Labs でのラボ ユーザーの追加を自動化する | Microsoft Docs
description: この記事では、Azure Resource Manager テンプレート、PowerShell、および CLI を使用して、Azure DevTest Labs でのラボへのユーザー追加を自動化する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: dc5522cfe694f193b9bbeeb3145808a367a62c12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519403"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボへのラボ ユーザーの追加を自動化する
Azure DevTest Labs によって、Azure portal を使用することにより、セルフサービスの開発テスト環境をすばやく作成できます。 しかし、複数のチームがあり、いくつかの DevTest Labs インスタンスがある場合、作成プロセスの自動化によって時間を節約できます。 [Azure Resource Manager テンプレート](https://github.com/Azure/azure-devtestlab/tree/master/Environments)では、ラボ、ラボ VM、カスタム イメージ、数式を作成し、自動でユーザーを追加できます。 この記事では、DevTest Labs インスタンスにユーザーを追加することに特に焦点を絞って説明します。

ユーザーをラボに追加するには、ラボに **DevTest Labs ユーザー** ロールを追加します。 この記事では、以下の方法のいずれかを使用して、ユーザーのラボへの追加を自動化する方法を示します。

- Azure Resource Manager のテンプレート
- Azure PowerShell コマンドレット 
- Azure CLI。

## <a name="use-azure-resource-manager-templates"></a>Azure リソース マネージャー テンプレートの使用
次のサンプルの Resource Manager テンプレートでは、ラボの **DevTest Labs ユーザー** ロールに追加されるユーザーを指定します。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

ラボを作成するのと同じテンプレートでロールを割り当てている場合、ロールの割り当てリソースとラボの間に依存関係を追加することを忘れないでください。 詳細については、[Azure Resource Manager のテンプレートでの依存関係の定義](../azure-resource-manager/templates/define-resource-dependency.md)に関するページを参照してください。

### <a name="role-assignment-resource-information"></a>ロールの割り当てリソースに関する情報
ロールの割り当てリソースでは、種類と名前を指定する必要があります。

最初に注意することは、リソースの種類はリソース グループ用になる `Microsoft.Authorization/roleAssignments` ではないということです。  代わりに、リソースの種類はパターン `{provider-namespace}/{resource-type}/providers/roleAssignments` に従います。 この場合、リソースの種類は `Microsoft.DevTestLab/labs/providers/roleAssignments` になります。

ロールの割り当て名自体は、グローバルに一意である必要があります。  割り当ての名前には、パターン `{labName}/Microsoft.Authorization/{newGuid}` が使用されます。 `newGuid` はテンプレート用のパラメーター値です。 これによって、ロールの割り当てが一意であることが確認されます。 GUID を作成するためのテンプレート関数がないので、任意の GUID ジェネレーター ツールを使用することで、GUID 自体を生成する必要があります。  

テンプレートで、ロールの割り当ての名前は `fullDevTestLabUserRoleName` 変数によって定義されます。 テンプレートからの正確な行は次のとおりです。

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>ロールの割り当てリソースのプロパティ
ロールの割り当て自体で 3 つのプロパティを定義します。 `roleDefinitionId`、`principalId`、`scope` が必要です。

### <a name="role-definition"></a>ロールの定義
ロールの定義 ID は、既存のロールの定義に対する文字列 ID です。 このロール ID は `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}` のフォームに含まれます。 

サブスクリプション ID は、`subscription().subscriptionId` テンプレート関数を使用することによって取得されます。  

`DevTest Labs User` 組み込みロールに対するロールの定義を取得する必要があります。 [DevTest Labs ユーザー](../role-based-access-control/built-in-roles.md#devtest-labs-user) ロールの GUID を取得するには、[Role Assignments REST API](/rest/api/authorization/roleassignments) または [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) コマンドレットを使用できます。

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

ロール ID は変数セクションで定義され、`devTestLabUserRoleId` という名前が付けられます。 テンプレートで、ロール ID は次のように設定されます: 111111111-0000-0000-11111111111111111。 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>プリンシパル ID
プリンシパル ID は、ラボにラボ ユーザーとして追加する必要がある Active Directory ユーザー、グループ、またはサービス プリンシパルのオブジェクト ID です。 テンプレートでは、パラメーターとして `ObjectId` を使用します。

[Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0)、Get-AzureRMADGroup、または [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell コマンドレットを使用することで、ObjectId を取得できます。 これらのコマンドレットによって、ID プロパティがある Active Directory オブジェクトが 1 つまたはその一覧が返されます。これが必要なオブジェクト ID です。 次の例では、会社の単一ユーザーのオブジェクト ID を取得する方法を示します。

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

[Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0)、[Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)、[Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0) を含む、Azure Active Directory PowerShell コマンドレットを使用することもできます。

### <a name="scope"></a>Scope
スコープでは、ロールの割り当てを適用する必要があるリソースまたはリソース グループを指定します。 リソースには、スコープは次のフォームに含まれます: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`。 テンプレートでは、`subscription-id` パーツに入力するための `subscription().subscriptionId` 関数および `resource-group-name` パーツに入力するための `resourceGroup().name` テンプレート関数を使用します。 これらの関数を使用することは、ロールを割り当てるラボが、現在のサブスクリプション、およびテンプレートのデプロイが行われる同じリソース グループに存在する必要があるということを意味します。 最後のパーツ `resource-name` は、ラボの名前です。 この値は、この例ではテンプレート パラメーターによって受け取ります。 

テンプレートのロール スコープ 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>テンプレートのデプロイ
まず、Resource Manager テンプレートのパラメーターに値を渡す、パラメーター ファイル (例: azuredeploy.parameters.json) を作成します。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

次に、[New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell コマンドレットを使用して、Resource Manager テンプレートをデプロイします。 次の例のコマンドでは、ユーザー、グループ、またはサービス プリンシパルをラボに対する DevTest Labs ユーザー ロールに割り当てます。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

グループのデプロイ名とロールの割り当て GUID が一意である必要があることに注意してください。 非一意の GUID のリソースの割り当てをデプロイしようとすると、`RoleAssignmentUpdateNotPermitted` エラーを受け取ります。

いくつかの Active Directory オブジェクトを自分のラボに対する DevTest Labs ユーザー ロールに追加するために、テンプレートを数回使用する場合は、PowerShell コマンドで動的オブジェクトを使用することを検討してください。 次の例では、[New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid) コマンドレットを使用して、リソース グループのデプロイ名とロールの割り当て GUID を動的に指定します。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Azure PowerShell の使用
概要で説明されているように、新しい Azure のロールの割り当てを作成して、ラボに対する **DevTest Labs ユーザー** ロールにユーザーを追加します。 PowerShell で、[New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) コマンドレットを使用してこれを行います。 このコマンドレットには、柔軟性のために多くの省略可能なパラメーターがあります。 `ObjectId`、`SigninName`、または `ServicePrincipalName` は、アクセス許可を付与されているオブジェクトとして指定できます。  

指定されたラボで DevTest Labs ユーザー ロールにユーザーを追加する、サンプルの Azure PowerShell コマンドは次のとおりです。

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

アクセス許可が付与されているリソースを指定するには、`ResourceName`、`ResourceType`、`ResourceGroup` の組み合わせ、または `scope` パラメーターによって指定できます。 どのパラメーターの組み合わせが使用されていても、Active Directory オブジェクト (ユーザー、グループ、またはサービス プリンシパル)、スコープ (リソース グループまたはリソース)、ロールの定義を一意に識別するために、コマンドレットに十分な情報を提供します。

## <a name="use-azure-command-line-interface-cli"></a>Azure コマンド ライン インターフェイス (CLI) を使用する
Azure CLI で、`az role assignment create` コマンドを使用して、ラボ ユーザーをラボに追加します。 Azure CLI コマンドレットの詳細については、「[Azure CLI を使用して Azure でのロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-cli.md)」を参照してください。

アクセス権が付与されているオブジェクトは、`objectId`、`signInName`、`spn` パラメーターによって指定できます。 オブジェクトにアクセス権が付与されているラボは、`scope` URL または `resource-name`、`resource-type`、`resource-group` パラメーターの組み合わせによって識別できます。

次の Azure CLI の例は、指定されたラボに対する DevTest Labs ユーザー ロールにユーザーを追加する方法を示します。  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [Azure CLI を使用して DevTest Labs で仮想マシンを作成して管理する](devtest-lab-vmcli.md)
- [Azure PowerShell を使用して DevTest Labs で仮想マシンを作成する](devtest-lab-vm-powershell.md)
- [コマンドライン ツールを使用した Azure DevTest Labs 仮想マシンの開始と停止](use-command-line-start-stop-virtual-machines.md)

