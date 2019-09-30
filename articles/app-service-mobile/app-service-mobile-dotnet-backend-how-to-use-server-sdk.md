---
title: Mobile Apps 用 .NET バックエンド サーバー SDK を操作する方法 | Microsoft Docs
description: Azure App Service Mobile Apps 用の .NET バックエンド サーバー SDK を操作する方法について説明します。
keywords: App Service, Azure App Service, モバイル アプリ, スケール, スケーラブル, アプリのデプロイ, Azure App のデプロイ
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: d277786fd08e1448b3d5ccf4fd45055fe069e4c0
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097778"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

このトピックでは、Azure App Service Mobile Apps の主要なシナリオで .NET バックエンド サーバー SDK を使用する方法について説明します。 Azure Mobile Apps SDK を使用すると、ASP.NET アプリケーションからモバイル クライアントを操作することができます。

> [!TIP]
> [Azure Mobile Apps 向け .NET サーバー SDK][2] は、オープン ソースとして GitHub で公開されています。 リポジトリには、サーバー全体の SDK ユニット テスト スイートといくつかのサンプル プロジェクトを含むすべてのソース コードが含まれています。
>
>

## <a name="reference-documentation"></a>リファレンス ドキュメント
サーバー SDK のリファレンス ドキュメントについては、[Azure Mobile Apps .NET のリファレンス][1]を参照してください。

## <a name="create-app"></a>方法:.NET モバイル アプリ バックエンドを作成する
新しいプロジェクトを開始する場合は、 [Azure Portal] または Visual Studio を使用して、App Service アプリケーションを作成できます。 App Service アプリケーションをローカルで実行することも、プロジェクトをクラウドベースの App Service モバイル アプリに発行することもできます。

既存のプロジェクトにモバイル機能を追加する場合は、「 [SDK をダウンロードして初期化する](#install-sdk) 」セクションを参照してください。

### <a name="create-a-net-backend-using-the-azure-portal"></a>Azure ポータルで .NET バックエンドを作成する
App Service モバイル バックエンドを作成するには、[クイックスタート チュートリアル][3]または次の手順に従います。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

**[テーブル API の作成]** の *[はじめに]* ブレードに戻り、 **[バックエンド言語]** として **[C#]** を選択します。 **[ダウンロード]** をクリックし、圧縮されたプロジェクト ファイルをローカル コンピューターに抽出して、Visual Studio でソリューションを開きます。

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Visual Studio 2017 を使用して .NET バックエンドを作成する

Visual Studio インストーラーを使用して Azure ワークロードをインストールし、Visual Studio から Azure Mobile Apps プロジェクトに発行します。 SDK のインストールが完了したら、次の手順を使用して ASP.NET アプリケーションを作成します。

1. **[新しいプロジェクト]** ダイアログを開きます ( **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト...]** の順にクリック)。
2. **[Visual C#]** を展開し、 **[Web]** を選択します。
3. **[ASP.NET Web アプリケーション (.NET Framework)]** を選択します。
4. プロジェクト名を入力します。 次に、 **[OK]** をクリックします
5. テンプレートの一覧から **[Azure Mobile App]** を選択します。
6. **[OK]** をクリックしてソリューションを作成します。
7. **ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[発行...]** を選択し、発行のターゲットとして **[App Service]** を選択します。
8. プロンプトに従って認証し、発行する新規または既存の Azure App Service を選択します。

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Visual Studio 2015 を使用して .NET バックエンドを作成する

Visual Studio で Azure Mobile Apps プロジェクトを作成するには、[Azure SDK for .NET][4] (バージョン 2.9.0 以降) をインストールします。 SDK のインストールが完了したら、次の手順を使用して ASP.NET アプリケーションを作成します。

1. **[新しいプロジェクト]** ダイアログを開きます ( **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト...]** の順にクリック)。
2. **[テンプレート]**  >  **[Visual C#]** の順に展開し、 **[Web]** を選択します。
3. **[ASP.NET Web アプリケーション]** を選択します。
4. プロジェクト名を入力します。 次に、 **[OK]** をクリックします
5. *ASP.NET 4.5.2 テンプレート*の **[Azure Mobile App]** を選択します。 このプロジェクトの発行先となるクラウドにモバイル バックエンドを作成するために、 **[Host in the cloud (クラウドにホストする)]** チェック ボックスをオンにします。
6. Click **OK**.

## <a name="install-sdk"></a>方法:SDK をダウンロードして初期化する
SDK は [NuGet.org]で入手できます。このパッケージには、SDK の使用を開始するために必要な基礎機能が含まれています。 SDK を初期化するには、 **HttpConfiguration** オブジェクトに対して操作を実行する必要があります。

### <a name="install-the-sdk"></a>SDK のインストール
SDK をインストールするには、Visual Studio でサーバー プロジェクトを右クリックして **[NuGet パッケージの管理]** を選択し、[Microsoft.Azure.Mobile.Server] パッケージを検索してから、 **[インストール]** をクリックします。

### <a name="server-project-setup"></a> サーバー プロジェクトの初期化
.NET バックエンド サーバー プロジェクトは、他の ASP.NET プロジェクトと同じように、OWIN スタートアップ クラスを組み込むことによって初期化します。 NuGet パッケージ `Microsoft.Owin.Host.SystemWeb`が参照されていることを確認します。 Visual Studio でこのクラスを追加するには、サーバー プロジェクトを右クリックして、 **[追加]**  >
 **[新しい項目]** 、 **[Web]**  >  **[全般]**  >  **[OWIN スタートアップ クラス]** の順に選択します。  次の属性を持つクラスが生成されます。

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

OWIN スタートアップ クラスの `Configuration()` メソッドで、 **HttpConfiguration** オブジェクトを使用して Azure Mobile Apps 環境を構成します。
次の例では、機能を追加せずにサーバー プロジェクトを初期化します。

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

個別の機能を有効化するには、**ApplyTo** を呼び出す前に、**MobileAppConfiguration** オブジェクトに対して拡張機能メソッドを呼び出す必要があります。 たとえば、次のコードでは、初期化中に `[MobileAppController]` 属性が設定されているすべての API コントローラーに既定のルートを追加します。

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Azure ポータルからのサーバーのクイックスタートは **UseDefaultConfiguration()** を呼び出します。 これは次のセットアップと同等です。

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

使用する拡張メソッドは次のとおりです。

* `AddMobileAppHomeController()` は、既定の Azure Mobile Apps ホーム ページを提供します。
* `MapApiControllers()` は、`[MobileAppController]` 属性で修飾された WebAPI コントローラーのカスタム API 機能を提供します。
* `AddTables()` は、テーブル コントローラーへの `/tables` エンドポイントのマッピングを提供します。
* `AddTablesWithEntityFramework()` は、Entity Framework ベースのコントローラーを使用して `/tables` エンドポイントをマッピングするための簡単な方法です。
* `AddPushNotifications()` を使用すると、Notification Hubs 用のデバイスを簡単に登録できます。
* `MapLegacyCrossDomainController()` は、ローカル開発のための標準 CORS ヘッダーを提供します。

### <a name="sdk-extensions"></a>SDK 拡張機能
次の NuGet ベースの拡張機能パッケージでは、アプリケーションで使用可能な各種モバイル機能が提供されます。 拡張機能の有効化は、初期化中に **MobileAppConfiguration** オブジェクトを使用して行います。

* [Microsoft.Azure.Mobile.Server.Quickstart] Mobile Apps の基本的なセットアップをサポートします。 構成に追加するには、初期化中に **UseDefaultConfiguration** 拡張機能メソッドを呼び出します。 この拡張機能には、次の拡張機能が含まれています。Notifications、Authentication、Entity、Tables、Cross-domain、および Home パッケージ。 このパッケージは、Azure Portal で利用可能な Mobile Apps クイック スタートで使用されます。
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Web サイトのルートに、 *このモバイル アプリが実行される既定のページ* を実装します。 構成に追加するには、 **AddMobileAppHomeController** 拡張メソッドを呼び出します。
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) データ処理のためのクラスが含まれており、データ パイプラインを設定します。 構成に追加するには、 **AddTables** 拡張メソッドを呼び出します。
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) Entity Framework で SQL Database のデータにアクセスできるようにします。 構成に追加するには、 **AddTablesWithEntityFramework** 拡張メソッドを呼び出します。
* [Microsoft.Azure.Mobile.Server.Authentication] 認証を有効にし、トークンの検証に使用する OWIN ミドルウェアを設定します。 構成に追加するには、**AddAppServiceAuthentication** 拡張メソッドと **IAppBuilder**.**UseAppServiceAuthentication** 拡張メソッドを呼び出します。
* [Microsoft.Azure.Mobile.Server.Notifications] プッシュ通知を有効にし、プッシュ登録エンドポイントを定義します。 構成に追加するには、 **AddPushNotifications** 拡張メソッドを呼び出します。
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) モバイル アプリから従来の Web ブラウザーにデータを提供するコントローラーを作成します。 構成に追加するには、 **MapLegacyCrossDomainController** 拡張メソッドを呼び出します。
* [Microsoft.Azure.Mobile.Server.Login] カスタム認証シナリオで使用される静的メソッドである AppServiceLoginHandler.CreateToken() メソッドを提供します。

## <a name="publish-server-project"></a>方法:サーバー プロジェクトを発行する
このセクションでは、Visual Studio から .NET バックエンド プロジェクトを発行する方法を示します。 [Git](../app-service/deploy-local-git.md) やその他の利用可能な方法を利用して、バックエンド プロジェクトをデプロイすることもできます。

1. Visual Studio でプロジェクトをリビルドして、NuGet パッケージを復元します。
2. ソリューション エクスプローラーで目的のプロジェクトを右クリックし、 **[発行]** をクリックします。 初めて発行するときには、発行プロファイルを定義する必要があります。 既に定義したプロファイルがある場合は、それを選択し、 **[発行]** をクリックします。
3. 発行先の選択を求められた場合は、 **[Microsoft Azure App Service]**  >  **[次へ]** の順にクリックして、必要であれば Azure の資格情報でサインインします。
   Visual Studio によって Azure から発行設定が直接ダウンロードされ、安全に保存されます。

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. **サブスクリプション**を選択し、 **[表示]** で **[リソースの種類]** を選択して、 **[モバイル アプリ]** を展開します。次に、モバイル アプリ バックエンドをクリックし、 **[OK]** をクリックします。

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. 発行プロファイル情報を確認し、 **[発行]** をクリックします。

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    モバイル アプリ バックエンドが正常に発行されると、そのことを示すランディング ページが表示されます。

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> 方法:テーブル コントローラーを定義する
SQL テーブルをモバイル クライアントに公開するためのテーブル コントローラーを定義します。  テーブル コントローラーを構成するには、3 つの手順が必要です。

1. データ転送オブジェクト (DTO) クラスを作成する。
2. Mobile DbContext クラスにテーブル参照を構成する。
3. テーブル コントローラーを作成する。

データ転送オブジェクト (DTO) は、 `EntityData`から継承するプレーンな C# オブジェクトです。  例:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO を使用して、SQL データベース内にテーブルを定義します。  データベース エントリを作成するには、使用している DbContext に `DbSet<>` プロパティを追加します。  Azure Mobile Apps の既定のプロジェクト テンプレートでは、DbContext が `Models\MobileServiceContext.cs`と記述されています。

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Azure SDK をインストール済みの場合は、テンプレート テーブル コントローラーを次のように作成できます。

1. Controllers フォルダーを右クリックし、 **[追加]**  >  **[コントローラー]** 」を参照してください。
2. **[Azure Mobile Apps のテーブル コントローラー]** オプションを選択し、 **[追加]** をクリックします。
3. **[コントローラーの追加]** ダイアログ ボックスで、次の手順を実行します。
   * **[モデル クラス]** ボックスの一覧で、新しい DTO を選択します。
   * **[DbContext]** ボックスの一覧で、モバイル サービス DbContext クラスを選択します。
   * コントローラー名は自動的に作成されます。
4. **[追加]** をクリックします。

クイックスタート サーバー プロジェクトには、シンプルな **TodoItemController**の例が含まれています。

### <a name="adjust-pagesize"></a>方法:テーブルのページング サイズを調整する
既定では、Azure Mobile Apps は、要求ごとに 50 個のレコードを返します。  ページングにより、クライアントが長期間 UI スレッドまたはサーバーを占有することがなくなるため、優れたユーザー エクスペリエンスが保証されます。 テーブルのページング サイズを変更するには、サーバー側の "許可されているクエリ サイズ" とクライアント側のページ サイズを大きくします。サーバー側の "許可されているクエリ サイズ" は、`EnableQuery` 属性を使用して調整します。

    [EnableQuery(PageSize = 500)]

PageSize が、クライアントから要求されるサイズ以上になるようにしてください。  クライアントのページ サイズを変更する方法の詳細については、特定のクライアントのハウツー ドキュメントを参照してください。

## <a name="how-to-define-a-custom-api-controller"></a>方法:カスタム API コントローラーを定義する
カスタム API コントローラーは、エンドポイントを公開して、モバイル アプリのバックエンドに最も基本的な機能を提供します。 [MobileAppController] 属性を使用して、モバイル固有の API コント ローラーを登録できます。 `MobileAppController` 属性は、ルートを登録し、Mobile Apps JSON シリアライザーを設定するほか、 [クライアント バージョン チェック](app-service-mobile-client-and-server-versioning.md)をオンにします。

1. Visual Studio で、Controllers フォルダーを右クリックして、 **[追加]**  >  **[コントローラー]** の順にクリックし、 **[Web API 2 コントローラー &mdash; 空]** を選択して **[追加]** をクリックします。
2. **コントローラー名** (`CustomController` など) を指定し、 **[追加]** をクリックします。
3. 新しいコントローラーのクラス ファイルに、次の using ステートメントを追加します。

        using Microsoft.Azure.Mobile.Server.Config;
4. 次の例に従って、API コントローラーのクラス定義に **[MobileAppController]** 属性を適用します。

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. 次の例に示すように、App_Start/Startup.MobileApp.cs ファイルに **MapApiControllers** 拡張メソッドの呼び出しを追加します。

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

`MapApiControllers()` の代わりに `UseDefaultConfiguration()` 拡張メソッドを使用することもできます。 クライアントは **MobileAppControllerAttribute** が適用されていないコントローラーにもアクセスできますが、こうしたコントローラーはモバイル アプリ クライアント SDK を使用するクライアントでは適切に使用されない場合があります。

## <a name="how-to-work-with-authentication"></a>方法:認証を操作する
Azure Mobile Apps は、App Service 認証/承認を使用してモバイル バックエンドをセキュリティで保護します。  このセクションでは、.NET バックエンド サーバー プロジェクトで以下の認証関連のタスクを実行する方法を説明します。

* [方法:サーバー プロジェクトに認証を追加する](#add-auth)
* [方法:アプリケーションにカスタム認証を使用する](#custom-auth)
* [方法:認証されたユーザー情報を取得する](#user-info)
* [方法:承認されたユーザーに対するデータ アクセスを制限する](#authorize)

### <a name="add-auth"></a>方法:サーバー プロジェクトに認証を追加する
**MobileAppConfiguration** オブジェクトを拡張し、OWIN ミドルウェアを構成すると、サーバー プロジェクトに認証を追加することができます。 [Microsoft.Azure.Mobile.Server.Quickstart] パッケージをインストールし、 **UseDefaultConfiguration** 拡張メソッドを呼び出している場合は、手順 3 に進むことができます。

1. Visual Studio で、 [Microsoft.Azure.Mobile.Server.Authentication] パッケージをインストールします。
2. Startup.cs プロジェクト ファイルで、 **Configuration** メソッドの先頭に次のコード行を追加します。

        app.UseAppServiceAuthentication(config);

    この OWIN ミドルウェア コンポーネントは、関連付けられている App Service ゲートウェイによって発行されたトークンを検証します。
3. 認証が必要なすべてのコントローラーまたはメソッドに `[Authorize]` 属性を追加します。

Mobile Apps バックエンドに対してクライアントを認証する方法については、「 [アプリケーションに認証を追加する](app-service-mobile-ios-get-started-users.md)」をご覧ください。

### <a name="custom-auth"></a>方法:アプリケーションにカスタム認証を使用する
> [!IMPORTANT]
> カスタム認証を有効にするには、まず、Azure Portal で App Service のプロバイダーを選択せずに App Service 認証を有効にする必要があります。 ホストされている場合は、これにより WEBSITE_AUTH_SIGNING_KEY 環境変数が有効になります。
> 
> 
> App Service 認証/承認プロバイダーの中に使用したいものがない場合は、独自のログイン システムを実装できます。 認証トークンの生成に役立つ [Microsoft.Azure.Mobile.Server.Login] パッケージをインストールします。  ユーザー資格情報を検証するための独自のコードを指定します。 たとえば、データベース内のソルトを使用してハッシュ化されたパスワードと照合することができます。 次の例では、(他の場所に定義されている) `isValidAssertion()` メソッドがこれらの照合を実行します。

カスタム認証を公開するには、ApiController を作成し、`register` アクションと `login` アクションを公開します。 クライアントは、カスタム UI を使用してユーザーから情報を収集する必要があります。  その後、この情報は、標準の HTTP POST 呼び出しで API に送信されます。 サーバーでアサーションを検証したら、 `AppServiceLoginHandler.CreateToken()` メソッドを使用してトークンを発行します。  ApiController で `[MobileAppController]` 属性を使用することは**できません**。

`login` アクションの例:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

前の例の LoginResult と LoginResultUser は、必要なプロパティを公開する、シリアル化可能なオブジェクトです。 クライアントでは、ログインの応答が次の形式の JSON オブジェクトとして返されるものと想定されています。

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

`AppServiceLoginHandler.CreateToken()` メソッドには、*audience* パラメーターと *issuer* パラメーターが含まれています。 どちらのパラメーターも、HTTPS スキームを使用してアプリケーション ルートの URL に設定します。 同様に、*secretKey* をアプリケーションの署名キーの値に設定する必要があります。 キーを生成してユーザーを偽装するために使用される可能性があるため、クライアントで署名キーを配布しないでください。 App Service でホストされているときに署名キーを取得するには、*WEBSITE\_AUTH\_SIGNING\_KEY* 環境変数を参照します。 ローカル デバッグの実行で必要な場合は、「 [認証に関するローカル デバッグ](#local-debug) 」セクションの手順に従ってキーを取得し、アプリケーション設定として保存します。

また、発行されたトークンには、他の要求および有効期限を含めることもできます。  少なくとも、発行されたトークンには、サブジェクト (**sub**) 要求が含まれている必要があります。

認証ルートをオーバーロードすることにより、標準クライアント `loginAsync()` メソッドをサポートできます。  クライアントが `client.loginAsync('custom');` を呼び出してログインする場合、ルートは `/.auth/login/custom` である必要があります。  カスタム認証コントローラーのルートは、 `MapHttpRoute()`を使用して設定できます。

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> `loginAsync()` アプローチを使用すると、後続のサービスのすべての呼び出しに認証トークンが添付されます。
>
>

### <a name="user-info"></a>方法:認証されたユーザー情報を取得する
ユーザーが App Service によって認証されると、.NET バックエンド コードで、割り当てられたユーザー ID とその他の情報にアクセスできます。 このユーザー情報を使用して、バックエンドで承認の決定を行うことができます。 次のコードは、要求に関連付けられているユーザー ID を取得します。

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID はプロバイダー固有のユーザー ID から派生し、特定のユーザーとログイン プロバイダーに対して静的です。  無効な認証トークンの SID は null です。

App Service では、ログイン プロバイダーからの特定の要求を行うこともできます。 各 ID プロバイダーは、ID プロバイダー SDK を使用してさらに多くの情報を提供できます。  たとえば、友人の情報については Facebook Graph API を使用できます。  Azure Portal のプロバイダー ブレードで、要求された要求を指定できます。 要求によっては、ID プロバイダーの追加の構成が必要です。

次のコードは、**GetAppServiceIdentityAsync** 拡張メソッドを呼び出して、ログイン資格情報を取得します。この情報には、Facebook Graph API に対する要求を行うために必要なアクセス トークンが含まれています。

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

**GetAppServiceIdentityAsync** 拡張メソッドを提供するには、`System.Security.Principal` の using ステートメントを追加します。

### <a name="authorize"></a>方法:承認されたユーザーに対するデータ アクセスを制限する
前のセクションでは、認証されたユーザーのユーザー ID を取得する方法について説明しました。 この値に基づいて、データとその他のリソースへのアクセスを制限できます。 たとえば、UserId 列をテーブルに追加して、ユーザー ID によってクエリの結果をフィルター処理すると、承認されたユーザーだけにデータが返されるように簡単に制限できます。 次のコードは、SID が TodoItem テーブルの UserId 列の値と一致する場合にのみデータ行を返します。

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

`Query()` メソッドは、フィルター処理を行うために LINQ で操作できる `IQueryable` を返します。

## <a name="how-to-add-push-notifications-to-a-server-project"></a>方法:サーバー プロジェクトにプッシュ通知を追加する
**MobileAppConfiguration** オブジェクトを拡張し、Notification Hubs クライアントを作成して、サーバー プロジェクトにプッシュ通知を追加します。

1. Visual Studio でサーバー プロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックして `Microsoft.Azure.Mobile.Server.Notifications` を見つけ、 **[インストール]** をクリックします。
2. 上記の手順を繰り返して、Notification Hubs クライアント ライブラリが含まれる `Microsoft.Azure.NotificationHubs` パッケージをインストールします。
3. App_Start/Startup.MobileApp.cs で、初期化中に **AddPushNotifications()** 拡張メソッドの呼び出しを追加します。

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Notification Hubs クライアントを作成する次のコードを追加します。

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

これで、Notification Hubs クライアントを使用して、登録済みデバイスにプッシュ通知を送信できるようになりました。 詳細については、「 [アプリケーションにプッシュ通知を追加する](app-service-mobile-ios-get-started-push.md)」をご覧ください。 Notification Hubs の詳細については、 [Notification Hubs の概要](../notification-hubs/notification-hubs-push-notification-overview.md)に関するページを参照してください。

## <a name="tags"></a>方法:タグを使用してターゲット プッシュを有効にする
Notification Hubs では、タグを使用して、ターゲットを絞った通知を特定の登録に送信できます。 いくつかのタグは自動的に作成されます。

* インストール ID は、特定のデバイスを識別します。
* 認証済みの SID に基づくユーザー ID は、特定のユーザーを識別します。

インストール ID には、**MobileServiceClient** の **installationId** プロパティからアクセスできます。  次の例では、Notification Hubs でインストール ID を使用して特定のインストールにタグを追加する方法を示します。

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

インストールを作成するときは、プッシュ通知の登録時にクライアントから提供されたタグはすべてバックエンドで無視されます。 クライアントがインストールにタグを追加できるようにするには、前述のパターンを使用してタグを追加するカスタム API を作成する必要があります。

例については、App Service Mobile Apps の完成したクイックスタート サンプルの[クライアントによって追加されるプッシュ通知タグ][5]に関するセクションを参照してください。

## <a name="push-user"></a>方法:認証されたユーザーにプッシュ通知を送信する
認証済みのユーザーがプッシュ通知に登録すると、ユーザー ID タグが登録に自動的に追加されます。 このタグを使用すると、そのユーザーが登録したすべてのデバイスにプッシュ通知を送信できます。 次のコードでは、要求を行ったユーザーの SID を取得し、そのユーザーのすべてのデバイス登録にテンプレート プッシュ通知を送信します。

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

認証されたクライアントからプッシュ通知を登録する場合は、登録を試みる前に、認証が完了していることを確認します。 詳細については、「.NET バックエンド向けの App Service Mobile Apps の完成したクイックスタート サンプル」の「[ユーザーへのプッシュ][6]」をご覧ください。

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>方法:.NET サーバー SDK のデバッグとトラブルシューティングを実行する
Azure App Service には、ASP.NET アプリケーションのデバッグとトラブルシューティングの方法が複数用意されています。

* [Azure App Service の監視](../app-service/web-sites-monitor.md)
* [Azure App Service での診断ログの有効化](../app-service/troubleshoot-diagnostic-logs.md)
* [Visual Studio での Azure App Service のトラブルシューティング](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>ログの記録
標準の ASP.NET トレース書き込みを使用して、App Service の診断ログを作成できます。 ログに書き込めるようにするには、モバイル アプリ バックエンドで診断を有効にする必要があります。

診断を有効にしてログに書き込むには、次の手順を実行します。

1. 「[アプリケーションのログ記録を有効にする (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows)」の手順に従います。
2. 次の using ステートメントをコード ファイルに追加します。

        using System.Web.Http.Tracing;
3. .NET バックエンドから診断ログに書き込むために、次のようにトレース ライターを作成します。

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. サーバー プロジェクトを再発行し、モバイル アプリ バックエンドにアクセスして、ログ記録付きでコード パスを実行します。
5. 「[ログ ファイルにアクセスする](../app-service/troubleshoot-diagnostic-logs.md#access-log-files)」で説明されているように、ログをダウンロードして評価します。

### <a name="local-debug"></a>認証に関するローカル デバッグ
クラウドに発行する前に変更をテストするために、ローカルでアプリケーションを実行できます。 ほとんどの Azure Mobile Apps バックエンドでは、Visual Studio で *F5* キーを押します。 ただし、認証を使用している場合は追加の考慮事項がいくつかあります。

App Service Authentication/Authorization を使用してクラウド ベースのモバイル アプリを構成する必要があります。また、クライアントに代替ログイン ホストとしてクラウド エンドポイントを指定する必要があります。 必要な手順の詳細については、クライアント プラットフォームのドキュメントを参照してください。

モバイル バックエンドに [Microsoft.Azure.Mobile.Server.Authentication] がインストールされていることを確認します。 次に、`MobileAppConfiguration` が `HttpConfiguration` に適用された後で、アプリケーションの OWIN Startup クラスに次のコードを追加します。

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

前の例では、Web.config ファイル内のアプリケーション設定 *authAudience* と *authIssuer* を、HTTPS スキームを使用して、それぞれアプリケーション ルートの URL に構成する必要があります。 同様に、*authSigningKey* をアプリケーションの署名キーの値に設定する必要があります。
署名キーを取得するには、次の手順を実行します。

1. [Azure Portal]
2. **[ツール]** 、 **[Kudu]** 、 **[移動]** の順にクリックします。
3. Kudu 管理サイトで、 **[環境]** をクリックします。
4. *WEBSITE\_AUTH\_SIGNING\_KEY* の値を見つけます。

ローカル アプリケーション構成で、*authSigningKey* パラメーターの署名キーを使用します。モバイル バックエンドは、ローカルで実行されているときに、クライアントがクラウドベースのエンドポイントから取得するトークンを検証できるようになりました。

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure Portal]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
