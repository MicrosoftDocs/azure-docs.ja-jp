---
title: サービス カタログ マネージド アプリを発行する
description: 組織のメンバーを対象とする Azure マネージド アプリケーションを作成する方法について説明します。
author: tfitzmac
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: b255cafb9040f87c902fe6c094c3e0db3d461e4b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093636"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>クイック スタート:マネージド アプリケーション定義を作成して発行する

このクイックスタートでは、[Azure Managed Applications](overview.md) の操作の概要を説明します。 社内のメンバーを対象とするマネージド アプリケーションを作成して発行することができます。

対象のサービス カタログにマネージド アプリケーションを発行するには、次の操作を行う必要があります。

* マネージド アプリケーションでデプロイするリソースを定義するテンプレートを作成する。
* マネージド アプリケーションをデプロイするときに、ポータルのユーザー インターフェイス要素を定義する。
* 必要なテンプレート ファイルを含む _.zip_ パッケージを作成する。
* どのユーザー、グループ、またはアプリケーションがユーザーのサブスクリプションのリソース グループにアクセスする必要があるかを決める。
* _.zip_ パッケージを指定して ID アクセスを要求するマネージド アプリケーション定義を作成する。

## <a name="create-the-arm-template"></a>ARM テンプレートを作成する

各マネージ アプリケーション定義には、_mainTemplate.json_ というファイルが含まれています。 この中で、デプロイする Azure リソースを定義します。 テンプレートは、通常の ARM テンプレートと違いはありません。

_mainTemplate.json_ というファイルを作成します。 名前の大文字と小文字は区別されます。

次の JSON をファイルに追加します。 このファイルで、ストレージ アカウントを作成するパラメーターを定義し、ストレージ アカウントのプロパティを指定します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountNamePrefix": {
      "type": "string"
    },
    "storageAccountType": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageEndpoint": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
    }
  }
}
```

_mainTemplate.json_ ファイルを保存します。

## <a name="define-your-create-experience"></a>作成エクスペリエンスを定義する

マネージド アプリケーションを作成するためのポータル エクスペリエンスは、発行者が定義します。 ポータル インターフェイスは、_createUiDefinition.json_ ファイルによって生成されます。 ドロップダウン、テキスト ボックス、パスワード ボックスなどの[コントロール要素](create-uidefinition-elements.md)を使用して、各パラメーターの入力をユーザーがどのように提供するかを定義します。

_createUiDefinition.json_ というファイルを作成します (この名前の大文字と小文字は区別されます)

次のスターター JSON をファイルに追加し、保存します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "storageConfig",
        "label": "Storage settings",
        "subLabel": {
          "preValidation": "Configure the infrastructure settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Storage settings",
        "elements": [
          {
            "name": "storageAccounts",
            "type": "Microsoft.Storage.MultiStorageAccountCombo",
            "label": {
              "prefix": "Storage account name prefix",
              "type": "Storage account type"
            },
            "defaultValue": {
              "type": "Standard_LRS"
            },
            "constraints": {
              "allowedTypes": [
                "Premium_LRS",
                "Standard_LRS",
                "Standard_GRS"
              ]
            }
          }
        ]
      }
    ],
    "outputs": {
      "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
      "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
      "location": "[location()]"
    }
  }
}
```

詳細については、[CreateUiDefinition の概要](create-uidefinition-overview.md)に関するページを参照してください。

## <a name="package-the-files"></a>ファイルのパッケージ化

_app.zip_ という _.zip_ ファイルに 2 つのファイルを追加します。 2 つのファイルは _.zip_ ファイルのルートに配置する必要があります。 このファイルをフォルダーに配置すると、マネージ アプリケーション定義を作成するときに、必要なファイルが存在しないことを示すエラーが発生します。

パッケージは、想定される実行場所からアクセスできる場所にアップロードしてください。 ストレージ アカウントに一意の名前を指定する必要があります。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>マネージド アプリケーション定義の作成

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory ユーザー グループまたはアプリケーションの作成

次に、顧客のリソースを管理するためのユーザー グループ、ユーザー、またはアプリケーションを選択します。 この ID には、割り当てられているロールに従って、マネージド リソース グループに対するアクセス許可が付与されています。 そのロールには、Azure の組み込みロール (所有者、共同作成者など) をどれでも使用できます。 新しい Active Directory ユーザー グループの作成する場合は、「[Azure Active Directory でグループを作成し、メンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

リソースの管理に使用するユーザー グループのオブジェクト ID が必要です。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>ロール定義 ID の取得

次に、ユーザー、ユーザー グループ、アプリケーションへのアクセス権を付与する Azure の組み込みのロールのロール定義 ID が必要となります。 通常、所有者、共同作成者、閲覧者のいずれかのロールを使います。 次のコマンドは、所有者ロールのロール定義 ID を取得する方法を示しています。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>マネージド アプリケーション定義の作成

マネージ アプリケーション定義を保存するリソース グループがまだない場合は、ここで作成します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

次に、マネージド アプリケーション定義のリソースを作成します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

コマンドが完了すると、リソース グループにマネージド アプリケーション定義が作成されます。

前の例で使用されているパラメーターは次のとおりです。

* **リソース グループ**: マネージド アプリケーション定義が作成されるリソース グループの名前。
* **ロック レベル**: 管理対象リソース グループに対して設定されるロックの種類。 これによって、このリソース グループに対して問題となるような操作を顧客が実行できないようにします。 現在サポートされているロック レベルは ReadOnly だけです。 ReadOnly が指定されている場合、マネージド リソース グループに存在するリソースの読み取りしか顧客は実行できません。 マネージド リソース グループへのアクセス権が付与されている発行元 ID は、ロックの対象外となります。
* **authorizations**:管理対象リソース グループへのアクセス許可を付与する際に使うプリンシパル ID とロール定義 ID を記述します。 `<principalId>:<roleDefinitionId>` の形式で指定します。 複数の値が必要な場合は、`<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>` という形式で指定します。 このとき値は、コンマで区切って指定します。
* **パッケージ ファイルの URI**: 必要なファイルが含まれた _.zip_ パッケージの場所。

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>マネージド アプリケーション定義用に独自のストレージを使用する

管理アプリケーション定義は、作成時に指定したストレージ アカウント内に保存することを選択できます。これにより、規制のニーズに合わせて場所とアクセスを完全に管理できます。

> [!NOTE]
> 独自のストレージの使用は、マネージド アプリケーション定義の ARM テンプレートまたは REST API デプロイでのみサポートされます。

### <a name="select-your-storage-account"></a>使うストレージ アカウントを選ぶ

サービス カタログで使用するマネージド アプリケーション定義を含めるための[ストレージ アカウントを作成](../../storage/common/storage-account-create.md)する必要があります。

ストレージ アカウントのリソース ID をコピーします。 これは、後で定義をデプロイするときに使用します。

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>ストレージ アカウントに "アプライアンス リソースプロバイダー" のロールの割り当てを設定する

マネージド アプリケーション定義をストレージ アカウントにデプロイするには、ストレージ アカウントのコンテナーに定義ファイルを書き込むことができるように、事前に **アプライアンス リソースプロバイダー** ロールに共同作成者のアクセス許可を付与する必要があります。

1. [Azure portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. **[アクセス制御 (IAM)]** を選択して、ストレージ アカウントのアクセス制御設定を表示します。 **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。
1. **[ロールの割り当ての追加]** ウィンドウで、 **[共同作成者]** ロールを選択します。
1. **[アクセスの割り当て先]** フィールドで **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。
1. **[選択]** で、**アプライアンス リソースプロバイダー** ロールを検索して選択します。
1. ロールの割り当てを保存します。

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>ARM テンプレートを使用してマネージド アプリケーション定義をデプロイする

パッケージ化されたマネージド アプリケーションをサービス カタログの新しいマネージド アプリケーション定義としてデプロイし、その定義ファイルを独自のストレージ アカウントに格納して保持するには、次の ARM テンプレートを使用します。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "applicationName": {
      "type": "string",
      "metadata": {
        "description": "Managed Application name"
      }
    },
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
    },
    "definitionStorageResourceID": {
      "type": "string",
      "metadata": {
        "description": "Storage account resource ID for where you're storing your definition"
      }
    },
    "_artifactsLocation": {
      "type": "string",
      "metadata": {
        "description": "The base URI where artifacts required by this template are located."
      }
    }
  },
  "variables": {
    "lockLevel": "None",
    "description": "Sample Managed application definition",
    "displayName": "Sample Managed application definition",
    "managedApplicationDefinitionName": "[parameters('applicationName')]",
    "packageFileUri": "[parameters('_artifactsLocation')]",
    "defLocation": "[parameters('definitionStorageResourceID')]",
    "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
    "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Solutions/applicationDefinitions",
      "apiVersion": "2020-08-21-preview",
      "name": "[variables('managedApplicationDefinitionName')]",
      "location": "[parameters('location')]",
      "properties": {
        "lockLevel": "[variables('lockLevel')]",
        "description": "[variables('description')]",
        "displayName": "[variables('displayName')]",
        "packageFileUri": "[variables('packageFileUri')]",
        "storageAccountId": "[variables('defLocation')]"
      }
    }
  ],
  "outputs": {}
}
```

`applicationDefinitions` プロパティに `storageAccountId` という名前の新しいプロパティが追加されています。その値として、対象の定義を格納するストレージ アカウントの ID を指定します。

アプリケーション定義ファイルが、指定されたストレージ アカウントの `applicationDefinitions` という名前のコンテナーに保存されることを確認できます。

> [!NOTE]
> セキュリティを強化するために、マネージド アプリケーション定義を作成し、[暗号化が有効になっている Azure ストレージ アカウント BLOB に保存できます](../../storage/common/storage-service-encryption.md)。 定義の内容は、ストレージ アカウントの暗号化オプションを使用して暗号化されます。 そのファイルに対するアクセス許可を持つユーザーのみが、サービス カタログ内の定義を確認できます。

## <a name="make-sure-users-can-see-your-definition"></a>ユーザーが定義を確認できるようにする

自身がアクセスできるマネージ アプリケーション定義に、組織の他のユーザーもアクセスできることを確認する必要があります。 定義に対して閲覧者以上のロールをユーザーに付与してください。 ユーザーはこのレベルのアクセスをサブスクリプションまたはリソース グループから継承している場合があります。 定義にアクセスできるユーザーを確認したり、ユーザーやグループを追加したりするには、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

マネージド アプリケーション定義を発行しました。 今度は、その定義のインスタンスをデプロイする方法を確認しましょう。

> [!div class="nextstepaction"]
> [クイック スタート: サービス カタログ アプリのデプロイ](deploy-service-catalog-quickstart.md)
