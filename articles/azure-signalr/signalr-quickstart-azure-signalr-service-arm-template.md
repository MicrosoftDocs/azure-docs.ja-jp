---
title: 'クイックスタート: Azure SignalR サービスを作成する - ARM テンプレート'
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure SignalR Service を作成する方法について説明します。
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: zhshang
ms.date: 10/02/2020
ms.openlocfilehash: 4ab029048b37a4dcb44ef405249dcb9e20de70cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94841617"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>クイックスタート: ARM テンプレートを使用して Azure SignalR Service をデプロイする

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure SignalR Service を作成する方法を説明します。 Azure SignalR Service は、Azure portal、PowerShell、CLI のいずれかを使用してデプロイできます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal にサインイン後、テンプレートが開きます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure portal から ARM テンプレートを使用して Azure に Azure SignalR Service をデプロイするためのボタン。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

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

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-signalr/)からのものです。

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

このテンプレートには、次の Azure リソースが 1 つ定義されています。

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal から ARM テンプレートを使用して Azure SignalR Service をデプロイするには、次のリンクを選択します。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure portal から ARM テンプレートを使用して Azure に Azure SignalR Service をデプロイするためのボタン。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

**[Deploy an Azure SignalR service]\(Azure SignalR Service のデプロイ\)** ページで、次の手順を実行します。

1. **[サブスクリプション]** が既定値になっているので、必要に応じて変更します。

2. **[リソース グループ]** の **[新規作成]** を選択し、新しいリソース グループの名前を入力し、 **[OK]** を選択します。

3. 新しいリソース グループを作成した場合は、リソース グループの **リージョン** を選択します。

4. 必要に応じて、Azure SignalR Service の新しい **名前** と **場所** (**eastus2** など) を入力します。 名前を指定しない場合は、自動的に生成されます。 Azure SignalR Service の場所は、リソース グループのリージョンと同じでも異なっていてもかまいません。 場所を指定しなかった場合は、リソース グループと同じリージョンに設定されます。

5. **[価格レベル]** ( **[Free_F1]** または **[Standard_S1]** ) を選択し、 **[Capacity]\(キャパシティ\)** (SignalR ユニットの数) を入力します。 **[サービス モード]** として、 **[既定]** (ハブ サーバーが必要)、 **[サーバーレス]** (サーバー接続を許可しない)、 **[クラシック]** (ハブにサーバー接続がある場合にのみハブ サーバーにルーティング) のいずれかを選択してください。 次に、 **[Enable Connectivity Logs]\(接続ログを有効にする\)** または **[Enable Messaging Logs]\(メッセージング ログを有効にする\)** を選択します。

    > [!NOTE]
    > **[Free_F1]** 価格レベルの場合、容量は 1 ユニットに制限されます。

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Azure portal から Azure SignalR Service を作成するための ARM テンプレートのスクリーンショット。":::

6. **[Review + create]\(レビュー + 作成\)** を選択します。

7. 利用規約を読んで、 **[作成]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> PowerShell スクリプトをローカルで実行したい場合は、まず「`Connect-AzAccount`」と入力して Azure の資格情報を設定してください。

ARM テンプレートを使用して Azure SignalR Service をデプロイするには、以下のコードを使用します。 次の項目を入力するよう求められます。

* 新しい Azure SignalR Service の名前とリージョン
* 新しいリソース グループの名前とリージョン
* Azure の価格レベル (**Free_F1** または **Standard_S1**)
* SignalR ユニット容量 (1、2、5、10、20、50、100)
  > [!NOTE]
  > **[Free_F1]** 価格レベルの場合、容量は 1 ユニットに制限されます。
* サービス モード: ハブ サーバーを必要とする場合は **Default**、サーバー接続を一切許可しない場合は **Serverless**、ハブにサーバー接続がある場合にのみハブ サーバーにルーティングする場合は **Classic**
* 接続またはメッセージングのログを有効にするかどうか (**true** または **false**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

ARM テンプレートを使用して Azure SignalR Service をデプロイするには、以下のコードを使用します。 次の項目を入力するよう求められます。

* 新しい Azure SignalR Service の名前とリージョン
* 新しいリソース グループの名前とリージョン
* Azure の価格レベル (**Free_F1** または **Standard_S1**)
* SignalR ユニット容量 (1、2、5、10、20、50、100)
    > [!NOTE]
    > **[Free_F1]** 価格レベルの場合、容量は 1 ユニットに制限されます。
* サービス モード: ハブ サーバーを必要とする場合は **Default**、サーバー接続を一切許可しない場合は **Serverless**、ハブにサーバー接続がある場合にのみハブ サーバーにルーティングする場合は **Classic**
* 接続またはメッセージングのログを有効にするかどうか (**true** または **false**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> デプロイが完了するまでに数分かかる場合があります。 Azure SignalR Service の名前とリソース グループの名前は書き留めておいてください。デプロイしたリソースを後で確認する際に使用します。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

新しい Azure SignalR Service の概要を確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、**SignalR** を検索して選択します。

2. SignalR の一覧から新しいサービスを選択します。 新しい Azure SignalR Service の **[概要]** ページが表示されます。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Azure SignalR Service の詳細を確認するには、以下の対話型コードを実行します。 新しいサービスの名前とリソース グループを入力する必要があります。

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Azure SignalR Service の詳細を確認するには、以下の対話型コードを実行します。 新しいサービスの名前とリソース グループを入力する必要があります。

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
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

## <a name="next-steps"></a>次のステップ

Resource Manager テンプレートの作成手順について説明したチュートリアルについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [ チュートリアル: 初めての ARM テンプレートを作成してデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
