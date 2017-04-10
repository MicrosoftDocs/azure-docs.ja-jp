---
title: "App Service API Apps - 変更内容 | Microsoft Docs"
description: "Azure App Service の API Apps の新機能について説明します。"
services: app-service\api
documentationcenter: .net
author: mohitsriv
manager: erikre
editor: tdykstra
ms.assetid: a9b58066-e8fd-48b8-a651-4613b1736433
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 30abc823a72431dfd12f1051434191ea904533f6
ms.openlocfilehash: 4970db1f7b780ed0048f4c1839fff33bed640278
ms.lasthandoff: 01/20/2017


---
# <a name="app-service-api-apps---whats-changed"></a>App Service API Apps - 変更内容
2015 年 11 月の Connect() イベントで、Azure App Service に対する複数の機能強化の内容が [発表](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/)されました。 この機能強化では、Mobile および Web Apps との適合性の向上、コンセプト カウントの抑制、デプロイおよび実行時のパフォーマンスの向上を実現するために基礎となる変更が API Apps に加えられました。 2015 年 11 月 30 日以降、Microsoft Azure 管理ポータルまたは最新のツールを使用して作成する新しい API アプリでは、これらの変更が反映されます。 この記事では、これらの変更内容について説明するほか、既存のアプリを再デプロイして、強化された機能を利用する方法を説明します。

## <a name="feature-changes"></a>機能の変更
API Apps の主な機能である認証、CORS、および API メタデータは、App Service に直接移されました。 この変更により、それらの機能はすべて Web、Mobile、および API Apps にわたって使用できます。 実際のところ、これら&3; つはすべて、リソース マネージャーで同じ **Microsoft.Web/site** リソース タイプを共有します。 API Apps ゲートウェイはもはや必要なくなり、API Apps では提供されません。 これにより、API Management ゲートウェイが&1; つのみになるので、Azure API Management も使いやすくなります。

![API Apps の概要](./media/app-service-api-whats-changed/api-apps-overview.png)

この API Apps 更新における重要な設計原則は、お客様が、API を現状のまま、使用する言語に取り入れることができるようにすることです。  API が既に Web アプリまたはモバイル アプリとしてデプロイされている場合は、アプリを再デプロイしなくても新しい機能を利用できます。 現在 API Apps のプレビューを使用している場合は、以下に示す移行のガイドラインを参照してください。

### <a name="authentication"></a>認証
API Apps、Mobile Services/Apps、および Web Apps の既存の設定不要の認証機能は統合され、管理ポータルの Azure App Service 認証ブレード&1; つで利用できるようになりました。 App Service での認証サービスの概要については、「 [App Service 認証/承認の展開](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)」を参照してください。

API シナリオの場合は、関連の新機能が複数あります。

* クライアント コードでセッション トークンの AAD トークンを交換することなしに、**Azure Active Directory を直接使用するためのサポート**: クライアントはベアラー トークンの仕様に従って Authorization ヘッダーに AAD トークンを含めるだけで構いません。 これはまた、クライアントまたはサーバー側で App Service 固有の SDK が必要ないことを意味します。 
* **サービス間または "内部" アクセス**: デーモン プロセスまたは、インターフェイスのない API へのアクセスを必要とする他の何らかのクライアントを使用している場合は、AAD サービス プリンシパルを使用してトークンを要求し、それをアプリケーションでの認証のために App Service に渡すことができます。
* **承認を遅延**: 多くのアプリケーションには、アプリケーションの部分ごとにさまざまなアクセス制限があります。 おそらく、公開したい API もあればサインインを求める API もあるでしょう。 元の認証/承認の機能は、オール オア ナッシングで、すべてのサイトでログインを必要としています。 このオプションはまだ存在しますが、別の方法として、App Service でユーザーを認証した後、アプリケーション コードで、アクセスを許可するかどうか決定を下すことができます。

新しい認証機能の詳細については、「 [Azure App Service での API Apps の認証と承認](app-service-api-authentication.md)」を参照してください。 既存の API アプリを以前の API アプリ モデルから新しいモデルに移行する方法については、この記事の「 [既存の API アプリを移行する](#migrating-existing-api-apps) 」を参照してください。

### <a name="cors"></a>CORS
CORS の構成には従来、**MS_CrossDomainOrigins** のコンマ区切りのアプリ設定が使われていましたが、現在では、そのためのブレードが Azure 管理ポータルに存在します。 あるいは、リソース マネージャーのツール (Azure PowerShell、CLI、 [リソース エクスプローラー](https://resources.azure.com/)など) を使用して CORS を構成することもできます。 **&lt;サイト名&gt;/web** リソースの **Microsoft.Web/sites/config** リソース タイプに対して **cors** プロパティを設定します。 次に例を示します。

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### <a name="api-metadata"></a>API のメタデータ
Web、Mobile、および API Apps にわたって API 定義ブレードを使用できるようになりました。 管理ポータルでは、相対 URL か、または API の Swagger 2.0 表現をホストするエンドポイントを指す絶対 URL を指定できます。 あるいは、リソース マネージャーのツールを使用して構成することもできます。 **&lt;サイト名&gt;/web** リソースの **Microsoft.Web/sites/config** リソース タイプに対して **apiDefinition** プロパティを設定します。 次に例を示します。

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

この時点で、メタデータ エンドポイントには認証なしでパブリックにアクセスできる必要があります。これは、多くのダウンストリーム クライアント (例: Visual Studio REST API クライアント生成や PowerApps の "API の追加" フロー) がそれを使用できるようにするためです。 すなわち、App Service 認証を使用し、アプリ自体から API 定義を公開するには、前述した認証の遅延オプションを使用することが必要になります。そうすれば、Swagger メタデータへのルートはパブリックになります。

## <a name="management-portal"></a>管理ポータル
ポータルで **[新規]、[Web + モバイル]、[API アプリ]** の順に選択すると、この記事で説明した新しい機能を反映する API アプリが作成されます。 **[参照]、[API Apps]** の順にクリックすると、これらの新しい API アプリが表示されます。 API アプリを参照すると、ブレードでは、Web および Mobile Apps の場合と同じレイアウトと機能を共有しています。 唯一の違いは、クイック スタート コンテンツと設定の順序付けです。

リソース グループ内のすべてのリソースを参照すると、前のプレビュー機能を備えた既存の API アプリ (または Logic Apps で作成した Marketplace API アプリ) が引き続き Logic Apps デザイナーに表示されます。

## <a name="visual-studio"></a>Visual Studio
ほとんどの Web Apps ツールは、**Microsoft.Web/sites** リソース タイプを同じ基礎として共有するため、新しい API アプリと連携して動作します。 ただし、Azure Visual Studio ツールは 2.8.1 以降のバージョンにアップグレードする必要があります。API に固有の機能が多数公開されているからです。 [Azure のダウンロード ページ](https://azure.microsoft.com/downloads/)から SDK をダウンロードします。

App Service の種類の合理化により、発行も **[発行] の [Microsoft Azure App Service]** の下で統合されています。

![API Apps の発行](./media/app-service-api-whats-changed/api-apps-publish.png)

SDK 2.8.1 の詳細については、発表に関する [ブログ投稿](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/)をお読みください。

あるいは、手動で管理ポータルから発行プロファイルをインポートして発行を有効にすることもできます。 ただし、クラウド エクスプローラー、コードの生成、API アプリの選択/作成には、SDK 2.8.1 またはそれ以降のバージョンが必要です。

## <a name="migrating-existing-api-apps"></a>既存の API アプリを移行する
カスタム API が API Apps の以前のプレビュー バージョンにデプロイされている場合は、2015 年 12 月 31 日までに API Apps の新しいモデルに移行する必要があります。 以前のモデルも新しいモデルも App Service でホストされる Web API をベースにしているため、既存のコードの大部分が再利用できます。

### <a name="hosting-and-redeployment"></a>ホスティングと再デプロイ
再デプロイの手順は、既存の Web API を App Service にデプロイする場合と同じです。 手順:

1. 空の API アプリを作成します。 そのためには、ポータルで [新規]、[API アプリ] の順に操作するか、Visual Studio の発行を使用するか、または Resource Manager ツールを使用します。 Resource Manager のツールまたはテンプレートを使用する場合は、**Microsoft.Web/sites** リソース タイプで **kind** 値を **api** に設定し、API シナリオ向けに管理ポータル内のクイックスタートおよび設定を行います。
2. App Service でサポートされている任意のデプロイ メカニズムを使用して、接続し、プロジェクトを空の API アプリにデプロイします。 詳細については、「 [Azure App Service のデプロイに関するドキュメント](../app-service-web/web-sites-deploy.md) 」を参照してください。 

### <a name="authentication"></a>認証
App Service 認証サービスでは、以前の API Apps モデルで使用されていたのと同じ機能をサポートします。 セッション トークンを使用し、SDK を必要とする場合は、次のクライアント SDK とサーバー SDK を使用します。

* クライアント: [Azure モバイル クライアント SDK](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
* サーバー: [Microsoft Azure Mobile App .NET 認証拡張機能](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) 

App Service のアルファ版 SDK については、次の SDK が廃止になりました。

* クライアント: [Microsoft Azure AppService SDK](http://www.nuget.org/packages/Microsoft.Azure.AppService)
* サーバー: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

ただし、特に、Azure Active Directory では、AAD トークンを直接使用する場合、App Service 固有の SDK は必要ありません。

### <a name="internal-access"></a>内部アクセス
以前の API Apps モデルには、組み込みの内部アクセス レベルが含まれていました。 この場合、要求に署名するために SDK を使用する必要がありました。 前述したように、新しい API Apps モデルでは、サービス間認証の替わりとして、App Service 固有の SDK を必要としない AAD サービス プリンシパルを使用できます。 詳細については、「 [Azure App Service での API Apps のサービス プリンシパル認証](app-service-api-dotnet-service-principal-auth.md)」を参照してください。

### <a name="discovery"></a>探索
以前の API Apps モデルは、同じゲートウェイの背後にある同じリソース グループ内で実行時に他の API アプリを検出するための API を備えていました。 この API は、マイクロサービス パターンを実装するアーキテクチャで特に便利です。 この API は直接サポートされませんが、次に示す複数のオプションを使用できます。

1. Azure リソース マネージャーの API を使用して探索します。
2. App Service でホストされる API の前に Azure API Management を配置します。 Azure API Management はファサードとして機能し、内部のトポロジが変更された場合でも、安定した外部 URL を指定できます。
3. 独自の探索用 API アプリをビルドし、起動時に探索用アプリにその他の API アプリが登録されるようにします。
4. デプロイ時に、すべての API アプリ (およびクライアント) のアプリ設定を他の API アプリのエンドポイントに設定します。 これはテンプレートのデプロイで実行可能であり、API Apps で URL を制御できるようになります。

## <a name="using-api-apps-with-logic-apps"></a>API Apps と Logic Apps の併用
新しい API Apps モデルは、 [Logic Apps スキーマ バージョン 2015-08-01](../logic-apps/logic-apps-schema-2015-08-01.md)とうまく連動します。

## <a name="next-steps"></a>次のステップ
詳細については、 [API Apps のドキュメント セクション](https://azure.microsoft.com/documentation/services/app-service/api/)の記事を参照してください。 これらの記事は更新され、API Apps の新しいモデルを反映しています。 さらに、移行に関する詳細またはガイダンスについては、次のフォーラムを参照してください。

* [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps)
* [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-api-apps)


