---
title: Azure App Service Mobile Apps で Cordova アプリを作成する | Microsoft Docs
description: Apache Cordova 開発向けの Azure モバイル アプリ バックエンドの使用を開始するには、このチュートリアルに従ってください。
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
tags: ''
keywords: cordova,javascript,モバイル,クライアント
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: crdun
ms.openlocfilehash: 7014d09bbb62e78c37a9496628e3509b6eaaa4ac
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768231"
---
# <a name="create-an-apache-cordova-app"></a>Apache Cordova アプリの作成
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure モバイル アプリ バックエンドを使用してクラウドベースのバックエンド サービスを Apache Cordova モバイル アプリに追加する方法を説明します。  新しいモバイル アプリ バックエンドと、アプリのデータを Azure に格納する簡単な *Todo リスト* Apache Cordova アプリを作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての Apache Cordova チュートリアルを実行する前に完了しておく必要があります。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* [Visual Studio Community 2017] 以降のバージョンがインストールされた PC
* [Apache Cordova の Visual Studio ツール]
* [アクティブな Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)

Visual Studio をバイパスし、Apache Cordova のコマンド ラインを直接使用することもできます。  コマンド ラインは、Mac コンピューターでチュートリアルを完了するときに便利です。  コマンドラインを使用する Apache Cordova クライアント アプリケーションのコンパイルは、このチュートリアルでは説明しません。

## <a name="create-an-azure-mobile-app-backend"></a>Azure モバイル アプリ バックエンドの作成
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[同様の手順を説明するビデオを見る](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>サーバー プロジェクトの構成
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Apache Cordova アプリのダウンロードと実行
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>次の手順
これでこのクイック スタート チュートリアルは終了です。次のいずれかのチュートリアルに進んでください。

* Apache Cordova アプリに[オフライン データを追加する](app-service-mobile-cordova-get-started-offline-data.md)
* [認証を追加する](app-service-mobile-cordova-get-started-users.md) 
* [プッシュ通知を追加する](app-service-mobile-cordova-get-started-push.md) 

Azure App Service の主要な概念を確認してください。

* [オフライン データ]
* [認証]
* [プッシュ通知]

SDK の使用方法を確認してください。

* [Apache Cordova SDK]
* [ASP.NET サーバー SDK]
* [Node.js サーバー SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: https://www.visualstudio.com/
[Apache Cordova の Visual Studio ツール]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[オフライン データ]: app-service-mobile-offline-data-sync.md
[認証]: app-service-mobile-auth.md
[プッシュ通知]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET サーバー SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js サーバー SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
