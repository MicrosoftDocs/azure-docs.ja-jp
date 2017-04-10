---
title: "初めての Python Web アプリを Azure に&5; 分で作成する | Microsoft Docs"
description: "サンプル Python アプリをデプロイして、App Service での Web アプリの実行がいかに簡単であるかを説明します。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 53d936366de30d3d149b170c4ef52574c65dcbc8
ms.lasthandoff: 03/18/2017


---
# <a name="create-your-first-python-web-app-in-azure-in-five-minutes"></a>初めての Python Web アプリを Azure に&5; 分で作成する
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

このクイック スタートを読むと、初めての Python Web アプリを [Azure App Service](../app-service/app-service-value-prop-what-is.md) に数分でデプロイできるようになります。

開始する前に、Azure CLI がインストールされていることを確認してください。 詳細については、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)を参照してください。

## <a name="log-in-to-azure"></a>Azure へのログイン
`az login` を実行し、画面の指示に従って Azure にログインします。
   
```azurecli
az login
```
   
## <a name="create-a-resource-group"></a>リソース グループの作成   
[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 ここには、Web アプリとその SQL Database バックエンドなど、まとめて管理するすべての Azure リソースを置くことができます。

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

`---location` に使用できる値を確認するには、Azure CLI コマンド `az appservice list-locations` を使用してください。

## <a name="create-an-app-service-plan"></a>App Service プランを作成する
"Free" [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を作成します。 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
```

## <a name="create-a-web-app"></a>Web アプリを作成する
`<app_name>` に一意の名前を指定して Web アプリを作成します。

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="deploy-sample-application"></a>サンプル アプリケーションをデプロイする
サンプル Python アプリを GitHub からデプロイします。

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-python-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Web アプリを確認する
Azure でアプリがライブ実行されるのを確認するには、次のコマンドを実行します。

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

初めての Python Web アプリを Azure App Services でライブ実行することができました。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ

[Web アプリの CLI スクリプト](app-service-cli-samples.md) サンプルに目を通します。

