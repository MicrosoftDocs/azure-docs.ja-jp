---
title: Azure Cache for Redis のベスト プラクティス
description: 次のベスト プラクティスに従って Azure Cache for Redis を効果的に使う方法を説明します。
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454174"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Azure Cache for Redis のベスト プラクティス 
次のベスト プラクティスに従うことにより、Azure Cache for Redis インスタンスを使用するときのパフォーマンスとコスト効率を最大限にできます。

## <a name="configuration-and-concepts"></a>構成と概念
 * **実稼働システムでは Standard レベルまたは Premium レベルを使用します。**  Basic レベルは単一ノード システムであり、データ レプリケーション機能や SLA がありません。 また、C1 以上のキャッシュを使用してください。  C0 キャッシュは、共有 CPU コアを使用し、メモリ量が少なく、"迷惑な隣人" の問題が発生する可能性が高いため、単純な開発/テストのシナリオに向いています。

 * **Redis がインメモリ データ ストアであることに注意します。**  [この記事](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)で、データ損失が発生する可能性のあるいくつかのシナリオについて説明しています。

 * [修正プログラムの適用やフェールオーバーによる](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)**接続の中断に対応できるようにシステムを開発します**。

 * メモリの負荷が高い状況下での**システムの応答性を向上させるように [maxmemory-reserved の設定](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)を構成します**。  この設定は、書き込み負荷の高いワークロードにとって、または Redis に大きい値 (100 KB 以上) を格納している場合、特に重要です。  キャッシュ サイズの 10% から始めて、書き込み負荷の高いロードがある場合に増やすことをお勧めします。 値を選択するときの[いくつかの考慮事項](cache-how-to-troubleshoot.md#considerations-for-memory-reservations)を参照してください。

 * **値が小さいほど Redis のパフォーマンスは向上する**ため、大きいデータを複数のキーに分割することを検討します。  [Redis に関するこちらのディスカッション](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)では、慎重に検討する必要があるいくつかの考慮事項を示しています。  値が大きい場合に生じる可能性のある問題の例については、 [こちらの記事](cache-how-to-troubleshoot.md#large-requestresponse-size) を参照してください。

 * **キャッシュ インスタンスとアプリケーションを同じリージョンに配置します。**  別のリージョンにあるキャッシュに接続すると、待ち時間が大幅に増加して、信頼性が低下します。  Azure の外部から接続することはできますが、これは、*特に Redis をキャッシュとして使用しているときには*お勧めできません。  Redis を単なるキー/値ストアとして使用している場合は、待ち時間は一番の問題ではない可能性があります。 

 * **接続を再利用します** - 新しい接続を作成すると費用がかかり、待ち時間が増加するため、接続はできる限り再利用します。 新しい接続を作成することを選択する場合は、(.NET や Java のようなマネージド メモリ言語でも) 必ず古い接続を閉じてから解放するようにしてください。

 * **少なくとも 15 秒間の*接続タイムアウト*を使用するようにクライアント ライブラリを構成します。** これにより、CPU 使用率が高い状況下でも、システムに接続するための時間を提供します。  タイムアウト値が小さいと、その時間内での接続の確立が保証されません。  何らかの問題 (高いクライアント CPU 使用率、高いサーバー CPU 使用率など) がある場合、接続タイムアウト値が短いと、接続試行が失敗することになります。 この動作は、多くの場合、悪い状況をさらに悪化させます。  タイムアウトを短くすると、役に立つ代わりに、システムに強制的に再接続試行プロセスを再開させることにより "*接続 -> 失敗 -> 再試行*" のループに陥る可能性があり、問題が悪化します。 一般的に、接続タイムアウトは 15 秒以上にしておくことをお勧めしています。 短時間で失敗させて結局再試行させるよりも、15 秒または 20 秒後に接続試行を成功させることをお勧めします。 そのような再試行ループでは、最初からシステムに時間をかけさせた場合よりも、停止時間が長く続く可能性があります。  
     > [!NOTE]
     > このガイダンスは、*接続試行*に固有のものであり、GET や SET などの*操作*が終了するのを待つ時間とは関係ありません。
 

 * **コストの高いコマンドを避けます** - 一部の REDIS 操作 ([KEYS コマンド](https://redis.io/commands/keys)など) は、*非常に*コストが高く、避ける必要があります。  詳細については、[コストの高いコマンドに関連したいくつかの考慮事項](cache-how-to-troubleshoot.md#expensive-commands)を参照してください。


 
## <a name="memory-management"></a>メモリ管理
Redis サーバー インスタンス内でのメモリ使用量に関連したいくつかの考慮すべき事項があります。  いくつかの例を次に示します。

 * **お使いのアプリケーションで機能する[削除ポリシー](https://redis.io/topics/lru-cache)を選択します。**  Azure Redis の既定のポリシーは *volatile-lru* です。これは、TTL 値が設定されているキーのみが削除対象となることを意味します。  TTL 値を持つキーがない場合は、システムはどのキーも削除しません。  メモリー不足のときに任意のキーをシステムに削除させるようにする場合は、*allkeys-lru* ポリシーを検討することができます。

 * **キーに有効期限値を設定します。**  これにより、メモリー不足が起こるまで待つのではなく、予防的にキーが削除されます。  メモリー不足のために削除が開始される場合、サーバーに追加の負荷が生じる可能性があります。  詳細については、[Expire](https://redis.io/commands/expire) コマンドと [ExpireAt](https://redis.io/commands/expireat) コマンドに関するドキュメントをご覧ください。
 
## <a name="client-library-specific-guidance"></a>クライアント ライブラリ固有のガイダンス
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - どちらのクライアントを使用すべきか](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lettuce (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.JS](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [ASP.NET セッション状態プロバイダー](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>再試行を実行するのに安全なタイミングとは
残念ながら、答えは簡単ではありません。  各アプリケーションが、どの操作を再試行でき、どれをできないかを決定する必要があります。  各操作には、さまざまな要件とキー間の依存関係があります。  いつくかの考慮事項を次に示します。

 * ユーザーが実行するよう要求したコマンドを Redis が正常に実行したとしても、クライアント側のエラーを受け取る場合があります。  例:
     - タイムアウトは、クライアント側の概念です。  操作がサーバーに到達すると、クライアントが待機するのをあきらめても、サーバーはコマンドを実行します。  
     - ソケット接続でエラーが発生した場合、操作が実際にサーバーで実行されたかどうかを知ることは不可能です。  たとえば、サーバーで要求が処理された後、クライアントで応答が受信される前に、接続エラーが発生することがあります。
 *  同じ操作を誤って 2 回実行した場合、アプリケーションはどのように反応するでしょうか。  たとえば、整数を 1 回だけでなく 2 回増分した場合、どうなるでしょうか。  アプリケーションは、複数の場所から同じキーに書き込みますか。  再試行ロジックが、アプリの他の部分によって設定された値を上書きしたらどうなるでしょうか。

エラー条件の下でコードがどのように動作するかをテストしたい場合は、[再起動機能](cache-administration.md#reboot)を使用することを検討してください。 これにより、接続の中断がアプリケーションにどのような影響を及ぼすかを確認できます。

## <a name="performance-testing"></a>パフォーマンス テスト
 * 独自のパフォーマンス テストを作成する前に、 **`redis-benchmark.exe` の使用から開始して**、考えられるスループット/待機時間の感触をつかんでください。  Redis ベンチマークのドキュメントは、[こちらにあります](http://redis.io/topics/benchmarks)。  Redis のベンチマークでは SSL はサポートされないため、テストを行う前に、[ポータルで非 SSL ポートを有効にする](cache-configure.md#access-ports)必要があります。  [redis-benchmark.exe の Windows 互換バージョンはこちらにあります](https://github.com/MSOpenTech/redis/releases)。
 * テストに使用するクライアント VM は、Redis Cache インスタンスと**同じリージョン内**にある必要があります。
 * **Dv2 VM シリーズ**はハードウェアが強力であり、最良の結果が得られるため、クライアントにはこれらを使用することをお勧めします。
 * 使用するクライアント VM が、テストするキャッシュと **少なくとも同等のコンピューティングと帯域幅*を持っていることを確認してください。 
 * Windows を使用している場合は、クライアント コンピューターで **VRSS を有効** にしてください。  [詳細についてはこちらをご覧ください](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx)。  PowerShell のサンプル スクリプト:
     >PowerShell -ExecutionPolicy Unrestricted Enable-NetAdapterRSS -Name (    Get-NetAdapter).Name 
     
 * **Premium レベルの Redis インスタンスを使用することを検討してください**。  これらのキャッシュ サイズでは、Redis インスタンスは CPU およびネットワークの両方が優れたハードウェアで実行されるため、ネットワーク待ち時間とスループットが改善します。
 
     > [!NOTE]
     > 参照用として、当社で観測したパフォーマンス結果を[こちらに公開](cache-faq.md#azure-cache-for-redis-performance)しています。   また、SSL/TLS では幾分かのオーバーヘッドが追加されるため、トランスポートの暗号化を使用している場合は、待ち時間とスループット、またはそのいずれかが異なる可能性があることに注意してください。
 
### <a name="redis-benchmark-examples"></a>Redis ベンチマークの例
**テスト前のセットアップ**:これにより、以下に一覧表示されている待ち時間とスループットのテスト コマンドに必要なデータでキャッシュ インスタンスを準備します。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**待機時間をテストするには**:これは、1k ペイロードを使用して GET 要求をテストします。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**スループットをテストするには:** これは、1k ペイロードを使用してパイプライン GET 要求をテストします。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
