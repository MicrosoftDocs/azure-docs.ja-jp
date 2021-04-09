---
title: App Service と Azure CLI を使用して SignalR Service を作成する
description: Azure CLI と App Service を使用して SignalR Service を作成します。 Azure SignalR Service の全 CLI コマンドについて説明します。
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/13/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 673a3583b1ec80fb3ad61ec35a1786a59939f6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563326"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>App Service で SignalR サービスを作成する

このサンプル スクリプトでは、リアルタイムのコンテンツ更新をクライアントにプッシュするために使用される、新しい Azure SignalR Service リソースを作成します。 また、SignalR サービスを使用した ASP.NET Core Web アプリをホストするための、新しい Web アプリと App Service プランも追加します。 Web アプリは、*AzureSignalRConnectionString* という名前のアプリ設定で構成されて、新しい SignalR サービス リソースに接続します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、Azure CLI の *signalr* 拡張機能を使います。 このサンプル スクリプトを使う前に、次のコマンドを実行して Azure CLI の *signalr* 拡張機能をインストールしてください。

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate unique names for the SignalR service, resource group, 
# app service, and app service plan
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"
myWebAppName=SignalRTestWebApp$randomNum
myAppSvcPlanName=$myAppSvcName"Plan"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Create an App Service plan.
az appservice plan create --name $myAppSvcPlanName --resource-group $myResourceGroupName --sku FREE

# Create the Web App
az webapp create --name $myWebAppName --resource-group $myResourceGroupName --plan $myAppSvcPlanName  

# Get the SignalR primary connection string
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "AzureSignalRConnectionString=$primaryConnectionString"
```

新しいリソース グループに対して生成された実際の名前はメモしておいてください。 これは出力に表示されます。 このリソース グループ名は、すべてのグループ リソースを削除するときに使います。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| command | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Azure SignalR Service リソースを作成します。 |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | キーを一覧表示します。これらのキーは、SignalR でリアルタイム コンテンツの更新をプッシュする際、アプリケーションによって使われます。 |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Web アプリをホストするための Azure App Service プランを作成します。 |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | App Service ホスティング プランを使用して Azure Web アプリを作成します。 |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Web アプリ用の新しいアプリ設定を追加します。 このアプリ設定は、SignalR 接続文字列を保存するために使用されます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

Azure SignalR Service のその他の CLI サンプル スクリプトについては、[Azure SignalR サービスのドキュメント](../signalr-reference-cli.md)をご覧ください。
