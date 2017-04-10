---
title: "Visual Studio 2015 を使用して Azure App Services にデプロイする | Microsoft Docs"
description: "この記事では、Visual Studio 2015 と Azure SDK を使用して、Web App、API App、または Mobile App を Azure Government にデプロイする方法について説明します。"
services: azure-government
cloud: gov
documentationcenter: 
author: sdubeymsft
manager: zakramer
ms.assetid: 8f9a3700-b9ee-43b7-b64d-2e6c3b57d4c0
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/03/2016
ms.author: sdubeymsft
translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 159c891dedb66dfaa238981b395b4bba074c5716
ms.lasthandoff: 02/22/2017


---
# <a name="deploy-to-azure-app-services-using-visual-studio-2015"></a>Visual Studio 2015 を使用して Azure App Services にデプロイする
この記事では、Visual Studio 2015 を使用して、Azure Government に Azure App Services アプリ (API アプリ、Web アプリ、モバイル アプリ) をデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件
* [Visual Studio の前提条件] (../app-service-api/app-service-api-dotnet-get-started.md#prerequisites) を参照して、Visual Studio 2015 と Azure SDK をインストールして構成します。
* [こちらの手順] (documentation-government-manage-subscriptions.md) に従って、Visual Studio を Azure Government アカウントに接続するように構成します。

## <a name="open-app-project-in-visual-studio"></a>Visual Studio でプロジェクトを開く
* Visual Studio で、既存のアプリソリューション/プロジェクトを開くか、[こちらの手順] (../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application) に従ってプロジェクトを作成するか、[こちらの手順] (../app-service-api/app-service-api-dotnet-get-started.md#download-the-sample-application) に従ってサンプル アプリをダウンロードします。
* アプリを Visual Studio で実行して、ローカルで動作することを確認します。

## <a name="deploy-to-azure-government"></a>Azure Government へのデプロイ
* **Visual Studio を Azure Government アカウントに接続するように構成**した後のアプリ サービスにデプロイするための手順は、Azure パブリックの場合とまったく同じです (アカウントへの接続手順は、「前提条件」セクションで既に実行しています)。
* アプリをデプロイするには、[こちらの手順] (../app-service-api/app-service-api-dotnet-get-started.md#a-idcreateapiappa-create-an-api-app-in-azure-and-deploy-code-to-it) に従ってください。

### <a name="references"></a>参照
* [Visual Studio を使用した Azure App Service への ASP.NET Web アプリのデプロイ] (../app-service-web/web-sites-dotnet-get-started.md)
* その他のデプロイ方法については、[Azure App Service へのアプリのデプロイ] (../app-service-web/web-sites-deploy.md) を参照してください。
* 一般的な App Service のドキュメントについては、[App Service - API Apps のドキュメント] (../app-service-api/index.md) を参照してください。

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、[Microsoft Azure Government のブログ](https://blogs.msdn.microsoft.com/azuregov/)をご覧ください。

