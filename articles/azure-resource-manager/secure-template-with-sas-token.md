---
title: SAS トークンを使用した Azure Resource Manager テンプレートの安全なデプロイ
description: Azure Resource Manager テンプレートを使用して、SAS トークンで保護されているリソースを Azure にデプロイします。 Azure PowerShell と Azure CLI を表示します。
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: tomfitz
ms.openlocfilehash: f396618350e4f4a9be09db421d073aec6ba52b65
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69037303"
---
# <a name="deploy-private-resource-manager-template-with-sas-token"></a>SAS トークンを使用したプライベート Resource Manager テンプレートのデプロイ

お使いのテンプレートがストレージ アカウントに配置されている場合、それが公開されないようにテンプレートへのアクセスを制限できます。 セキュリティで保護されたテンプレートにアクセスするには、そのテンプレート用に SAS (Shared Access Signature) トークンを作成し、デプロイ時にそのトークンを提供します。 この記事では、Azure PowerShell または Azure CLI を使用して SAS トークンでテンプレートをデプロイする方法について説明します。

## <a name="create-storage-account-with-secured-container"></a>セキュリティで保護されたコンテナーでのストレージ アカウントの作成

次のスクリプトでは、パブリック アクセスがオフになっているストレージ アカウントとコンテナーを作成できます。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>ストレージ アカウントへのテンプレートのアップロード

これで、お使いのテンプレートをストレージ アカウントにアップロードする準備ができました。 使用するテンプレートへのパスを指定します。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>デプロイ時に SAS トークンを指定する

ストレージ アカウントにプライベートのテンプレートをデプロイするため、SAS トークンを生成してテンプレートの URI に含めます。 デプロイの完了に必要な時間を確保できるように有効期限を設定します。

> [!IMPORTANT]
> テンプレートを含む BLOB は、アカウントの所有者のみがアクセスできます。 ただし、BLOB の SAS トークンを作成すると、その URI を持つ誰もが BLOB にアクセスできるようになります。 もし別のユーザーが URI を傍受した場合、そのユーザーは、テンプレートにアクセスできます。 SAS トークンはお使いのテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。
>

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

リンクされたテンプレートでの SAS トークン使用例については、「 [Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。


## <a name="next-steps"></a>次の手順
* テンプレートのデプロイ方法については、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](resource-group-template-deploy.md)」を参照してください。
* テンプレートのデプロイ用の完全なサンプル スクリプトについては、[Resource Manager テンプレート スクリプトのデプロイ](resource-manager-samples-powershell-deploy.md)に関するページを参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
