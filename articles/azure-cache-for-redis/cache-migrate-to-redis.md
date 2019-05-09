---
title: Managed Cache Service アプリケーションの Redis への移行 - Azure | Microsoft Docs
description: Managed Cache Service アプリケーションおよび In-Role Cache アプリケーションを Azure Cache for Redis に移行する方法について説明します
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 116e54fd39af801cf8941a974da2b72c483097dc
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237023"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Managed Cache Service から Azure Cache for Redis への移行
Azure Managed Cache Service を使用するアプリケーションの Azure Cache for Redis への移行は、キャッシュ アプリケーションで使用されている Managed Cache Service の機能によっては、最小限の変更をアプリケーションに対して行うだけで実現できます。 API はまったく同じではありませんがよく似ており、Managed Cache Service を使用してキャッシュにアクセスする既存コードの多くは最小限の変更で再利用できます。 この記事では、Azure Cache for Redis を使用するように Managed Cache Service アプリケーションを移行する際に必要な構成とアプリケーションの変更を行う方法、および Azure Cache for Redis の機能の一部を使用して Managed Cache Service キャッシュの機能を実装する方法について説明します。

>[!NOTE]
>Managed Cache Service および In-Role Cache は、2016 年 11 月 30 日に[廃止](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)されました。 Azure Cache for Redis に移行する In-Role Cache デプロイがある場合は、この記事の手順に従って実行できます。

## <a name="migration-steps"></a>移行の手順
Managed Cache Service アプリケーションを Azure Cache for Redis を使用するように移行するには、以下の手順で行う必要があります。

* Managed Cache Service の機能を Azure Cache for Redis にマップする
* キャッシュ プランを選択する
* キャッシュを作成する
* キャッシュ クライアントを構成する
  * Managed Cache Service の構成を削除する
  * StackExchange.Redis NuGet パッケージを使用してキャッシュ クライアントを構成する
* Managed Cache Service のコードを移行する
  * ConnectionMultiplexer クラスを使用してキャッシュに接続する
  * キャッシュのプリミティブ データ型にアクセスする
  * キャッシュ内で .NET オブジェクトを使用する
* ASP.NET のセッション状態と出力キャッシュを Azure Cache for Redis に移行する 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Managed Cache Service の機能を Azure Cache for Redis にマップする
Azure Managed Cache Service と Azure Cache for Redis は似ていますが、一部の機能の実装方法が異なります。 このセクションでは、そのような違いのいくつかについて説明し、Managed Cache Service の機能を Azure Cache for Redis で実装する場合のガイダンスを提供します。

| Managed Cache Service の機能 | Managed Cache Service のサポート | Azure Cache for Redis のサポート |
| --- | --- | --- |
| 名前付きキャッシュ |既定のキャッシュが構成され、Standard および Premium キャッシュ プランでは、必要に応じて最大 9 個の名前付きキャッシュを追加構成できます。 |Azure Cache for Redis には、名前付きキャッシュに似た機能の実装に使用できるデータベースの数を構成できます (既定は 16 個)。 詳細については、「[What are Redis databases? (Redis データベースとは)](cache-faq.md#what-are-redis-databases)」と「[既定の Redis サーバー構成](cache-configure.md#default-redis-server-configuration)」を参照してください。 |
| 高可用性 |Standard および Premium キャッシュ プランでは、キャッシュ内のアイテムの高可用性が提供されます。 アイテムが障害によって失われた場合でも、キャッシュ内のアイテムのバックアップ コピーを使用できます。 セカンダリ キャッシュへの書き込みは同期的に行われます。 |高可用性は Standard および Premium キャッシュ プランで利用でき、2 ノードのプライマリ/レプリカ構成を使用します (Premium キャッシュではシャードごとにプライマリ/レプリカ ペアがあります)。 レプリカへの書き込みは非同期的に行われます。 詳細については、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)に関するページを参照してください。 |
| 通知 |名前付きキャッシュでさまざまなキャッシュ操作が発生したとき、クライアントは非同期の通知を受け取ることができます。 |クライアント アプリケーションは、Redis のパブリッシュ/サブスクライブまたは [キースペース通知](cache-configure.md#keyspace-notifications-advanced-settings) を使用して、同様の通知機能を実現できます。 |
| ローカル キャッシュ |特に高速のアクセスのため、キャッシュされたオブジェクトのコピーをクライアントにローカルに格納します。 |クライアント アプリケーションは、ディクショナリまたは類似のデータ構造を使用してこの機能を実装する必要があります。 |
| 削除ポリシー |None または LRU。 既定のポリシーは LRU です。 |Azure Cache for Redis は、volatile-lru、allkeys-lru、volatile-random、allkeys-random、volatile-ttl、noeviction の各削除ポリシーをサポートします。 既定のポリシーは volatile-lru です。 詳細については、「 [既定の Redis サーバー構成](cache-configure.md#default-redis-server-configuration)」を参照してください。 |
| 有効期限ポリシー |既定の有効期限ポリシーは絶対であり、既定の有効期限は 10 分です。 スライド式ポリシーおよび期限なしポリシーも使用できます。 |既定ではキャッシュ内のアイテムは期限切れしませんが、キャッシュ設定のオーバーロードを使用して書き込みごとに有効期限を構成できます。 |
| リージョンとタグ付け |リージョンは、キャッシュされるアイテムのサブグループです。 また、リージョンでは、タグと呼ばれる追加の説明文を使用してキャッシュされるアイテムに注釈を付けることもできます。 リージョンでは、そのリージョン内のタグ付きアイテムに対する検索操作を実行できます。 リージョン内のすべてのアイテムは、キャッシュ クラスターの 1 つのノードに格納されます。 |Azure Cache for Redis は 1 つのノードで構成される (Redis クラスターが有効になっていない場合) ため、Managed Cache Service のリージョンの概念は適用されません。 Redis はキーを取得するときの検索とワイルドカード操作をサポートするので、説明的なタグをキー名に埋め込み、それを使用して後でアイテムを取得できます。 Redis を使用したタグ付けソリューションの実装の例については、 [Redis でのキャッシュタグの実装](https://stackify.com/implementing-cache-tagging-redis/)に関するページをご覧ください。 |
| シリアル化 |Managed Cache は、NetDataContractSerializer、BinaryFormatter、およびカスタム シリアライザーの使用をサポートします。 既定値は NetDataContractSerializer です。 |キャッシュに格納する前の .NET オブジェクトのシリアル化は、クライアント アプリケーションで行う必要があります。使用するシリアライザーはクライアント アプリケーションの開発者が選択します。 詳細とサンプル コードについては、「 [キャッシュ内で .NET オブジェクトを使用する](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)」を参照してください。 |
| キャッシュ エミュレーター |Managed Cache には、ローカル キャッシュ エミュレーターが用意されています。 |Azure Cache for Redis にはエミュレーターはありませんが、[redis-server.exe の MSOpenTech ビルドをローカルに実行する](cache-faq.md#cache-emulator) ことで、エミュレーターを体験できます。 |

## <a name="choose-a-cache-offering"></a>キャッシュ プランを選択する
Microsoft Azure Cache for Redis は以下のレベルでご利用いただけます。

* **Basic** – 単一ノード。 複数のサイズ、最大 53 GB
* **Standard** – 2 ノード (プライマリ/レプリカ)。 複数のサイズ、最大 53 GB 99.9% の SLA。
* **Premium** – 最大 10 個のシャードがある 2 ノード (プライマリ/レプリカ)。 6 GB から 530 GB までの複数のサイズ。 Standard レベルのすべての機能と、[Redis クラスター](cache-how-to-premium-clustering.md)、[Redis の永続化](cache-how-to-premium-persistence.md)、[Azure Virtual Network](cache-how-to-premium-vnet.md) のサポートを含むその他の機能。 99.9% の SLA。

各レベルは、機能と価格ごとに異なります。 機能については、このガイドで後述します。価格の詳細については、[キャッシュ価格の詳細](https://azure.microsoft.com/pricing/details/cache/)ページをご覧ください。

移行では最初に、前の Managed Cache Service キャッシュのサイズに合ったサイズを選択し、アプリケーションの要件に応じてスケールアップまたはスケールダウンします。 Azure Cache for Redis の適切なサービスの選択に関する詳細については、「[What Azure Cache for Redis offering and size should I use](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)」(どの Azure Cache for Redis サービスとサイズを使用すればよいですか) をご覧ください。

## <a name="create-a-cache"></a>キャッシュを作成する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>キャッシュ クライアントを構成する
キャッシュを作成し、構成したら、次のステップは Managed Cache Service を削除し、Azure Cache for Redis 構成と参照を追加し、キャッシュ クライアントがキャッシュにアクセスできるようにすることです。

* Managed Cache Service の構成を削除する
* StackExchange.Redis NuGet パッケージを使用してキャッシュ クライアントを構成する

### <a name="remove-the-managed-cache-service-configuration"></a>Managed Cache Service の構成を削除する
Azure Cache for Redis 用にクライアント アプリケーションを構成するには、その前に、Managed Cache Service の NuGet パッケージをアンインストールすることによって、既存の Managed Cache Service の構成とアセンブリ参照を削除する必要があります。

Managed Cache Service の NuGet パッケージをアンインストールするには、**ソリューション エクスプローラー**でクライアント プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 **[インストール済みのパッケージ]** ノードを選択し、[Search installed packages (インストール済みパッケージの検索)] ボックスに「**WindowsAzure.Caching**」と入力します。 **[Windows** **Azure Cache**] (または、NuGet パッケージのバージョンによっては **[Windows** **Azure Caching]**) を選択し、**[アンインストール]** をクリックして、**[閉じる]** をクリックします。

![Azure Managed Cache Service NuGet パッケージのアンインストール](./media/cache-migrate-to-redis/IC757666.jpg)

Managed Cache Service の NuGet パッケージをアンインストールすると、クライアント アプリケーションの app.config または web.config の Managed Cache Service アセンブリおよび Managed Cache Service エントリが削除されます。 NuGet パッケージのアンインストール時に、カスタマイズした設定の一部が削除されない場合があるため、web.config または app.config を開き、次の要素が削除されていることを確認します。

`dataCacheClients` エントリが `configSections` 要素から削除されていることを確認します。 `configSections` 要素全体を削除しないようにしてください。`dataCacheClients` エントリが存在する場合、それだけを削除します。

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

`dataCacheClients` セクションが削除されていることを確認します。 `dataCacheClients` セクションは次の例のようになります。

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Managed Cache Service の構成を削除した後は、次のセクションで説明するようにキャッシュ クライアントを構成できます。

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>StackExchange.Redis NuGet パッケージを使用してキャッシュ クライアントを構成する
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Managed Cache Service のコードを移行する
StackExchange.Azure Cache for Redis クライアントの API は、Managed Cache Service に似ています。 このセクションでは相違点の概要を示します。

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>ConnectionMultiplexer クラスを使用してキャッシュに接続する
Managed Cache Service では、キャッシュへの接続は `DataCacheFactory` および `DataCache` クラスによって処理されました。 Azure Cache for Redis では、これらの接続は `ConnectionMultiplexer` クラスによって管理されます。

キャッシュにアクセスするファイルの先頭に、次の using ステートメントを追加します。

```csharp
using StackExchange.Redis
```

この名前空間が解決しない場合は、「[Quickstart: Use Azure Cache for Redis with a .NET application (クイック スタート: .NET アプリケーションと共に Azure Cache for Redis を使用する)](cache-dotnet-how-to-use-azure-redis-cache.md)」の説明のとおりに、StackExchange.Redis NuGet パッケージを追加したことを確認してください。

> [!NOTE]
> StackExchange.Redis クライアントでは .NET Framework 4 以降が必要であることに注意してください。
> 
> 

Azure Cache for Redis インスタンスに接続するには、静的メソッド `ConnectionMultiplexer.Connect` を呼び出して、エンドポイントとキーを渡します。 アプリケーション内の `ConnectionMultiplexer` インスタンスを共有する方法の 1 つに、次の例のように、接続されたインスタンスを返す静的プロパティを設定する方法があります。 この手法は、単一の接続 `ConnectionMultiplexer`　インスタンスを初期化するスレッドセーフな方法を提供します。 この例では、 `abortConnect` が false に設定されており、キャッシュへの接続が確立されていない場合でも呼び出しが成功します。 `ConnectionMultiplexer` の主な機能の 1 つは、ネットワーク問題などの原因が解決されると、キャッシュへの接続が自動的に復元されることです。

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

キャッシュのエンドポイント、キー、およびポートは、ご利用のキャッシュ インスタンスの **[Azure Cache for Redis]** ブレードから取得できます。 詳細については、[Azure Cache for Redis のプロパティ](cache-configure.md#properties)に関するページを参照してください。

接続が確立されたら、`ConnectionMultiplexer.GetDatabase` メソッドを呼び出すことで Azure Cache for Redis データベースへの参照が返されます。 `GetDatabase` メソッドから返されるオブジェクトは、手付かずで受け渡しされる軽量のオブジェクトであり、保存する必要はありません。

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

StackExchange.Redis クライアントは、キャッシュ内のアイテムのアクセスと保存に `RedisKey` および `RedisValue` 型を使用します。 これらの型は文字列などのほとんどのプリミティブ言語型にマップし、多くの場合、直接は使用されません。 Redis String は最も基本的な Redis 値の種類であり、シリアル化されたバイナリ ストリームなどの多くの種類のデータを格納でき、この型を直接使用することはできませんが、名前に `String` を含むメソッドを使用します。 ほとんどのプリミティブ データ型について、コレクションまたはキャッシュ内の他の Redis を格納する場合を除き、 データ型、`StringSet`と`StringGet`メソッドを使用してキャッシュから項目を格納及び取得します。 

`StringSet` 及び `StringGet` は、Managed Cache Service の `Put` 及び `Get` メソッドに似ていますが、.NET オブジェクトを設定し、キャッシュの中に取得する前にまずシリアル化しなければならないという大きな違いがあります。 

`StringGet` を呼び出すと、オブジェクトが存在する場合はそのオブジェクトが返され、存在しない場合は null が返されます。 この場合、必要なデータソースから値を取得してキャッシュ内に格納しておき後で使用することができます。 このパターンは "キャッシュ アサイド パターン" といいます。

キャッシュ内の項目の有効期限を指定するには、`StringSet` の `TimeSpan` パラメーターを使用します。

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Cache for Redis はプリミティブ データ型に加え、.NET オブジェクトに対応していますが、.NET オブジェクトをキャッシュするためには、あらかじめシリアル化しておく必要があります。 このシリアル化はアプリケーション開発者が行わなければなりません。逆にそのことでシリアライザーの選択に幅が生まれ、開発者にとってのメリットとなっています。 詳細とサンプル コードについては、「 [キャッシュ内で .NET オブジェクトを使用する](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)」を参照してください。

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>ASP.NET のセッション状態と出力キャッシュを Azure Cache for Redis に移行する
Azure Cache for Redis には、ASP.NET セッション状態とページ出力キャッシュの両方に対するプロバイダーがあります。 これらのプロバイダーの Managed Cache Service バージョンを使用するアプリケーションを移行するには、まず web.config から既存のセクションを削除した後、Azure Cache for Redis バージョンのプロバイダーを構成します。 Azure Cache for Redis の ASP.NET プロバイダーの使用方法については、「[ASP.NET Session State Provider for Azure Cache for Redis](cache-aspnet-session-state-provider.md)」(Azure Cache for Redis の ASP.NET セッション状態プロバイダー) および「[ASP.NET Output Cache Provider for Azure Cache for Redis](cache-aspnet-output-cache-provider.md)」(Azure Cache for Redis の ASP.NET 出力キャッシュ プロバイダー) を参照してください。

## <a name="next-steps"></a>次の手順
[Azure Cache for Redis ドキュメント](https://azure.microsoft.com/documentation/services/cache/)のチュートリアル、サンプル、ビデオ、およびその他をご確認ください。

