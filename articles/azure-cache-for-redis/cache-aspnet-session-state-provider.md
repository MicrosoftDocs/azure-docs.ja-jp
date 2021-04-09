---
title: Cache ASP.NET セッション状態プロバイダー
description: Azure Cache for Redis を使用してメモリ内に ASP.NET セッション状態を格納する方法を説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 05/01/2017
ms.openlocfilehash: ce77f5074d707da5cfb251a103653b96e4644b5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92544530"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Azure Cache for Redis の ASP.NET セッション状態プロバイダー

Azure Cache for Redis には、セッション状態プロバイダーが用意されています。セッション状態プロバイダーを使用すると、セッション状態を SQL Server データベースにではなく Azure Cache for Redis でメモリ内に格納することができます。 キャッシュ セッション状態プロバイダーを使用するには、まず対象のキャッシュを構成し、Azure Cache for Redis Session State NuGet パッケージを使用して、キャッシュに必要な構成を ASP.NET アプリケーションに対して行います。 ASP.NET Core アプリケーションの場合は、[ASP.NET Core でのセッションと状態管理](/aspnet/core/fundamentals/app-state)に関するページを参照してください。

ユーザー セッションの状態をなんらかの形で格納しないのは、実際のクラウド アプリケーションでは実用的でない場合が多いですが、方法によっては、パフォーマンスとスケーラビリティに与える影響が大きくなります。 状態を格納する必要がある場合は、状態の量を少なくし、Cookie に格納することをお勧めします。 この方法を利用できない場合は、ASP.NET セッション状態と分散型メモリ内キャッシュのプロバイダーを使用することを次にお勧めします。 パフォーマンスとスケーラビリティの観点から最もお勧めできないのが、データベースを利用したセッション状態プロバイダーを使用する方法です。 このトピックでは、Azure Cache for Redis の ASP.NET セッション状態プロバイダーを使用する方法について説明します。 その他のセッション状態のオプションについては、 [ASP.NET セッション状態のオプション](#aspnet-session-state-options)に関するトピックを参照してください。

## <a name="store-aspnet-session-state-in-the-cache"></a>ASP.NET セッション状態をキャッシュに格納する

Visual Studio で Azure Cache for Redis Session State NuGet パッケージを使用してクライアント アプリケーションを構成するには、 **[ツール]** メニューで **[NuGet パッケージ マネージャー]** 、 **[パッケージ マネージャー コンソール]** の順にクリックします。

[`Package Manager Console`] ウィンドウで、次のコマンドを実行します。
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Premium レベルでクラスター機能を使用する場合は、[RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 以降を使用する必要があります。そうしないと、例外がスローされます。 2\.0.1 以降への移行は重大な変更です。詳しくは、「[v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)」(v2.0.0 の重大な変更の詳細) をご覧ください。 この記事の更新時点での、このパッケージの最新バージョンは 2.2.3 です。
> 
> 

Redis セッション状態プロバイダー NuGet パッケージは、StackExchange.Redis.StrongName パッケージに依存します。 StackExchange.Redis.StrongName パッケージは、プロジェクト内に存在しなければインストールされます。

>[!NOTE]
>厳密な名前を持つ StackExchange.Redis.StrongName パッケージ以外に、厳密な名前を持たない StackExchange.Redis バージョンもあります。 プロジェクトで厳密な名前を持たないバージョンの StackExchange.Redis を使っている場合は、それをアンインストールする必要があります。そうしないと、プロジェクトで名前の競合が発生します。 これらのパッケージの詳細については、[.NET キャッシュ クライアントの構成](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)に関するトピックを参照してください。
>
>

NuGet パッケージがダウンロードされ、必要なアセンブリ参照が追加されて、次のセクションが web.config ファイルに追加されます。 このセクションには、Azure Cache for Redis セッション状態プロバイダーを使うために ASP.NET アプリケーションに必要な構成が含まれます。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

コメント化されたセクションには、属性の例と各属性のサンプル設定が記述されています。

属性の構成には、Microsoft Azure ポータルのキャッシュ ブレードの値を使用してください。その他の値は適宜構成します。 キャッシュのプロパティにアクセスする方法については、「[Configure Azure Cache for Redis settings (Azure Cache for Redis の設定を構成する)](cache-configure.md#configure-azure-cache-for-redis-settings)」を参照してください。

* **host** : キャッシュ エンドポイントを指定します。
* **port**: TLS の設定に応じて、非 TLS/SSL ポートまたは TLS/SSL ポートを使用します。
* **accessKey** : キャッシュのプライマリ キーまたはセカンダリ キーを使用します。
* **ssl**: キャッシュとクライアント間の通信を TLS で保護する場合は true、保護しない場合は false を指定します。 必ず適切なポートを指定してください。
  * 既定では、新しいキャッシュに対して非 TLS ポートは無効になっています。 TLS ポートを使用するには、この設定に true を指定します。 非 TLS ポートの有効化の詳細については、[キャッシュの構成](cache-configure.md)に関するトピックの「[アクセス ポート](cache-configure.md#access-ports)」セクションを参照してください。
* **throwOnError**: 失敗時に例外がスローされるようにする場合は true、操作の失敗時にエラー メッセージが表示されないようにする場合は false を指定します。 静的 Microsoft.Web.Redis.RedisSessionStateProvider.LastException プロパティをチェックすることでエラーを確認できます。 既定値は true です。
* **retryTimeoutInMilliseconds**: 失敗した操作がこの時間に再試行されます。ミリ秒単位で指定します。 最初は 20 ミリ秒後に再試行され、その後 retryTimeoutInMilliseconds の時間が経過するまで 1 秒ごとに再試行されます。 この時間を過ぎるとすぐに、操作が最後に 1 回再試行されます。 操作が失敗した場合、throwOnError 設定に応じて、例外がスローされて呼び出し元に戻ります。 既定値は 0 です。これは再試行されないことを意味します。
* **databaseId** : キャッシュ出力データに使用するデータベースを指定します。 指定されていない場合は、既定値の 0 が使用されます。
* **applicationName**: キーが `{<Application Name>_<Session ID>}_Data` として Redis に格納されます。 この命名規則により、複数のアプリケーションで同じ Redis インスタンスを共有できます。 このパラメーターは省略可能です。指定されていない場合は、既定値が使用されます。
* **connectionTimeoutInMilliseconds** : この設定によって、StackExchange.Redis クライアントの connectTimeout 設定をオーバーライドすることができます。 指定されていない場合は、connectTimeout 設定の既定値である 5000 が使用されます。 詳細については、 [StackExchange.Redis 構成モデル](https://go.microsoft.com/fwlink/?LinkId=398705)に関するページを参照してください。
* **operationTimeoutInMilliseconds** : この設定によって、StackExchange.Redis クライアントの syncTimeout 設定をオーバーライドすることができます。 指定されていない場合は、syncTimeout 設定の既定値である 1000 が使用されます。 詳細については、 [StackExchange.Redis 構成モデル](https://go.microsoft.com/fwlink/?LinkId=398705)に関するページを参照してください。
* **redisSerializerType** - この設定では、Redis に送信されるセッションの内容のカスタム シリアル化を指定できます。 指定される型では、`Microsoft.Web.Redis.ISerializer` を実装し、パラメーターのないパブリック コンストラクターを宣言する必要があります。 既定では `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` が使用されます。

これらのプロパティの詳細については、 [Redis の ASP.NET セッション状態プロバイダーの発表](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)に関する元のブログ投稿を参照してください。

web.config の標準の InProc セッション状態プロバイダー セクションを忘れずにコメント アウトしてください。

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

これらの手順を実行すると、アプリケーションが Azure Cache for Redis セッション状態プロバイダーを使用するように構成されます。 アプリケーションでセッション状態を使用すると、状態が Azure Cache for Redis インスタンスに格納されます。

> [!IMPORTANT]
> 既定のメモリ内の ASP.NET セッション状態プロバイダーに格納できるデータと異なり、キャッシュに格納されるデータは、シリアル化可能である必要があります。 Redis のセッション状態プロバイダーを使用するときは、セッション状態に格納されるデータ型がシリアル化可能であることを確認してください。
> 
> 

## <a name="aspnet-session-state-options"></a>ASP.NET セッション状態のオプション

* メモリ内のセッション状態プロバイダー: このプロバイダーでは、メモリ内にセッション状態が格納されます。 このプロバイダーを使用する利点は、単純で高速なことです。 ただし、メモリ内プロバイダーを使用する場合、このプロバイダーは分散型でないため、Web Apps は拡張できません。
* SQL Server のセッション状態プロバイダー: このプロバイダーでは、SQL Server 内にセッション状態が格納されます。 永続的なストレージにセッション状態を格納する場合は、このプロバイダーを使います。 Web アプリは拡張できますが、セッションに SQL Server を使うと、Web アプリのパフォーマンスに影響を与えます。 パフォーマンスを向上させるために、このプロバイダーを[インメモリ OLTP 構成](/archive/blogs/sqlserverstorageengine/asp-net-session-state-with-sql-server-in-memory-oltp)で使用することもできます。
* Azure Cache for Redis セッション状態プロバイダーなどの分散型メモリ内セッション状態プロバイダー: このプロバイダーを使用すると、両方の長所を活用できます。 Web アプリで、単純かつ高速で、スケーラブルなセッション状態プロバイダーを使うことができます。 このプロバイダーではセッション状態がキャッシュに格納されるため、一時的なネットワーク障害など、分散型メモリ内キャッシュとの通信時に関するすべての要素をアプリで考慮する必要があります。 キャッシュの使用に関するベスト プラクティスについては、Microsoft のパターンとプラクティスの「[Azure Cloud Application Design and Implementation Guidance (Azure クラウド アプリケーションの設計と実装のガイダンス)](https://github.com/mspnp/azure-guidance)」の「[キャッシュに関するガイダンス](/azure/architecture/best-practices/caching)」を参照してください。

セッション状態とその他のベスト プラクティスの詳細については、 [Web 開発に関するベスト プラクティス (Azure を使用した実際のクラウド アプリケーションの構築)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)に関するページを参照してください。

## <a name="third-party-session-state-providers"></a>サードパーティのセッション状態プロバイダー

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache ignite](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>次のステップ

「[Azure Cache for Redis の ASP.NET 出力キャッシュ プロバイダー](cache-aspnet-output-cache-provider.md)」を参照してください。