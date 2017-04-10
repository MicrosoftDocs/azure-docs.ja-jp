---
title: "Azure CLI とテンプレートを使用してリソースをデプロイする | Microsoft Docs"
description: "Azure Resource Manager と Azure CLI を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 71b73c6a7b86c4ba3a890d15811958d35ac9a359
ms.lasthandoff: 03/16/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [ポータル](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

このトピックでは、[Azure CLI 2.0](/cli/azure/install-az-cli2) と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。  テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。 テンプレートがストレージ アカウントに存在する場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

## <a name="deploy"></a>デプロイ

* デプロイを速やかに開始するには、次のコマンドを使用してインライン パラメーターを含むローカル テンプレートをデプロイします。

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create \
      --name ExampleDeployment \
      --resource-group ExampleGroup \
      --template-file storage.json \
      --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  デプロイが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のような結果を含むメッセージが表示されます。

  ```azurecli
  "provisioningState": "Succeeded",
  ```
  
* `az account set` コマンドは、既定のサブスクリプション以外のサブスクリプションを使用する場合にのみ必要です。 すべてのサブスクリプションとその ID を表示するには、次のコマンドを使用します。

  ```azurecli
  az account list
  ```

* 外部テンプレートをデプロイするには、**template-uri** パラメーターを使用します。
   
   ```azurecli
   az group deployment create \
       --name ExampleDeployment \
       --resource-group ExampleGroup \
       --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
       --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
   ```

* パラメーター値をファイルで渡すには、次のコマンドを使用します。

   ```azurecli
   az group deployment create \
       --name ExampleDeployment \
       --resource-group ExampleGroup \
       --template-file storage.json \
       --parameters @storage.parameters.json
   ```

   パラメーター ファイルは次の形式にする必要があります。

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
        "storageNamePrefix": {
            "value": "contoso"
        },
        "storageSKU": {
            "value": "Standard_GRS"
        }
     }
   }
   ```


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

完全モードを使用するには、モード パラメーターを使用します。

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
```

## <a name="deploy-template-from-storage-with-sas-token"></a>SAS トークンを使用してストレージからテンプレートをデプロイする
SAS トークンを使用したデプロイの際に、テンプレートをストレージ アカウントに追加し、リンクできます。

> [!IMPORTANT]
> 次の手順に従うことにより、テンプレートを含む BLOB はアカウントの所有者だけがアクセスできるようになります。 ただし、BLOB の SAS トークンを作成すると、その URI を持つ誰もが BLOB にアクセスできるようになります。 もし別のユーザーが URI を傍受した場合、そのユーザーは、テンプレートにアクセスできます。 SAS トークンの使用はテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。
> 
> 

### <a name="add-private-template-to-storage-account"></a>ストレージ アカウントにプライベートのテンプレートを追加する
次の例では、プライベート ストレージ アカウントのコンテナーを設定し、テンプレートをアップロードします。
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>デプロイ時に SAS トークンを指定する
ストレージ アカウントにプライベートのテンプレートをデプロイするため、SAS トークンを生成してテンプレートの URI に含めます。 デプロイの完了に必要な時間を確保できるように有効期限を設定します。
   
```azurecli
seconds='@'$(( $(date +%s) + 1800 ))
expiretime=$(date +%Y-%m-%dT%H:%MZ --date=$seconds)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

リンクされたテンプレートでの SAS トークン使用例については、「 [Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

## <a name="debug"></a>デバッグ

失敗したデプロイの操作についての情報を確認するには、次のコマンドを使用します。
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。

## <a name="complete-deployment-script"></a>デプロイメント スクリプトの完了

次の例は、[テンプレートのエクスポート](resource-manager-export-template.md)機能により生成されたテンプレートをデプロイするための Azure CLI 2.0 スクリプトを示しています。

```azurecli
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

# -e: immediately exit if any command has a non-zero exit status
# -o: prevents errors in a pipeline from being masked
# IFS new value is less likely to cause confusing bugs when looping arrays or arguments (e.g. $@)

usage() { echo "Usage: $0 -i <subscriptionId> -g <resourceGroupName> -n <deploymentName> -l <resourceGroupLocation>" 1>&2; exit 1; }

declare subscriptionId=""
declare resourceGroupName=""
declare deploymentName=""
declare resourceGroupLocation=""

# Initialize parameters specified from command line
while getopts ":i:g:n:l:" arg; do
    case "${arg}" in
        i)
            subscriptionId=${OPTARG}
            ;;
        g)
            resourceGroupName=${OPTARG}
            ;;
        n)
            deploymentName=${OPTARG}
            ;;
        l)
            resourceGroupLocation=${OPTARG}
            ;;
        esac
done
shift $((OPTIND-1))

#Prompt for parameters is some required parameters are missing
if [[ -z "$subscriptionId" ]]; then
    echo "Subscription Id:"
    read subscriptionId
    [[ "${subscriptionId:?}" ]]
fi

if [[ -z "$resourceGroupName" ]]; then
    echo "ResourceGroupName:"
    read resourceGroupName
    [[ "${resourceGroupName:?}" ]]
fi

if [[ -z "$deploymentName" ]]; then
    echo "DeploymentName:"
    read deploymentName
fi

if [[ -z "$resourceGroupLocation" ]]; then
    echo "Enter a location below to create a new resource group else skip this"
    echo "ResourceGroupLocation:"
    read resourceGroupLocation
fi

#templateFile Path - template file to be used
templateFilePath="template.json"

if [ ! -f "$templateFilePath" ]; then
    echo "$templateFilePath not found"
    exit 1
fi

#parameter file path
parametersFilePath="parameters.json"

if [ ! -f "$parametersFilePath" ]; then
    echo "$parametersFilePath not found"
    exit 1
fi

if [ -z "$subscriptionId" ] || [ -z "$resourceGroupName" ] || [ -z "$deploymentName" ]; then
    echo "Either one of subscriptionId, resourceGroupName, deploymentName is empty"
    usage
fi

#login to azure using your credentials
az account show 1> /dev/null

if [ $? != 0 ];
then
    az login
fi

#set the default subscription id
az account set --name $subscriptionId

set +e

#Check for existing RG
az group show $resourceGroupName 1> /dev/null

if [ $? != 0 ]; then
    echo "Resource group with name" $resourceGroupName "could not be found. Creating new resource group.."
    set -e
    (
        set -x
        az resource group create --name $resourceGroupName --location $resourceGroupLocation 1> /dev/null
    )
    else
    echo "Using existing resource group..."
fi

#Start deployment
echo "Starting deployment..."
(
    set -x
    az resource group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath
)

if [ $?  == 0 ];
 then
    echo "Template has been successfully deployed"
fi
```

## <a name="next-steps"></a>次のステップ
* .NET クライアント ライブラリを使用したリソースのデプロイの例については、「[Deploy resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」 (.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ) を参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
* ソリューションを別の環境にデプロイする方法については、「 [Microsoft Azure の開発環境とテスト環境](solution-dev-test-environments.md)」を参照してください。
* セキュリティで保護された値を渡す KeyVault 参照を使用する方法については、「 [デプロイメント時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。
* デプロイの自動化についての 4 回シリーズの解説については、「[Automating application deployments to Azure Virtual Machines (Azure Virtual Machines へのアプリケーションのデプロイの自動化)](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 このシリーズでは、アプリケーションのアーキテクチャ、アクセスとセキュリティ、可用性と拡張性、およびアプリケーションのデプロイについて説明しています。


