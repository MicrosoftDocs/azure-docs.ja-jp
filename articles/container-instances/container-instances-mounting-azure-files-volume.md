---
title: "Azure Container Instances での Azure Files ボリュームのマウント"
description: "Azure Files ボリュームをマウントして、Azure Container Instances で状態を保持する方法について説明します"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 4248a3769ba8a0fb067b3904d55d487fe67e5778
ms.contentlocale: ja-jp
ms.lasthandoff: 08/12/2017

---

# <a name="mounting-an-azure-file-share-with-azure-container-instances"></a>Azure Container Instances での Azure ファイル共有のマウント

既定では、Azure Container Instances はステートレスです。 コンテナーがクラッシュまたは停止すると、すべての状態が失われます。 コンテナーの有効期間後も状態を保持するには、外部ストアからボリュームをマウントする必要があります。 この記事では、Azure Container Instances で使用できるように Azure ファイル共有をマウントする方法について説明します。

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する

Azure Container Instances で Azure ファイル共有を使用するには、まず、ファイル共有を作成する必要があります。 次のスクリプトを実行して、ファイル共有および共有自体をホストするためのストレージ アカウントを作成してください。 ストレージ アカウント名はグローバルに一意にする必要があるため、このスクリプトは、ベース文字列にランダムな値を追加します。

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>ストレージ アカウント アクセスの詳細を取得する

Azure Container Instances で Azure ファイル共有をボリュームとしてマウントするには、ストレージ アカウント名、共有名、ストレージ アクセス キーの 3 つの値が必要です。 

上記のスクリプトでは、ストレージ アカウント名は最後にランダムな値で作成されました。 最後の文字列 (ランダムな部分を含む) にクエリーを実行するには、次のコマンドを使用します。

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group myResourceGroup --query "[?contains(name,'mystorageaccount')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

共有名は既にわかっているため (上記のスクリプトでは *acishare*)、あとはストレージ アカウント キーです。このキーを見つけるには、次のコマンドを使用します。

```azurecli-interactive
$STORAGE_KEY=$(az storage account keys list --resource-group myResourceGroup --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>Azure Key Vault でストレージ アカウント アクセスの詳細を格納する

ストレージ アカウント キーによりデータへのアクセスが保護されます。したがって、このキーは、Azure Key Vault に格納することをお勧めします。 

Azure CLI を使用してキー コンテナーを作成します。

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g myResourceGroup
```

`enabled-for-template-deployment` スイッチを使用すると、Azure Resource Manager が、デプロイ時にキー コンテナーからシークレットをプルできます。

ストレージ アカウント キーを新しいシークレットとしてキー コンテナーに格納します。

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>ボリュームをマウントする

Azure ファイル共有をボリュームとしてコンテナーにマウントするプロセスは、2 つのステップに分かれています。 まず、コンテナー グループを定義する一環として共有の詳細を指定します。その後、グループ内の 1 つ以上のコンテナーで、ボリュームをどのようにマウントするかを指定します。

ボリュームをマウントできるように定義するには、Azure Resource Manager テンプレートで `volumes` 配列をコンテナー グループ定義に追加し、それを個別のコンテナーの定義で参照します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }  
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

テンプレートには、ストレージ アカウント名とキーがパラメーターとして含まれています。このパラメーターは、別のパラメーター ファイルで指定できます。 パラメーター ファイルを設定するには、ストレージ アカウント名、Azure Key Vault のリソース ID、ストレージ キーを格納するときに使用したキー コンテナー シークレットの3 つの値が必要です。 これまでの手順に従っている場合、この値は次のスクリプトで取得できます。

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

その値をパラメーター ファイルに挿入します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },    
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>コンテナーをデプロイして、ファイルを管理する

テンプレートが定義されている場合は、Azure CLI を使用して、コンテナーを作成し、そのボリュームをマウントできます。 テンプレート ファイルの名前が *azuredeploy.json*、パラメーター ファイルの名前が *azuredeploy.parameters.json* の場合、コマンド ラインは次のようになります。

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group myResourceGroup
```

コンテナーが起動したら、**seanmckenna/aci-hellofiles** イメージ経由でデプロイされる単純な Web アプリを使用して、指定したマウント パスにある Azure ファイル共有のファイルを管理できます。 この Web アプリの IP アドレスは以下を使用して取得します。

```azurecli-interactive
az container show --resource-group myResourceGroup --name hellofiles -o table
```

[Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com)などのツールを使用して、ファイル共有に書き込まれるファイルを取得して検査できます。

>[!NOTE]
> Azure Resource Manager テンプレートやパラメーター ファイルの使用、および Azure CLI でのデプロイの詳細については、「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-cli.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Container Instances の[クイック スタート](container-instances-quickstart.md)を使用して、最初のコンテナーをデプロイします
- [Azure Container Instances とコンテナー オーケストレーターの関係](container-instances-orchestrator-relationship.md)について学習します

