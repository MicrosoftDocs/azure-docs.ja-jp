---
title: Azure Cache for Redis 管理に関してよくあるご質問
description: Azure Cache for Redis の管理に役立つ、よくある質問に対する回答について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537662"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Azure Cache for Redis 管理に関してよくあるご質問
この記事では、Azure Cache for Redis の管理に役立つ、よくある質問に対する回答について説明します。

## <a name="common-questions-and-answers"></a>一般的な質問と回答
このセクションでは、次のよくあるご質問を取り上げます。

* [Redis への接続に非 TLS/SSL ポートを有効にする必要がある状況](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [いくつかの運用上のベスト プラクティスについて](#what-are-some-production-best-practices)
* [一般的な Redis コマンドの使用に関するいくつかの考慮事項](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [キャッシュのベンチマークを実行およびテストする方法](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [ThreadPool 拡大の重要な詳細情報](#important-details-about-threadpool-growth)
* [StackExchange.Redis を使用するときにサーバー GC を有効にしてクライアントでのスループットを向上させる](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [接続のパフォーマンスに関する考慮事項](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Redis への接続に非 TLS/SSL ポートを有効にする必要がある状況
Redis サーバーはネイティブで TLS をサポートしませんが、Azure Cache for Redis ではサポートされます。 Azure Cache for Redis に接続しようとしていて、クライアントが StackExchange.Redis のように TLS をサポートしている場合は、TLS を使用する必要があります。

>[!NOTE]
>既定では、新しい Azure Cache for Redis インスタンスの TLS 以外のポートは無効になっています。 クライアントが TLS をサポートしていない場合は、[Azure Cache for Redis でのキャッシュの構成](cache-configure.md)に関するページの「[アクセス ポート](cache-configure.md#access-ports)」セクションの指示に従って、非 TLS ポートを有効にする必要があります。
>
>

`redis-cli` などの Redis ツールは TLS ポートを使用できません。ただし、`stunnel` などのユーティリティを使用すると、ツールを TLS ポートに安全に接続することができます。詳細については、[Redis のプレビュー リリース用の ASP.NET セッション状態プロバイダーの発表](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)に関するブログ記事を参照してください。

Redis ツールのダウンロードの詳細については、「 [Redis コマンドの実行方法](cache-development-faq.md#how-can-i-run-redis-commands) 」セクションを参照してください。

### <a name="what-are-some-production-best-practices"></a>いくつかの運用上のベスト プラクティスについて
* [StackExchange.Redis のベスト プラクティス](#stackexchangeredis-best-practices)
* [構成と概念](#configuration-and-concepts)
* [パフォーマンス テスト](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis のベスト プラクティス
* `AbortConnect` を "false" に設定してから、ConnectionMultiplexer による自動再接続を待ってください。 [詳細についてはこちらをご覧ください](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md)。
* ConnectionMultiplexer は再利用し、要求ごとに新しく作成しないようにしてください。 [こちら](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)に記載されている `Lazy<ConnectionMultiplexer>` パターンをお勧めします。
* 値が小さいほど Redis のパフォーマンスは向上するため、大きなデータは複数のキーに分割することを検討してください。 [この Redis に関する議論](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)では、100 KB は大きいと見なされています。 値が大きい場合に生じる可能性のある問題の例については、 [こちらの記事](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) を参照してください。
* タイムアウトが起こらないように [ThreadPool の設定](#important-details-about-threadpool-growth) を構成してください。
* connectTimeout については既定の 5 秒以上を使用してください。 この間隔により、ネットワーク ブリップが発生した場合に接続を再確立するための十分な時間が StackExchange.Redis に与えられます。
* 実行中のさまざまな操作に関連するパフォーマンス コストを把握してください。 たとえば、 `KEYS` コマンドは O(n) 操作であるため、使用しないでください。 [redis.io のサイト](https://redis.io/commands/) に、Redis でサポートされる各操作の時間計算量の詳細が記載されています。 各コマンドをクリックして、操作ごとの時間計算量を確認してください。

#### <a name="configuration-and-concepts"></a>構成と概念
* 実稼働システムでは Standard レベルまたは Premium レベルを使用する。 Basic レベルは単一ノード システムであり、データ レプリケーション機能や SLA がありません。 また、C1 以上のキャッシュを使用してください。 通常、C0 キャッシュは単純な開発/テスト シナリオで使用されます。
* Redis は **インメモリ** データ ストアであることに注意してください。 [こちらの記事](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) を参照し、データが失われる可能性のあるシナリオについて把握してください。
* [修正プログラムの適用やフェールオーバーによる](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)接続の中断に対応できるようなシステムを開発する。

#### <a name="performance-testing"></a>パフォーマンス テスト
* 独自のパフォーマンス テストを作成する前に、 `redis-benchmark.exe` を使用して実現可能なスループットを確認してください。 `redis-benchmark` では TLS はサポートされていないため、テストを行うには、[Azure portal で非 TLS ポートを有効にする](cache-configure.md#access-ports)必要があります。 例については、「 [キャッシュのベンチマークを実行およびテストする方法](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* テストに使用するクライアント VM は、Azure Cache for Redis インスタンスと同じリージョンにある必要があります。
* Dv2 VM シリーズはハードウェアが強力であり、最良の結果が得られるため、クライアントにはこれらのシリーズを使用することをお勧めします。
* クライアント VM については、コンピューティング能力と帯域幅がテスト対象のキャッシュと同等以上であるものを選択してください。
* Windows を使用している場合は、クライアント コンピューターで VRSS を有効にしてください。 [詳細についてはこちらをご覧ください](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11))。
* Premium レベルでは、Redis インスタンスが CPU およびネットワークの両方が優れたハードウェアで実行されるため、ネットワーク待機時間およびスループットが改善します。

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>一般的な Redis コマンドの使用に関するいくつかの考慮事項

* 完了するのに時間がかかる特定の Redis コマンドについては、その影響を完全に理解しないまま使用するのは避けてください。 たとえば、運用環境で [KEYS](https://redis.io/commands/keys) コマンドを実行しないでください。 キーの数によっては、復帰に時間がかかる可能性があります。 Redis はシングル スレッド サーバーであり、一度に 1 つずつコマンドを処理します。 KEYS の後に他のコマンドが発行されている場合、それらのコマンドは Redis によって KEYS コマンドが処理されるまで処理されません。 [redis.io のサイト](https://redis.io/commands/) に、Redis でサポートされる各操作の時間計算量の詳細が記載されています。 各コマンドをクリックして、操作ごとの時間計算量を確認してください。
* キー サイズ - 小さなキー/値と大きなキー/値のどちらを使用するか。 これはシナリオによって異なります。 ご利用のシナリオで、より大きなキーが必要な場合は、ConnectionTimeout を調整してから、値を再度試して再試行ロジックを調整することができます。 Redis サーバーの観点からは、値が小さいほど、パフォーマンスは向上します。
* これらの考慮事項は、サイズの大きな値を Redis に格納できないという意味ではありません。次の点を考慮する必要があります。 待機時間は長くなります。 サイズの大きなデータ セットとサイズの小さなデータ セットがある場合は、前の「[StackExchange.Redis 構成オプションについて](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do)」に説明したように、それぞれ異なるタイムアウト値と再試行回数が構成された複数の ConnectionMultiplexer インスタンスを使用できます。

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>キャッシュのベンチマークを実行およびテストする方法
* [キャッシュ診断の有効化](cache-how-to-monitor.md#enable-cache-diagnostics)によってキャッシュの正常性を[監視](cache-how-to-monitor.md)できるようにします。 Azure Portal でメトリックを表示できますが、任意のツールを使用して、メトリックを [ダウンロードして確認](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) することも可能です。
* redis-benchmark.exe を使用して Redis サーバーのロード テストを実行できます。
* ロード テスト用のクライアントと Azure Cache for Redis が同じリージョン内にあることを確認します。
* redis-cli.exe を使用し、INFO コマンドを使用してキャッシュを監視します。
* 負荷が高いことが原因でメモリの断片化が発生している場合は、キャッシュのサイズをスケール アップする必要があります。
* Redis ツールのダウンロードの詳細については、「 [Redis コマンドの実行方法](cache-development-faq.md#how-can-i-run-redis-commands) 」セクションを参照してください。

以下のコマンドは、redis-benchmark.exe の使用例です。 正確な結果を得るために、以下のコマンドはキャッシュと同じリージョンにある VM で実行してください。

* 1 k ペイロードを使用してパイプライン SET 要求をテストする

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* 1 k ペイロードを使用してパイプライン GET 要求をテストする。

>[!NOTE]
> まず上の SET テストを実行し、キャッシュにデータを格納してください。
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>ThreadPool 拡大の重要な詳細情報
CLR ThreadPool には、"Worker" スレッドと "I/O Completion Port" (IOCP) スレッドの 2 種類があります。

* `Task.Run(…)` メソッドや `ThreadPool.QueueUserWorkItem(…)` メソッドの処理などには、Worker スレッドが使用されます。 これらのスレッドは、バック グラウンド スレッドで作業が発生する必要がある場合に、CLR でさまざまなコンポーネントによっても使用されます。
* IOCP スレッドは、ネットワークからの読み取りの場合など、非同期 IO が発生する場合に使用されます。

スレッド プールは、各種のスレッドについて「最小」設定に達するまで、新しい worker スレッドまたは I/O 完了スレッドをオンデマンドで (スロットルなしで) 提供します。 既定では、スレッドの最小数はシステム上のプロセッサの数に設定されます。

既存の (ビジー) スレッドの数がスレッドの "最小" 数に達すると、ThreadPool は新しいスレッドを挿入する速度を、500 ミリ秒ごとに 1 スレッドへとスロットルします。 通常、ご利用のシステムで、IOCP スレッドを必要とする作業のバーストが取得された場合、その作業は高速に処理されます。 ただし、作業のバーストが構成済みの「最小」設定を超えた場合は、次の 2 つの状態のうちどちらかが発生するまで ThreadPool が待機するので、多少の遅延が生じます。

* 既存のスレッドの 1 つが空き状態になり、作業を処理する。
* 既存のスレッドが 500 ミリ秒間空いた状態にならないと、新しいスレッドが作成されます。

基本的に、これは、ビジー スレッド数が最小スレッド数より大きい場合、ネットワーク トラフィックがアプリケーションによって処理される前に 500 ミリ秒の遅延が生じる可能性があることを意味しています。 また、既存のスレッドが (私の記憶によると) 15 秒を超えてアイドル状態になると、そのスレッドはクリーンアップされ、拡大と縮小のこのサイクルが繰り返されることがあります。

StackExchange.Redis (ビルド 1.0.450 以降) からのエラー メッセージの例を見れば、ThreadPool の統計情報が出力されていることがわかります (IOCP と WORKER に関する下記の詳細を参照)。

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

前の例では、IOCP スレッドについて、6 つのビジー スレッドが存在し、システムは 4 つの最小スレッドを許容するように構成されていることがわかります。 この場合、6 > 4 であることから、クライアントは 500 ミリ秒の遅延を 2 回検出している可能性があります。

IOCP スレッドまたは WORKER スレッドの拡大がスロットルされた場合、StackExchange.Redis がタイムアウトになる可能性があることに注意してください。

#### <a name="recommendation"></a>推奨

この情報に基づき、顧客が IOCP スレッドと WORKER スレッドの最小構成値を既定値よりも大きく設定することを強くお勧めします。 あるアプリケーションでは適切な値であっても別のアプリケーションで高すぎたり低すぎたりする可能性があるため、これをどのような値にする必要があるかについて画一的なガイダンスを提供することはできません。 この設定は複雑なアプリケーションの他の部分のパフォーマンスにも影響を与える可能性があるので、各顧客は特定のニーズに合わせてこの設定を調整する必要があります。 適切な値として、まず 200 または 300 に設定し、テストして必要に応じて調整します。

この設定を構成する方法

* この設定は、`global.asax.cs` 内の [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) メソッドを使用してプログラムによって変更することをお勧めします。 次に例を示します。

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > このメソッドによって指定された値はグローバル設定であり、AppDomain 全体に影響を与えます。 たとえば、4 コア マシンをお持ちの場合で、実行時の *minWorkerThreads* および *minIoThreads* を CPU あたり 50 に設定したい場合は、**ThreadPool.SetMinThreads(200, 200)** を使用します。

* 最小スレッド数の設定は、通常、`%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` にある `Machine.config` 内の `<processModel>` 構成要素の下にある [*minIoThreads* または *minWorkerThreads* 構成設定](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100))を使用して指定することもできます。 **この方法で最小スレッド数を設定する方法は、一般的はにお勧めできません。これはシステム全体の設定だからです。**

  > [!NOTE]
  > この構成要素で指定される値は、 "*コアごと*" の設定となります。 たとえば、4 コア マシンをお持ちの場合で、実行時の *minIoThreads* 設定を 200 にしたい場合は、`<processModel minIoThreads="50"/>` を使用します。
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>StackExchange.Redis を使用するときにサーバー GC を有効にしてクライアントでのスループットを向上させる
StackExchange.Redis を使用するときにサーバー GC を有効にすると、クライアントが最適化され、パフォーマンスとスループットを向上させることができます。 サーバー GC とそれを有効にする方法の詳細については、次の記事を参照してください。

* [サーバー GC を有効にするには](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [ガベージ コレクションの基礎](/dotnet/standard/garbage-collection/fundamentals)
* [ガベージ コレクションとパフォーマンス](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>接続のパフォーマンスに関する考慮事項

各価格レベルには、クライアント接続、メモリ、および帯域幅についてさまざまな制限があります。 各キャッシュのサイズが特定の接続数 *まで* 許容される一方で、Redis への各接続はそれにオーバーヘッドが関連付けられています。 このようなオーバーヘッドの例には、TLS/SSL 暗号化の結果としての CPU とメモリの使用量があります。 指定したキャッシュ サイズの最大接続数の上限は、負荷が低いキャッシュを想定しています。 接続オーバーヘッドからの読み込みに *加えて*、クライアントの操作からの読み込みがシステムの容量を超える場合、現在のキャッシュ サイズが接続数の上限を超えていない場合でも、キャッシュ容量の問題が発生する可能性があります。

各レベルの異なる接続制限について詳しくは、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)についてのページを参照してください。 接続と他の既定の構成について詳しくは、「[既定の Redis サーバー構成](cache-configure.md#default-redis-server-configuration)」をご覧ください。

## <a name="next-steps"></a>次のステップ

その他の [Azure Cache for Redis のよくあるご質問](cache-faq.md)について。