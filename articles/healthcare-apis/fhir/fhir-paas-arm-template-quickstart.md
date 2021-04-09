---
title: 'クイックスタート: ARM テンプレートを使用して Azure API for FHIR をデプロイする'
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure API for FHIR® (Fast Healthcare Interoperability Resources) をデプロイする方法について説明します。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: cavoeg
ms.date: 10/06/2020
ms.openlocfilehash: 8d7f71477e50e4771bbc7670312943eadf1a8657
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018656"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>クイックスタート: ARM テンプレートを使用して Azure API for FHIR をデプロイする

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure API for FHIR® (Fast Healthcare Interoperability Resources) をデプロイする方法について説明します。 Azure API for FHIR は、Azure portal、PowerShell、CLI のいずれかを使用してデプロイできます。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal にサインイン後、テンプレートが開きます。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure portal から ARM テンプレートを使用して Azure API for FHIR サービスを Azure にデプロイする。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

# <a name="portal"></a>[ポータル](#tab/azure-portal)

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。
* [Azure PowerShell](/powershell/azure/install-az-ps) (コードをローカルで実行したい場合)。

# <a name="cli"></a>[CLI](#tab/CLI)

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。
* コードをローカルで実行したい場合:
    * Bash シェル ([Git for Windows](https://gitforwindows.org) に付属する Git Bash など)。
    * [Azure CLI](/cli/azure/install-azure-cli)。

---

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/)からのものです。

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

このテンプレートには、次の Azure リソースが 1 つ定義されています。

* **Microsoft.HealthcareApis/services**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>テンプレートのデプロイ

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal から ARM テンプレートを使用して Azure API for FHIR をデプロイするには、次のリンクを選択します。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure portal から ARM テンプレートを使用して Azure API for FHIR サービスを Azure にデプロイする。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

**[Deploy Azure API for FHIR]\(Azure API for FHIR のデプロイ\)** ページで、次の作業を行います。

1. **[サブスクリプション]** が既定値になっているので、必要に応じて別のサブスクリプションに変更します。

2. **[リソース グループ]** の **[新規作成]** を選択し、新しいリソース グループの名前を入力し、 **[OK]** を選択します。

3. 新しいリソース グループを作成した場合は、リソース グループの **リージョン** を選択します。

4. 新しい **サービス名** を入力し、Azure API for FHIR の **場所** を選択します。 この場所は、リソース グループのリージョンと同じでも異なっていてもかまいません。

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="Azure portal から ARM テンプレートを使用して Azure API for FHIR をデプロイする。":::

5. **[Review + create]\(レビュー + 作成\)** を選択します。

6. 利用規約を読んで、 **[作成]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> PowerShell スクリプトをローカルで実行したい場合は、まず「`Connect-AzAccount`」と入力して Azure の資格情報を設定してください。

`Microsoft.HealthcareApis` リソースプロバイダーがまだサブスクリプションに登録されていない場合は、以下に示した対話型のコードで登録できます。 Azure Cloud Shell でコードを実行するには、コード ブロックの上部にある **[使ってみる]** を選択してください。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

ARM テンプレートを使用して Azure API for FHIR サービスをデプロイするには、以下のコードを使用します。 このコードでは、新しい FHIR サービスの名前と新しいリソース グループの名前、さらに、それぞれの場所を入力するように求められます。

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

`Microsoft.HealthcareApis` リソースプロバイダーがまだサブスクリプションに登録されていない場合は、以下に示した対話型のコードで登録できます。 Azure Cloud Shell でコードを実行するには、コード ブロックの上部にある **[使ってみる]** を選択してください。

```azurecli-interactive
az extension add --name healthcareapis
```

ARM テンプレートを使用して Azure API for FHIR サービスをデプロイするには、以下のコードを使用します。 このコードでは、新しい FHIR サービスの名前と新しいリソース グループの名前、さらに、それぞれの場所を入力するように求められます。

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> デプロイが完了するまでに数分かかる場合があります。 Azure API for FHIR サービスの名前とリソース グループの名前は書き留めておいてください。デプロイしたリソースを後で確認する際に使用します。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

新しい Azure API for FHIR サービスの概要を確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、**Azure API for FHIR** を検索して選択します。

2. FHIR の一覧から新しいサービスを選択します。 新しい Azure API for FHIR サービスの **[概要]** ページが表示されます。

3. 新しい FHIR API アカウントがプロビジョニングされていることを確認するために、**FHIR メタデータ エンドポイント** の横にあるリンクを選択して、FHIR API 機能ステートメントをフェッチします。 このリンクは `https://<service-name>.azurehealthcareapis.com/metadata` という形式になっています。 アカウントがプロビジョニングされると、大きな JSON ファイルが表示されます。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Azure API for FHIR サービスの詳細を確認するには、以下の対話型コードを実行します。 新しいサービスの名前とリソース グループを入力する必要があります。

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Azure API for FHIR サービスの詳細を確認するには、以下の対話型コードを実行します。 新しいサービスの名前とリソース グループを入力する必要があります。

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループを削除してください。リソース グループを削除すれば、リソース グループ内のリソースが削除されます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を検索して選択します。

2. リソース グループの一覧で、リソース グループの名前を選択します。

3. リソース グループの **[概要]** ページで、 **[リソース グループの削除]** を選択します。

4. 確認のダイアログ ボックスでリソース グループの名前を入力し、 **[削除]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

ARM テンプレートの作成手順については、[初めての ARM テンプレートを作成してデプロイするチュートリアル](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)を参照してください。

## <a name="next-steps"></a>次のステップ

このクイックスタート ガイドでは、ご利用のサブスクリプションに Azure API for FHIR をデプロイしました。 Azure API for FHIR に対してその他の設定を行うには、追加設定の攻略ガイドに進んでください。 Azure API for FHIR を使い始める準備ができたら、アプリケーションの登録方法についての詳しい情報をご覧ください。

>[!div class="nextstepaction"]
>[Azure API for FHIR の追加設定](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[アプリケーションの登録の概要](fhir-app-registration.md)
