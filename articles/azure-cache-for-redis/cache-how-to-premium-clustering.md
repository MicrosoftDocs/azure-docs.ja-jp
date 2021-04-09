---
title: Redis クラスタリングの構成 - Premium Azure Cache for Redis
description: Premium レベルの Azure Cache for Redis インスタンス用に Redis を作成して管理する方法について説明します
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f1e84c838d310721cba604274388ae2767eb1502
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389673"
---
# <a name="configure-redis-clustering-for-a-premium-azure-cache-for-redis-instance"></a>Premium Azure Cache for Redis インスタンス用の Redis クラスタリングを構成する

Azure Cache for Redis では、 [Redis での実装](https://redis.io/topics/cluster-tutorial)と同じように Redis クラスターが提供されます。 Redis クラスターには、次の利点があります。 

* データセットを複数のノードに自動的に分割する機能。 
* ノードのサブセットで障害が発生したとき、またはクラスターの他の部分と通信できないときに、操作を続行する機能。 
* より多くのスループット: シャードの数を増やすと、スループットは比例して増加します。 
* より多くのメモリ サイズ: シャードの数を増やすと比例的に増加します。  

クラスタリングでは、クラスター化されたキャッシュで使用できる接続の数は増加しません。 Premium キャッシュのサイズ、スループット、帯域幅の詳細については、「[適切なレベルの選択](cache-overview.md#choosing-the-right-tier)」を参照してください。

Azure では、Redis クラスターは、各シャードがプライマリ/レプリカ ペアを持つプライマリ/レプリカ モデルとして提供され、レプリケーションは Azure Cache for Redis によって管理されます。 

## <a name="set-up-clustering"></a>クラスタリングを設定する

クラスタリングは、キャッシュの作成中に **[Azure Cache for Redis の新規作成]** ブレードで有効化されます。 

1. Premium キャッシュを作成するには、[Azure portal](https://portal.azure.com) にサインインし、 **[リソースの作成]** を選択します。 キャッシュは、Azure ポータルだけでなく、Resource Manager テンプレート、PowerShell、または Azure CLI を使用して作成することもできます。 Azure Cache for Redis の作成について詳しくは、[キャッシュの作成](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)に関するページを参照してください。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="リソースの作成。":::
   
2. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="[Azure Cache for Redis] を選択します。":::

3. **[新規 Redis Cache]** ページで、新しい Premium キャッシュの設定を構成します。
   
   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 | 
   | **サブスクリプション** | ドロップダウンでご自身のサブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 | 
   | **リソース グループ** | ドロップダウンでリソース グループを選択するか、 **[新規作成]** を選択して新しいリソース グループ名を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 | 
   | **場所** | ドロップダウンで場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
   | **キャッシュの種類** | ドロップダウンで Premium キャッシュを選択し、Premium 機能を構成します。 詳細については、「[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)」を参照してください。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |

4. **[ネットワーク]** タブを選択するか、ページの下部にある **[ネットワーク]** ボタンをクリックします。

5. **[ネットワーク]** タブで、接続方法を選択します。 Premium キャッシュ インスタンスの場合、パブリック IP アドレスまたはサービス エンドポイント経由で公的に接続することも、プライベート エンドポイントを使用してプライベートに接続することもできます。

6. **[次へ: 詳細]** タブを選択するか、ページの下部にある **[次へ: 詳細]** ボタンをクリックします。

7. Premium キャッシュ インスタンスの **[詳細]** タブで、非 TLS ポート、クラスタリング、データ永続化の設定を構成します。 クラスタリングを有効にするには、 **[有効]** をクリックします。

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering.png" alt-text="クラスタリングの切り替え。":::

    クラスター内に最大 10 個のシャードを作成できます。 **[有効]** をクリックした後に、スライダーを操作するか 1 から 10 の値を入力して **[シャード数]** を設定し、 **[OK]** をクリックします。

    各シャードは Azure によって管理されるプライマリ/レプリカ キャッシュ ペアであり、キャッシュの合計サイズはシャードの数に価格レベルで選択したキャッシュ サイズを掛けることによって計算されます。

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png" alt-text="クラスタリングの切り替えの選択。":::

    キャッシュを作成した後は、クラスター化されていないキャッシュと同じようにアクセスして使用できます。また、Redis はキャッシュのシャード全体にデータを分配します。 診断が[有効](cache-how-to-monitor.md#enable-cache-diagnostics)になっている場合は、シャードごとにメトリックが個別にキャプチャされ、Azure Cache for Redis ブレードに[表示](cache-how-to-monitor.md)できます。 

8. **[次へ: タグ]** タブを選択するか、ページの下部にある **[次へ: タグ]** ボタンをクリックします。

9. 必要に応じて、 **[タグ]** タブで、リソースを分類する場合は名前と値を入力します。 

10. **[Review + create]\(レビュー + 作成\)** を選択します。 [確認および作成] タブが表示され、Azure によって構成が検証されます。

11. 緑色の検証に成功のメッセージが表示された後、 **[作成]** を選択します。

キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。 

> [!NOTE]
> 
> クラスタリングが構成されているときは、クライアント アプリケーションに対していくつか小さな変更が必要です。 詳細については、「 [クラスタリングを使用するためにクライアント アプリケーションを変更する必要がありますか](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

StackExchange.Redis クライアントを使用したクラスタリングの操作でのサンプル コードについては、[Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) サンプルの [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) 部分を参照してください。

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>実行中の Premium キャッシュのクラスター サイズを変更する
クラスタリングが有効になっている実行中の Premium キャッシュのクラスター サイズを変更するには、 **[リソース] メニュー** の **[Cluster Size]\(クラスターのサイズ\)** をクリックします。

![Redis クラスター サイズ][redis-cache-redis-cluster-size]

クラスター サイズを変更するには、スライダーを使用するか、 **[シャード数]** ボックスに 1 ～ 10 の範囲の数値を入力し、 **[OK]** をクリックして保存します。

クラスター サイズを増やすと、スループットとキャッシュの最大サイズが増えます。 クラスター サイズを増やして、クライアントが利用できる最大接続数が増えることはありません。

> [!NOTE]
> クラスターのスケーリングでは、[MIGRATE](https://redis.io/commands/migrate) コマンドが実行されます。このコマンドはコストを要するコマンドであるため、影響を最小限にするために、この操作をオフ ピーク時に実行することを検討してください。 移行プロセス中には、サーバーの負荷が急増します。 クラスターのスケーリングは時間を要する処理であり、必要な時間は、キーの数とこれらのキーに関連付けられている値のサイズによって異なります。
> 
> 

## <a name="clustering-faq"></a>クラスタリングの FAQ

次の一覧は、Azure Redis Cache のクラスタリングに関するよく寄せられる質問への回答です。

* [クラスタリングを使用するためにクライアント アプリケーションを変更する必要がありますか](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [クラスターにはキーはどのように配布されるのですか](#how-are-keys-distributed-in-a-cluster)
* [作成できる最大キャッシュ サイズはどれくらいですか](#what-is-the-largest-cache-size-i-can-create)
* [すべての Redis クライアントがクラスタリングをサポートしますか](#do-all-redis-clients-support-clustering)
* [クラスタリングが有効になっているとき、キャッシュに接続するにはどうすればよいですか](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [キャッシュの個々のシャードに直接接続できますか](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [以前に作成したキャッシュのクラスタリングを構成できますか。](#can-i-configure-clustering-for-a-previously-created-cache)
* [Basic または Standard キャッシュのクラスタリングを構成できますか。](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Redis ASP.NET セッション状態および出力キャッシュ プロバイダーでクラスタリングを使用できますか](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [StackExchange.Redis とクラスタリングを使用すると、MOVE 例外が発生します。どうすればよいですか。](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>クラスタリングを使用するためにクライアント アプリケーションを変更する必要がありますか
* クラスタリングが有効になっている場合は、データベース 0 のみを使用できます。 クライアント アプリケーションで複数のデータベースを使用している状態で、0 以外のデータベースに対して読み取りまたは書き込みを試みると、次の例外がスローされます。 `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  詳細については、「 [Redis Cluster Specification - Implemented subset (Redis クラスターの仕様 - 実装済みのサブセット)](https://redis.io/topics/cluster-spec#implemented-subset)」を参照してください。
* [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)を使用する場合は、1.0.481 以降を使用する必要があります。 クラスタリングが有効になっていないキャッシュに接続するときに使用するものと同じ [エンドポイント、ポート、キー](cache-configure.md#properties) を使用して、キャッシュに接続します。 唯一の違いは、すべての読み取りと書き込みをデータベース 0 に対して実行する必要があることです。
  
  他のクライアントの要件は異なる場合があります。 「 [すべての Redis クライアントがクラスタリングをサポートしますか](#do-all-redis-clients-support-clustering)
* アプリケーションで 1 つのコマンドにバッチ処理された複数のキー操作を使用する場合は、すべてのキーを同じシャードに配置する必要があります。 キーを同じシャードに配置するには、「[クラスターにはキーはどのように配布されるのですか](#how-are-keys-distributed-in-a-cluster)」を参照してください。
* Redis ASP.NET セッション状態プロバイダーを使用する場合は、2.0.1 以降を使用する必要があります。 「 [Redis ASP.NET セッション状態および出力キャッシュ プロバイダーでクラスタリングを使用できますか](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>クラスターにはキーはどのように配布されるのですか
Redis の [キー配布モデル](https://redis.io/topics/cluster-spec#keys-distribution-model) に関するドキュメントによると、キー スペースは 16384 スロットに分割されます。 各キーはハッシュされ、クラスターのノード全体に配布される、これらのスロットのいずれかに割り当てられます。 ハッシュ タグを使用して同じシャードに複数のキーが配置されていることを確認するために、キーのどの部分をハッシュするかを構成することができます。

* ハッシュ タグのあるキー - キーの任意の部分を `{` と `}` で囲むと、キーのその部分のみが、キーのハッシュ スロットを決定するためにハッシュされます。 たとえば、`{key}1`、`{key}2`、`{key}3` という 3 つのキーは、名前の `key` 部分のみがハッシュされるため、同じシャードに配置されます。 キーのハッシュ タグ仕様に関する完全なリストについては、「 [キーのハッシュ タグ](https://redis.io/topics/cluster-spec#keys-hash-tags)」を参照してください。
* ハッシュ タグのないキー - キー名全体がハッシュに使用されます。 そのため、キャッシュのシャード全体で統計的に均等に配布されます。

最高のパフォーマンスとスループットを得るために、キーを均等に配布することをお勧めします。 ハッシュ タグのあるキーを使用する場合、キーが均等に配布されていることを確認するのはアプリケーションの責任です。

詳細については、「[Keys distribution model (キー配布モデル)](https://redis.io/topics/cluster-spec#keys-distribution-model)」、「[Redis Cluster data sharding (Redis クラスターのデータ シャーディング)](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)」、および「[Keys hash tags (キーのハッシュ タグ)](https://redis.io/topics/cluster-spec#keys-hash-tags)」を参照してください。

StackExchange.Redis クライアントを使用した同じシャードでのクラスタリングおよびキーの検索の操作におけるサンプル コードについては、[Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) サンプルの [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) 部分を参照してください。

### <a name="what-is-the-largest-cache-size-i-can-create"></a>作成できる最大キャッシュ サイズはどれくらいですか
Premium の最大キャッシュ サイズは、120 GB です。 最大 10 個のシャードを作成できるので、最大サイズは 1.2 TB です。 さらに大きいサイズが必要な場合は、 [追加を要求](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)できます。 詳細については、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)に関するページを参照してください。

### <a name="do-all-redis-clients-support-clustering"></a>すべての Redis クライアントがクラスタリングをサポートしますか
すべてのクライアントが Redis クラスタリングをサポートしているわけではありません。 使用しているライブラリのドキュメントをチェックして、クラスタリングをサポートするライブラリとバージョンを使用していることを確認してください。 StackExchange.Redis は、その新しいバージョンでクラスタリングをサポートする 1 つのライブラリです。 他のクライアントの詳細については、「[Redis cluster tutorial (Redis クラスター チュートリアル)](https://redis.io/topics/cluster-tutorial)」の「[Playing with the cluster (クラスターの使用)](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster)」を参照してください。 

Redis クラスタリング プロトコルでは、各クライアントがクラスタリング モードで各シャードに直接接続する必要があり、'MOVED' や 'CROSSSLOTS' などの新しいエラー応答も定義されます。 クロススロット マルチキー要求を行っている場合は、クラスター モードのキャッシュでクラスタリングをサポートしないクライアントを使用しようとすると、多数の [MOVED リダイレクト例外](https://redis.io/topics/cluster-spec#moved-redirection)が発生するか、または単にアプリケーションが中断される場合があります。

> [!NOTE]
> StackExchange.Redis をクライアントとして使用する場合は、クラスタリングが正常に動作するように、 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 以降の最新バージョンを使用してください。 move 例外について問題がある場合は、 [move 例外](#move-exceptions) の詳細をご覧ください。
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>クラスタリングが有効になっているとき、キャッシュに接続するにはどうすればよいですか
クラスタリングが有効になっていないキャッシュに接続するときに使うものと同じ[エンドポイント](cache-configure.md#properties)、[ポート](cache-configure.md#properties)、[キー](cache-configure.md#access-keys)を使って、キャッシュに接続できます。 Redis がバックエンドのクラスタリングを管理するので、クライアントから管理する必要はありません。

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>キャッシュの個々のシャードに直接接続できますか
クラスタリング プロトコルでは、クライアントが正しいシャード接続を実行する必要があります。 したがって、クライアントが自動的に、これを正しく実行する必要があります。 つまり、各シャードは、キャッシュ インスタンスと総称される、プライマリ/レプリカ キャッシュ ペアで構成されています。 GitHub で Redis リポジトリの [不安定な](https://redis.io/download) ブランチにある redis-cli ユーティリティを使用して、これらのキャッシュ インスタンスに接続できます。 このバージョンは、 `-c` スイッチ付きで起動した場合、基本的なサポートを実装しています。 詳細については、[https://redis.io](https://redis.io) の「[Redis cluster tutorial](https://redis.io/topics/cluster-tutorial)」 (Redis クラスター チュートリアル) にある「[Playing with the cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster)」 (クラスターの使用) をご覧ください。

TLS 以外の場合は、次のコマンドを使用します。

```bash
Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
...
Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)
```

TLS の場合は、`1300N` を `1500N` に置き換えます。

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>以前に作成したキャッシュのクラスタリングを構成できますか。
はい。 まず、キャッシュが Premium でない場合は、スケーリングしてキャッシュを確実に Premium にしてください。 次に、クラスター構成オプション (クラスターを有効にするオプションを含む) が表示されるようになります。 キャッシュが作成されるか、または初めてクラスタリングを有効にした後、クラスター サイズを変更できます。

   >[!IMPORTANT]
   >クラスタリングを有効すると元には戻せません。 また、クラスタリングが有効であり、かつシャードが 1 つだけのキャッシュは、クラスタリング *なしの* 同じサイズのキャッシュとは動作が *異なります*。

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Basic または Standard キャッシュのクラスタリングを構成できますか。
クラスタリングは、Premium キャッシュでのみ使用できます。

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Redis ASP.NET セッション状態および出力キャッシュ プロバイダーでクラスタリングを使用できますか
* **Redis 出力キャッシュ プロバイダー** - 変更する必要はありません。
* **Redis セッション状態プロバイダー** - クラスタリングを使用するには、[RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 以降を使用する必要があります。そうしないと、例外がスローされます。 これは重大な変更です。詳細については、「[v2.0.0 Breaking Change Details (v2.0.0 の重大な変更の詳細)](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)」を参照してください。

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>StackExchange.Redis とクラスタリングを使用すると、MOVE 例外が発生します。どうすればよいですか。
クラスタリングを使用しているときに StackExchange.Redis を使うと、`MOVE` 例外が発生することがあります。この場合は、[StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) 以降を使用しているかどうかを確認してください。 StackExchange.Redis を使用するための .NET アプリケーションの構成手順については、「[キャッシュ クライアントの構成](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cache for Redis の機能について

* [Azure Cache for Redis Premium サービス レベル](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
