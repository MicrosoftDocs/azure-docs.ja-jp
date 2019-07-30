---
title: Azure App Service Mobile Apps で Cordova アプリを作成する | Microsoft Docs
description: Apache Cordova 開発向けの Azure モバイル アプリ バックエンドの使用を開始するには、このチュートリアルに従ってください。
services: app-service\mobile
documentationcenter: javascript
author: elamalani
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
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8a1634cfa7b701401927859a9fbd3fb203b61a77
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445653"
---
# <a name="create-an-apache-cordova-app"></a>Apache Cordova アプリの作成
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started) を今すぐチェックしてください。
>

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

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>データベース接続を作成し、クライアントとサーバー プロジェクトを構成する
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Apache Cordova アプリのダウンロードと実行
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]
<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Apache Cordova の Visual Studio ツール]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx