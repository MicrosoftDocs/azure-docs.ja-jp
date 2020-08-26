---
title: Azure CLI のサンプル スクリプト - Azure App Configuration ストアの作成
titleSuffix: Azure App Configuration
description: サンプル Azure CLI スクリプトを使用して、Azure App Configuration ストアを作成します。 スクリプトで使用されているコマンドへのリファレンス記事のリンクを参照してください。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: sample
ms.date: 01/24/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7b3221c55cef6207ea38ac1375202acd8b8ab4f1
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588328"
---
# <a name="create-an-azure-app-configuration-store"></a>Azure App Configuration ストアの作成

このサンプル スクリプトでは、新しいリソース グループ内に Azure App Configuration の新しいインスタンスを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location eastus \
  --resource-group $myResourceGroupName \
  --query endpoint \
  --sku free \
  -o tsv
  )

# Get the AppConfig connection string 
appConfigConnectionString=$(az appconfig credential list \
--resource-group $myResourceGroupName \
--name $myAppConfigStoreName \
--query "[?name=='Secondary Read Only'] .connectionString" -o tsv)

# Echo the connection string for use in your application
echo "$appConfigConnectionString"
```

新しいリソース グループに対して生成された実際の名前はメモしておいてください。 このリソース グループ名は、すべてのグループ リソースを削除するときに使います。

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して新しいリソース グループと App Configuration ストアを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az appconfig create](/cli/azure/appconfig#az-appconfig-create) | App Configuration ストア リソースを作成します。 |
| [az appconfig credential list](/cli/azure/appconfig/credential#az-appconfig-credential-list) | App Configuration ストアのアクセス キーを一覧表示します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の App Configuration の CLI サンプル スクリプトは、[Azure App Configuration の CLI サンプル](../cli-samples.md)のページにあります。
