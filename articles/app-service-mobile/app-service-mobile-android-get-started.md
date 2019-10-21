---
title: Azure App Service Mobile Apps で Android アプリを作成する | Microsoft Docs
description: Android 開発向けの Azure モバイル アプリ バックエンドの使用を開始するには、このチュートリアルに従ってください。
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: emalani
ms.openlocfilehash: 0e12017918432ff264375a9489478ac81f3e69b2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388928"
---
# <a name="create-an-android-app"></a>Android アプリの作成
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となるエンドツーエンドの統合サービスをサポートしています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。
>
> モバイル アプリケーションにクラウド サービスを統合することを検討している場合は、今すぐ [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) にサインアップしてください。

## <a name="overview"></a>概要
このチュートリアルでは、Azure モバイル アプリ バックエンドを使用してクラウド ベースのバックエンド サービスを Android モバイル アプリに追加する方法を説明します。  新しいモバイル アプリ バックエンドと、アプリのデータを Azure に格納する簡単な *Todo リスト* Android アプリを作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての Android チュートリアルを実行する前に完了しておく必要があります。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* Android Studio 統合開発環境と最新の Android プラットフォームを含む [Android Developer Tools](https://developer.android.com/sdk/index.html)
* Azure Mobile Android SDK
* [アクティブな Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>新しい Azure モバイル アプリ バックエンドを作成する
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>データベース接続を作成し、クライアントとサーバー プロジェクトを構成する
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Android アプリを実行する
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
