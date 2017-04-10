---
title: "Azure App Service Mobile Apps で Android アプリを作成する | Microsoft Docs"
description: "Android 開発向けの Azure モバイル アプリ バックエンドの使用を開始するには、このチュートリアルに従ってください。"
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: adrianha
editor: 
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 7752cd091d7952a905001f14414eba84b90f0d51
ms.lasthandoff: 03/09/2017


---
# <a name="create-an-android-app"></a>Android アプリの作成
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure モバイル アプリ バックエンドを使用してクラウド ベースのバックエンド サービスを Android モバイル アプリに追加する方法を説明します。  新しいモバイル アプリ バックエンドと、アプリのデータを Azure に格納する簡単な *Todo リスト* Android アプリを作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての Android チュートリアルを実行する前に完了しておく必要があります。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* Android Studio 統合開発環境と最新の Android プラットフォームを含む [Android Developer Tools](https://developer.android.com/sdk/index.html)
* Azure Mobile Android SDK。ダウンロードしたクイックスタート プロジェクトの一部として自動的に参照されます。
* [アクティブな Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>新しい Azure モバイル アプリ バックエンドを作成する
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>サーバー プロジェクトの構成
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-android-app"></a>Android アプリのダウンロードと実行
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

