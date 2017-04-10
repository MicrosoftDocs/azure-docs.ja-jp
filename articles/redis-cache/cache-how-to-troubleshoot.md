---
title: "Azure Redis Cache のトラブルシューティング方法 | Microsoft Docs"
description: "Azure Redis Cache の一般的な問題を解決する方法について説明します。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 74fd40d7cd2ce5aecf364ba8ead4e6dfcbf6ed54


---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Azure Redis Cache のトラブルシューティング方法
この記事では、次のカテゴリの Azure Redis Cache の問題をトラブルシューティングする場合のガイダンスを提供します。

* [クライアント側のトラブルシューティング](#client-side-troubleshooting) - このセクションでは、Azure Redis Cache に接続されているアプリケーションが原因で発生した問題を特定し、解決するためのガイドラインを示します。
* [サーバー側のトラブルシューティング](#server-side-troubleshooting) - このセクションでは、Azure Redis Cache サーバー側で発生した問題を特定し、解決するためのガイドラインを示します。
* [StackExchange.Redis のタイムアウトの例外](#stackexchangeredis-timeout-exceptions) -このセクションでは、StackExchange.Redis クライアントの使用時に発生した問題のトラブルシューティングに関する情報を提供します。

> [!NOTE]
> このガイドのトラブルシューティング手順のいくつかには、Redis コマンドを実行し、さまざまなパフォーマンス メトリックを監視する手順が含まれています。 詳細および手順については、「 [追加情報](#additional-information) 」セクションの記事を参照してください。
> 
> 

## <a name="client-side-troubleshooting"></a>クライアント側のトラブルシューティング
このセクションでは、クライアント アプリケーションの状態が原因で発生する問題のトラブルシューティングについて説明します。

* [クライアントのメモリ不足](#memory-pressure-on-the-client)
* [トラフィックのバースト](#burst-of-traffic)
* [クライアントでの高い CPU 使用率](#high-client-cpu-usage)
* [クライアント側の帯域幅の超過](#client-side-bandwidth-exceeded)
* [大きい要求/応答サイズ](#large-requestresponse-size)
* [Redis のデータが正常ではない](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>クライアントのメモリ不足
#### <a name="problem"></a>問題点
クライアント コンピューターのメモリー不足により、あらゆる種類のパフォーマンス問題が発生します。問題が発生すると、Redis インスタンスによって遅延なく送信されたデータの処理に遅延が生じる場合があります。 メモリ不足が発生すると、通常、システムはディスク上の仮想メモリに物理メモリのデータをページングする必要があります。 この *ページ フォールト* により、システムの処理速度が大幅に低下します。

#### <a name="measurement"></a>測定
1. コンピューターでのメモリ使用量を監視して、使用可能なメモリを超過しないようにします。 
2. `Page Faults/Sec` パフォーマンス カウンターを監視します。 ほとんどのシステムは通常の操作時でもページ フォールトが発生する場合があるため、タイムアウトに対応するこのページ フォールト パフォーマンス カウンターが急激に増えていないかを監視してください。

#### <a name="resolution"></a>解決策
クライアントを、より多いメモリのより大きいクライアント VM サイズにアップグレードするか、メモリ使用パターンを詳しく調べ、メモリの消費量を減らします。

### <a name="burst-of-traffic"></a>トラフィックのバースト
#### <a name="problem"></a>問題点
`ThreadPool` の設定が適切でないトラフィックのバーストが原因で、Redis Server から既に送信されていてもクライアント側ではまだ使用されていないデータの処理に遅延が発生することがあります。

#### <a name="measurement"></a>測定
[このような](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)コードを使用して、`ThreadPool` 統計が時間の経過と共にどのように変化するかを監視します。 StackExchange.Redis から `TimeoutException` メッセージを確認することもできます。 例を以下に示します。

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

上記のメッセージには、次のようないくつかの注目すべき問題が示されています。

1. `IOCP` セクションと `WORKER` セクションに、`Busy` 値が `Min` 値より大きいことが示されていることに注目してください。 これは、 `ThreadPool` 設定を調整する必要があることを意味します。
2. `in: 64221`にも注目してください。 これは、64211 バイトがカーネル ソケット レイヤーで受信されたが、アプリケーション (StackExchange.Redis など) ではまだ読み取られていないことを示します。 これは、通常、サーバーからの送信の場合と同じくらいすばやくアプリケーションがネットワークからデータを読み取らないことを意味します。

#### <a name="resolution"></a>解決策
スレッド プールがバースト時にすばやくスケールアップするように、 [ThreadPool 設定](https://gist.github.com/JonCole/e65411214030f0d823cb) を構成します。

### <a name="high-client-cpu-usage"></a>クライアントでの高い CPU 使用率
#### <a name="problem"></a>問題点
クライアントでの高い CPU 使用率は、システムが実行要求された作業に対応できないことを示します。 これは、Redis が短時間で応答を送信した場合でも、クライアントが Redis からの応答を適切なタイミングで処理できない可能性があることを意味します。

#### <a name="measurement"></a>測定
Azure ポータル、または関連するパフォーマンス カウンターを介してシステム全体の CPU 使用率を監視します。 システム全体の CPU が高くなると同時に 1 つのプロセスの CPU 使用率が低くなる可能性があるため、 *プロセス* CPU を監視しないように注意してください。 タイムアウトに対応する CPU 使用率が急激に増えていないか監視します。 CPU が高くなると、「[トラフィックのバースト](#burst-of-traffic)」セクションに示されている `TimeoutException` エラー メッセージに高い値の `in: XXX` が表示される場合もあります。

> [!NOTE]
> StackExchange.Redis 1.1.603 以降では、`TimeoutException` エラー メッセージに `local-cpu` メトリックが含まれます。 最新バージョンの [StackExchange.Redis NuGet パッケージ](https://www.nuget.org/packages/StackExchange.Redis/)を使用するようにしてください。 タイムアウトの信頼性を高めるため、コードに示されるバグは常に修正されています。したがって、最新バージョンを使用することが重要です。
> 
> 

#### <a name="resolution"></a>解決策
より多い CPU 容量のより大きい VM サイズにアップグレードするか、CPU が急増した原因を調べます。 

### <a name="client-side-bandwidth-exceeded"></a>クライアント側の帯域幅の超過
#### <a name="problem"></a>問題点
異なるサイズのクライアント コンピューターでは、それぞれ使用できるネットワーク帯域幅に制限があります。 クライアントで使用可能な帯域幅が超過した場合、サーバーから送信されたデータはクライアント側ですぐには処理されません。 これにより、タイムアウトが発生する場合があります。

#### <a name="measurement"></a>測定
[このような](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)コードを使用して、帯域幅の使用量が時間の経過と共にどのように変化しているかを監視します。 (Azure Web サイトのような) アクセス許可が制限された一部の環境では、このコードが正常に実行されない場合があることに注意してください。

#### <a name="resolution"></a>解決策
クライアント VM のサイズを増やすか、ネットワーク帯域幅の消費を減らします。

### <a name="large-requestresponse-size"></a>大きい要求/応答サイズ
#### <a name="problem"></a>問題点
大きい要求/応答が原因でタイムアウトが発生することがあります。 たとえば、クライアントで構成されているタイムアウト値が 1 秒であるとします。 アプリケーションは同時に 2 つのキー ('A' と 'B' など) を (同じ物理ネットワーク接続を使用して) 要求します。 ほとんどのクライアントでは要求の "パイプライン処理" がサポートされるため、'A' と 'B' の両方の要求は、応答を待つことなく順にサーバーにネットワーク上で送信されます。 サーバーは同じ順序で応答を返します。 'A' の応答がある程度大きい場合、後続の要求がタイムアウトになることがあります。 

このシナリオを以下の例で説明します。 このシナリオでは、要求 'A' と 'B' はすばやく送信され、サーバーは応答 'A' と 'B' の送信をすばやく開始しますが、データの転送時間が原因で、サーバーがすばやく応答したとしても、他の要求の後方に取り残された 'B' はタイムアウトになります。

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>測定
これを測定するのは困難です。 基本的には、クライアント コードをインストルメント化して、大きい要求と応答を追跡する必要があります。 

#### <a name="resolution"></a>解決策
1. Redis は、少数の大きい値ではなく、多数の小さい値用に最適化されています。 データをより小さい関連値に分割することをお勧めします。 値をより小さくすることをお勧めする理由の詳細については、「[What is the ideal value size range for redis?Is 100KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)」(Redis に最適な値のサイズ範囲は何ですか? 100 KB では大きすぎますか?) という投稿を参照してください。
2. VM (クライアントおよび Redis Cache Server 用) のサイズを増やし、帯域幅容量を大きくして、より大きい応答のデータ転送時間を短縮します。 サーバーのみ、またはクライアントのみの帯域幅を増やすだけでは十分でない場合があることに注意してください。 帯域幅使用量を測定し、現在使用している VM のサイズの容量と比較します。
3. 使用する `ConnectionMultiplexer` オブジェクトと、さまざまな接続に対するラウンドロビン要求の数を増やします。

### <a name="what-happened-to-my-data-in-redis"></a>Redis のデータが正常ではない
#### <a name="problem"></a>問題点
Azure Redis Cache インスタンスにあるはずの特定のデータがないように見えます。

#### <a name="resolution"></a>解決策
考えられる原因と解決策については、「 [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) 」 (Redis のデータが正常ではない) を参照してください。

## <a name="server-side-troubleshooting"></a>サーバー側のトラブルシューティング
このセクションでは、キャッシュ サーバーの状態が原因で発生する問題のトラブルシューティングについて説明します。

* [サーバーのメモリ不足](#memory-pressure-on-the-server)
* [CPU 使用率またはサーバーの負荷が高い](#high-cpu-usage-server-load)
* [サーバー側の帯域幅の超過](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>サーバーのメモリ不足
#### <a name="problem"></a>問題点
サーバー側のメモリ不足により、要求処理を遅らせる可能性があるあらゆる種類のパフォーマンス問題が発生します。 メモリ不足が発生すると、通常、システムはディスク上の仮想メモリに物理メモリのデータをページングする必要があります。 この *ページ フォールト* により、システムの処理速度が大幅に低下します。 このメモリ不足の原因として考えられるものをいくつか以下に示します。 

1. キャッシュがデータでいっぱいになった。 
2. Redis でメモリの断片化が多く発生している。この原因は、ほとんどの場合、大きいオブジェクトを格納したことによるものです (Redis は小さいオブジェクト用に最適化されています。詳細については、「[What is the ideal value size range for redis? Is 100KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)」 (Redis に最適な値のサイズ範囲は何ですか? 100 KB では大きすぎますか?) という投稿を参照してください)。 

#### <a name="measurement"></a>測定
Redis は 2 つのメトリックを表示します。これらは、この問題の特定に役立つ場合があります。 1 つは `used_memory`、もう 1 つは `used_memory_rss` です。 [これらのメトリック](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)は Azure Portal または [Redis INFO](http://redis.io/commands/info) コマンドを使用して確認できます。

#### <a name="resolution"></a>解決策
メモリ使用量を正常な状態に保つために、実行可能なものとして考えられる変更内容をいくつか以下に示します。

1. [メモリ ポリシーを構成](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) し、キーに有効期限を設定します。 ただし、断片化が発生している場合は、これだけでは十分でない可能性があることに注意してください。
2. [maxmemory-reserved 値を構成](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) します。
3. 大きいキャッシュ オブジェクトをより小さい関連オブジェクトに分割します。
4. [スケーリング](cache-how-to-scale.md) します。
5. [Redis クラスターが有効な Premium キャッシュ](cache-how-to-premium-clustering.md)を使用している場合は、[シャードの数を増やす](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache)ことができます。

### <a name="high-cpu-usage--server-load"></a>CPU 使用率またはサーバーの負荷が高い
#### <a name="problem"></a>問題点
高い CPU 使用率は、Redis が短時間で応答を送信したとしても、クライアントが Redis からの応答を適切なタイミングで処理できない可能性があることを意味している場合があります。

#### <a name="measurement"></a>測定
Azure ポータル、または関連するパフォーマンス カウンターを介してシステム全体の CPU 使用率を監視します。 システム全体の CPU が高くなると同時に 1 つのプロセスの CPU 使用率が低くなる可能性があるため、 *プロセス* CPU を監視しないように注意してください。 タイムアウトに対応する CPU 使用率が急激に増えていないか監視します。

#### <a name="resolution"></a>解決策
[スケーリング](cache-how-to-scale.md) するか、CPU が急激に増えた原因を調べます。 

### <a name="server-side-bandwidth-exceeded"></a>サーバー側の帯域幅の超過
#### <a name="problem"></a>問題点
異なるサイズのキャッシュ インスタンスでは、それぞれ使用できるネットワーク帯域幅に制限があります。 サーバーで使用可能な帯域幅を超過すると、データはすばやくクライアントに送信されません。 これにより、タイムアウトが発生する場合があります。

#### <a name="measurement"></a>測定
指定したレポート期間中にキャッシュから読み取られた、メガバイト単位での 1 秒あたりのデータ量 (MB/秒) である、 `Cache Read` メトリックを監視できます。 この値は、このキャッシュで使用されるネットワーク帯域幅に対応しています。 サーバー側のネットワーク帯域幅の制限に対してアラートを設定する場合は、この `Cache Read` カウンターを使用してアラートを作成できます。 キャッシュのさまざまな価格レベルとサイズで観測された帯域幅の制限値について、 [こちらの表](cache-faq.md#cache-performance) を参照し、測定値と表に示されている値を比較してください。

#### <a name="resolution"></a>解決策
価格レベルとキャッシュ サイズで観測された最大帯域幅に常に近い場合は、ガイドとして[この表](cache-faq.md#cache-performance)の値を使用し、ネットワーク帯域幅がより大きい価格レベルまたはサイズに[スケーリング](cache-how-to-scale.md)することを検討してください。

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis のタイムアウトの例外
StackExchange.Redis では、同期操作に `synctimeout` という名前の構成設定 (既定値は 1,000 ミリ秒) を使用します。 同期呼び出しが規定時間内に完了しなかった場合、StackExchange.Redis クライアントは、以下の例のようなタイムアウト エラーをスローします。

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


このエラー メッセージには、問題の原因と考えられる解決策を示すのに役立つメトリックが含まれます。 次の表には、エラー メッセージのメトリックに関する詳細が含まれています。

| エラー メッセージのメトリック | 詳細 |
| --- | --- |
| inst |最後のタイム スライスで発行されたコマンドは 0 です。 |
| mgr |ソケット マネージャーが `socket.select` を実行しています。これは、何らかのアクションを実行する必要があるソケットを示すよう OS に要求していることを意味します。基本的には、実行すべきことがあっても認識されないため、リーダーがネットワークから積極的に読み取ることはありません。 |
| キュー |合計 73 個の実行中の操作があります。 |
| qu |実行中の操作のうち 6 個は未送信キューにあり、発信ネットワークにまだ書き込まれていません。 |
| qs |実行中の操作のうち 67 個はサーバーに送信されていますが、まだ応答されていません。 応答は `Not yet sent by the server` または `sent by the server but not yet processed by the client.` の場合があります。 |
| qc |実行中の操作のうち 0 個が返信されているようですが、完了ループで待機しているため、まだ完了のマークは付けられていません。 |
| wr |アクティブなライターがあります (6 個の未送信要求は無視されていないことを意味します)。これは、"バイト数/アクティブなライター数" で表されます。 |
| という名前で、 |アクティブなリーダーはなく、NIC で読み取ることができるバイト数はゼロです。これは、"バイト数/アクティブなリーダー数" で表されます。 |

### <a name="steps-to-investigate"></a>調査手順
1. ベスト プラクティスとして、StackExchange.Redis クライアントの使用時に接続する場合に以下のパターンを使用していることを確認します。

    ```c#
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
    ````

    詳細については、 [StackExchange.Redis を使用するキャッシュへの接続](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)に関するページを参照してください。

1. Azure Redis Cache とクライアント アプリケーションが、Azure の同じリージョンにあることを確認します。 たとえば、キャッシュは米国東部にあるが、クライアントが米国西部にあり、要求が `synctimeout` 間隔内に完了しない場合はタイムアウトになることがあります。また、ローカル開発用コンピューターからのデバッグ時にタイムアウトになることがあります。 
   
    キャッシュとクライアントを同じ Azure リージョン内に配置することを強くお勧めします。 リージョン間呼び出しを含むシナリオの場合、接続文字列に `synctimeout` プロパティを含めることで、`synctimeout` 間隔を既定値の 1000 ミリ秒間隔より大きい値に設定する必要があります。 次の例は、 `synctimeout` を 2000 ミリ秒に設定した StackExchange.Redis キャッシュ接続文字列のスニペットを示しています。
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. 最新バージョンの [StackExchange.Redis NuGet パッケージ](https://www.nuget.org/packages/StackExchange.Redis/)を使用するようにしてください。 タイムアウトの信頼性を高めるため、コードに示されるバグは常に修正されています。したがって、最新バージョンを使用することが重要です。
3. サーバーまたはクライアントに帯域幅制限を適用している場合、要求が完了するまで時間がかかるため、タイムアウトになります。 タイムアウトの原因がサーバーのネットワーク帯域幅によるものかどうかを確認する場合は、「 [サーバー側の帯域幅の超過](#server-side-bandwidth-exceeded)」を参照してください。 タイムアウトの原因がクライアントのネットワーク帯域幅によるものかどうかを確認する場合は、「 [クライアント側の帯域幅の超過](#client-side-bandwidth-exceeded)」を参照してください。
4. サーバーまたはクライアントに CPU 制約を適用していますか?
   
   * クライアントに CPU 制約を適用しているかどうかを確認します。制約している場合、`synctimeout` 間隔内で要求が処理されないために、タイムアウトになることがあります。 クライアント サイズを大きくするか、負荷を分散すると、これを制御するのに役立ちます。 
   * `CPU` [キャッシュ パフォーマンス メトリック](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)を監視して、サーバーに CPU 制約を適用しているかどうかを確認します。 Redis に CPU 制約が適用されている場合、送信された要求はタイムアウトになることがあります。 これに対処するために、Premium キャッシュの複数のシャードに負荷を分散させるか、より大きいサイズまたは価格レベルにアップグレードすることができます。 詳細については、「 [サーバー側の帯域幅の超過](#server-side-bandwidth-exceeded)」を参照してください。
5. サーバー上での処理に時間がかかるコマンドはありますか? Redis サーバーの処理に長い時間がかかるコマンドの実行時間が長いと、タイムアウトが発生する場合があります。 実行時間の長いコマンドの例として、キーの数が多い `mget`、`keys *`、適切に記述されていない lua スクリプトなどがあります。 redis-cli クライアントを使用して Azure Redis Cache インスタンスに接続するか、[Redis コンソール](cache-configure.md#redis-console)を使用して [SlowLog](http://redis.io/commands/slowlog) コマンドを実行し、予想より時間がかかっている要求がないかを確認できます。 Redis サーバーと StackExchange.Redis は、少数の大きい要求ではなく、多数の小さい要求用に最適化されています。 データをより小さいチャンクに分割することで、この状態が改善される場合があります。 
   
    redis-cli と stunnel を使用する Azure Redis Cache SSL エンドポイントへの接続については、「 [Redis のプレビュー リリースの ASP.NET セッション状態プロバイダーの通知](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) 」というブログ投稿を参照してください。 詳細については、「 [SlowLog](http://redis.io/commands/slowlog)」を参照してください。
6. Redis サーバーの負荷が高いとタイムアウトが生じる場合があります。 `Redis Server Load` [キャッシュ パフォーマンス メトリック](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)を監視することで、サーバーの負荷を監視できます。 100 (最大値) のサーバーの負荷は、Redis サーバーが要求を処理しており、ビジー状態であり、アイドル時間がないことを示します。 特定の要求がサーバーを占有しているかどうかを確認するには、前の段落で説明したように、SlowLog コマンドを実行します。 詳細については、「 [CPU 使用率またはサーバーの負荷が高い](#high-cpu-usage-server-load)」を参照してください。
7. ネットワーク ブリップの原因と思われる、クライアント側のイベントは他にありますか?  クライアント (Web、worker ロールまたは IaaS VM) で、クライアント インスタンス数のスケール アップまたはダウンなどのイベントが存在するかどうか、あるいは新しいバージョンのクライアントまたは自動スケールのデプロイが有効になっているかどうかを確認してください。弊社のテストでは、自動スケールまたはスケール アップ/ダウンが原因で送信ネットワーク接続が数秒間失われたことが判明しました。 StackExchange.Redis コードはこのようなイベントに対応し、再接続します。 この再接続時間中に、キュー内の要求がタイムアウトになる場合があります。
8. タイムアウトになった Redis Cache に対するいくつかの小さい要求の前に大きい要求がありましたか?  エラー メッセージの `qs` パラメーターは、クライアントからサーバーに送信されたが、まだ応答が処理されていない要求の数を示します。 StackExchange.Redis は単一の TCP 接続を使用し、一度に読み取ることができる応答は 1 つのみであるため、この値が増え続ける可能性があります。 最初の操作がタイムアウトになった場合でも、サーバーに対するデータの送受信は続行され、これが完了するまで他の要求はブロックされるため、タイムアウトになります。 1 つの解決策は、キャッシュがワークロードに対して十分な大きさであることを確認し、大きい値をより小さいチャンクに分割して、タイムアウトの可能性を最小限に抑えることです。 この他に考えられる解決策は、クライアントで `ConnectionMultiplexer` オブジェクトのプールを使用し、新しい要求の送信時に負荷が最も少ない `ConnectionMultiplexer` を選択することです。 そうすれば、1 つのタイムアウトが原因で他の要求もタイムアウトになることはありません。
9. `RedisSessionStateprovider` を使用している場合には、再試行のタイムアウトを正しく設定していることを確認してください。 `retrytimeoutInMilliseconds` は `operationTimeoutinMilliseonds` よりも高くする必要があります。そうしないと、再試行が発生しません。 次の例では、`retrytimeoutInMilliseconds` は 3000 に設定されています。 詳細については、「[Azure Redis Cache の ASP.NET セッション状態プロバイダー](cache-aspnet-session-state-provider.md)」と「[How to use the configuration parameters of Session State Provider and Output Cache Provider (セッション状態プロバイダーと出力キャッシュ プロバイダーの構成パラメーターの使用方法)](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)」を参照してください。

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


1. `Used Memory RSS` と `Used Memory` を[監視](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)して、Azure Redis Cache サーバーのメモリ使用量を確認します。 削除ポリシーが適用されている場合、Redis は、 `Used_Memory` がキャッシュ サイズに達したときにキーの削除を開始します。 `Used Memory RSS` が `Used memory` よりわずかに上回っているのが理想的です。 この差が大きい場合、(内部または外部で) メモリの断片化が発生していることを意味します。 `Used Memory RSS` が `Used Memory` より小さい場合、キャッシュ メモリの一部がオペレーティング システムにスワップされていることを意味します。 このような場合、大幅に遅延することがあります。 Redis では割り当てがメモリ ページにマップされる方法が制御されないため、 `Used Memory RSS` が高いと、多くの場合、メモリ使用量が急激に増加します。 Redis がメモリを解放すると、メモリはアロケーターに戻されますが、アロケーターがシステムにメモリを戻すとは限りません。 `Used Memory` 値と、オペレーティング システムによってレポートされるメモリの消費量は異なる場合があります。 ファクト メモリは Redis で使用された後、解放されても、システムに戻されていないことが原因である可能性があります。 メモリの問題を軽減するために、次の手順を実行できます。
   
   * キャッシュをより大きいサイズにアップグレードし、システムのメモリ制限に達しないようにします。
   * キーに有効期限を設定し、古い値が事前に削除されるようにします。
   * `used_memory_rss` キャッシュ メトリックを監視します。 この値がキャッシュのサイズに近づくと、パフォーマンスの問題が発生しやすくなります。 Premium キャッシュを使用する場合は、データを複数のシャードに分散させるか、より大きいキャッシュ サイズにアップグレードします。
   
   詳細については、「 [サーバーのメモリ不足](#memory-pressure-on-the-server)」を参照してください。

## <a name="additional-information"></a>追加情報
* [Redis Cache のサービス内容と適切なサイズの選択](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [キャッシュのベンチマークを実行およびテストする方法](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Redis コマンドの実行方法](cache-faq.md#how-can-i-run-redis-commands)
* [Azure Redis Cache の監視方法](cache-how-to-monitor.md)




<!--HONumber=Nov16_HO3-->


