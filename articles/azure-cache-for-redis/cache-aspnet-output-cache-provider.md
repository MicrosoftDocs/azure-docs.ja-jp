---
title: Azure Cache for Redis の ASP.NET 出力キャッシュ プロバイダー
description: Azure Cache for Redis を使用して ASP.NET ページ出力をキャッシュする方法について説明します。 Redis 出力キャッシュ プロバイダーは、出力キャッシュ データ用のプロセス外ストレージ メカニズムです。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: 6d711b07a10e04dcdf31259f3e53c9687af28e28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993401"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Azure Cache for Redis の ASP.NET 出力キャッシュ プロバイダー

Redis 出力キャッシュ プロバイダーは、出力キャッシュ データ用のプロセス外ストレージ メカニズムです。 このデータは、完全な HTTP 応答専用です (ページ出力キャッシュ)。 プロバイダーは、ASP.NET 4 で導入された新しい出力キャッシュ プロバイダー拡張機能ポイントに接続します。 ASP.NET Core アプリケーションについては、「[ASP.NET Core での応答のキャッシュ](/aspnet/core/performance/caching/response)」を参照してください。 

Redis 出力キャッシュ プロバイダーを使用するには、まず対象のキャッシュを構成し、Redis 出力キャッシュ プロバイダー NuGet パッケージを使用して ASP.NET アプリケーションを構成します。 このトピックでは、Redis 出力キャッシュ プロバイダーを使用するようにアプリケーションを構成する手順について説明します。 Azure Cache for Redis インスタンスを作成および構成する方法の詳細については、[キャッシュの作成](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)に関するページを参照してください。

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET ページ出力をキャッシュに格納する

Visual Studio で Azure Cache for Redis Session State NuGet パッケージを使用してクライアント アプリケーションを構成するには、 **[ツール]** メニューで **[NuGet パッケージ マネージャー]** 、 **[パッケージ マネージャー コンソール]** の順にクリックします。

[`Package Manager Console`] ウィンドウで、次のコマンドを実行します。

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Redis 出力キャッシュ プロバイダー NuGet パッケージは、StackExchange.Redis.StrongName パッケージに依存します。 StackExchange.Redis.StrongName パッケージは、プロジェクト内に存在しなければインストールされます。 Redis Output Cache Provider NuGet パッケージについて詳しくは、[RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet ページを参照してください。

>[!NOTE]
>厳密な名前を持つ StackExchange.Redis.StrongName パッケージ以外に、厳密な名前を持たない StackExchange.Redis バージョンもあります。 プロジェクトで厳密な名前を持たないバージョンの StackExchange.Redis を使っている場合は、それをアンインストールする必要があります。そうしないと、プロジェクトで名前の競合が発生します。 これらのパッケージの詳細については、[.NET キャッシュ クライアントの構成](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)に関するトピックを参照してください。

NuGet パッケージがダウンロードされ、必要なアセンブリ参照が追加されて、次のセクションが web.config ファイルに追加されます。 このセクションには、Redis 出力キャッシュ プロバイダーを使うために ASP.NET アプリケーションに必要な構成が含まれます。

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

属性の構成には、Microsoft Azure ポータルのキャッシュ ブレードの値を使用してください。その他の値は適宜構成します。 キャッシュのプロパティにアクセスする方法については、「[Configure Azure Cache for Redis settings (Azure Cache for Redis の設定を構成する)](cache-configure.md#configure-azure-cache-for-redis-settings)」を参照してください。

| 属性 | Type | Default | 説明 |
| --------- | ---- | ------- | ----------- |
| *host* | string | "localhost" | Redis サーバーの IP アドレスまたはホスト名 |
| *port* | 正の整数 | 6379 (TLS/SSL 以外)<br/>6380 (TLS/SSL) | Redis サーバー ポート |
| *accessKey* | string | "" | Redis 認証が有効になっている場合は、Redis サーバーのパスワード。 既定では、値は空の文字列です。これは、セッション状態プロバイダーが Redis サーバーに接続するときにパスワードを使用しないことを意味します。 **Redis サーバーが Azure Redis Cache のようなパブリックにアクセス可能なネットワークにある場合は、セキュリティを強化するために必ず Redis 認証を有効にして、安全なパスワードを提供してください。** |
| *ssl* | boolean | **false** | Redis サーバーに TLS 経由で接続するかどうか。 Redis は初期状態では TLS をサポートしていないため、この値は既定では **false** です。 **初期状態で SSL をサポートする Azure Redis Cache を使用している場合は、セキュリティを強化するためにこれを必ず true に設定してください。**<br/><br/>既定では、新しいキャッシュに対して非 TLS ポートは無効になっています。 TLS ではないポートを使用するには、この設定に **true** を指定します。 非 TLS ポートの有効化の詳細については、[キャッシュの構成](cache-configure.md)に関するトピックの「[アクセス ポート](cache-configure.md#access-ports)」セクションを参照してください。 |
| *databaseIdNumber* | 正の整数 | 0 | *この属性は、web.config または AppSettings でのみ指定できます。*<br/><br/>使用する Redis データベースを指定します。 |
| *connectionTimeoutInMilliseconds* | 正の整数 | StackExchange.Redis によって提供されます | StackExchange.Redis.ConnectionMultiplexer の作成時に *ConnectTimeout* を設定するために使用されます。 |
| *operationTimeoutInMilliseconds* | 正の整数 | StackExchange.Redis によって提供されます | StackExchange.Redis.ConnectionMultiplexer の作成時に *SyncTimeout* を設定するために使用されます。 |
| *connectionString* (有効な StackExchange.Redis 接続文字列) | string | *該当なし* | AppSettings または web.config へのパラメーター参照、または有効な StackExchange.Redis 接続文字列。 この属性は、*host*、*port*、*accessKey*、*ssl*、およびその他の StackExchange.Redis 属性の値を指定できます。 *connectionString* の詳細については、「[属性に関する注意事項](#attribute-notes)」セクションの「[connectionString の設定](#setting-connectionstring)」を参照してください。 |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *該当なし* | *これらの属性は、web.config または AppSettings でのみ指定できます。*<br/><br/>これらの属性を使用して、接続文字列を指定します。 *settingsClassName* は、*settingsMethodName* で指定されたメソッドを含むアセンブリ修飾クラス名である必要があります。<br/><br/>*settingsMethodName* で指定するメソッドは、戻り値の型が **string** の public、static、および void (パラメーターを取らない) である必要があります。 このメソッドは、実際の接続文字列を返します。 |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *該当なし* | *これらの属性は、web.config または AppSettings でのみ指定できます。*<br/><br/>これらの属性を使用して、セッション状態/出力キャッシュからのログと StackExchange.Redis からのログを提供することにより、アプリケーションをデバッグします。 *loggingClassName* は、*loggingMethodName* で指定されたメソッドを含むアセンブリ修飾クラス名である必要があります。<br/><br/>*loggingMethodName* で指定するメソッドは、戻り値の型が **System.IO.TextWriter** の public、static、および void (パラメーターを取らない) である必要があります。 |
| *applicationName* | string | 現在のプロセスのモジュール名または "/" | *SessionStateProvider のみ*<br/>*この属性は、web.config または AppSettings でのみ指定できます。*<br/><br/>Redis Cache で使用するアプリ名プレフィックス。 お客様は、異なる目的で同じ Redis Cache を使用できます。 セッション キーが競合しないようにするために、アプリケーション名をプレフィックスとして付けることができます。 |
| *throwOnError* | boolean | true | *SessionStateProvider のみ*<br/>*この属性は、web.config または AppSettings でのみ指定できます。*<br/><br/>エラーが発生したときに例外をスローするかどうか。<br/><br/>*throwOnError* の詳細については、「[属性に関する注意事項](#attribute-notes)」セクションの「[*throwOnError* に関する注意事項](#notes-on-throwonerror)」を参照してください。 |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*。 |
| *retryTimeoutInMilliseconds* | 正の整数 | 5000 | *SessionStateProvider のみ*<br/>*この属性は、web.config または AppSettings でのみ指定できます。*<br/><br/>操作が失敗したときに再試行する時間の長さ。 この値が *operationTimeoutInMilliseconds* より小さい場合、プロバイダーは再試行しません。<br/><br/>*retryTimeoutInMilliseconds* の詳細については、「[属性に関する注意事項](#attribute-notes)」セクションの「[*retryTimeoutInMilliseconds* に関する注意事項](#notes-on-retrytimeoutinmilliseconds)」を参照してください。 |
| *redisSerializerType* | string | *該当なし* | Microsoft.Web.Redis.ISerializer を実装し、値をシリアル化および逆シリアル化するカスタム ロジックを含むクラスのアセンブリ修飾型名を指定します。 詳細については、「[属性に関する注意事項](#attribute-notes)」セクションの「[*redisSerializerType*](#about-redisserializertype) について」を参照してください。 |

## <a name="attribute-notes"></a>属性に関する注意事項

### <a name="setting-connectionstring"></a>*connectionString* の設定

*connectionString* の値は、AppSettings から実際の接続文字列 (そのような文字列が AppSettings に存在する場合) をフェッチするためのキーとして使用されます。 AppSettings 内に見つからない場合、*connectionString* の値は、web.config **ConnectionString** セクション (そのセクションが存在する場合) から実際の接続文字列をフェッチするためのキーとして使用されます。 接続文字列が AppSettings または web.config **ConnectionString** セクションに存在しない場合は、StackExchange.Redis.ConnectionMultiplexer の作成時に、*connectionString* のリテラル値が接続文字列として使用されます。

次の例は、*connectionString* の使用方法を示しています。

#### <a name="example-1"></a>例 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

`web.config` では、実際の値の代わりに上記のキーをパラメーター値として使用します。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>例 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

`web.config` では、実際の値の代わりに上記のキーをパラメーター値として使用します。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>例 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>*throwOnError* に関する注意事項

現在、セッション操作中にエラーが発生した場合、セッション状態プロバイダーは例外をスローします。 これにより、アプリケーションがシャットダウンされます。

この動作は、既存の ASP.NET セッション状態プロバイダー ユーザーの期待をサポートしながら、必要に応じて例外に対処する機能も提供するように変更されています。 既定の動作では、エラーが発生すると引き続き例外がスローされます。これは他の ASP.NET セッション状態プロバイダーと同じです。既存のコードは以前と同じように動作するはずです。

*throwOnError* を **false** に設定すると、エラーが発生したときに例外をスローするのではなく、何も行われずに失敗します。 エラーが発生したかどうかを確認し、発生した場合に例外を確認するには、静的プロパティ *Microsoft.Web.Redis.RedisSessionStateProvider.LastException* を調べてください。

### <a name="notes-on-retrytimeoutinmilliseconds"></a>*retryTimeoutInMilliseconds* に関する注意事項

これにより、再試行ロジックが提供され、一部のセッション操作がネットワーク障害などの理由で失敗時に再試行する必要がある場合のケースが簡略化されます。また、再試行タイムアウトを制御したり、再試行を完全に除外したりすることもできます。

*retryTimeoutInMilliseconds* を 2000 などの数値に設定すると、セッション操作が失敗したときに、2000 ミリ秒間再試行されてからそれがエラーとして扱われます。 そのため、セッション状態プロバイダーにこの再試行ロジックを適用させるには、タイムアウトを構成するだけで済みます。 最初の再試行は 20 ミリ秒後に行われますが、ネットワーク障害が発生したときには、ほとんどの場合、これで十分です。 その後、タイムアウトするまで、1 秒ごとに再試行されます。タイムアウトの直後に、タイムアウトを (最大でも) 1 秒短縮しないようにするためにもう一度試行されます。

再試行が不要と思われる場合 (たとえば、Redis サーバーをアプリケーションと同じマシンで実行している場合)、または再試行ロジックを自分で処理する場合は、*retryTimeoutInMilliseconds* を 0 に設定します。

### <a name="about-redisserializertype"></a>*redisSerializerType* について

既定では、Redis に値を格納するためのシリアル化は、**BinaryFormatter** クラスが提供するバイナリ形式で行われます。 **Microsoft.Web.Redis.ISerializer** を実装し、値をシリアル化および逆シリアル化するカスタム ロジックを持つクラスのアセンブリ修飾型名を指定するには、*redisSerializerType* を使用します。 例として、JSON.NET を使用した JSON シリアライザー クラスを次に示します。

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

このクラスが **MyCompanyDll** という名前のアセンブリで定義されているとすると、それを使用するようにパラメーター *redisSerializerType* を設定できます。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>出力キャッシュ ディレクティブ

出力をキャッシュする各ページに、OutputCache ディレクティブを追加します。

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

前の例では、キャッシュされたページ データが 60 秒間キャッシュに保持され、パラメーターの組み合わせごとに異なるバージョンのページがキャッシュされます。 OutputCache ディレクティブの詳細については、「 [@OutputCache](/previous-versions/dotnet/netframework-4.0/hdxfb6cy(v=vs.100))に関するページを参照してください。

ここまでの手順を実行すると、アプリケーションは Redis 出力キャッシュ プロバイダーを使用するように構成されます。

## <a name="third-party-output-cache-providers"></a>サードパーティ出力キャッシュ プロバイダー

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>次のステップ

「[ASP.NET Session State Provider for Azure Cache for Redis (Azure Cache for Redis の ASP.NET セッション状態プロバイダー)](cache-aspnet-session-state-provider.md)」を参照してください。