---
title: Azure CLI を使って Web App for Containers を管理する - Azure App Service | Microsoft Docs
description: Azure CLI を使って Web App for Containers を管理します。
keywords: Azure App Service, Web アプリ, CLI, Linux, OSS
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 21f6963fbaada4524f27602454d38e7252a5e8b9
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720389"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Azure CLI を使って Web App for Containers を管理する

この記事のコマンドを使用して、Azure CLI を使って Web App for Containers を作成および管理できます。
この CLI の新しいバージョンは、次の 2 つの方法で使用を開始できます。

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) をコンピューターにインストールする
* [Azure Cloud Shell (プレビュー)](../../cloud-shell/overview.md) を使用する

## <a name="create-a-linux-app-service-plan"></a>Linux App Service プランの作成

Linux App Service プランを作成するには、次のコマンドを使用できます。

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>カスタム Docker コンテナー Web アプリを作成する

Web アプリを作成し、カスタム Docker コンテナーを実行するように構成するには、次のコマンドを使用できます。

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Docker コンテナーのログ記録をアクティブにする

Docker コンテナーのログ記録をアクティブにするには、次のコマンドを使用できます。

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>既存の Web App for Containers アプリのカスタム Docker コンテナーを変更する

作成済みのアプリの現在の Docker イメージを新しいイメージに変更するには、次のコマンドを使用できます。

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>プライベート レジストリの Docker イメージを使用する

プライベート レジストリのイメージを使用するようにアプリを構成できます。 レジストリの URL、ユーザー名、およびパスワードを指定する必要があります。 これは、次のコマンドを使用して実現できます。

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>カスタム Docker イメージの継続的なデプロイを有効にする

次のコマンドで CD 機能を有効にして Webhook URL を取得できます。 この URL を使用して、DockerHub または Azure Container Registry のリポジトリを構成できます。

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>組み込みのランタイム フレームワークの 1 つを使用して Web App for Containers アプリを作成する

PHP 5.6 Web App for Containers アプリを作成するには、次のコマンドをご利用いただけます。

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>既存の Web App for Containers アプリのフレームワークのバージョンを変更する

作成済みのアプリの現在のフレームワークのバージョンを Node.js 6.11 に変更するには、次のコマンドを使用できます。

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Web アプリの Git へのデプロイを設定する

アプリの Git へのデプロイを設定するには、次のコマンドを使用できます。

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>次の手順

* [Azure App Service on Linux とは](app-service-linux-intro.md)
* [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (プレビュー)](../../cloud-shell/overview.md)
* [Azure App Service でステージング環境を設定する](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Web App for Containers での継続的デプロイ](app-service-linux-ci-cd.md)
