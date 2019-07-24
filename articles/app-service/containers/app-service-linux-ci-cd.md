---
title: Web App for Containers での継続的なデプロイ - Azure App Service | Microsoft Docs
description: Web App for Containers で継続的なデプロイを設定する方法について説明します。
keywords: Azure App Service, Linux, Docker, acr, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: 4acadc4c08ef50e7d52303689c38c43f81187669
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315534"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Web App for Containers での継続的デプロイ

このチュートリアルでは、管理された [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) リポジトリまたは [Docker Hub](https://hub.docker.com) からのカスタム コンテナー イメージの継続的なデプロイを構成します。

## <a name="enable-continuous-deployment-with-acr"></a>ACR で継続的デプロイを有効にする

![ACR Webhook のスクリーンショット](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ページの左側にある **[App Service]** オプションを選択します。
3. 継続的デプロイを構成するアプリの名前を選択します。
4. **[コンテナーの設定]** ページで、**[単一コンテナー]** を選択します
5. **[Azure Container Registry]** を選択します
6. **[継続的なデプロイ] > [オン]** を選択します
7. **[保存]** を選択して、継続的デプロイを有効にします。

## <a name="use-the-acr-webhook"></a>ACR Webhook を使用する

継続的デプロイを有効にした後は、Azure Container Registry の Webhook ページで、新しく作成した Webhook を表示できます。

![ACR Webhook のスクリーンショット](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

Container Registry で、[Webhooks] をクリックすると現在の Webhook が表示されます。

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Docker Hub で継続的デプロイを有効にする (省略可能)

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ページの左側にある **[App Service]** オプションを選択します。
3. 継続的デプロイを構成するアプリの名前を選択します。
4. **[コンテナーの設定]** ページで、**[単一コンテナー]** を選択します
5. **[Docker Hub]** を選択します
6. **[継続的なデプロイ] > [オン]** を選択します
7. **[保存]** を選択して、継続的デプロイを有効にします。

![アプリ設定のスクリーンショット](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Webhook URL をコピーします。 Docker Hub に対する Webhook を追加するには、<a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Docker Hub に対する Webhook</a> の説明に従ってください。

## <a name="next-steps"></a>次の手順

* [Azure App Service on Linux の概要](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [App Service on Linux での .NET Core Web アプリの作成](quickstart-dotnetcore.md)
* [App Service on Linux で Ruby Web アプリを作成する](quickstart-ruby.md)
* [Web App for Containers で Docker/Go Web アプリをデプロイする](quickstart-docker-go.md)
* [Azure App Service on Linux の FAQ](./app-service-linux-faq.md)
* [Azure CLI を使用して Web App for Containers を管理する](./app-service-linux-cli.md)
