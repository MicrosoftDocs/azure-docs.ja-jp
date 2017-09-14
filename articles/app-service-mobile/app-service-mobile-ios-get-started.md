---
title: "Azure App Service Mobile Apps で iOS アプリを作成する | Microsoft Docs"
description: "このチュートリアルに従って、Objective-C または Swift で iOS を開発用するための Azure モバイル アプリ バックエンドの使用を開始します。"
services: app-service\mobile
documentationcenter: ios
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 36936ae66c458fcbedeec95cfa2f573a40c8af53
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="create-an-ios-app"></a>iOS アプリの作成
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、 [Azure Mobile Apps](app-service-mobile-value-prop.md)(クラウドのバックエンド サービス) を iOS アプリに追加する方法について説明します。 まず、新しいモバイル バックエンドを作成します。 その後、簡単な *Todo リスト* iOS アプリを使用して Azure にデータを格納します。

このチュートリアルを完了するには、Mac と [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/) が必要です。

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>手順 I: 新しい Azure Mobile App バックエンドの作成
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>手順 II: バックエンド プロジェクトの構成
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>手順 III: iOS アプリのダウンロードと実行
[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

