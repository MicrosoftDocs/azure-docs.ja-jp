---
title: Cache ASP.NET 出力キャッシュ プロバイダー
description: Azure Cache for Redis を使用して ASP.NET ページ出力をキャッシュする方法について説明します。
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 02/14/2017
ms.author: yegu
ms.openlocfilehash: 3aa2e9e773eb0c07b5f10a57dabf1138b9f3f288
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232911"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Azure Cache for Redis の ASP.NET 出力キャッシュ プロバイダー
Redis 出力キャッシュ プロバイダーは、出力キャッシュ データ用のプロセス外ストレージ メカニズムです。 このデータは、完全な HTTP 応答専用です (ページ出力キャッシュ)。 プロバイダーは、ASP.NET 4 で導入された新しい出力キャッシュ プロバイダー拡張機能ポイントに接続します。

Redis 出力キャッシュ プロバイダーを使用するには、まず対象のキャッシュを構成し、Redis 出力キャッシュ プロバイダー NuGet パッケージを使用して ASP.NET アプリケーションを構成します。 このトピックでは、Redis 出力キャッシュ プロバイダーを使用するようにアプリケーションを構成する手順について説明します。 Azure Cache for Redis インスタンスを作成および構成する方法の詳細については、[キャッシュの作成](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)に関するページを参照してください。

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET ページ出力をキャッシュに格納する
Visual Studio で Azure Cache for Redis Session State NuGet パッケージを使用してクライアント アプリケーションを構成するには、**[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

[`Package Manager Console`] ウィンドウで、次のコマンドを実行します。
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Redis 出力キャッシュ プロバイダー NuGet パッケージは、StackExchange.Redis.StrongName パッケージに依存します。 StackExchange.Redis.StrongName パッケージは、プロジェクト内に存在しなければインストールされます。 Redis Output Cache Provider NuGet パッケージについて詳しくは、[RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet ページを参照してください。

>[!NOTE]
>厳密な名前を持つ StackExchange.Redis.StrongName パッケージ以外に、厳密な名前を持たない StackExchange.Redis バージョンもあります。 プロジェクトで厳密な名前を持たないバージョンの StackExchange.Redis を使っている場合は、それをアンインストールする必要があります。そうしないと、プロジェクトで名前の競合が発生します。 これらのパッケージの詳細については、[.NET キャッシュ クライアントの構成](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)に関するトピックを参照してください。
>
>

NuGet パッケージがダウンロードされ、必要なアセンブリ参照が追加されて、次のセクションが web.config ファイルに追加されます。 このセクションには、Redis 出力キャッシュ プロバイダーを使うために ASP.NET アプリケーションに必要な構成が含まれます。

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <!-- For more details check https://github.com/Azure/aspnet-redis-providers/wiki -->
      <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
      <!-- 'databaseId' and 'applicationName' can be used with both options. -->
      <!--
      <add name="MyRedisOutputCache" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
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
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

コメント化されたセクションには、属性の例と各属性のサンプル設定が記述されています。

属性の構成には、Microsoft Azure ポータルのキャッシュ ブレードの値を使用してください。その他の値は適宜構成します。 キャッシュのプロパティにアクセスする方法については、「[Configure Azure Cache for Redis settings (Azure Cache for Redis の設定を構成する)](cache-configure.md#configure-azure-cache-for-redis-settings)」を参照してください。

* **host** : キャッシュ エンドポイントを指定します。
* **port** : SSL の設定に応じて、非 SSL ポートまたは SSL ポートを使用します。
* **accessKey** : キャッシュのプライマリ キーまたはセカンダリ キーを使用します。
* **ssl** : キャッシュとクライアント間の通信を SSL で保護する場合は true、保護しない場合は false を指定します。 必ず適切なポートを指定してください。
  * 既定では、新しいキャッシュに対して非 SSL ポートは無効になっています。 SSL ポートを使用するには、この設定に true を指定します。 非 SSL ポートの有効化の詳細については、[キャッシュの構成](cache-configure.md)に関するトピックの「[アクセス ポート](cache-configure.md#access-ports)」セクションを参照してください。
* **databaseId** : キャッシュ出力データに使用するデータベースを指定します。 指定されていない場合は、既定値の 0 が使用されます。
* **applicationName**: キーが `<AppName>_<SessionId>_Data` として Redis に格納されます。 この命名規則により、複数のアプリケーションで同じキーを共有できます。 このパラメーターは省略可能です。指定されていない場合は、既定値が使用されます。
* **connectionTimeoutInMilliseconds** : この設定によって、StackExchange.Redis クライアントの connectTimeout 設定をオーバーライドすることができます。 指定されていない場合は、connectTimeout 設定の既定値である 5000 が使用されます。 詳細については、 [StackExchange.Redis 構成モデル](https://go.microsoft.com/fwlink/?LinkId=398705)に関するページを参照してください。
* **operationTimeoutInMilliseconds** : この設定によって、StackExchange.Redis クライアントの syncTimeout 設定をオーバーライドすることができます。 指定されていない場合は、syncTimeout 設定の既定値である 1000 が使用されます。 詳細については、 [StackExchange.Redis 構成モデル](https://go.microsoft.com/fwlink/?LinkId=398705)に関するページを参照してください。

出力をキャッシュする各ページに、OutputCache ディレクティブを追加します。

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

前の例では、キャッシュされたページ データが 60 秒間キャッシュに保持され、パラメーターの組み合わせごとに異なるバージョンのページがキャッシュされます。 OutputCache ディレクティブの詳細については、「 [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)に関するページを参照してください。

ここまでの手順を実行すると、アプリケーションは Redis 出力キャッシュ プロバイダーを使用するように構成されます。

## <a name="next-steps"></a>次の手順
「[ASP.NET Session State Provider for Azure Cache for Redis (Azure Cache for Redis の ASP.NET セッション状態プロバイダー)](cache-aspnet-session-state-provider.md)」を参照してください。

