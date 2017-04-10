---
title: "Managed Cache Service アプリケーションの Redis への移行 - Azure | Microsoft Docs"
description: "Managed Cache Service アプリケーションおよび In-Role Cache アプリケーションを Azure Redis Cache に移行する方法の説明"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 03/08/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: af5e181ce254fefe55c847d9988dd8245c75e864
ms.lasthandoff: 03/09/2017


---
# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Managed Cache Service から Azure Redis Cache への移行
Azure Managed Cache Service を使用するアプリケーションの Azure Redis Cache への移行は、キャッシュ アプリケーションで使用されている Managed Cache Service の機能によっては、最小限の変更をアプリケーションに対して行うだけで実現できます。 API はまったく同じではありませんがよく似ており、Managed Cache Service を使用してキャッシュにアクセスする既存コードの多くは最小限の変更で再利用できます。 このトピックでは、Managed Cache Service アプリケーションを Azure Redis Cache を使用するように移行するために必要な構成とアプリケーションの変更を行う方法、および Azure Redis Cache の機能を使用して Managed Cache Service キャッシュの機能を実装する方法について説明します。

## <a name="migration-steps"></a>移行の手順
Managed Cache Service アプリケーションを Azure Redis Cache を使用するように移行するには、以下の手順で行う必要があります。

* Managed Cache Service の機能を Azure Redis Cache にマップする
* キャッシュ プランを選択する
* キャッシュを作成する
* キャッシュ クライアントを構成する
  * Managed Cache Service の構成を削除する
  * StackExchange.Redis NuGet パッケージを使用してキャッシュ クライアントを構成する
* Managed Cache Service のコードを移行する
  * ConnectionMultiplexer クラスを使用してキャッシュに接続する
  * キャッシュのプリミティブ データ型にアクセスする
  * キャッシュ内で .NET オブジェクトを使用する
* ASP.NET のセッション状態と出力キャッシュを Azure Redis Cache に移行する 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Managed Cache Service の機能を Azure Redis Cache にマップする
Azure Managed Cache Service と Azure Redis Cache は似ていますが、一部の機能の実装方法が異なります。 このセクションでは、そのような違いのいくつかについて説明し、Managed Cache Service の機能を Azure Redis Cache で実装する場合のガイダンスを提供します。

| Managed Cache Service の機能 | Managed Cache Service のサポート | Azure Redis Cache のサポート |
| --- | --- | --- |
| 名前付きキャッシュ |既定のキャッシュが構成され、Standard および Premium キャッシュ プランでは、必要に応じて最大&9; 個の名前付きキャッシュを追加構成できます。 |Azure Redis キャッシュには、名前付きキャッシュに似た機能の実装に使用できるデータベースがあります (データベースの数は既定で 16 個ですが、構成できます)。 詳細については、「[What are Redis databases? (Redis データベースとは)](cache-faq.md#what-are-redis-databases)」と「[既定の Redis サーバー構成](cache-configure.md#default-redis-server-configuration)」を参照してください。 |
| 高可用性 |Standard および Premium キャッシュ プランでは、キャッシュ内のアイテムの高可用性が提供されます。 アイテムが障害によって失われた場合でも、キャッシュ内のアイテムのバックアップ コピーを使用できます。 セカンダリ キャッシュへの書き込みは同期的に行われます。 |高可用性は Standard および Premium キャッシュ プランで利用でき、2 ノードのプライマリ/レプリカ構成を使用します (Premium キャッシュではシャードごとにプライマリ/レプリカ ペアがあります)。 レプリカへの書き込みは非同期的に行われます。 詳細については、 [Azure Redis Cache の価格に関するページ](https://azure.microsoft.com/pricing/details/cache/)を参照してください。 |
| 通知 |名前付きキャッシュでさまざまなキャッシュ操作が発生したとき、クライアントは非同期の通知を受け取ることができます。 |クライアント アプリケーションは、Redis のパブリッシュ/サブスクライブまたは [キースペース通知](cache-configure.md#keyspace-notifications-advanced-settings) を使用して、同様の通知機能を実現できます。 |
| ローカル キャッシュ |特に高速のアクセスのため、キャッシュされたオブジェクトのコピーをクライアントにローカルに格納します。 |クライアント アプリケーションは、ディクショナリまたは類似のデータ構造を使用してこの機能を実装する必要があります。 |
| 削除ポリシー |None または LRU。 既定のポリシーは LRU です。 |Azure Redis Cache は、volatile-lru、allkeys-lru、volatile-random、allkeys-random、volatile-ttl、noeviction の各削除ポリシーをサポートします。 既定のポリシーは volatile-lru です。 詳細については、「 [既定の Redis サーバー構成](cache-configure.md#default-redis-server-configuration)」を参照してください。 |
| 有効期限ポリシー |既定の有効期限ポリシーは絶対であり、既定の有効期限は&10; 分です。 スライド式ポリシーおよび期限なしポリシーも使用できます。 |既定ではキャッシュ内のアイテムは期限切れしませんが、キャッシュ設定のオーバーロードを使用して書き込みごとに有効期限を構成できます。 詳細については、「 [オブジェクトをキャッシュに追加する、キャッシュから削除する](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache)」を参照してください。 |
| リージョンとタグ付け |リージョンは、キャッシュされるアイテムのサブグループです。 また、リージョンでは、タグと呼ばれる追加の説明文を使用してキャッシュされるアイテムに注釈を付けることもできます。 リージョンでは、そのリージョン内のタグ付きアイテムに対する検索操作を実行できます。 リージョン内のすべてのアイテムは、キャッシュ クラスターの&1; つのノードに格納されます。 |Redis キャッシュは&1; つのノードで構成され (Redis クラスターが有効になっていない場合)、Managed Cache Service のリージョンの概念は適用されません。 Redis はキーを取得するときの検索とワイルドカード操作をサポートするので、説明的なタグをキー名に埋め込み、それを使用して後でアイテムを取得できます。 Redis を使用したタグ付けソリューションの実装の例については、 [Redis でのキャッシュタグの実装](http://stackify.com/implementing-cache-tagging-redis/)に関するページをご覧ください。 |
| シリアル化 |Managed Cache は、NetDataContractSerializer、BinaryFormatter、およびカスタム シリアライザーの使用をサポートします。 既定値は NetDataContractSerializer です。 |キャッシュに格納する前の .NET オブジェクトのシリアル化は、クライアント アプリケーションで行う必要があります。使用するシリアライザーはクライアント アプリケーションの開発者が選択します。 詳細とサンプル コードについては、「 [キャッシュ内で .NET オブジェクトを使用する](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)」を参照してください。 |
| キャッシュ エミュレーター |Managed Cache には、ローカル キャッシュ エミュレーターが用意されています。 |Azure Redis Cache にはエミュレーターはありませんが、 [redis-server.exe の MSOpenTech ビルドをローカルに実行する](cache-faq.md#cache-emulator) ことで、エミュレーターを体験できます。 |

## <a name="choose-a-cache-offering"></a>キャッシュ プランを選択する
Microsoft Azure Redis Cache には、次のレベルがあります。

* **Basic** – 単一ノード。 複数のサイズ、最大 53 GB
* **Standard** –&2; ノード (プライマリ/レプリカ)。 複数のサイズ、最大 53 GB 99.9% の SLA。
* **Premium** – 最大 10 個のシャードがある 2 ノード (プライマリ/レプリカ)。 6 GB から 530 GB までの複数のサイズ (詳細はお問い合わせください)。 Standard レベルのすべての機能と、[Redis クラスター](cache-how-to-premium-clustering.md)、[Redis の永続化](cache-how-to-premium-persistence.md)、[Azure Virtual Network](cache-how-to-premium-vnet.md) のサポートを含むその他の機能。 99.9% の SLA。

各レベルは、機能と価格ごとに異なります。 機能については、このガイドで後述します。価格の詳細については、[キャッシュ価格の詳細](https://azure.microsoft.com/pricing/details/cache/)ページをご覧ください。

移行では最初に、前の Managed Cache Service キャッシュのサイズに合ったサイズを選択し、アプリケーションの要件に応じてスケールアップまたはスケールダウンします。 Azure Redis Cache の適切なプランの選択に関する詳細なガイダンスについては、「 [Redis Cache のサービス内容と適切なサイズの選択](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)」をご覧ください。

## <a name="create-a-cache"></a>キャッシュを作成する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>キャッシュ クライアントを構成する
キャッシュを作成して構成した後は、Managed Cache Service の構成を削除し、Azure Redis Cache の構成と参照を追加して、キャッシュ クライアントがキャッシュにアクセスできるようにします。

* Managed Cache Service の構成を削除する
* StackExchange.Redis NuGet パッケージを使用してキャッシュ クライアントを構成する

### <a name="remove-the-managed-cache-service-configuration"></a>Managed Cache Service の構成を削除する
Azure Redis Cache 用にクライアント アプリケーションを構成するには、その前に、Managed Cache Service の NuGet パッケージをアンインストールすることによって、既存の Managed Cache Service の構成とアセンブリ参照を削除する必要があります。

Managed Cache Service の NuGet パッケージをアンインストールするには、**ソリューション エクスプローラー**でクライアント プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 **[インストール済みのパッケージ]** ノードを選択し、[Search installed packages (インストール済みパッケージの検索)] ボックスに「**WindowsAzure.Caching**」と入力します。 **[Windows** **Azure Cache**] (または、NuGet パッケージのバージョンによっては **[Windows** **Azure Caching]**) を選択し、**[アンインストール]** をクリックして、**[閉じる]** をクリックします。

![Azure Managed Cache Service NuGet パッケージのアンインストール](./media/cache-migrate-to-redis/IC757666.jpg)

Managed Cache Service の NuGet パッケージをアンインストールすると、クライアント アプリケーションの app.config または web.config の Managed Cache Service アセンブリおよび Managed Cache Service エントリが削除されます。 NuGet パッケージをアンインストールしてもカスタマイズした設定は削除されない場合があるので、web.config または app.config を開き、次の要素が完全に削除されていることを確認します。

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
StackExchange.Redis キャッシュ クライアントの API は、Managed Cache Service に似ています。 このセクションでは相違点の概要を示します。

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>ConnectionMultiplexer クラスを使用してキャッシュに接続する
Managed Cache Service では、キャッシュへの接続は `DataCacheFactory` および `DataCache` クラスによって処理されました。 Azure Redis Cache では、これらの接続は `ConnectionMultiplexer` クラスによって管理されます。

キャッシュにアクセスするファイルの先頭に、次の using ステートメントを追加します。

```c#
using StackExchange.Redis
```

この名前空間が解決しない場合は、「 [キャッシュ クライアントの構成](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)」で説明されているように StackExchange.Redis NuGet パッケージを追加したことを確認してください。

> [!NOTE]
> StackExchange.Redis クライアントでは .NET Framework 4 以降が必要であることに注意してください。
> 
> 

Azure Redis Cache インスタンスに接続するには、静的メソッド `ConnectionMultiplexer.Connect` を呼び出して、エンドポイントとキーを渡します。 アプリケーション内の `ConnectionMultiplexer` インスタンスを共有する方法の&1; つに、次の例のように、接続されたインスタンスを返す静的プロパティを設定する方法があります。 これにより、接続された&1; つの `ConnectionMultiplexer` インスタンスだけがスレッドセーフな方法で初期化されます。 この例では、 `abortConnect` が false に設定されており、キャッシュへの接続が確立されていない場合でも呼び出しが成功します。 `ConnectionMultiplexer` の主な機能の&1; つは、ネットワーク問題などの原因が解決されると、キャッシュへの接続が自動的に復元されることです。

```c#
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

キャッシュのエンドポイント、キー、ポートは、ご利用のキャッシュ インスタンスの **[Redis Cache]** ブレードから取得できます。 詳細については、 [Redis Cache のプロパティ](cache-configure.md#properties)に関するセクションを参照してください。

接続が確立されたら、 `ConnectionMultiplexer.GetDatabase` メソッドを呼び出して Redis Cache データベースへの参照を取得します。 `GetDatabase` メソッドから返されるオブジェクトは、手付かずで受け渡しされる軽量のオブジェクトであり、保存する必要はありません。

```c#
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

StackExchange.Redis クライアントは、キャッシュ内のアイテムのアクセスと保存に `RedisKey` および `RedisValue` 型を使用します。 これらの型は文字列などのほとんどのプリミティブ言語型にマップし、多くの場合、直接は使用されません。 Redis String は最も基本的な Redis 値の種類であり、シリアル化されたバイナリ ストリームなどの多くの種類のデータを格納でき、この型を直接使用することはできませんが、名前に `String` を含むメソッドを使用します。 コレクションまたは他の Redis データ型をキャッシュに格納していない場合、ほとんどのプリミティブ データ型について、キャッシュのアイテムの格納と取得には `StringSet` および `StringGet` メソッドを使用します。 

`StringSet` および `StringGet` は Managed Cache Service の `Put` および `Get` メソッドととてもよく似ていますが、大きな違いの&1; つは、キャッシュの .NET オブジェクトを設定および取得する前にシリアル化する必要があることです。 

`StringGet` を呼び出すと、オブジェクトが存在する場合はそのオブジェクトが返され、存在しない場合は null が返されます。 その場合、目的のデータ ソースから値を取得してキャッシュに格納しておき、後で使用することができます。 これを "キャッシュ アサイド パターン" といいます。

キャッシュ内の項目の有効期限を指定するには、`StringSet` の `TimeSpan` パラメーターを使用します。

```c#
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Redis Cache はプリミティブ データ型に加え、.NET オブジェクトに対応していますが、.NET オブジェクトをキャッシュするためには、あらかじめシリアル化しておく必要があります。 これは、アプリケーション開発者が行う必要があります。 これにより、開発者はシリアライザーを柔軟に選択できます。 詳細とサンプル コードについては、「 [キャッシュ内で .NET オブジェクトを使用する](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)」を参照してください。

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>ASP.NET のセッション状態と出力キャッシュを Azure Redis Cache に移行する
Azure Redis Cache には、ASP.NET セッション状態とページ出力キャッシュの両方に対するプロバイダーがあります。 これらのプロバイダーの Managed Cache Service バージョンを使用するアプリケーションを移行するには、まず web.config から既存のセクションを削除した後、Azure Redis Cache バージョンのプロバイダーを構成します。 Azure Redis Cache ASP.NET プロバイダーの使用方法については、「[Azure Redis Cache の ASP.NET セッション状態プロバイダー](cache-aspnet-session-state-provider.md)」および「[Azure Redis Cache の ASP.NET 出力キャッシュ プロバイダー](cache-aspnet-output-cache-provider.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[Azure Redis Cache ドキュメント](https://azure.microsoft.com/documentation/services/cache/) のチュートリアル、サンプル、ビデオ、その他をご覧ください。


