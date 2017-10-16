---
title: "Azure Web App for Containers での継続的なデプロイ | Microsoft Docs"
description: "Azure Web App for Containers で継続的なデプロイを設定する方法について説明します。"
keywords: Azure App Service, Linux, OSS, ACR
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: 27a2c95c09197b3439d3fac7c74d253df2b32b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-deployment-with-azure-web-app-for-containers"></a>Azure Web App for Containers での継続的なデプロイ

このチュートリアルでは、管理対象の [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/) リポジトリまたは [Docker Hub](https://hub.docker.com) からのカスタム コンテナー イメージに継続的なデプロイを構成します。

## <a name="step-1---sign-in-to-azure"></a>手順 1 - Azure にサインインする

Azure Portal (http://portal.azure.com) にサインインします

## <a name="step-2---enable-container-continuous-deployment-feature"></a>手順 2 - コンテナーの継続的なデプロイ機能を有効にする

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) で次のコマンドを実行して、継続的なデプロイ機能を有効にできます。

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
``` 

**[Azure Portal](https://portal.azure.com/)** で、ページの左側にある **[App Service]** オプションをクリックします。

Docker Hub の継続的なデプロイを構成するアプリの名前をクリックします。

**[App settings (アプリ設定)]** で、`true` を使用して `DOCKER_ENABLE_CI` と呼ばれるアプリ設定を追加します。

![アプリ設定のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>手順 3 - Webhook URL を準備する

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) で次のコマンドを実行して、Webhook URL を取得できます。

```azurecli-interactive
az webapp deployment container show-cd-url -n sname1 -g rgname
``` 

Webhook URL には、エンドポイント `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook` が必要です。

`publishingusername` および `publishingpwd` を取得するには、Azure Portal を使用して Web アプリの発行プロファイルをダウンロードします。

![webhook 2 追加のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>手順 4 - webhook を追加する

### <a name="azure-container-registry"></a>Azure Container Registry

レジストリ ポータル ブレードで、**[Webhooks] \(Webhook\)** をクリックし、**[追加]** をクリックして、新しい webhook を作成します。 **[Create webhook]\(webhook の作成\)** ブレードで、webhook に名前を付けます。 [Webhook URI] には、**手順 3** で取得した URL を指定する必要があります

スコープがコンテナー イメージを含むリポジトリとして定義されていることを確認してください。

![webhook のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

イメージが更新されると、Web アプリも新しいイメージで自動的に更新されます。

### <a name="docker-hub"></a>Docker Hub

Docker Hub ページで、**[Webhooks (Webhook)]**、**[CREATE A WEBHOOK (Webhook の作成)]** の順にクリックします。

![webhook 1 追加のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

[Webhook URL] には、**手順 3** で取得した URL を指定する必要があります

![webhook 2 追加のイメージを挿入](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

イメージが更新されると、Web アプリも新しいイメージで自動的に更新されます。

## <a name="next-steps"></a>次のステップ

* [Azure Web App for Containers とは](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)
* [Azure Web App for Containers で .NET Core を使用する](quickstart-dotnetcore.md)
* [Azure Web App for Containers で Ruby を使用する](quickstart-ruby.md)
* [Azure Web App for Containers のカスタム Docker イメージを使用する方法](quickstart-custom-docker-image.md)
* [Azure App Service Web App for Containers の FAQ](./app-service-linux-faq.md) 
* [Azure CLI 2.0 を使って Web App for Containers を管理する](./app-service-linux-cli.md)
