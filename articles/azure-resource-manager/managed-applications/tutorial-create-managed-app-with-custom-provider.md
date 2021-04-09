---
title: チュートリアル - カスタム アクションとカスタム リソース
description: このチュートリアルでは、Azure カスタム プロバイダーを備えた Azure マネージド アプリケーションを作成する方法について説明します。
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 50a65583c41dd94b174a33432afcf42b31e67df0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96437050"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>チュートリアル:カスタム アクションおよびリソースを備えたマネージド アプリケーションを作成する

このチュートリアルでは、カスタム アクションおよびリソースを備えた独自のマネージド アプリケーションを作成します。 このマネージド アプリケーションは、`Overview` ページ上でのカスタム アクション、`Table of Content` 内で独立したメニュー項目として表示されるカスタムのリソースの種類、カスタム リソース ページ上のカスタム コンテキスト アクションを備えたものになります。

このチュートリアルに含まれる手順は次のとおりです。

> [!div class="checklist"]
> * マネージド アプリケーション インスタンスを作成するためのユーザー インターフェイス定義ファイルを作成する
> * [Azure カスタム プロバイダー](../custom-providers/overview.md)、Azure ストレージ アカウント、Azure 関数を指定したデプロイ テンプレートを作成する
> * カスタム アクションとリソースを指定したビュー定義アーティファクトを作成する
> * マネージド アプリケーションの定義をデプロイする
> * マネージド アプリケーションのインスタンスをデプロイする
> * カスタム アクションを実行し、カスタム リソースを作成する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下に関する知識が必要です。

* [マネージド アプリケーションの定義を作成および公開する](publish-service-catalog-app.md)方法。
* [Azure portal を使用してサービス カタログ アプリをデプロイする](deploy-service-catalog-quickstart.md)方法。
* [マネージド アプリケーション用の Azure portal のユーザー インターフェイスを作成する](create-uidefinition-overview.md)方法。
* [ビュー定義アーティファクト](concepts-view-definition.md)の機能。
* [Azure カスタムプロバイダー](../custom-providers/overview.md)の機能。

## <a name="user-interface-definition"></a>ユーザー インターフェイスの定義

このチュートリアルでは、マネージド アプリケーションを作成します。アプリケーションのマネージド リソース グループには、カスタム プロバイダー インスタンス、ストレージ アカウント、関数を含めます。 この例で使用する Azure 関数は、アクションとリソースに関するカスタム プロバイダーの操作を処理する API を実装するものです。 Azure ストレージ アカウントは、カスタム プロバイダー リソースの基本ストレージとして使用されます。

マネージド アプリケーション インスタンスの作成に必要なこのユーザー インターフェイス定義ファイルには、`funcname` と `storagename` という入力要素があります。 ストレージ アカウント名と関数名はグローバルに一意である必要があります。 既定では、関数ファイルは [サンプル関数パッケージ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)からデプロイされますが、*createUiDefinition.json* 内にパッケージ リンクの入力要素を追加することでこれを変更できます。

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

*createUiDefinition.json* の出力は次のとおりです。

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

*createUiDefinition.json* の完全なサンプルについては、「[リファレンス: ユーザー インターフェイス要素のアーティファクト](reference-createuidefinition-artifact.md)」を参照してください。

## <a name="template-with-custom-provider"></a>カスタム プロバイダーを指定したテンプレート

カスタム プロバイダーを備えたマネージド アプリケーション インスタンスを作成するには、**mainTemplate.json** の中で名前が **public**、種類が **Microsoft.CustomProviders/resourceProviders** のカスタム プロバイダー リソースを定義する必要があります。 そのリソースで、サービスのリソースの種類とアクションを定義します。 Azure 関数と Azure ストレージ アカウントのインスタンスをデプロイするには、種類が `Microsoft.Web/sites` と `Microsoft.Storage/storageAccounts` のリソースをそれぞれ定義します。

このチュートリアルでは、リソースの種類 `users`、カスタム アクション `ping`、カスタム リソース `users` のコンテキストで実行するカスタム アクション `users/contextAction` を、それぞれ作成します。 リソースの種類とアクションのそれぞれに、[createUiDefinition.json](#user-interface-definition) で指定された名前の関数を指すエンドポイントを指定します。 **routingType** は、リソースの種類なら `Proxy,Cache`、アクションなら `Proxy` に、それぞれ指定します。

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

*mainTemplate.json* のサンプルの全容については、「[リファレンス:デプロイ テンプレート アーティファクト](reference-main-template-artifact.md)」を参照してください。

## <a name="view-definition-artifact"></a>ビュー定義アーティファクト

マネージド アプリケーションの中のカスタム アクションとカスタム リソースを含めたユーザーインターフェイスを定義するには、**viewDefinition.json** アーティファクトを作成する必要があります。 ビュー定義アーティファクトの詳細については、「[Azure Managed Applications のビュー定義アーティファクト](concepts-view-definition.md)」を参照してください。

このチュートリアルでは、次のものを定義します。
* 基本的な入力テキストを使ってカスタム アクション `TestAction` を表したツールバー ボタンを備えた *[概要]* ページ。
* カスタムのリソースの種類 `users` を表示する *[ユーザー]* ページ。
* 種類が `users` のカスタム リソースのコンテキストで実行される、 *[ユーザー]* ページ内のカスタム リソース アクション `users/contextAction`。

次の例は、[概要] ページのビュー構成を示したものです。

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

以下の例には、カスタム リソース アクションを備えた [ユーザー] リソース ページの構成が含まれています。

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

*viewDefinition.json* のサンプルの全容については、「[リファレンス:ビュー定義アーティファクト](reference-view-definition-artifact.md)」を参照してください。

## <a name="managed-application-definition"></a>マネージド アプリケーションの定義

マネージド アプリケーションに関する次のアーティファクトを zip アーカイブとしてパッケージ化し、ストレージにアップロードします。

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

ファイルはいずれも、ルート レベルに配置する必要があります。 アーティファクトが入ったパッケージは、GitHub BLOB や Azure ストレージ アカウント BLOB などの任意のストレージに格納できます。 アプリケーション パッケージをストレージ アカウントにアップロードするスクリプトは次のとおりです。 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

以下の Azure CLI スクリプトを実行するか、Azure portal で手順に従って、サービス カタログ マネージド アプリケーションの定義をデプロイします。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**マネージド アプリケーション センター**」と入力し、選択します。
2. **[Managed Applications Center]\(マネージド アプリケーション センター\)** 上で **[サービス カタログ アプリケーション定義]** を選択し、 **[追加]** をクリックします。 
    
    ![サービス カタログの追加](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. サービス カタログの定義を作成するための値を指定します。

    * サービス カタログの定義に一意の **名前** のほか、(オプションで) **表示名** と *説明* を指定します。
    * **サブスクリプション**、**リソース グループ**、アプリケーションの定義が作成される **場所** を選択します。 zip パッケージ用に使用しているものと同じリソース グループを使用することも、新しいリソース グループを作成することもできます。
    * **[パッケージ ファイルの URI]** には、前の手順で作成した zip ファイルのパスを指定します。

    ![値を指定する](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. [Authentication and Lock Level]\(認証とロックのレベル\) セクションで、 **[Add Authorization]\(承認の追加\)** を選択します。

    ![承認を追加する](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. リソースを管理する Azure Active Directory グループを選択し、 **[OK]** を選択します。

   ![承認グループを追加する](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. すべての値を指定したら、 **[Create]\(作成\)** を選択します。

   ![マネージド アプリケーション定義を作成する](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>マネージド アプリケーション インスタンス

マネージド アプリケーションの定義をデプロイしたら、以下のスクリプトを実行するか、Azure portal で手順に従って、カスタム プロバイダーを備えたマネージド アプリケーション インスタンスをデプロイします。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**マネージド アプリケーション センター**」と入力し、選択します。
2. **[Managed Applications Center]\(マネージド アプリケーション センター\)** 上で **[サービス カタログ アプリケーション]** を選択し、 **[追加]** をクリックします。 

    ![マネージド アプリケーションの追加](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. **[サービス カタログ アプリケーション]** ページで、検索ボックスにサービス カタログの定義の表示名を入力します。 前の手順で作成した定義を選択して、 **[作成]** をクリックします。

    ![サービス カタログの選択](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. サービス カタログの定義からマネージド アプリケーション インスタンスを作成するための値を指定します。

    * **サブスクリプション**、**リソース グループ**、アプリケーション インスタンスが作成される **場所** を選択します。
    * Azure 関数と Azure ストレージ アカウントにそれぞれ一意の名前を指定します。

    ![アプリケーションの設定](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. 検証に合格したら、 **[OK]** をクリックしてマネージド アプリケーションのインスタンスをデプロイします。 
    
    ![マネージド アプリケーションのデプロイ](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>カスタム アクションとリソース

サービス カタログ アプリケーション インスタンスをデプロイした後には、2 つの新しいリソース グループが作成されています。 1 つめのリソース グループ `applicationGroup` にはマネージド アプリケーションのインスタンスが含まれます。2 つめのリソース グループ `managedResourceGroup` は、そのマネージド アプリケーションのためのリソース (**カスタム プロバイダー** など) を保持するものです。

![アプリケーション リソース グループ](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

マネージド アプリケーション インスタンスに移動すると、[概要] ページでは **カスタム アクション** の実行、[ユーザー] ページでは **users** カスタム リソースの作成、カスタム リソースでは **カスタム コンテキスト アクション** の実行ができます。

* [概要] ページに移動し、[Ping Action]\(Ping アクション\) ボタンをクリックします。

![カスタム アクションの実行](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* [ユーザー] ページに移動し、[追加] ボタンをクリックします。 リソースを作成するための入力を指定し、フォームを送信します。

![[ユーザー] で選択された [追加] ボタンを示すスクリーンショット。](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* [ユーザー] ページに移動し、"users" リソースを選択して [Custom Context Action]\(カスタム コンテキスト アクション\) をクリックします。

![[Custom Context Action]\(カスタム コンテキスト アクション\) が選択されていることを示すスクリーンショット。](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>ヘルプを探しています

Azure Managed Applications に関して不明な点がある場合には、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps) で質問してみてください。 同様の質問が既に質問され回答されているため、投稿する前にまず確認してください。 `azure-managedapps` タグを追加して早く応答を受け取りましょう。

## <a name="next-steps"></a>次のステップ

マネージド アプリケーションを Microsoft Azure Marketplace に発行するには、「[Marketplace の Azure マネージド アプリケーション](../../marketplace/create-new-azure-apps-offer.md)」を参照してください。

[Azure カスタム プロバイダー](../custom-providers/overview.md)について学習します。
