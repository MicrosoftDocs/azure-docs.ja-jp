---
title: "Azure App Service の Mobile Apps について"
description: "App Service が企業のモバイル アプリにもたらす利点について説明します。"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: ac35ff9fe1c5f315c4de08de951f505627ec412b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="getting-started"> </a>Azure App Service の Mobile Apps について
Azure App Service は、プロの開発者のための完全に管理された[サービスとしてのプラットフォーム](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) 製品であり、 Web、モバイル、および統合シナリオ向けに豊富な機能を提供します。 

Azure App Service の Mobile Apps 機能は、エンタープライズ開発者とシステム インテグレーター向けの、スケーラブルでグローバルに利用できるモバイル アプリケーション開発プラットフォームです。

![Mobile Apps の機能の概要を表す図](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>モバイル アプリを使用する理由
Mobile Apps の機能を使ってできることを次に示します。

* **ネイティブ アプリとクロスプラットフォーム アプリの構築**: iOS、Android、Windows のネイティブ アプリを構築する場合でも、Xamarin や Cordova (PhoneGap) のクロスプラットフォーム アプリを構築する場合でも、ネイティブ SDK を使用して App Service を活用することができます。
* **エンタープライズ システムへの接続**: Mobile Apps の機能を使用して、企業へのサインオンを数分で追加し、企業のオンプレミス リソースまたはクラウド リソースに接続することができます。
* **データ同期を使用したオフライン対応アプリの構築**: オフラインでも動作し、企業のデータ ソースや SaaS (サービスとしてのソフトウェア) API と接続したときに Mobile Apps を使用してバックグラウンドでデータを同期するアプリを構築することにより、モバイル ユーザーの生産性が向上します。
* **瞬時に多数のユーザーへプッシュ通知**: 顧客のニーズに合わせてカスタマイズされ、適切なタイミングで送信される、任意のデバイスへの即時のプッシュ通知によって顧客の関心を引きます。

## <a name="mobile-apps-features"></a>Mobile Apps の機能
クラウド対応のモバイル開発には次の機能が重要です。

* **認証と承認**: エンタープライズ認証用の Azure Active Directory などの ID プロバイダーや、Facebook、Google、Twitter、Microsoft アカウントのようなソーシャル プロバイダーの豊富な一覧から選択します。 Mobile Apps は、各プロバイダーに対して OAuth 2.0 サービスを提供します。 また、プロバイダー固有の機能を実現するために、ID プロバイダーの SDK を統合することもできます。

    [認証機能]の詳細を確認してください。

* **データ アクセス**: Mobile Apps では、Azure SQL Database またはオンプレミス SQL Server にリンクされた、モバイル対応の OData v3 データ ソースを提供します。 このサービスは Entity Framework に基づいているため、他の NoSQL および SQL データ プロバイダー ([Azure Table Storage]、MongoDB、[Azure Cosmos DB] など) や SaaS API プロバイダー (Office 365、Salesforce.com など) と簡単に統合できます。

* **オフライン同期**: クライアント SDK を使用することで、オフライン データ セットで動作する堅牢で応答性の高いモバイル アプリケーションを簡単に構築できます。 このデータ セットは、競合解決のサポートも含め、バックエンド データと自動的に同期できます。

  [データ機能]の詳細を確認してください。

* **プッシュ通知**: クライアント SDK は Azure Notification Hubs の登録機能とシームレスに統合されます。これにより、数百万人のユーザーにプッシュ通知を同時に送信できます。

  [プッシュ通知機能]の詳細を確認してください。

* **クライアント SDK**: ネイティブ開発 ([iOS]、[Android]、[Windows])、クロスプラットフォーム開発 ([Xamarin.iOS および Xamarin.Android]、[Xamarin Forms])、ハイブリッド アプリケーション開発 ([Apache Cordova]) に対応したクライアント SDK の完全なセットを提供します。 各クライアント SDK は、オープン ソースであり、MIT ライセンスで使用できます。

## <a name="azure-app-service-features"></a>Azure App Service の機能
次のプラットフォーム機能は、モバイル運用サイトに役立ちます。

* **自動スケール**: App Service では、スケールアップやスケールアウトをすばやく実行して、将来の顧客負荷に対処することができます。 VM の数やサイズを手動で選択するか、負荷またはスケジュールに基づいてモバイルアプリ バックエンドのスケール設定を行うように自動スケールを設定します。

  [自動スケール]の詳細を確認してください。

* **ステージング環境**: App Service では、サイトの複数のバージョンを実行できるため、A/B テスト、大規模な DevOps 計画の一環としての運用環境でのテスト、新しいバックエンドのインプレース ステージングを実行できます。

  [ステージング環境]の詳細を確認してください。

* **継続的なデプロイ**: App Service は一般的なサプライ チェーン管理 (SCM) システムと統合できるため、バックエンドの新しいバージョンを SCM システムのブランチにプッシュすることで自動的にデプロイできます。

  [デプロイ オプション]の詳細を確認してください。

* **仮想ネットワーク**: App Service は、仮想ネットワーク、Azure ExpressRoute、またはハイブリッド接続を使用してオンプレミスのリソースに接続できます。

  [ハイブリッド接続]、[仮想ネットワーク]、[ExpressRoute] の詳細をご覧ください。

* **分離/専用環境**: App Service は、Azure App Service アプリを高スケールで安全に実行するために、完全に分離された専用環境で実行できます。 この環境は、高スケール、分離、またはセキュリティで保護されたネットワーク アクセスを必要とするアプリケーション ワークロードに最適です。

  [App Service 環境]の詳細を確認してください。

## <a name="next-steps"></a>次のステップ

Azure App Service の Mobile Apps の使用を開始するには、[作業の開始]に関するチュートリアルに従ってください。 このチュートリアルでは、モバイル バックエンドと各種クライアントを作成する方法についての基礎を取り上げています。 また、認証、オフライン同期、プッシュ通知の統合についても取り上げています。 このチュートリアルは、クライアント アプリケーションごとに 1 回ずつ、複数回行うことができます。

Mobile Apps の詳細については、[学習マップ]を確認してください。
Azure App Service プラットフォームの詳細については、 [Azure App Service]に関するページを参照してください。

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[作業の開始]: app-service-mobile-ios-get-started.md
[Azure Table Storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/documentdb-get-started.md
[認証機能]: ./app-service-mobile-auth.md
[データ機能]: ./app-service-mobile-offline-data-sync.md
[プッシュ通知機能]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS および Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
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

