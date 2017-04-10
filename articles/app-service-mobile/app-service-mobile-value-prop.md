---
title: "モバイル アプリとは"
description: "App Service が企業のモバイル アプリにもたらす利点について説明します。"
services: app-service\mobile
documentationcenter: 
author: adrianhall
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 43ad434b21fd358b8d72702445126dea5ce179cb


---
# <a name="getting-started"> </a>Mobile Apps とは
Azure App Service は、プロの開発者のための完全に管理された [サービスとしてのプラットフォーム](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) 製品であり、Web、モバイル、および統合シナリオ向けに豊富な機能を提供します。 *Azure App Service* の *Mobile Apps* は、エンタープライズ開発者とシステム インテグレーターを対象とした、拡張性が高くグローバルに使用可能なモバイル アプリ開発プラットフォームであり、モバイル開発者に豊富な機能セットを提供します。

![Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>モバイル アプリを使用する理由
*Azure App Service* の *Mobile Apps* は、エンタープライズ開発者とシステム インテグレーターを対象とした、拡張性が高くグローバルに使用可能なモバイル アプリ開発プラットフォームであり、モバイル開発者に豊富な機能セットを提供します。 モバイル アプリでは、次のことを実行できます。

* **ネイティブ アプリとクロスプラットフォーム アプリの構築** - iOS、Android、Windows のネイティブ アプリを構築する場合でも、Xamarin や Cordova (Phonegap) のクロスプラットフォーム アプリを構築する場合でも、ネイティブ SDK を使用して App Service を活用することができます。
* **エンタープライズ システムへの接続** - Mobile Apps を使用して、企業へのサインオンを数分で追加し、企業のオンプレミス リソースまたはクラウド リソースに接続することができます。
* **データ同期を使用したオフライン対応アプリの構築** - オフラインでも動作し、企業のデータ ソースや SaaS API と接続したときに Mobile Apps を使用してバックグラウンドでデータを同期するアプリを構築することにより、モバイル ユーザーの生産性が向上します。
* **瞬時に多数のユーザーへプッシュ通知** - 顧客のニーズに合わせてカスタマイズされ、適切なタイミングで送信される、任意のデバイスへの即時のプッシュ通知によって顧客の関心を引きます。

## <a name="mobile-app-features"></a>モバイル アプリの機能
クラウド対応のモバイル開発には次の機能が重要です。

* **認証と承認** - エンタープライズ認証用の Azure Active Directory などの ID プロバイダーや、Facebook、Google、Twitter、Microsoft アカウントのようなソーシャル プロバイダーの豊富な一覧から選択します。  Azure Mobile Apps は、各プロバイダーに対して OAuth 2.0 サービスを提供します。  また、プロバイダー固有の機能を実現するために、ID プロバイダーの SDK を統合することもできます。
  
  [認証機能]の詳細を確認してください。
* **データ アクセス** - Azure Mobile Apps では、SQL Azure またはオンプレミス SQL Server にリンクされた、モバイル対応の OData v3 データ ソースを提供します。  このサービスは Entity Framework に基づいているため、他の NoSQL および SQL データ プロバイダー ([Azure Table Storage]、MongoDB、[DocumentDB] など) や SaaS API プロバイダー (Office 365、Salesforce.com など) と簡単に統合できます。
* **オフライン同期** - クライアント SDK を使用することで、オフライン データ セットで動作する堅牢で応答性の高いモバイル アプリケーションを簡単に構築できます。オフライン データ セットは、競合解決のサポートも含め、バックエンド データと自動的に同期できます。
  
  [データ機能]の詳細を確認してください。
* **プッシュ通知** - クライアント SDK は Azure Notification Hubs の登録機能とシームレスに統合されます。これにより、数百万人のユーザーにプッシュ通知を同時に送信できます。
  
  [プッシュ通知機能]の詳細を確認してください。
* **クライアント SDK** - ネイティブ開発 ([iOS]、[Android]、[Windows])、クロスプラットフォーム開発 ([iOS および Android 用 Xamarin]、[Xamarin Forms])、ハイブリッド アプリケーション開発 ([Apache Cordova]) に対応したクライアント SDK の完全なセットを提供します。  各クライアント SDK は、オープンソースであり、MIT ライセンスで使用できます。

## <a name="azure-app-service-features"></a>Azure App Service の機能
次のプラットフォーム機能は、一般的に、モバイル運用サイトに役立ちます。

* **自動スケール** - App Service では、スケール アップやスケール アウトをすばやく実行して、将来の顧客負荷に対処することができます。 VM の数やサイズを手動で選択するか、負荷またはスケジュールに基づいてモバイル アプリ バックエンドのスケール設定を行うように自動スケールを設定します。
  
  [自動スケール]の詳細を確認してください。
* **ステージング環境** - App Service では、サイトの複数のバージョンを実行できるため、A/B テスト、大規模な DevOps 計画の一環としての運用環境でのテスト、新しいバックエンドのインプレース ステージングを実行できます。
  
  [ステージング環境]の詳細を確認してください。
* **継続的なデプロイ** - App Service は一般的な SCM システムと統合できるため、バックエンドの新しいバージョンを SCM システムのブランチにプッシュすることで自動的にデプロイできます。
  
  [デプロイ オプション]の詳細を確認してください。
* **仮想ネットワーク** - App Service は、仮想ネットワーク、ExpressRoute、またはハイブリッド接続を使用してオンプレミスのリソースに接続できます。
  
  [ハイブリッド接続]、[仮想ネットワーク]、[ExpressRoute] の詳細をご覧ください。
* **分離/専用環境** - App Service は、Azure App Service アプリを高スケールで安全に実行するために、完全に分離された専用環境で実行できます。  これは、高スケール、分離、またはセキュリティで保護されたネットワーク アクセスを必要とするアプリケーション ワークロードに最適です。
  
  [App Service 環境]の詳細を確認してください。

## <a name="getting-started"></a>Getting Started (概要)
Mobile Apps の使用を開始するには、 [作業の開始] に関するチュートリアルに従ってください。  このチュートリアルでは、モバイル バックエンドと任意のクライアントを生成した後、認証、オフライン同期、およびプッシュ通知を統合する方法の基礎を取り上げます。  [作業の開始] に関するチュートリアルは、クライアント アプリケーションごとに&1; 回ずつ、複数回行うことができます。

Azure Mobile Apps の詳細については、 [学習マップ]を確認してください。
Azure App Service プラットフォームの詳細については、 [Azure App Service]に関するページを参照してください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/mobile/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[作業の開始]: app-service-mobile-ios-get-started.md
[Azure Table Storage]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[認証機能]: ./app-service-mobile-auth.md
[データ機能]: ./app-service-mobile-offline-data-sync.md
[プッシュ通知機能]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[iOS および Android 用 Xamarin]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[自動スケール]: ../app-service-web/web-sites-scale.md
[ステージング環境]: ../app-service-web/web-sites-staged-publishing.md
[デプロイ オプション]: ../app-service-web/web-sites-deploy.md
[ハイブリッド接続]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[仮想ネットワーク]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[App Service 環境]: ../app-service-web/app-service-app-service-environment-intro.md
[学習マップ]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/



<!--HONumber=Jan17_HO3-->


