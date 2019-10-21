---
title: Azure App Service Mobile Apps で iOS アプリを作成する | Microsoft Docs
description: このチュートリアルに従って、Objective-C または Swift で iOS を開発用するための Azure モバイル アプリ バックエンドの使用を開始します。
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a0b73ba248ae9a12f9ee01f591ddbbedecc25f70
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388686"
---
# <a name="create-an-ios-app"></a>iOS アプリの作成

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center は、モバイル アプリ開発の中心となるエンドツーエンドの統合サービスをサポートしています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。
>
> モバイル アプリケーションにクラウド サービスを統合することを検討している場合は、今すぐ [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) にサインアップしてください。

## <a name="overview"></a>概要

このチュートリアルでは、[Azure App Service Mobile Apps](app-service-mobile-value-prop.md) (クラウドのバックエンド サービス) を iOS アプリに追加する方法について説明します。 最初の手順で、Azure 上に新しいモバイル バックエンドを作成します。 次に、Azure にデータを保存する単純な "*Todo リスト*" iOS サンプル アプリをダウンロードします。

このチュートリアルを完了するには、Mac と [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/) が必要です。

## <a name="create-a-new-azure-mobile-app-backend"></a>新しい Azure モバイル アプリ バックエンドを作成する

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>データベース接続を作成し、クライアントとサーバー プロジェクトを構成する
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>iOS アプリを実行する

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
