---
title: Azure Cache for Redis 開発に関してよくあるご質問
description: Azure Cache for Redis の開発に役立つ、よくある質問に対する回答について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: bafd8a9752d2587ec52fe586e442e3bfc86d7537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585770"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Azure Cache for Redis 開発に関してよくあるご質問

この記事では、Azure Cache for Redis の開発に役立つ、よくある質問に対する回答について説明します。

## <a name="common-questions-and-answers"></a>一般的な質問と回答
このセクションでは、次のよくあるご質問を取り上げます。

* [Azure Cache for Redis の使用を開始する方法](#how-can-i-get-started-with-azure-cache-for-redis)
* [StackExchange.Redis 構成オプションについて](#what-do-the-stackexchangeredis-configuration-options-do)
* [使用可能な Azure Cache for Redis クライアントについて](#what-azure-cache-for-redis-clients-can-i-use)
* [Azure Cache for Redis のローカル エミュレーターの有無について](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Redis コマンドの実行方法](#how-can-i-run-redis-commands)
* [Azure Cache for Redis の MSDN クラス ライブラリ リファレンスが提供されない理由](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Azure Cache for Redis を PHP セッションのキャッシュとして使用できるか](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Redis データベースについて](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Azure Cache for Redis の使用を開始する方法
Azure Cache for Redis の使用を開始する方法はいくつかあります。

* [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)、[ASP.NET](cache-web-app-howto.md)、[Java](cache-java-get-started.md)、[Node.js](cache-nodejs-get-started.md)、[Python](cache-python-get-started.md) で使用可能なチュートリアルのいずれかを確認できます。
* [Microsoft Azure Cache for Redis を使用して高パフォーマンス アプリケーションを構築する方法](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)のビデオを見ることができます。
* プロジェクトの開発言語と一致するクライアントのドキュメントで、Redis を使用する方法を確認できます。 Azure Cache for Redis で使用できる Redis クライアントは多数あります。 Redis クライアントの一覧については、「[https://redis.io/clients](https://redis.io/clients)」を参照してください。

Azure アカウントをお持ちでない場合は、次の操作を行います。

* [無料で Azure アカウントを開きます](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero)。 Azure の有料サービスを試用できるクレジットが提供されます。 このクレジットを使い切ってもアカウントは維持されるため、無料の Azure サービスと機能をご利用になれます。
* [Visual Studio サブスクライバーの特典を有効にします](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero)。 MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>StackExchange.Redis 構成オプションについて
StackExchange.Redis には多くのオプションが用意されています。 ここでは、いくつかの一般的な設定について説明します。 StackExchange.Redis オプションの詳細については、 [StackExchange.Redis の構成](https://stackexchange.github.io/StackExchange.Redis/Configuration)に関するページを参照してください。

| 構成オプション | 説明 | 推奨 |
| --- | --- | --- |
| AbortOnConnectFail |true の場合、ネットワーク障害の後に再接続が行われません。 |StackExchange.Redis が自動的に再接続するように、false に設定します。 |
| ConnectRetry |初期接続中に接続試行を繰り返す回数。 |次の注意事項を参考にしてください。 |
| ConnectTimeout |接続操作のタイムアウト (ミリ秒単位)。 |次の注意事項を参考にしてください。 |

通常は、クライアントの既定値で十分です。 ワークロードに基づいてオプションを微調整できます。

* **再試行**
  * 一般的に ConnectRetry と ConnectTimeout に関しては、フェイル ファストして再試行することをお勧めします。 これは、ワークロードと、クライアントが Redis コマンドを発行してから応答を受け取るまでに要する時間の平均に基づいたガイダンスです。
  * 自分で接続の状態を確認して再接続するのではなく、StackExchange.Redis が自動的に再接続するように設定します。 **ConnectionMultiplexer.IsConnected プロパティは使用しません**。
  * 問題の肥大化 - ある問題が発生し、再試行によって問題が肥大化して、解決しないことがあります。 問題の肥大化が発生した場合は、Microsoft Patterns & Practices グループ発行の「[再試行に関する一般的なガイダンス](/azure/architecture/best-practices/transient-faults)」に説明されている指数バックオフ再試行アルゴリズムの使用を検討する必要があります。
  
* **タイムアウト値**
  * ワークロードを考慮したうえで値を適宜設定します。 大きな値を保存する場合は、タイムアウトを大きな値に設定します。
  * StackExchange.Redis が再接続できるように、 `AbortOnConnectFail` を false に設定します。
  * アプリケーションに対して ConnectionMultiplexer インスタンスを 1 つ使用します。 「 [ConnectionMultiplexer クラスを使用してキャッシュに接続する](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)」に示されているように、LazyConnection を使用して、Connection プロパティから返される単一のインスタンスを作成できます。
  * 診断用には、 `ConnectionMultiplexer.ClientName` プロパティを、アプリ インスタンスの一意な名前に設定します。
  * カスタム ワークロードに対しては複数の `ConnectionMultiplexer` インスタンスを使用します。
    * アプリケーションの負荷が変化する場合は、このモデルに従うことができます。 次に例を示します。
    * 1 つのマルチプレクサーを使用して、サイズの大きなキーを処理できます。
    * 1 つのマルチプレクサーを使用して、サイズの小さなキーを処理できます。
    * 使用する ConnectionMultiplexer ごとに、異なる接続タイムアウト値と再試行ロジックを設定できます。
    * 診断を容易にするために、各マルチプレクサーで `ClientName` プロパティを設定します。
    * このガイダンスにより、`ConnectionMultiplexer` あたりの待機時間が合理化される場合があります。

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>使用可能な Azure Cache for Redis クライアントについて
Redis のメリットの 1 つが、クライアントが多数存在しており、さまざまな開発言語を多数サポートしている点です。 現在のクライアントの一覧については、 [Radis クライアント](https://redis.io/clients)に関するページをご覧ください。 さまざまな言語とクライアントのチュートリアルについては、[Azure Cache for Redis の使用方法](cache-dotnet-how-to-use-azure-redis-cache.md)に関するページとその関連記事を目次から見つけて参照してください。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Azure Cache for Redis のローカル エミュレーターの有無について
Azure Cache for Redis のローカル エミュレーターがなくても、ローカル コンピューターの [Redis コマンド ライン ツール](https://github.com/MSOpenTech/redis/releases/)から、redis-server.exe の MSOpenTech のバージョンを実行して接続し、以下の例のように、ローカル キャッシュ エミュレーターと同じような使い心地を得ることができます。

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

必要に応じて、オンラインの Azure Cache for Redis の[既定のキャッシュ設定](cache-configure.md#default-redis-server-configuration)とより正確に一致するように、[redis.conf](https://redis.io/topics/config) ファイルを構成します。

### <a name="how-can-i-run-redis-commands"></a>Redis コマンドの実行方法
[Azure Cache for Redis でサポートされない Redis コマンド](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)に関するセクションに示されているコマンドを除き、[Redis コマンド](https://redis.io/commands#)のページに示されているすべてのコマンドを使用できます。 Redis コマンドを実行するにはオプションがいくつかあります。

* Standard または Premium キャッシュがある場合は、 [Redis コンソール](cache-configure.md#redis-console)を使用して Redis コマンドを実行できます。 Redis コンソールは、Azure Portal で Redis コマンドを安全に実行するための方法です。
* Redis コマンド ライン ツールを使用することもできます。 これらを使用するには、次の手順を実行します。
* [Redis コマンド ライン ツール](https://github.com/MSOpenTech/redis/releases/)をダウンロードします。
* `redis-cli.exe`を使用してキャッシュに接続します。 次の例に示すように、-h スイッチを使用してキャッシュ エンドポイントを渡し、-a を使用してキーを渡します。
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Redis コマンド ライン ツールは TLS ポートを使用できません。ただし、`stunnel` などのユーティリティを使用すると、ツールを TLS ポートに安全に接続することができます。詳細については、ブログ記事「[Azure Cache for Redis で Redis コマンドライン ツールを使用する方法](./cache-how-to-redis-cli-tool.md)」の記事を参照してください。
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Azure Cache for Redis の MSDN クラス ライブラリ リファレンスが提供されない理由
Microsoft Azure Cache for Redis は、広く普及しているオープン ソースのインメモリ データ ストアである Redis がベースとなっています。 これには、多くのプログラミング言語に対するさまざまな [Redis クライアント](https://redis.io/clients) によってアクセスできます。 各クライアントは、[Redis コマンド](https://redis.io/commands)を使用して Azure Cache for Redis インスタンスを呼び出す独自の API を持ちます。

クライアントはそれぞれ異なるため、MSDN には単独の一元的なクラス リファレンスは用意されていません。各クライアントで独自のリファレンス ドキュメントが管理されています。 リファレンス ドキュメントのほかに、チュートリアルもいくつか用意されています。チュートリアルでは、さまざまな言語とキャッシュ クライアントを使用して Azure Cache for Redis を使用する方法について説明します。 これらのチュートリアルについては、[Azure Cache for Redis の使用方法](cache-dotnet-how-to-use-azure-redis-cache.md)に関するページとその関連記事を目次から見つけて参照してください。

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Azure Cache for Redis を PHP セッションのキャッシュとして使用できるか
はい。Azure Cache for Redis を PHP セッションのキャッシュとして使用するには、`session.save_path` に Azure Cache for Redis インスタンスへの接続文字列を指定します。

> [!IMPORTANT]
> Azure Cache for Redis を PHP セッションのキャッシュとして使用する場合、次の例に示すように、キャッシュへの接続に使用するセキュリティ キーを URL エンコードする必要があります。
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> キーが URL エンコードされていない場合、次のようなメッセージの例外が表示されます。`Failed to parse session.save_path`
>

Azure Cache for Redis を PhpRedis クライアントで PHP セッションのキャッシュとして使用する方法の詳細については、[PHP セッション ハンドラー](https://github.com/phpredis/phpredis#php-session-handler)に関するページを参照してください。

### <a name="what-are-redis-databases"></a>Redis データベースとは

Redis データベースとは、単に同じ Redis インスタンス内でデータを論理的に切り離したものです。 キャッシュ メモリは、すべてのデータベースで共有され、特定のデータベースの実際のメモリ使用量は、そのデータベースに格納されているキー/値によって異なります。 たとえば、C6 キャッシュは 53 GB のメモリを備え、P5 の場合は 120 GB になります。 この 53 GB/120 GB すべてを 1 つのデータベースに配置することも、複数のデータベースに分割することもできます。 

> [!NOTE]
> クラスタリングを有効にして Premium Azure Cache for Redis を使用すると、使用できるのはデータベース 0 だけになります。 これは Redis に固有の制限事項です。Azure Cache for Redis の制限事項ではありません。 詳細については、「[クラスタリングを使用するためにクライアント アプリケーションを変更する必要がありますか](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)」を参照してください。
> 
> 

## <a name="next-steps"></a>次のステップ

その他の [Azure Cache for Redis のよくあるご質問](cache-faq.md)について。