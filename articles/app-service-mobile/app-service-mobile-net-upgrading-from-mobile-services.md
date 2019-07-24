---
title: Mobile Services から Azure App Service にアップグレードする
description: 簡単に Mobile Services アプリケーションを App Service Mobile App にアップグレードする方法について説明します。
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: f5ffc795e6469971d1eaf335d6683f94d05f0807
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278614"
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>既存の .NET Azure Mobile Service を App Service にアップグレードする
App Service Mobile は、Microsoft Azure を使用してモバイル アプリケーションを構築する新しい方法です。 詳細については、「 [Mobile Apps とは]」を参照してください。

このトピックでは、既存の .NET バックエンド アプリケーションを Azure Mobile Services から新しい App Service Mobile Apps にアップグレードする方法について説明します。 このアップグレードの実行中も、既存の Mobile Services アプリケーションの動作を続行できます。   Node.js バックエンド アプリケーションにアップグレードする必要がある場合は、[既存の Node.js Mobile Services のアップグレード](app-service-mobile-node-backend-upgrading-from-mobile-services.md)に関するページを参照してください。

Azure App Service にアップグレードされたモバイル バックエンドは、App Service のすべての機能にアクセスでき、Mobile Services の価格ではなく [App Service の価格]に従って課金されます。

## <a name="migrate-vs-upgrade"></a>移行とアップグレード
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> アップグレードする前に、 [移行する](app-service-mobile-migrating-from-mobile-services.md) ことをお勧めします。 そうすることで、同じ App Service プランに両方のバージョンのアプリケーションを指定できるため、追加コストが発生しません。
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Mobile Apps .NET サーバー SDK の機能強化
新しい [Mobile Apps SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) にアップグレードすると、次のような利点があります。

* NuGet の依存関係の柔軟性の向上。 ホスティング環境で独自のバージョンの NuGet パッケージが提供されなくなったので、互換性のある代わりのバージョンを使用できます。 ただし、Mobile Server SDK または依存関係に対して新しい重要なバグ修正やセキュリティ更新プログラムがある場合は、サービスを手動で更新する必要があります。
* モバイル SDK の柔軟性の向上。 認証、テーブル API、プッシュ登録エンドポイントなど、セットアップされる機能とルートを明示的に制御できます。 詳細については、[Azure Mobile Apps で .NET サーバー SDK を使用する方法](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)に関するページを参照してください。
* ASP.NET プロジェクトの他の種類とルートのサポート。 MVC および Web API コントローラーをモバイル バックエンド プロジェクトと同じプロジェクトでホストできるようになりました。
* 新しい App Service 認証機能のサポート。Web アプリとモバイル アプリの両方で共通の認証構成を使用できます。

## <a name="overview"></a>基本的なアップグレードの概要
多くの場合、新しい Mobile Apps サーバー SDK に切り替えて、コードを新しい Mobile App インスタンスに再発行するだけで、簡単にアップグレードできます。 ただし、一部のシナリオは、高度な認証シナリオやスケジュールされたジョブの操作など、いくつか追加の構成が必要になります。 これについてはそれぞれ以降のセクションで説明します。

> [!TIP]
> アップグレードを開始する前に、このトピックの残りの部分を読み、よく理解しておくことをお勧めします。 下に列記した機能のうち、使用する機能をすべてメモしてください。
>
>

Mobile Services クライアント SDK と新しい Mobile Apps サーバー SDK の間に互換性は **ありません** 。 アプリ用にサービスを接続する場合は、発行されたクライアントを現在使用しているサイトに変更を発行しないでください。 代わりに、複製として機能する新しいモバイル アプリを作成する必要があります。 このアプリケーションを同じ App Service プランに指定することで、追加の財務費用が発生しないようにすることができます。

アプリケーションには 2 つのバージョンがあります。1 つは変わらず、発行されたアプリとしてそのままの状態で機能し、もう 1 つはユーザーがアップグレードし、新しいクライアント リリースのターゲットにすることができます。 自分のペースでコードを移動し、テストできますが、バグの修正が両方に適用されることを確認する必要があります。 必要な数の (そのままの状態の) クライアント アプリを最新バージョンに更新したら、必要に応じて、元の移行されたアプリを削除することができます。

このアップグレード プロセスの全概要は、次のとおりです。

1. 新しい Mobile App を作成する
2. 新しい Server SDK を使用するようにプロジェクトを更新する
3. クライアント アプリケーションの新しいバージョンをリリースする
4. (省略可能) 元の移行されたインスタンスを削除する

## <a name="mobile-app-version"></a>2 番目のアプリケーション インスタンスを作成する
アップグレードの最初のステップは、新しいバージョンのアプリケーションをホストする Mobile App リソースを作成することです。 既存のモバイル サービスを既に移行している場合は、同じホスティング プランでこのバージョンを作成します。 [Azure Portal] を開き、移行済みのアプリケーションに移動します。 実行されている App Service プランをメモしてをおきます。

次に、 [.NET バックエンドの作成手順](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app)に従って、2 番目のアプリケーション インスタンスを作成します。 App Service プランまたは "ホスティング プラン" を選択するよう求められたら、移行済みアプリケーションのプランを選択します。

Mobile Services で使用したのと同じデータベースと通知ハブを使用することもできます。 [Azure Portal] を開き、元のアプリケーションに移動してから、**[設定]** > **[アプリケーションの設定]** の順にクリックして、これらの値をコピーできます。 **[接続文字列]** の `MS_NotificationHubConnectionString` と `MS_TableConnectionString` をコピーします。 新しいアップグレード サイトに移動し、接続文字列を貼り付けて既存の値を上書きします。 アプリに必要な他のアプリケーション設定について、このプロセスを繰り返します。

アプリケーションの ASP.NET プロジェクトのコピーを作成し、新しいサイトに発行します。 新しい URL で更新されたクライアント アプリケーションのコピーを使用して、すべて予期したとおりに機能することを検証します。

## <a name="updating-the-server-project"></a>サーバー プロジェクトの更新
Mobile Apps は、Mobile Services ランタイムとほぼ同じ機能を持つ新しい [Mobile App サーバー SDK] を提供します。 最初に、Mobile Services パッケージへの参照をすべて削除する必要があります。 NuGet パッケージ マネージャーで、 `WindowsAzure.MobileServices.Backend`を検索します。 ほとんどのアプリに、`WindowsAzure.MobileServices.Backend.Tables` と `WindowsAzure.MobileServices.Backend.Entity` を含む、ここに示されているいくつかのパッケージが表示されます。 そのような場合は、依存関係ツリーの最下位パッケージ ( `Entity`など) から削除を開始します。 プロンプトが表示されても、すべての従属パッケージを削除しないでください。 `WindowsAzure.MobileServices.Backend` 自体を削除するまで、このプロセスを繰り返します。

この時点で、プロジェクトは Mobile Services SDK を参照しなくなります。

次に、Mobile Apps SDK の参照を追加します。 このアップグレードでは、ほとんどの開発者が `Microsoft.Azure.Mobile.Server.Quickstart` パッケージをダウンロードしてインストールしようと考えます。これにより、必要な設定全体を取得できるからです。

SDK の違いにより、かなり多くのコンパイラ エラーが発生しますが、これらは簡単に対処でき、このセクションの残りの部分で説明します。

### <a name="base-configuration"></a>基本構成
次に、WebApiConfig.cs で、

```csharp
// Use this class to set configuration options for your mobile service
ConfigOptions options = new ConfigOptions();

// Use this class to set WebAPI configuration options
HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));
```

を以下に置き換えることができます。

```csharp
HttpConfiguration config = new HttpConfiguration();
new MobileAppConfiguration()
    .UseDefaultConfiguration()
.ApplyTo(config);

```

> [!NOTE]
> 新しい .NET サーバー SDK と、アプリから機能を追加/削除する方法の詳細については、 [.NET サーバー SDK の使用方法] に関するトピックを参照してください。
>
>

アプリで認証機能を使用する場合は、OWIN ミドルウェアを登録する必要もあります。 この場合は、上記の構成コードを新しい OWIN スタートアップ クラスに移動する必要があります。

1. NuGet パッケージ `Microsoft.Owin.Host.SystemWeb` がプロジェクトにまだ含まれていない場合は、追加します。
2. Visual Studio でプロジェクトを右クリックして、**[追加]** -> **[新しい項目]** の順に選択します。 **[Web]** -> **[全般]** -> **[OWIN スタートアップ クラス]** の順に選択します。
3. MobileAppConfiguration の上記のコードを `WebApiConfig.Register()` から、新しいスタートアップ クラスの `Configuration()` メソッドに移動します。

`Configuration()` メソッドが以下で終了することを確認します。

```csharp
app.UseWebApi(config)
app.UseAppServiceAuthentication(config);
```

認証に関するその他の変更については、以降の完全な認証セクションで説明します。

### <a name="working-with-data"></a>データの操作
Mobile Services では、モバイル アプリ名が Entity Framework セットアップの既定のスキーマ名として機能していました。

以前と同じスキーマが参照されるようにするには、以下を使用して、アプリケーションの DbContext にスキーマを設定します。

```csharp
string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");
```

上記のようにする場合は、MS_MobileServiceName が設定されていることを確認してください。 アプリケーションで既にこのようにカスタマイズしている場合は、別のスキーマ名を指定することもできます。

### <a name="system-properties"></a>システム プロパティ
#### <a name="naming"></a>名前を付ける
Azure Mobile Services サーバー SDK の場合、システム プロパティには必ず、次のようにプレフィックスとして下線を 2 つ (`__`) 含めるようにしてください。

* __createdAt
* __updatedAt
* __deleted
* __version

Mobile Services クライアント SDK には、この形式でシステム プロパティを解析するための特別なロジックがあります。

Azure Mobile Apps では、現在、システム プロパティで特別な形式は使用しておらず、以下の名前が使用されています。

* createdAt
* updatedAt
* deleted
* version

Mobile Apps クライアント SDK では新しいシステム プロパティ名を使用するため、クライアント コードを変更する必要はありません。 ただし、直接サービスへの REST 呼び出しを行う場合は、適宜クエリを変更する必要があります。

#### <a name="local-store"></a>ローカル ストア
システム プロパティの名前を変更すると、Mobile Services のオフライン同期ローカル データベースと Mobile Apps との互換性がなくなります。 可能であれば、保留中の変更がサーバーに送信されるまで、クライアント アプリを Mobile Services から Mobile Apps にアップグレードしないでください。 したがって、アップグレードされたアプリは、新しいデータベースのファイル名を使用する必要があります。

操作キューに保留中のオフラインの変更がある状態で、クライアント アプリを Mobile Services から Mobile Apps にアップグレードする場合は、新しい列名を使用するようにシステム データベースを更新する必要があります。 iOS の場合は、軽量な移行を使用して列名を変更することで、これを行うことができます。 Android および .NET 管理対象クライアントの場合は、カスタム SQL を記述して、データ オブジェクト テーブルの列の名前を変更する必要があります。

iOS の場合は、データ エンティティのコア データ スキーマを変更し、以下の内容と一致させる必要があります。 `createdAt`、`updatedAt` および `version` では、プレフィックスとして `ms_` を使用しなくなったことに注意してください。

| Attribute | type | Note |
| --- | --- | --- |
| id |文字列、必須のマーク |リモート ストア内のプライマリ キー |
| createdAt |日付 |(省略可能) createdAt システム プロパティにマップします。 |
| updatedAt |日付 |(省略可能) updatedAt システム プロパティにマップします。 |
| version |String |(省略可能) 競合の検出に使用され、バージョンにマップします。 |

#### <a name="querying-system-properties"></a>システム プロパティの照会
Azure Mobile Services の場合、既定ではシステム プロパティは送信されず、クエリ文字列 `__systemProperties`を使用して要求された場合にのみ、送信されます。 これに対し、Azure Mobile Apps のシステム プロパティは、サーバー SDK オブジェクト モデルの一部であるため、 **常に選択** されます。

この変更は主に、 `MappedEntityDomainManager`の拡張機能など、ドメイン マネージャーのカスタム実装に影響します。 Mobile Services では、クライアントがシステム プロパティを要求することがない場合、実際にはすべてのプロパティをマップしない `MappedEntityDomainManager` を使用できます。 ただし、Azure Mobile Apps では、これらのマップされないプロパティが原因で、GET クエリでエラーが発生します。

この問題の最も簡単な解決方法は、DTO を変更し、`EntityData` ではなく `ITableData` から継承するようにすることです。 その後、省略する必要があるフィールドに `[NotMapped]` 属性を追加します。

たとえば、次のような場合は、システム プロパティなしで `TodoItem` を定義します。

```csharp
using System.ComponentModel.DataAnnotations.Schema;

public class TodoItem : ITableData
{
    public string Text { get; set; }

    public bool Complete { get; set; }

    public string Id { get; set; }

    [NotMapped]
    public DateTimeOffset? CreatedAt { get; set; }

    [NotMapped]
    public DateTimeOffset? UpdatedAt { get; set; }

    [NotMapped]
    public bool Deleted { get; set; }

    [NotMapped]
    public byte[] Version { get; set; }
}
```

注: `NotMapped` でエラーが発生する場合は、アセンブリ `System.ComponentModel.DataAnnotations` への参照を追加します。

### <a name="cors"></a>CORS
Mobile Services には、ASP.NET CORS ソリューションをラップすることによる CORS のサポートがいくつか含まれていました。 このラップ レイヤーは開発者がさらに制御できるように削除されたため、 [ASP.NET CORS サポート](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)を直接利用することができます。

CORS を使用する場合の主な懸念事項は、クライアント SDK が適切に機能するように `eTag` と `Location` ヘッダーを許可する必要があることです。

### <a name="push-notifications"></a>プッシュ通知
プッシュについては、サーバー SDK に含まれていない主な項目として、PushRegistrationHandler クラスがあります。 登録処理は Mobile Apps アプリとはわずかに異なり、タグのない登録が既定で有効になっています。 カスタム API を使用してタグを管理することができます。 詳細については、 [タグの登録](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) 手順に関する記述を参照してください。

### <a name="scheduled-jobs"></a>スケジュールされたジョブ
スケジュールされたジョブは、Mobile Apps には組み込まれません。そのため、.NET バックエンド内にある既存のジョブは個別にアップグレードする必要があります。 1 つの方法としては、スケジュールされた [Web ジョブ]をモバイル アプリ コード サイトで作成します。 また、ジョブ コードを保持するコントローラーを設定し、必要なスケジュールでそのエンドポイントをヒットするように [Azure Scheduler] を構成することもできます。

### <a name="miscellaneous-changes"></a>その他の変更
モバイル クライアントで使用されるすべての ApiController に、 `[MobileAppController]` 属性が必要になりました。 これは既定では含まれなくなったため、他の ApiController はモバイル フォーマッタによる影響を受けることはありません。

`ApiServices` オブジェクトは SDK に含まれなくなりました。 Mobile App の設定にアクセスするために、次の手順を使用できます。

```csharp
MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
```

同様に、ロギングは標準の ASP.NET トレース書き込みを使用して行われるようになりました。

```csharp
ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
traceWriter.Info("Hello, World");  
```

## <a name="authentication"></a>認証に関する考慮事項
Mobile Services の認証コンポーネントは、App Service の認証/承認機能に移動されました。 これをサイトに対して有効にする方法の詳細については、 [モバイル アプリへの認証の追加](app-service-mobile-ios-get-started-users.md) に関するトピックを参照してください。

AAD、Facebook、Google などの一部のプロバイダーでは、コピー アプリケーションから既存の登録を利用できます。 ID プロバイダーのポータルに移動して、新しいリダイレクト URL を登録に追加するだけです。 その後、クライアント ID とシークレットを使用して、App Service の認証/承認を構成します。

### <a name="controller-action-authorization"></a>コントローラー アクションの認証
標準の ASP.NET `[Authorize]` 属性を使用する場合は、`[AuthorizeLevel(AuthorizationLevel.User)]` 属性のすべてのインスタンスの変更が必要になりました。 さらに、他の ASP.NET アプリケーションと同様に、現在、コントローラーは既定では匿名になります。
管理者やアプリケーションなど、他の AuthorizeLevel オプションのいずれかを使用していた場合、これらは使用されなくなったことに注意してください。 代わりに共有シークレットに対して AuthorizationFilter をセットアップするか、サービス間呼び出しを安全に有効にするように AAD サービス プリンシパルを構成することができます。

### <a name="getting-additional-user-information"></a>追加のユーザー情報の取得
以下の `GetAppServiceIdentityAsync()` メソッドを使用して、アクセス トークンを含む、追加のユーザー情報を取得することができます。

```csharp
FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();
```

さらに、アプリケーションでユーザー ID をデータベースに格納しているなど、ユーザー ID と依存関係がある場合は、Mobile Services と App Service Mobile Apps のユーザー ID が異なることに注意する必要があります。 ただし、そのような場合でも Mobile Services のユーザー ID を取得することはできます。 すべての ProviderCredentials サブクラスには、UserId プロパティがあります。 ここでは、前の例を引き続き使用します。

```csharp
string mobileServicesUserId = creds.Provider + ":" + creds.UserId;
```

ユーザー ID と依存関係がある場合は、可能であれば、ID プロバイダーへの同じ登録を使用することが重要です。 ユーザー ID は通常、使用されたアプリケーション登録に制限されるため、新しい登録を導入した場合、ユーザーとそのデータを照合する際に問題が生じる可能性があります。

### <a name="custom-authentication"></a>カスタム認証
アプリでカスタム認証ソリューションを使用する場合は、アップグレードされたサイトがシステムにアクセスできることを確認する必要があります。 [.NET サーバー SDK の概要] に示されているカスタム認証の新しい手順に従って、ソリューションを統合します。 カスタム認証コンポーネントはまだプレビュー段階であることに注意してください。

## <a name="updating-clients"></a>クライアントの更新
モバイル アプリ バックエンドを運用している場合は、それを利用する新しいバージョンのクライアント アプリケーションを使用できます。 Mobile Apps には新しいバージョンのクライアント SDK も含まれ、上記のサーバー アップグレードと同様に、Mobile Apps バージョンをインストールする前に Mobile Services SDK へのすべての参照を削除する必要があります。

バージョン間での主な変更の 1 つは、コンストラクターでアプリケーション キーが不要になったことです。 現在は、モバイル アプリの URL を渡すだけです。 たとえば、.NET クライアントの場合、現在の `MobileServiceClient` コンストラクターは以下のようになります。

```csharp
public static MobileServiceClient MobileService = new MobileServiceClient(
    "https://contoso.azurewebsites.net", // URL of the Mobile App
);
```

新しい SDK のインストールおよび新しい構造の使用については、以下のリンクを介して参照できます。

* [iOS バージョン 3.0.0 以降](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) バージョン 2.0.0 以降](app-service-mobile-dotnet-how-to-use-client-library.md)

アプリケーションでプッシュ通知を使用する場合は、変更されている内容もあるため、プラットフォームごとに固有の登録手順をメモしてください。

新しいクライアント バージョンの準備ができたら、アップグレードしたサーバー プロジェクトで試してみます。 機能することを確認したら、新しいバージョンのアプリケーションを顧客にリリースできます。 最後に、顧客がこれらの更新プログラムを受け取ったら、Mobile Services バージョンのアプリを削除できます。 これで、最新の Mobile Apps サーバー SDK を使用する App Service モバイル アプリに完全にアップグレードできました。

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Mobile Apps とは]: app-service-mobile-value-prop.md
[Mobile App サーバー SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /azure/scheduler/
[Web ジョブ]: https://github.com/Azure/azure-webjobs-sdk/wiki
[.NET サーバー SDK の使用方法]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[App Service の価格]: https://azure.microsoft.com/pricing/details/app-service/
[.NET サーバー SDK の概要]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
