---
title: Azure Cache for Redis のタイムアウトのトラブルシューティング
description: Redis サーバーでの修正プログラムの適用や StackExchange.Redis タイムアウト例外など、Azure Cache for Redis における一般的なタイムアウト問題を解決する方法について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/18/2019
ms.openlocfilehash: bf8b20dadd2fcd78657aa6877e796b645332dd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88213454"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Azure Cache for Redis のタイムアウトのトラブルシューティング

このセクションでは、Azure Cache for Redis に接続するときに発生するタイムアウトの問題のトラブルシューティングについて説明します。

- [Redis サーバーでの修正プログラムの適用](#redis-server-patching)
- [StackExchange.Redis のタイムアウトの例外](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> このガイドのトラブルシューティング手順のいくつかには、Redis コマンドを実行し、さまざまなパフォーマンス メトリックを監視する手順が含まれています。 詳細および手順については、「 [追加情報](#additional-information) 」セクションの記事を参照してください。
>

## <a name="redis-server-patching"></a>Redis サーバーでの修正プログラムの適用

Azure Cache for Redis では、提供されるマネージド サービス機能の一部として、サーバー ソフトウェアが定期的に更新されます。 この[修正プログラム適用](cache-failover.md)アクティビティは、ほとんどはユーザーに認識されずに行われます。 フェールオーバーの間に、Redis サーバー ノードに修正プログラムが適用されていると、これらのノードに接続されている Redis クライアントでは、これらのノード間で接続が切り替えられるため、一時的なタイムアウトが発生する可能性があります。 修正プログラムの適用によりアプリケーションに対して発生する可能性がある副作用、および修正プログラム適用イベントの処理を改善する方法の詳細については、「[フェールオーバーはクライアント アプリケーションにどのような影響を与えますか?](cache-failover.md#how-does-a-failover-affect-my-client-application)」を参照してください。

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis のタイムアウトの例外

StackExchange.Redis は、同期操作のために `synctimeout` という名前の構成設定 (既定値は 5000 ミリ秒) を使用します。 同期呼び出しがこの時間内に完了しない場合、StackExchange.Redis クライアントは、次の例のようなタイムアウト エラーをスローします。

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

このエラー メッセージには、問題の原因と考えられる解決策を示すのに役立つメトリックが含まれます。 次の表には、エラー メッセージのメトリックに関する詳細が含まれています。

| エラー メッセージのメトリック | 詳細 |
| --- | --- |
| inst |最後のタイム スライスで発行されたコマンドは 0 です |
| mgr |ソケット マネージャーが `socket.select` を実行しています。これは、何かを実行する必要があるソケットを示すよう OS に要求していることを示します。 リーダーは、何も実行する必要がないと認識しているため、ネットワークからアクティブに読み取っていません。 |
| queue |合計 73 個の実行中の操作があります。 |
| qu |実行中の操作のうちの 6 個が未送信キューに存在し、まだ発信ネットワークに書き込まれていません。 |
| qs |実行中の操作のうちの 67 個がサーバーに送信されましたが、応答はまだ使用可能になっていません。 応答は `Not yet sent by the server` または `sent by the server but not yet processed by the client.` の場合があります。 |
| qc |実行中の操作のうちの 0 個が応答を受信しましたが、完了ループで待機しているため、まだ完了としてマークされていません。 |
| wr |アクティブなライターが存在します (6 個の未送信要求は無視されていないことを示します)。バイト数/アクティブなライター数 |
| in |アクティブなリーダーはなく、NIC で読み取ることができるバイト数はゼロです。これは、"バイト数/アクティブなリーダー数" で表されます。 |

次の手順を使用して、考えられる根本原因を調査できます。

1. ベスト プラクティスとして、StackExchange.Redis クライアントを使用するときに、次のパターンを使用して接続していることを確認します。

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    詳細については、 [StackExchange.Redis を使用するキャッシュへの接続](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)に関するページを参照してください。

1. サーバーとクライアント アプリケーションが Azure の同じリージョン内に存在することを確認します。 たとえば、キャッシュは米国東部にあるが、クライアントが米国西部にあり、要求が `synctimeout` の間隔内に完了しない場合はタイムアウトになることがあります。あるいは、ローカルの開発用コンピューターからデバッグしている場合はタイムアウトになることがあります。 

    キャッシュとクライアントを同じ Azure リージョン内に配置することを強くお勧めします。 リージョン間呼び出しを含むシナリオの場合は、接続文字列に `synctimeout` プロパティを含めて、`synctimeout` の間隔を既定の 5000 ミリ秒間隔より大きい値に設定する必要があります。 次の例は、Azure Cache for Redis によって提供された StackExchange.Redis 用の接続文字列 (`synctimeout` は 2000 ミリ秒) のスニペットを示しています。

    ```output
    synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. 最新バージョンの [StackExchange.Redis NuGet パッケージ](https://www.nuget.org/packages/StackExchange.Redis/)を使用するようにしてください。 タイムアウトの信頼性を高めるため、コードに示されるバグは常に修正されています。したがって、最新バージョンを使用することが重要です。
1. 要求がサーバーまたはクライアントの帯域幅の制限に制約されている場合は、それらの要求の完了にかかる時間が長くなるため、タイムアウトになることがあります。 タイムアウトの原因がサーバー上のネットワーク帯域幅にあるかどうかを確認するには、「[サーバー側の帯域幅の制限](cache-troubleshoot-server.md#server-side-bandwidth-limitation)」を参照してください。 タイムアウトの原因がクライアントのネットワーク帯域幅にあるかどうかを確認するには、「[クライアント側の帯域幅の制限](cache-troubleshoot-client.md#client-side-bandwidth-limitation)」を参照してください。
1. サーバーまたはクライアントに CPU 制約を適用していますか?

   - クライアントの CPU に制約されているかどうかを確認します。 CPU 使用率が高いと、要求が `synctimeout` の間隔内に処理されず、要求がタイムアウトになることがあります。クライアント サイズを大きくするか、負荷を分散すると、この問題を制御するのに役立ちます。
   - CPU の[キャッシュ パフォーマンス メトリック](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)を監視することにより、サーバー上で CPU 制約が発生しているかどうかを確認します。 Redis に CPU 制約が適用されているときに要求を受信した場合、これらの要求はタイムアウトになることがあります。この状況に対処するために、Premium キャッシュの複数のシャードに負荷を分散させるか、より大きいサイズまたは価格レベルにアップグレードすることができます。 詳細については、「[サーバー側の帯域幅の制限](cache-troubleshoot-server.md#server-side-bandwidth-limitation)」を参照してください。
1. サーバー上での処理に時間がかかるコマンドはありますか? Redis サーバー上での処理に時間がかかる実行時間の長いコマンドがあると、タイムアウトになることがあります。 実行時間の長いコマンドの詳細については、「[実行時間の長いコマンド](cache-troubleshoot-server.md#long-running-commands)」を参照してください。 redis-cli クライアントまたは [Redis コンソール](cache-configure.md#redis-console)を使用して、Azure Cache for Redis インスタンスに接続できます。 次に、[SLOWLOG](https://redis.io/commands/slowlog) コマンドを実行して、予測より遅い要求があるかどうかを確認します。 Redis サーバーと StackExchange.Redis は、少数の大きい要求ではなく、多数の小さい要求用に最適化されています。 データをより小さいチャンクに分割することで、この状態が改善される場合があります。

    redis-cli と stunnel を使用したキャッシュの TLS/SSL エンドポイントへの接続については、[Redis のプレビュー リリース用の ASP.NET セッション状態プロバイダーの発表](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)に関するブログ記事を参照してください。
1. Redis サーバーの負荷が高いとタイムアウトが生じる場合があります。 `Redis Server Load` [キャッシュ パフォーマンス メトリック](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)を監視することで、サーバーの負荷を監視できます。 100 (最大値) のサーバーの負荷は、Redis サーバーが要求を処理しており、ビジー状態であり、アイドル時間がないことを示します。 特定の要求がサーバーを占有しているかどうかを確認するには、前の段落で説明したように、SlowLog コマンドを実行します。 詳細については、「CPU 使用率またはサーバーの負荷が高い」を参照してください。
1. ネットワーク ブリップの原因と思われる、クライアント側のイベントは他にありますか? 共通イベントには、クライアント インスタンスの数のスケールアップ/スケールダウン、新しいバージョンのクライアントのデプロイ、または自動スケールの有効化が含まれます。 当社のテストでは、自動スケールまたはスケールアップ/スケールダウンによって発信ネットワーク接続が数秒間失われる場合があることがわかりました。 StackExchange.Redis コードはこのようなイベントに対応し、再接続します。 再接続中に、キュー内のすべての要求がタイムアウトになることがあります。
1. タイムアウトになったキャッシュへの数個の小さい要求の前に大きい要求がありましたか? エラー メッセージ内のパラメーター `qs` は、クライアントからサーバーに送信されたが、まだ応答を処理していない要求の数を示します。 StackExchange.Redis は単一の TCP 接続を使用し、一度に読み取ることができる応答は 1 つのみであるため、この値が増え続ける可能性があります。 最初の操作がタイムアウトになっても、サーバーとの間のそれ以降のデータの送信は停止されません。 他の要求は、大きい要求が完了するまでブロックされるため、タイムアウトになることがあります。 1 つの解決策は、キャッシュがワークロードに対して十分な大きさであることを確認し、大きい値をより小さいチャンクに分割して、タイムアウトの可能性を最小限に抑えることです。 この他に考えられる解決策は、クライアントで `ConnectionMultiplexer` オブジェクトのプールを使用し、新しい要求の送信時に負荷が最も少ない `ConnectionMultiplexer` を選択することです。 複数の接続オブジェクトにわたって読み込むと、1 つのタイムアウトのために他の要求もタイムアウトになることは防止されます。
1. `RedisSessionStateProvider` を使用している場合は、再試行タイムアウトを正しく設定していることを確認してください。 `retryTimeoutInMilliseconds` は `operationTimeoutInMilliseconds` よりも高くする必要があります。そうしないと、再試行が発生しません。 次の例では、`retryTimeoutInMilliseconds` は 3000 に設定されています。 詳細については、「[ASP.NET Session State Provider for Azure Cache for Redis ](cache-aspnet-session-state-provider.md)」(Azure Cache for Redis の ASP.NET セッション状態プロバイダー) と「[How to use the configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)」(セッション状態プロバイダーと出力キャッシュ プロバイダーの構成パラメーターの使用方法) を参照してください。

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. `Used Memory RSS` と `Used Memory` を[監視](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)して、Azure Cache for Redis サーバーのメモリ使用量を確認します。 削除ポリシーが適用されている場合、Redis は、 `Used_Memory` がキャッシュ サイズに達したときにキーの削除を開始します。 `Used Memory RSS` が `Used memory` よりわずかに上回っているのが理想的です。 この差が大きい場合は、メモリの断片化 (内部または外部) が発生していることを示します。 `Used Memory RSS` が `Used Memory` より小さい場合、キャッシュ メモリの一部がオペレーティング システムにスワップされていることを意味します。 このスワップが発生した場合、大幅に遅延することがあります。 Redis は割り当てがメモリ ページにマップされる方法を制御できないため、`Used Memory RSS` が高いと、多くの場合、メモリ使用量のスパイクが発生します。 Redis サーバーがメモリを解放すると、アロケーターはそのメモリを取得しますが、それをシステムに戻す場合も戻さない場合もあります。 `Used Memory` 値と、オペレーティング システムによってレポートされるメモリの消費量は異なる場合があります。 メモリは Redis によって使用された後、解放された可能性がありますが、システムには戻されていません。 メモリの問題を軽減するために、次の手順を実行できます。

   - システムのメモリの制限に達することがないように、キャッシュをより大きいサイズにアップグレードします。
   - キーに有効期限を設定し、古い値が事前に削除されるようにします。
   - `used_memory_rss` キャッシュ メトリックを監視します。 この値がキャッシュのサイズに近づくと、パフォーマンスの問題が発生し始める可能性があります。 Premium キャッシュを使用している場合は、データを複数のシャードに分散させます。または、より大きいキャッシュ サイズにアップグレードします。

   詳細については、「[Redis サーバーでのメモリ不足](cache-troubleshoot-server.md#memory-pressure-on-redis-server)」を参照してください。

## <a name="additional-information"></a>関連情報

- [Azure Cache for Redis のクライアント側の問題に関するトラブルシューティング](cache-troubleshoot-client.md)
- [Azure Cache for Redis のサーバー側の問題に関するトラブルシューティング](cache-troubleshoot-server.md)
- [キャッシュのベンチマークを実行およびテストする方法](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Azure Cache for Redis を監視する方法](cache-how-to-monitor.md)
