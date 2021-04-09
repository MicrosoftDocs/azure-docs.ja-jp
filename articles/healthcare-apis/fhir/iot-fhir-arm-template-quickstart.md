---
title: 'クイックスタート: ARM テンプレートを使用して Azure IoT Connector for FHIR (プレビュー) をデプロイする'
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure IoT Connector for FHIR (プレビュー) をデプロイする方法について説明します。
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 01/21/2021
ms.openlocfilehash: b241484b8e8d981036fff4998d475b8b80fae42d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019341"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>クイックスタート: Azure Resource Manager (ARM) テンプレートを使用して Azure IoT Connector for FHIR (プレビュー) をデプロイする

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure IoT Connector for Fast Healthcare Interoperability Resources (FHIR&#174;)* という Azure API for FHIR の機能をデプロイする方法について説明します。 Azure IoT Connector for FHIR の実践的なインスタンスをデプロイするために、このテンプレートでは、さらに親 Azure API for FHIR サービスをデプロイし、デバイス シミュレーターから Azure IoT Connector for FHIR にテレメトリをエクスポートする Azure IoT Central アプリケーションもデプロイします。 Azure IoT Connector for FHIR は、Azure portal、PowerShell、CLI のいずれかから ARM テンプレートを実行してデプロイできます。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal にサインイン後、テンプレートが開きます。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure portal から ARM テンプレートを使用して Azure IoT Connector for FHIR を Azure にデプロイする。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

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

この[テンプレート](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json)には、次の Azure リソースが定義されています。

* **Microsoft.HealthcareApis/services**
* **Microsoft.HealthcareApis/services/iomtconnectors**
* **Microsoft.IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>テンプレートのデプロイ

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal から ARM テンプレートを使用して Azure IoT Connector for FHIR をデプロイするには、次のリンクを選択します。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure portal から ARM テンプレートを使用して Azure IoT Connector for FHIR サービスを Azure にデプロイする。":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

**[Deploy Azure API for FHIR]\(Azure API for FHIR のデプロイ\)** ページで、次の作業を行います。

1. **[サブスクリプション]** が既定値になっているので、必要に応じて別のサブスクリプションに変更します。

2. **[リソース グループ]** の **[新規作成]** を選択し、新しいリソース グループの名前を入力し、 **[OK]** を選択します。

3. 新しいリソース グループを作成した場合は、リソース グループの **リージョン** を選択します。

4. **[FHIR Service Name]\(FHIR サービス名\)** に、新しい Azure API for FHIR インスタンスの名前を入力します。

5. Azure API for FHIR の **[場所]** を選択します。 この場所は、リソース グループのリージョンと同じでも異なっていてもかまいません。

6. **[Iot Connector Name]\(IoT Connector 名\)** に、Azure IoT Connector for FHIR インスタンスの名前を入力します。

7. **[接続名]** に、Azure IoT Connector for FHIR 内に作成される接続の名前を入力します。 シミュレートされたデバイスのテレメトリを Azure IoT Central アプリケーションが Azure IoT Connector for FHIR にプッシュするときに、この接続が使用されます。

8. **[Iot Central Name]\(IoT Central 名\)** に、新しい Azure IoT Central アプリケーションの名前を入力します。 このアプリケーションは、"*患者の継続的なモニタリング*" テンプレートを使用してデバイスをシミュレートします。

9. **[IoT Central Location]\(IoT Central の場所\)** ボックスの一覧から IoT Central アプリケーションの場所を選択します。 

10. **[Review + create]\(レビュー + 作成\)** を選択します。

11. 利用規約を読んで、 **[作成]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> PowerShell スクリプトをローカルで実行したい場合は、まず「`Connect-AzAccount`」と入力して Azure の資格情報を設定してください。

`Microsoft.HealthcareApis` リソースプロバイダーがまだサブスクリプションに登録されていない場合は、以下に示した対話型のコードで登録できます。 Azure Cloud Shell でコードを実行するには、コード ブロックの上部にある **[使ってみる]** を選択してください。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

`Microsoft.IoTCentral` リソースプロバイダーがまだサブスクリプションに登録されていない場合は、以下に示した対話型のコードで登録できます。 Azure Cloud Shell でコードを実行するには、コード ブロックの上部にある **[使ってみる]** を選択してください。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

ARM テンプレートを使用して Azure IoT Connector for FHIR サービスをデプロイするには、以下のコードを使用します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

`Microsoft.HealthcareApis` リソースプロバイダーがまだサブスクリプションに登録されていない場合は、以下に示した対話型のコードで登録できます。 Azure Cloud Shell でコードを実行するには、コード ブロックの上部にある **[使ってみる]** を選択してください。

```azurecli-interactive
az extension add --name healthcareapis
```

`Microsoft.IoTCentral` リソースプロバイダーがまだサブスクリプションに登録されていない場合は、以下に示した対話型のコードで登録できます。

```azurecli-interactive
az extension add --name azure-iot
```

ARM テンプレートを使用して Azure IoT Connector for FHIR サービスをデプロイするには、以下のコードを使用します。

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> デプロイが完了するまでに数分かかる場合があります。 Azure API for FHIR サービス、Azure IoT Central アプリケーション、リソース グループの名前は書き留めておいてください。デプロイしたリソースを後で確認する際に使用します。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

新しい Azure API for FHIR サービスの概要を確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、**Azure API for FHIR** を検索して選択します。

2. FHIR の一覧から新しいサービスを選択します。 新しい Azure API for FHIR サービスの **[概要]** ページが表示されます。

3. 新しい FHIR API アカウントがプロビジョニングされていることを確認するために、**FHIR メタデータ エンドポイント** の横にあるリンクを選択して、FHIR API 機能ステートメントをフェッチします。 このリンクは `https://<service-name>.azurehealthcareapis.com/metadata` という形式になっています。 アカウントがプロビジョニングされると、大きな JSON ファイルが表示されます。

4. 新しい Azure IoT Connector for FHIR がプロビジョニングされていることを確認するために、左側のナビゲーション メニューから **[IoT コネクタ (プレビュー)]** を選択して **[IoT Connectors]\(IoT Connector\)** ページを開きます。 このページに、プロビジョニングされた Azure IoT Connector for FHIR が表示され、 *[状態]* の値が *[オンライン]* になっていること、 *[接続]* の値が *1* になっていること、また、 *[デバイス マッピング]* と *[FHIR マッピング]* の両方に *[成功]* アイコンが表示されていることが必要です。

5. [Azure portal](https://portal.azure.com) で、 **[IoT Central アプリケーション]** を検索して選択します。

6. IoT Central アプリケーションの一覧から、新しいサービスを選択します。 新しい IoT Central アプリケーションの **[概要]** ページが表示されます。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Azure API for FHIR サービスの詳細を確認するには、以下の対話型コードを実行します。 新しい FHIR サービスの名前とリソース グループを入力する必要があります。

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> 現時点では、Azure IoT Connector for FHIR に PowerShell コマンドはありません。 Azure IoT Connector for FHIR が正しくプロビジョニングされていることを確認するには、 **[ポータル]** タブにある検証プロセスを使用してください。

Azure IoT Central アプリケーションの詳細を確認するには、以下の対話型コードを実行します。 新しい IoT Central アプリケーションの名前とリソース グループを入力する必要があります。

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[CLI](#tab/CLI)

Azure API for FHIR サービスの詳細を確認するには、以下の対話型コードを実行します。 新しい FHIR サービスの名前とリソース グループを入力する必要があります。

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> 現時点では、Azure IoT Connector for FHIR に CLI コマンドはありません。 Azure IoT Connector for FHIR が正しくプロビジョニングされていることを確認するには、 **[ポータル]** タブにある検証プロセスを使用してください。

Azure IoT Central アプリケーションの詳細を確認するには、以下の対話型コードを実行します。 新しい IoT Central アプリケーションの名前とリソース グループを入力する必要があります。

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>IoT データを Azure IoT Connector for FHIR (プレビュー) に接続する
> [!IMPORTANT]
> このガイドに含まれるデバイス マッピング テンプレートは、IoT Central 内でデータ エクスポート (レガシ) と共に使用するように設計されています。

現在、IoT Central アプリケーションには、データのエクスポートを設定するための PowerShell コマンドや CLI コマンド、ARM テンプレートはありません。 そのため、Azure portal を使用した以下の手順に従ってください。  

IoT Central アプリケーションをデプロイすると、すぐに使用できる 2 つのシミュレートされたデバイスでテレメトリの生成が開始されます。 このチュートリアルでは、Azure IoT Connector for FHIR を介して、テレメトリを *Smart Vitals Patch* シミュレーターから FHIR に取り込みます。 IoT データを Azure IoT Connector for FHIR にエクスポートするには、[IoT Central 内でデータ エクスポート (レガシ) を設定する](../../iot-central/core/howto-export-data-legacy.md)必要があります。 [Data export]\(データ エクスポート\) (レガシ) ページで、次の作業を行います。
- エクスポート先として *[Azure Event Hubs]* を選択します。
- **[Event Hubs 名前空間]** フィールドでは、値 *[接続文字列を使用します]* を選択します。
- **[接続文字列]** フィールドでは、前の手順で取得した Azure IoT Connector for FHIR の接続文字列を指定します。
- **[エクスポートするデータ]** フィールドでは、 **[テレメトリ]** オプションを *[オン]* のままにしておきます。

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Azure API for FHIR でデバイス データを表示する

Azure IoT Connector for FHIR によって作成された FHIR ベースの Observation リソースを、Postman を使用して FHIR サーバーで表示できます。 [Azure API for FHIR にアクセスするように Postman](access-fhir-postman-tutorial.md) を設定し、心拍の値を含む Observation FHIR リソースを表示するように `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` に対して `GET` 要求を行います。

> [!TIP]
> Azure API for FHIR データ プレーンへの適切なアクセス権をユーザーに確実に付与します。 [Azure のロールベースのアクセス制御 (Azure RBAC)](configure-azure-rbac.md) を使用して、必要なデータ プレーン ロールを割り当てます。

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

このクイックスタート ガイドでは、Azure API for FHIR リソースに Azure IoT Connector for FHIR 機能をデプロイしました。 以下の次のステップの中からいずれかを選択して、Azure IoT Connector for FHIR の詳細を確認してください。

Azure IoT Connector for FHIR 内のさまざまなデータ フロー ステージについて説明します。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR のデータ フロー](iot-data-flow.md)

デバイスと FHIR マッピング テンプレートを使用して IoT コネクタを構成する方法について説明します。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR のマッピング テンプレート](iot-mapping-templates.md)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。 FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。
