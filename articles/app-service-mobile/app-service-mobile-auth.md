---
title: モバイル アプリを対象とした Azure App Service での認証および承認 | Microsoft Docs
description: 特にモバイル アプリを対象とした Azure App Service の認証/承認機能の概念リファレンスと概要
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 87bdfcc827155e5dd0a02ffb1640bf7e9cd4e479
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715787"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>モバイル アプリを対象とした Azure App Service での認証および承認

この記事では、App Service のバックエンドでネイティブ モバイル アプリを開発するときの認証と承認のしくみについて説明します。 App Service では統合認証および承認を利用できます。このため、App Service のコードを変更しなくても、お使いのモバイル アプリでユーザーをサインインさせることができます。 これにより、アプリケーションの保護が容易になり、またユーザーごとのデータにも対応できるようになります。 

この記事では、モバイル アプリの開発に重点を置いて説明します。 モバイル アプリを対象とした App Service の認証と承認をすぐに開始する場合は、「[iOS アプリに認証を追加する][iOS]」(または [Android]、[Windows]、[Xamarin.iOS]、[Xamarin.Android]、[Xamarin.Forms]、[Cordova]) のチュートリアルのいずれかを参照してください。 

App Service での認証と承認のしくみについては、「[Azure App Service での認証および承認](../app-service/overview-authentication-authorization.md)」を参照してください。

## <a name="authentication-with-provider-sdk"></a>プロバイダー SDK を使用した認証

App Service ですべてを構成したら、App Service にサインインできるようにモバイル クライアントを変更できます。 これには 2 つの方法はあります。

* 特定の ID プロバイダーが発行する SDK を使用して、ID を確立し、App Service にアクセスできるようにします。
* 1 行のコードを使用して、Mobile Apps クライアント SDK でユーザーがサインインできるようにします。

> [!TIP]
> ほとんどのアプリケーションで、ユーザーのサインイン時に一貫性のあるエクスペリエンスを実現し、トークン更新サポートを使用して、プロバイダーが指定する他の利点を得るために、プロバイダー SDK を使用する必要があります。
> 
> 

プロバイダー SDK を使用すると、ユーザーは、アプリが実行しているオペレーティング システムとより緊密に統合するエクスペリエンスにサインインできます。 この方法により、プロバイダーのトークンとクライアント上のユーザー情報の一部が得られるので、グラフ API の使用やユーザー エクスペリエンスのカスタマイズが容易になります。 クライアント コードがユーザーをサインインさせ、クライアント コードがプロバイダーのトークンにアクセスするため、これは "クライアント フロー" または "クライアント主導のフロー" と、ブログやフォーラムで呼ばれることがあります。

プロバイダーのトークンが取得されたら、App Service に送信して検証する必要があります。 App Service では、トークンの検証後、クライアントに返される新しい App Service トークンが作成されます。 Mobile Apps クライアント SDK には、この交換を管理してアプリケーション バックエンドへのすべての要求にトークンを自動的に添付するヘルパー メソッドがあります。 開発者はプロバイダー トークンへの参照を保持できます。

認証フローの詳細については、[App Service の認証フロー](../app-service/overview-authentication-authorization.md#authentication-flow)に関する記事をご覧ください。 

## <a name="authentication-without-provider-sdk"></a>プロバイダー SDK を使用しない認証

SDK プロバイダーを設定しない場合は、Azure App Service の Mobile Apps 機能によって自動的にサインインさせることができます。 Mobile Apps クライアント SDK が、選択したプロバイダーの Web ビューを開き、ユーザーをサインインさせます。 サーバーがユーザーをサインインさせるプロセスを管理し、クライアント SDK がプロバイダーのトークンを受け取ることはないため、これは "サーバー フロー" または "サーバー主導のフロー" と、ブログやフォーラムで呼ばれることがあります。

このフローを開始するコードは、各プラットフォームの認証のチュートリアルに含まれています。 フローの最後では、クライアント SDK が App Service トークンを取得し、そのトークンがアプリケーション バックエンドへのすべての要求に自動的に添付されます。

認証フローの詳細については、[App Service の認証フロー](../app-service/overview-authentication-authorization.md#authentication-flow)に関する記事をご覧ください。 
## <a name="more-resources"></a>その他のリソース

以下のチュートリアルで、[サーバー主導のフロー](../app-service/overview-authentication-authorization.md#authentication-flow)を使用し、モバイル クライアントに認証を追加する方法について説明しています。

* [iOS アプリに認証を追加する][iOS]
* [Android アプリに認証を追加する][Android]
* [Windows アプリに認証を追加する][Windows]
* [Xamarin.iOS アプリに認証を追加する][Xamarin.iOS]
* [Xamarin.Android アプリに認証を追加する][Xamarin.Android]
* [Xamarin.Forms アプリに認証を追加する][Xamarin.Forms]
* [Cordova アプリに認証を追加する][Cordova]

Azure Active Directory の[クライアント主導のフロー](../app-service/overview-authentication-authorization.md#authentication-flow)を使用する場合は、次のリソースを使用します。

* [iOS 向け Active Directory 認証ライブラリを使用する][ADAL-iOS]
* [Android 向け Active Directory 認証ライブラリを使用する][ADAL-Android]
* [Windows および Xamarin 向け Active Directory 認証ライブラリを使用する][ADAL-dotnet]

Facebook の[クライアント主導のフロー](../app-service/overview-authentication-authorization.md#authentication-flow)を使用する場合は、次のリソースを使用します。

* [Facebook SDK for iOS を使用する](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Twitter の[クライアント主導のフロー](../app-service/overview-authentication-authorization.md#authentication-flow)を使用する場合は、次のリソースを使用します。

* [Twitter Fabric for iOS を使用する](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Google の[クライアント主導のフロー](../app-service/overview-authentication-authorization.md#authentication-flow)を使用する場合は、次のリソースを使用します。

* [Google Sign-In SDK for iOS を使用する](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
