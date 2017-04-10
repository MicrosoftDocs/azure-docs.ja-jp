---
title: "初めての PHP Web アプリを Azure に&5; 分で作成する | Microsoft Docs"
description: "サンプル PHP アプリをデプロイして、App Service での Web アプリの実行がいかに簡単であるかを説明します。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: afecc8997631bf507c797e56a9e3fc0d1df27614
ms.lasthandoff: 03/21/2017


---
# <a name="create-your-first-php-web-app-in-azure-in-five-minutes"></a>初めての PHP Web アプリを Azure に&5; 分で作成する
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

このクイック スタートを読むと、初めての PHP Web アプリを [Azure App Service](../app-service/app-service-value-prop-what-is.md) に数分でデプロイできるようになります。

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
Linux コンテナーを実行する "Standard" [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を作成します。 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --is-linux --sku S1
```

## <a name="create-a-web-app"></a>Web アプリを作成する
`<app_name>` に一意の名前を指定して Web アプリを作成します。

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="configure-the-linux-container"></a>Linux コンテナーを構成する
既定の PHP 7.0.6 イメージを使用するように Linux コンテナーを構成します。

```azurecli
az appservice web config update --php-version 7.0.6 --name <app_name> --resource-group myResourceGroup
```
## <a name="deploy-sample-application"></a>サンプル アプリケーションをデプロイする
サンプル PHP アプリを GitHub からデプロイします。

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-php-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Web アプリを確認する
Azure でアプリがライブ実行されるのを確認するには、次のコマンドを実行します。

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

初めての PHP Web アプリを Azure App Services でライブ実行することができました。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ

[Web アプリの CLI スクリプト](app-service-cli-samples.md) サンプルに目を通します。

