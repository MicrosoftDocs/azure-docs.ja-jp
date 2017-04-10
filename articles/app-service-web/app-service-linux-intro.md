---
title: "App Service on Linux の概要 | Microsoft Docs"
description: "App Service on Linux について説明します。"
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9ed502efadbc27ca507fa5be455f32c3bd1b9eee
ms.lasthandoff: 03/10/2017


---
# <a name="introduction-to-app-service-on-linux"></a>App Service on Linux の概要
Azure App Service on Linux は、現在パブリック プレビューの段階です。Linux 上での Web アプリのネイティブな実行をサポートしています。

## <a name="overview"></a>Overview
顧客は App Service on Linux を使用して、サポートされているアプリケーション スタック向けに Web アプリを Linux 上でネイティブにホストすることができます。 次のセクションは、現在サポートされているアプリケーション スタックの一覧です。 

## <a name="features"></a>Features (機能)
App Service on Linux は現在、次のアプリケーション スタックをサポートしています。

* Node.js
    * 4.5.0
    * 4.4.7
    * 6.2.2
    * 6.6.0
    * 6.9.3
* PHP
    * 5.6.23
    * 7.0.6
* .NET Core
    * 1.0
* Ruby
    * 2.3

顧客は次のものを使用してアプリケーションをデプロイすることができます。

* FTP
* ローカル Git
* GitHub
* Bitbucket

アプリケーションのスケーリング方法は次のとおりです。

* 顧客は、App Service プランのレベルを変更することで、Web アプリのスケールアップとスケールダウンを実行できます。
* 顧客は、SKU の範囲内で、アプリケーションをスケールアウトして、複数のアプリ インスタンスを実行することができます。

Kudu の場合、基本的な機能の一部を次のもので使用できます。

* 環境
* デプロイメント
* 基本的なコンソール

## <a name="limitations"></a>制限事項
Azure Portal では、App Service on Linux で現在使用できる機能のみが表示され、残りは非表示となります。 さらに機能を有効にすると、その機能がポータルに表示されます。

仮想ネットワーク統合、Azure Active Directory/サード パーティの認証、Kudu サイト拡張機能などの一部の機能は、完成していません。 これらの機能が完成したら、ドキュメントとブログを更新し、変更についてお知らせします。

このパブリック プレビューは現在、次のリージョンでのみご利用いただけます。

* 米国西部
* 西ヨーロッパ 
* 東南アジア

Web Apps on Linux は専用 App Service プランでのみサポートされており、Free レベルまたは Shared レベルは存在しません。 また、標準の Web アプリと Linux 向け Web アプリの App Service プランは相互に排他的であり、Linux 向けでない App Service プラン内で Linux 向け Web アプリを作成することはできません。

Web Apps on Linux は、Linux 向けでない Web アプリが同じリージョンにないリソース グループ内で作成される必要があります。

Linux 上の Web Apps では、コンパイルされていないソースからの .NET Core アプリのデプロイはまだサポートされていません。 最初に .NET Core アプリをローカルで発行/コンパイルした後、発行されたサイト ビットをアプリにプッシュする必要があります。

## <a name="next-steps"></a>次のステップ
App Service on Linux の使用を開始するには、次のリンクを参照してください。 質問や問題は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿できます。

* [App Service on Linux での Web Apps の作成](app-service-linux-how-to-create-a-web-app.md)
* [App Service on Linux 向けにカスタム Docker イメージを使用する方法](app-service-linux-using-custom-docker-image.md)
* [Web Apps on Linux での Node.js 向け PM2 構成の使用](app-service-linux-using-nodejs-pm2.md)
* [Azure App Service Web Apps on Linux での .NET Core の使用](app-service-linux-using-dotnetcore.md)
* [Azure App Service Web Apps on Linux での Ruby の使用](app-service-linux-using-ruby.md)
* [Azure App Service Web Apps on Linux のFAQ](app-service-linux-faq.md)


