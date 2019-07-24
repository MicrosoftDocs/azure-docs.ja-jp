---
title: Mobile Services から Azure App Service (Node.js) へのアップグレード
description: 簡単に Mobile Services アプリケーションを App Service Mobile App にアップグレードする方法について説明します。
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 5a74097521cc7dceea2c47f78c1dbb2f17e292aa
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726682"
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>App Service への既存の Node.js Azure Mobile Service のアップグレード
App Service Mobile は、Microsoft Azure を使用してモバイル アプリケーションを構築する新しい方法です。 詳細については、「 [Mobile Apps とは]」を参照してください。

この記事では、既存の Node.js バックエンド アプリケーションを Azure Mobile Services から新しい App Service Mobile Apps にアップグレードする方法について説明します。 このアップグレードの実行中も、既存の Mobile Services アプリケーションの動作を続行できます。  Node.js バックエンド アプリケーションにアップグレードする必要がある場合は、[既存の .NET Mobile Services のアップグレード](app-service-mobile-net-upgrading-from-mobile-services.md)に関するページを参照してください。

Azure App Service にアップグレードされたモバイル バックエンドは、App Service のすべての機能にアクセスでき、Mobile Services の価格ではなく [App Service 価格]に従って課金されます。

## <a name="migrate-vs-upgrade"></a>移行とアップグレード
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> アップグレードする前に、 [移行する](app-service-mobile-migrating-from-mobile-services.md) ことをお勧めします。 そうすることで、同じ App Service プランに両方のバージョンのアプリケーションを指定できるため、追加コストが発生しません。
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Mobile Apps Node.js サーバー SDK の機能強化
新しい [Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) にアップグレードすると、次のような多くの機能が強化されます。

* [Express フレームワーク](https://expressjs.com/en/index.html)に基づく新しい軽量の Node SDK は、リリースされる新しい Node のバージョンに対応できるように設計されています。Express ミドルウェアでアプリケーションの動作をカスタマイズすることができます。
* Mobile Services SDK に比べ、パフォーマンスが大幅に向上します。
* モバイル バックエンドと共に Web サイトをホストできるようになりました。同様に、既存の express.v4 アプリケーションに Azure Mobile SDK を簡単に追加することができます。
* クロスプラットフォームとローカル開発用に構築されており、Mobile Apps SDK を開発して Windows、Linux、および OSX プラットフォーム上でローカルに実行できます。 デプロイメントの前の [Mocha](https://mochajs.org/) テストの実行などの一般的な Node 開発手法を簡単に使用できるようになりました。

## <a name="overview"></a>基本的なアップグレードの概要
Node.js のアップグレードをバックエンドで支援するために、Azure App Service には互換性パッケージが用意されています。  アップグレード後、新しいサイトが提供され、それを新しい App Service サイトにデプロイすることができます。

Mobile Services クライアント SDK と新しい Mobile Apps サーバー SDK の間に互換性は **ありません** 。 アプリ用にサービスを接続する場合は、発行されたクライアントを現在使用しているサイトに変更を発行しないでください。 代わりに、複製として機能する新しいモバイル アプリを作成する必要があります。 このアプリケーションを同じ App Service プランに指定することで、追加の財務費用が発生しないようにすることができます。

アプリケーションには 2 つのバージョンがあります。1 つは変わらず、発行されたアプリとしてそのままの状態で機能し、もう 1 つはユーザーがアップグレードし、新しいクライアント リリースのターゲットにすることができます。 自分のペースでコードを移動し、テストできますが、バグの修正が両方に適用されることを確認する必要があります。 必要な数の (そのままの状態の) クライアント アプリを最新バージョンに更新したら、必要に応じて、元の移行されたアプリを削除することができます。 モバイル アプリと同じ App Service プランでホストされている場合は、追加の金銭的コストが発生することはありません。

このアップグレード プロセスの全概要は、次のとおりです。

1. 既存の (移行する) Azure Mobile Service をダウンロードします。
2. 互換性パッケージを使用してプロジェクトを Azure Mobile App に変換します。
3. 相違点 (認証設定など) があれば修正します。
4. 変換済みの Azure Mobile App プロジェクトを新しい App Service にデプロイします。
5. 新しい Mobile App を使用するクライアント アプリケーションの新しいバージョンをリリースします。
6. (省略可能) 元の移行されたモバイル サービス アプリを削除する。

削除は、元の移行されたモバイル サービスのトラフィックがないときに実行されます。

## <a name="install-npm-package"></a> 前提条件のインストール
[Node] はローカル コンピューターにインストールする必要があります。  加えて互換性パッケージをインストールする必要があります。  Node のインストール後は、新しい cmd または PowerShell プロンプトから次のコマンドを実行することができます。

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Azure Mobile Services スクリプトの取得
* [Azure Portal] にログインします。
* **[すべてのリソース]** または **[App Services]** を使用して Mobile Services サイトを見つけます。
* サイト内で **[ツール]** -> **[Kudu]** -> **[移動]** の順にクリックして Kudu のサイトを開きます。
* **[デバッグ コンソール]** -> **[PowerShell]** をクリックしてデバッグ コンソールを開きます。
* 各ディレクトリを順にクリックして `site/wwwroot/App_Data/config` に移動します。
* `scripts` ディレクトリの横にあるダウンロード アイコンをクリックします。

これにより、ZIP 形式でスクリプトがダウンロードされます。  ローカル コンピューターに新しいディレクトリを作成し、そのディレクトリに `scripts.ZIP` ファイルを展開します。  これによって `scripts` ディレクトリが作成されます。

## <a name="scaffold-app"></a> 新しい Azure Mobile Apps バックエンドのスキャフォールディング
scripts ディレクトリが格納されているディレクトリから次のコマンドを実行します。

```scaffold-mobile-app scripts out```

スキャフォールディング済みの Azure Mobile Apps バックエンドが `out` ディレクトリに作成されます。  必須ではありませんが、 `out` ディレクトリは、ソース コード リポジトリにチェック インすることをお勧めします。

## <a name="deploy-ama-app"></a> Azure Mobile Apps バックエンドのデプロイ
デプロイ時には次の作業が必要となります。

1. [Azure Portal] で新しいモバイル アプリを作成します。
2. 接続先のデータベースに対して `createViews.sql` スクリプトを実行します。
3. モバイル サービスに関連付けられているデータベースを新しい App Service に関連付けます。
4. 他のリソース (Notification Hubs など) があれば新しい App Service に関連付けます。
5. 生成されたコードを新しいサイトにデプロイします。

### <a name="create-a-new-mobile-app"></a>新しい Mobile App を作成する
1. [Azure Portal] にログインします。
2. **[+ 新規]** > **[Web + モバイル]** > **[モバイル アプリ]** の順にクリックし、モバイル アプリ バックエンドの名前を入力します。
3. **[リソース グループ]** で、既存のリソース グループを選択するか、新しく作成します (アプリと同じ名前を使用)。

    別の App Service プランを選択するか、または新しいプランを作成することもできます。 App Services プランの詳細と、さまざまな価格レベルおよび目的の場所で新しいプランを作成する方法については、「 [Azure App Service プランの詳細な概要](../app-service/overview-hosting-plans.md)」を参照してください。
4. **[App Service プラン]** には既定のプラン ( [Standard レベル](https://azure.microsoft.com/pricing/details/app-service/)) が選択されています。 別のプランを選択することも、[新しいプランを作成](../app-service/app-service-plan-manage.md#create-an-app-service-plan)することもできます。 App Service プランの設定により、アプリに関連付けられる[場所、機能、コスト、コンピューティング リソース](https://azure.microsoft.com/pricing/details/app-service/)が決まります。

    プランを決定したら、 **[作成]** をクリックします。 モバイル アプリ バックエンドが作成されます。

### <a name="run-createviewssql"></a>CreateViews.SQL の実行
スキャフォールディング済みのアプリには、 `createViews.sql`というファイルが存在します。  ターゲット データベースに対してこのスクリプトを実行する必要があります。  ターゲット データベースの接続文字列は、移行済みのモバイル サービスから取得できます (**[設定]** ページの **[接続文字列]**)。  名前は `MS_TableConnectionString`です。

このスクリプトは、SQL Server Management Studio または Visual Studio 内から実行できます。

### <a name="link-the-database-to-your-app-service"></a>App Service へのデータベースの関連付け
App Service に既存のデータベースを関連付けるには、次の作業を行います。

* [Azure Portal] で、App Service を開きます。
* **[すべての設定]** -> **[データ接続]** の順に選択します。
* **[+ 追加]** をクリックします。
* ボックスの一覧から **[SQL Database]**
* **[SQL Database]** で既存のデータベースを選択し、**[選択]** をクリックします。
* **[接続文字列]** にデータベースのユーザー名とパスワードを入力し、**[OK]** をクリックします。
* **[Add data connections]\(データ接続の追加)\** ページで **[OK]** をクリックします。

ユーザー名とパスワードは、移行するモバイル サービスのターゲット データベースの [接続文字列] を見て確認できます。

### <a name="set-up-authentication"></a>認証の設定
Azure Mobile Apps は、そのサービス内から Azure Active Directory、Facebook、Google、Microsoft、Twitter 認証を使用するように構成することができます。  カスタム認証は、個別に開発する必要があります。  詳細については、[認証の概念]と[認証のクイック スタート]に関するドキュメントを参照してください。  

## <a name="updating-clients"></a>モバイル クライアントの更新
モバイル アプリ バックエンドを運用している場合は、それを利用する新しいバージョンのクライアント アプリケーションを使用できます。 Mobile Apps には新しいバージョンのクライアント SDK も含まれ、上記のサーバー アップグレードと同様に、Mobile Apps バージョンをインストールする前に Mobile Services SDK へのすべての参照を削除する必要があります。

バージョン間での主な変更の 1 つは、コンストラクターでアプリケーション キーが不要になったことです。
現在は、モバイル アプリの URL を渡すだけです。 たとえば、.NET クライアントの場合、現在の `MobileServiceClient` コンストラクターは以下のようになります。

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

新しい SDK のインストールおよび新しい構造の使用については、以下のリンクを介して参照できます。

* [Android バージョン 2.2 以降](app-service-mobile-android-how-to-use-client-library.md)
* [iOS バージョン 3.0.0 以降](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) バージョン 2.0.0 以降](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova バージョン 2.0 以降](app-service-mobile-cordova-how-to-use-client-library.md)

アプリケーションでプッシュ通知を使用する場合は、変更されている内容もあるため、プラットフォームごとに固有の登録手順をメモしてください。

新しいクライアント バージョンの準備ができたら、アップグレードしたサーバー プロジェクトで試してみます。 機能することを確認したら、新しいバージョンのアプリケーションを顧客にリリースできます。 最後に、顧客がこれらの更新プログラムを受け取ったら、Mobile Services バージョンのアプリを削除できます。 これで、最新の Mobile Apps サーバー SDK を使用する App Service モバイル アプリに完全にアップグレードできました。

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Mobile Apps とは]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[App Service 価格]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[認証の概念]: ../app-service/overview-authentication-authorization.md
[認証のクイック スタート]: app-service-mobile-auth.md

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
