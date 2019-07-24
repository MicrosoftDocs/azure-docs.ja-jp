---
title: Azure Cache for Redis のトラブルシューティング方法 | Microsoft Docs
description: Azure Cache for Redis の一般的な問題を解決する方法について説明します。
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527168"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Azure Cache for Redis のトラブルシューティング方法

この記事は、Azure Cache for Redis インスタンスへの接続時に発生する可能性のあるさまざまなカテゴリの問題のトラブルシューティングに役立ちます。

- [クライアント側のトラブルシューティング](#client-side-troubleshooting)は、キャッシュに接続しているアプリケーションでの問題を識別して解決するのに役立ちます。
- [サーバー側のトラブルシューティング](#server-side-troubleshooting)は、Azure Cache for Redis の側で発生する問題を識別して解決するのに役立ちます。
- [データ損失のトラブルシューティング](#data-loss-troubleshooting)は、キーが予測されるが、キャッシュ内に見つからないインシデントを識別して解決するのに役立ちます。
- [StackExchange.Redis のタイムアウトの例外](#stackexchangeredis-timeout-exceptions)は、StackExchange.Redis ライブラリでの問題のトラブルシューティングに関する具体的なガイダンスを提供します。

> [!NOTE]
> このガイドのトラブルシューティング手順のいくつかには、Redis コマンドを実行し、さまざまなパフォーマンス メトリックを監視する手順が含まれています。 詳細および手順については、「 [追加情報](#additional-information) 」セクションの記事を参照してください。
>
>

## <a name="client-side-troubleshooting"></a>クライアント側のトラブルシューティング

このセクションでは、クライアント アプリケーションの状態が原因で発生する問題のトラブルシューティングについて説明します。

- [クライアントのメモリ不足](#memory-pressure-on-the-client)
- [トラフィックのバースト](#burst-of-traffic)
- [クライアントでの高い CPU 使用率](#high-client-cpu-usage)
- [クライアント側の帯域幅の超過](#client-side-bandwidth-exceeded)
- [大きい要求/応答サイズ](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>クライアントのメモリ不足

クライアント コンピューターでのメモリ不足は、キャッシュからの応答の処理を遅延させる可能性のあるすべての種類のパフォーマンスの問題につながります。 メモリ不足になると、システムはデータをディスクにページングする可能性があります。 この _ページ フォールト_ により、システムの処理速度が大幅に低下します。

クライアントでのメモリ不足を検出するには:

- コンピューターでのメモリ使用量を監視して、使用可能なメモリを超えることがないようにします。
- クライアントの `Page Faults/Sec` パフォーマンス カウンターを監視します。 通常の動作中、ほとんどのシステムでいくつかのページ フォールトが発生します。 要求タイムアウトに対応するページ フォールトのスパイクは、メモリ不足を示している場合があります。

クライアントでの深刻なメモリ不足は、次のいくつかの方法で緩和できます。

- メモリの使用パターンを詳しく調査して、クライアントでのメモリ使用量を削減します。
- クライアント VM をメモリ量の多いより大きいサイズにアップグレードします。

### <a name="burst-of-traffic"></a>トラフィックのバースト

`ThreadPool` の設定が適切でないトラフィックのバーストが原因で、Redis Server から既に送信されていてもクライアント側ではまだ使用されていないデータの処理に遅延が発生することがあります。

[`ThreadPoolLogger` の例](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)を使用して、`ThreadPool` 統計が時間の経過と共にどのように変化するかを監視します。 さらに調査するには、次のように StackExchange.Redis の `TimeoutException` メッセージを使用できます。

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

前の例外には、次のような興味深いいくつかの問題が存在します。

- `IOCP` セクションと `WORKER` セクションに、`Busy` 値が `Min` 値より大きいことが示されていることに注目してください。 この違いは、`ThreadPool` 設定に調整が必要なことを示します。
- `in: 64221`にも注目してください。 この値は、64,211 バイトがクライアントのカーネル ソケット レイヤーで受信されたが、まだアプリケーションによって読み取られていないことを示します。 この違いは、通常、アプリケーション (StackExchange.Redis など) がネットワークからデータを読み取る速度がサーバーのデータ送信速度より遅いことを示します。

バースト シナリオのもとでスレッド プールがすばやくスケールアップするように [`ThreadPool` 設定を構成](https://gist.github.com/JonCole/e65411214030f0d823cb)できます。

### <a name="high-client-cpu-usage"></a>クライアントでの高い CPU 使用率

クライアントでの高い CPU 使用率は、実行を依頼されている作業にシステムがついていけないことを示します。 キャッシュがすばやく応答を送信したとしても、クライアントは適切なタイミングでその応答を処理できない可能性があります。

Azure Portal で使用可能なメトリックか、またはコンピューター上のパフォーマンス カウンターを使用して、クライアントのシステム全体での CPU 使用率を監視します。 1 つのプロセスの CPU 使用率は低くても、システム全体では高くなる場合があるため、*プロセス* CPU を監視しないように注意してください。 タイムアウトに対応する CPU 使用率が急激に増えていないか監視します。 CPU 使用率が高いと、「[トラフィックのバースト](#burst-of-traffic)」セクションで説明されているように、`TimeoutException` エラー メッセージ内の `in: XXX` の値も高くなる可能性があります。

> [!NOTE]
> StackExchange.Redis 1.1.603 以降では、`TimeoutException` エラー メッセージに `local-cpu` メトリックが含まれます。 最新バージョンの [StackExchange.Redis NuGet パッケージ](https://www.nuget.org/packages/StackExchange.Redis/)を使用するようにしてください。 タイムアウトの信頼性を高めるため、コードに示されるバグは常に修正されています。したがって、最新バージョンを使用することが重要です。
>
>

クライアントの高い CPU 使用率を緩和するには:

- CPU スパイクの原因を調査します。
- クライアントを CPU 容量の多いより大きい VM サイズにアップグレードします。

### <a name="client-side-bandwidth-exceeded"></a>クライアント側の帯域幅の超過

クライアント マシンには、そのアーキテクチャに応じて、それぞれ使用できるネットワーク帯域幅に制限がある場合があります。 クライアントがネットワーク容量を過負荷にすることで使用可能な帯域幅を超えている場合、データは、クライアント側でサーバーから送信される速度より速くは処理されません。 この状況により、タイムアウトが発生する場合があります。

[`BandwidthLogger` の例](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)を使用して、帯域幅の使用量が時間の経過と共にどのように変化するかを監視します。 (Azure Web サイトのような) アクセス許可が制限された一部の環境では、このコードが正常に実行されない場合があります。

緩和するには、ネットワーク帯域幅の消費を削減するか、またはクライアント VM サイズをネットワーク容量の多いサイズに増やします。

### <a name="large-requestresponse-size"></a>大きい要求/応答サイズ

大きい要求/応答が原因でタイムアウトが発生することがあります。 例として、クライアントで構成されているタイムアウト値が 1 秒であるとします。 アプリケーションは同時に 2 つのキー ("A" と "B" など) を (同じ物理ネットワーク接続を使用して) 要求します。 ほとんどのクライアントでは、要求 'A' と要求 'B' の両方がそれぞれの応答を待たずに次々に送信される、要求の "パイプライン処理" がサポートされます。 サーバーは同じ順序で応答を返します。 応答 'A' が大きい場合、その応答は、以降の要求のためのタイムアウトのほとんどを消費する可能性があります。

次の例では、要求 'A' と 'B' がすばやくサーバーに送信されます。 サーバーは、すばやく応答 'A' と 'B' を送信し始めます。 データ転送時間のため、サーバーがすばやく応答したとしても、応答 'B' は応答 'A' が終了するまで待つ必要があります。

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

この要求/応答を測定するのは困難です。 大きい要求や応答を追跡するように、クライアント コードをインストルメント化する必要があります。

大きい応答サイズの解決策はさまざまですが、次のものが含まれます。

1. アプリケーションを少数の大きい値ではなく、多数の小さい値用に最適化します。
    - データをより小さい関連値に分割することをお勧めします。
    - より小さい値が推奨される理由の詳細については、[Redis に最適な値のサイズ範囲は何ですか? 100 KB では大きすぎますか?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) に関する投稿を参照してください。
1. より高い帯域幅機能を使用できるように VM のサイズを増やします。
    - クライアントまたはサーバー VM の帯域幅を増やすと、より大きい応答のデータ転送時間が削減される可能性があります。
    - 両方のコンピューターの現在のネットワーク使用量を現在の VM サイズの制限と比較します。 サーバーのみ、またはクライアントのみの帯域幅を増やすだけでは十分でない可能性があります。
1. アプリケーションが使用する接続オブジェクトの数を増やします。
    - ラウンド ロビン方式を使用して、さまざまな接続オブジェクト経由で要求を発行します。

## <a name="server-side-troubleshooting"></a>サーバー側のトラブルシューティング

このセクションでは、キャッシュ サーバーの状態が原因で発生する問題のトラブルシューティングについて説明します。

- [サーバーのメモリ不足](#memory-pressure-on-the-server)
- [CPU 使用率またはサーバーの負荷が高い](#high-cpu-usage--server-load)
- [サーバー側の帯域幅の超過](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>サーバーのメモリ不足

サーバー側のメモリ不足により、要求処理を遅らせる可能性があるあらゆる種類のパフォーマンス問題が発生します。 メモリ不足になると、システムはデータをディスクにページングする可能性があります。 この _ページ フォールト_ により、システムの処理速度が大幅に低下します。 このメモリ不足の原因として考えられるものをいくつか以下に示します。

- キャッシュに、その最大容量に近いデータが格納されている。
- Redis でメモリの断片化が大量に発生している。 Redis は小さいオブジェクト用に最適化されているため、この断片化は、大きいオブジェクトを格納することによって最も頻繁に発生します。

Redis は、[INFO](https://redis.io/commands/info) コマンドを通して、この問題を識別するために役立つ "used_memory" と "used_memory_rss" という 2 つの統計を公開しています。 ポータルを使用して[これらのメトリックを表示](cache-how-to-monitor.md#view-metrics-with-azure-monitor)できます。

メモリ使用量を正常な状態に保つために実行できる、可能性のあるいくつかの変更を次に示します。

- [メモリ ポリシーを構成](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) し、キーに有効期限を設定します。 断片化が発生している場合、このポリシーでは十分でない可能性があります。
- [maxmemory-reserved 値を構成](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) します。 詳細については、後で説明する追加の[メモリ予約に関する考慮事項](#considerations-for-memory-reservations)を参照してください。
- 大きいキャッシュ オブジェクトをより小さい関連オブジェクトに分割します。
- 潜在的な影響について早期に通知される、使用済みメモリなどのメトリックに関する[アラートを作成](cache-how-to-monitor.md#alerts)します。
- メモリ容量の多いより大きいキャッシュ サイズに[スケーリング](cache-how-to-scale.md)します。

#### <a name="considerations-for-memory-reservations"></a>メモリ予約に関する考慮事項

メモリ予約値 (maxmemory-reserved など) を更新すると、キャッシュ パフォーマンスに影響を与える場合があります。 49 GB のデータが格納されている 53 GB キャッシュがあるとします。 予約値を 8 GB に変更すると、システムの最大使用可能メモリが 45 GB に削除されます。 _used_memory_ または _used_memory_rss_ の値が 45 GB より大きい場合、システムは _used_memory_ と _used_memory_rss_ の両方が 45 GB 未満になるまでデータを削除する可能性があります。 削除することによってサーバーの負荷やメモリの断片化が増える可能性もあります。

### <a name="high-cpu-usage--server-load"></a>CPU 使用率またはサーバーの負荷が高い

高いサーバーの負荷または CPU 使用率は、サーバーが適切なタイミングで要求を処理できないことを示します。 サーバーは応答が遅くなり、要求レートについていけない可能性があります。

CPU またはサーバーの負荷などの[メトリックを監視](cache-how-to-monitor.md#view-metrics-with-azure-monitor)します。 タイムアウトに対応する CPU 使用率が急激に増えていないか監視します。

高いサーバーの負荷を軽減するために実行できるいくつかの変更を次に示します。

- [コストの高いコマンド](#expensive-commands)の実行や深刻なメモリ不足によるページ フォールトなどの、CPU スパイクの原因を調査します。
- 潜在的な影響について早期に通知される、CPU または サーバーの負荷などのメトリックに関する[アラートを作成](cache-how-to-monitor.md#alerts)します。
- CPU 容量の多いより大きいキャッシュ サイズに[スケーリング](cache-how-to-scale.md)します。

#### <a name="expensive-commands"></a>コストの高いコマンド

すべての Redis コマンドが均等に作成されているわけではありません。実行コストはコマンドによって異なります。 [Redis コマンドのドキュメント](https://redis.io/commands)は、各コマンドの時間複雑度を示しています。 これらのコマンドのパフォーマンスへの影響を把握するために、キャッシュで実行しているコマンドを確認することをお勧めします。 たとえば、[KEYS](https://redis.io/commands/keys) コマンドは多くの場合、それが O(N) 操作であることを認識せずに使用されます。 [SCAN](https://redis.io/commands/scan) を使用して KEYS を回避することにより CPU スパイクを削減できます。

[SLOWLOG](https://redis.io/commands/slowlog) コマンドを使用すると、サーバーに対して実行されているコストの高いコマンドを測定できます。

### <a name="server-side-bandwidth-exceeded"></a>サーバー側の帯域幅の超過

キャッシュ サイズが違えば、ネットワーク帯域幅容量も異なります。 サーバーが使用可能な帯域幅を超えている場合、データはすばやくクライアントに送信されません。 サーバーが十分な速さでデータをクライアントにプッシュできないため、クライアント要求はタイムアウトする可能性があります。

"キャッシュの読み取り" および "キャッシュの書き込み" メトリックを使用すると、サーバー側の帯域幅がどれだけ使用されているかを確認できます。 ポータルで[これらのメトリックを表示](cache-how-to-monitor.md#view-metrics-with-azure-monitor)できます。

ネットワーク帯域幅の使用量が最大容量に近い状況を緩和するには:

- ネットワーク要求を削減するようにクライアント呼び出し動作を変更します。
- 潜在的な影響について早期に通知される、キャッシュの読み取りやキャッシュの書き込みなどのメトリックに関する[アラートを作成](cache-how-to-monitor.md#alerts)します。
- ネットワーク帯域幅容量の多いより大きいキャッシュ サイズに[スケーリング](cache-how-to-scale.md)します。

## <a name="data-loss-troubleshooting"></a>データ損失のトラブルシューティング

Azure Cache for Redis インスタンスにあるはずの特定のデータがないように見えます。

考えられる原因と解決策については、「 [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) 」 (Redis のデータが正常ではない) を参照してください。

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis のタイムアウトの例外

StackExchange.Redis は、同期操作に `synctimeout` という名前の構成設定 (既定値は 1000 ミリ秒) を使用します。 同期呼び出しがこの時間内に完了しない場合、StackExchange.Redis クライアントは、次の例のようなタイムアウト エラーをスローします。

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

このエラー メッセージには、問題の原因と考えられる解決策を示すのに役立つメトリックが含まれます。 次の表には、エラー メッセージのメトリックに関する詳細が含まれています。

| エラー メッセージのメトリック | 詳細 |
| --- | --- |
| inst |最後のタイム スライスで発行されたコマンドは 0 です |
| mgr |ソケット マネージャーが `socket.select` を実行しています。これは、何かを実行する必要があるソケットを示すよう OS に要求していることを示します。 リーダーは、何も実行する必要がないと認識しているため、ネットワークからアクティブに読み取っていません。 |
| キュー |合計 73 個の実行中の操作があります。 |
| qu |実行中の操作のうちの 6 個が未送信キューに存在し、まだ発信ネットワークに書き込まれていません。 |
| qs |実行中の操作のうちの 67 個がサーバーに送信されましたが、応答はまだ使用可能になっていません。 応答は `Not yet sent by the server` または `sent by the server but not yet processed by the client.` の場合があります。 |
| qc |実行中の操作のうちの 0 個が応答を受信しましたが、完了ループで待機しているため、まだ完了としてマークされていません。 |
| wr |アクティブなライターが存在します (6 個の未送信要求は無視されていないことを示します)。バイト数/アクティブなライター数 |
| in |アクティブなリーダーはなく、NIC で読み取ることができるバイト数はゼロです。これは、"バイト数/アクティブなリーダー数" で表されます。 |

### <a name="steps-to-investigate"></a>調査手順

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

    キャッシュとクライアントを同じ Azure リージョン内に配置することを強くお勧めします。 リージョン間呼び出しを含むシナリオの場合、接続文字列に `synctimeout` プロパティを含めることで、`synctimeout` 間隔を既定値の 1000 ミリ秒間隔より大きい値に設定する必要があります。 次の例は、Azure Cache for Redis によって提供された StackExchange.Redis 用の接続文字列 (`synctimeout` は 2000 ミリ秒) のスニペットを示しています。

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. 最新バージョンの [StackExchange.Redis NuGet パッケージ](https://www.nuget.org/packages/StackExchange.Redis/)を使用するようにしてください。 タイムアウトの信頼性を高めるため、コードに示されるバグは常に修正されています。したがって、最新バージョンを使用することが重要です。
1. 要求がサーバーまたはクライアントの帯域幅の制限に制約されている場合は、それらの要求の完了にかかる時間が長くなるため、タイムアウトになることがあります。 タイムアウトがサーバーのネットワーク帯域幅のためであるかどうかを確認するには、「[サーバー側の帯域幅の超過](#server-side-bandwidth-exceeded)」を参照してください。 タイムアウトがクライアントのネットワーク帯域幅のためであるかどうかを確認するには、「[クライアント側の帯域幅の超過](#client-side-bandwidth-exceeded)」を参照してください。
1. サーバーまたはクライアントに CPU 制約を適用していますか?

   - クライアントの CPU に制約されているかどうかを確認します。 CPU 使用率が高いと、要求が `synctimeout` の間隔内に処理されず、要求がタイムアウトになることがあります。クライアント サイズを大きくするか、負荷を分散すると、この問題を制御するのに役立ちます。
   - `CPU` [キャッシュ パフォーマンス メトリック](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)を監視することによって、サーバーの CPU に制約されているかどうかを確認します。 Redis に CPU 制約が適用されているときに要求を受信した場合、これらの要求はタイムアウトになることがあります。この状況に対処するために、Premium キャッシュの複数のシャードに負荷を分散させるか、より大きいサイズまたは価格レベルにアップグレードすることができます。 詳細については、「 [サーバー側の帯域幅の超過](#server-side-bandwidth-exceeded)」を参照してください。
1. サーバー上での処理に時間がかかるコマンドはありますか? Redis サーバー上での処理に時間がかかる実行時間の長いコマンドがあると、タイムアウトになることがあります。 実行時間の長いコマンドの詳細については、「[コストの高いコマンド](#expensive-commands)」を参照してください。 redis-cli クライアントまたは [Redis コンソール](cache-configure.md#redis-console)を使用して、Azure Cache for Redis インスタンスに接続できます。 次に、[SLOWLOG](https://redis.io/commands/slowlog) コマンドを実行して、予測より遅い要求があるかどうかを確認します。 Redis サーバーと StackExchange.Redis は、少数の大きい要求ではなく、多数の小さい要求用に最適化されています。 データをより小さいチャンクに分割することで、この状態が改善される場合があります。

    redis-cli と stunnel を使用したキャッシュの SSL エンドポイントへの接続については、「[Redis のプレビュー リリースの ASP.NET セッション状態プロバイダーの通知](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)」のブログの投稿を参照してください。
1. Redis サーバーの負荷が高いとタイムアウトが生じる場合があります。 `Redis Server Load` [キャッシュ パフォーマンス メトリック](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)を監視することで、サーバーの負荷を監視できます。 100 (最大値) のサーバーの負荷は、Redis サーバーが要求を処理しており、ビジー状態であり、アイドル時間がないことを示します。 特定の要求がサーバーを占有しているかどうかを確認するには、前の段落で説明したように、SlowLog コマンドを実行します。 詳細については、「CPU 使用率またはサーバーの負荷が高い」を参照してください。
1. ネットワーク ブリップの原因と思われる、クライアント側のイベントは他にありますか?  共通イベントには、クライアント インスタンスの数のスケールアップ/スケールダウン、新しいバージョンのクライアントのデプロイ、または自動スケールの有効化が含まれます。 当社のテストでは、自動スケールまたはスケールアップ/スケールダウンによって発信ネットワーク接続が数秒間失われる場合があることがわかりました。 StackExchange.Redis コードはこのようなイベントに対応し、再接続します。 再接続中に、キュー内のすべての要求がタイムアウトになることがあります。
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

   詳細については、「 [サーバーのメモリ不足](#memory-pressure-on-the-server)」を参照してください。

## <a name="additional-information"></a>追加情報

- [Azure Cache for Redis のサービス内容と適切なサイズの選択](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [キャッシュのベンチマークを実行およびテストする方法](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Redis コマンドの実行方法](cache-faq.md#how-can-i-run-redis-commands)
- [Azure Cache for Redis を監視する方法](cache-how-to-monitor.md)
