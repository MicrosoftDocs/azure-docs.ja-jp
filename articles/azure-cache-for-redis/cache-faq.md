---
title: Azure Cache for Redis に関する FAQ | Microsoft Docs
description: Azure Cache for Redis についてよく寄せられる質問に対する回答、パターン、ベスト プラクティスについて説明します。
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 04/29/2019
ms.author: yegu
ms.openlocfilehash: 42d0d7dcc4e10e6f9bfad02a68f3ec176b8a7fb4
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315991"
---
# <a name="azure-cache-for-redis-faq"></a>Azure Cache for Redis に関する FAQ
Azure Cache for Redis についてよく寄せられる質問に対する回答、パターン、ベスト プラクティスについて説明します。

## <a name="what-if-my-question-isnt-answered-here"></a>ここに質問の答えがない場合はどうすればいいですか。
質問がここに表示されていない場合はご連絡ください。答えを見つけるお手伝いをします。

* この FAQ の最後に掲載されているコメントに質問を投稿し、Azure Cache チームや他のコミュニティ メンバーとこの記事についてやり取りすることができます。
* さらに多くの人と情報交換する場合は、 [Azure Cache MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) に質問を投稿すれば、Azure Cache チームや他のコミュニティ メンバーとやり取りすることができます。
* 機能要求を作成する場合は、要求とアイデアを [Azure Redis Cache のユーザーの声](https://feedback.azure.com/forums/169382-cache)に送信することができます。
* また、 [Azure Cache 外部フィードバック](mailto:azurecache@microsoft.com)にメールをお送りいただくこともできます。

## <a name="azure-cache-for-redis-basics"></a>Azure Cache for Redis の基本
このセクションの FAQ では、Azure Cache for Redis の基本について説明します。

* [Azure Cache for Redis とは](#what-is-azure-cache-for-redis)
* [Azure Cache for Redis の使用を開始する方法](#how-can-i-get-started-with-azure-cache-for-redis)

以下の FAQ では、Azure Cache for Redis に関する基本的な概念と質問を取り上げており、回答は他の FAQ セクションのいずれかに示されています。

* [Azure Cache for Redis のサービス内容と適切なサイズの選択](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [使用可能な Azure Cache for Redis クライアントについて](#what-azure-cache-for-redis-clients-can-i-use)
* [Azure Cache for Redis のローカル エミュレーターの有無について](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [キャッシュの正常性とパフォーマンスの監視方法](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>計画に関する FAQ
* [Azure Cache for Redis のサービス内容と適切なサイズの選択](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure Cache for Redis のパフォーマンス](#azure-cache-for-redis-performance)
* [キャッシュを配置するリージョン](#in-what-region-should-i-locate-my-cache)
* [Azure Cache for Redis の課金方法](#how-am-i-billed-for-azure-cache-for-redis)
* [Azure Cache for Redis を Azure Government Cloud、Azure China Cloud、または Microsoft Azure Germany で使用できるか](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>開発に関する FAQ
* [StackExchange.Redis 構成オプションについて](#what-do-the-stackexchangeredis-configuration-options-do)
* [使用可能な Azure Cache for Redis クライアントについて](#what-azure-cache-for-redis-clients-can-i-use)
* [Azure Cache for Redis のローカル エミュレーターの有無について](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Redis コマンドの実行方法](#how-can-i-run-redis-commands)
* [他のいくつかの Azure サービスと異なり Azure Cache for Redis の MSDN クラス ライブラリ リファレンスが提供されない理由](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Azure Cache for Redis を PHP セッションのキャッシュとして使用できるか](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Redis データベースについて](#what-are-redis-databases)

## <a name="security-faqs"></a>セキュリティに関する FAQ
* [Redis への接続に非 SSL ポートを有効にする必要がある状況](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>運用に関する FAQ
* [いくつかの運用上のベスト プラクティスについて](#what-are-some-production-best-practices)
* [一般的な Redis コマンドの使用に関するいくつかの考慮事項](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [キャッシュのベンチマークを実行およびテストする方法](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [ThreadPool 拡大の重要な詳細情報](#important-details-about-threadpool-growth)
* [StackExchange.Redis を使用するときにサーバー GC を有効にしてクライアントでのスループットを向上させる](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [接続のパフォーマンスに関する考慮事項](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>監視とトラブルシューティングに関する FAQ
このセクションの FAQ では、監視とトラブルシューティングに関する一般的な質問について説明します。 Azure Cache for Redis インスタンスの監視とトラブルシューティングの詳細については、「[Azure Cache for Redis を監視する方法](cache-how-to-monitor.md)」と [Azure Cache for Redis のトラブルシューティング方法](cache-how-to-troubleshoot.md)についてのページを参照してください。

* [キャッシュの正常性とパフォーマンスの監視方法](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [タイムアウトが発生する理由](#why-am-i-seeing-timeouts)
* [クライアントがキャッシュから切断される理由](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>事前のキャッシュ オファリングに関する FAQ
* [どの Azure Cache を利用すればよいですか。](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Azure Cache for Redis とは
Azure Cache for Redis は、人気のあるオープンソース ソフトウェア [Redis](https://redis.io/) が基になっています。 これを使用すると、Microsoft によって管理されている、セキュリティで保護された専用 Azure Cache for Redis に Azure 内の任意のアプリケーションからアクセスできます。 詳細については、azure.com の [Azure Cache for Redis](https://azure.microsoft.com/services/cache/) の製品ページを参照してください。

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Azure Cache for Redis の使用を開始する方法
Azure Cache for Redis の使用を開始する方法はいくつかあります。

* [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)、[ASP.NET](cache-web-app-howto.md)、[Java](cache-java-get-started.md)、[Node.js](cache-nodejs-get-started.md)、[Python](cache-python-get-started.md) で使用可能なチュートリアルのいずれかを確認できます。
* [Microsoft Azure Cache for Redis を使用して高パフォーマンス アプリケーションを構築する方法](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)のビデオを見ることができます。
* プロジェクトの開発言語と一致するクライアントのドキュメントで、Redis を使用する方法を確認できます。 Azure Cache for Redis で使用できる Redis クライアントは多数あります。 Redis クライアントの一覧については、「[https://redis.io/clients](https://redis.io/clients)」を参照してください。

Azure アカウントをお持ちでない場合は、次の操作を行います。

* [無料で Azure アカウントを開きます](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero)。 Azure の有料サービスを試用できるクレジットが提供されます。 このクレジットを使い切ってもアカウントは維持されるため、無料の Azure サービスと機能をご利用になれます。
* [Visual Studio サブスクライバーの特典を有効にします](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero)。 MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Azure Cache for Redis のサービス内容と適切なサイズの選択
Azure Cache for Redis には、さまざまなレベルの**サイズ**、**帯域幅**、**高可用性**、**SLA** に関するオプションが用意されています。

Cache のオプションを選択するときの考慮事項を次に示します。

* **メモリ**:Basic レベルと Standard レベルでは、250 MB ～ 53 GB です。 Premium サービス レベルでは、最大 1.2 TB (クラスター) または 120 GB (非クラスター) が提供されます。 詳細については、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)に関するページを参照してください。
* **ネットワーク パフォーマンス**:高いスループットを必要とするワークロードがある場合、Premium レベルでは、Standard や Basic と比較してより広い帯域幅が提供されます。 また、各レベル内では、キャッシュをホストする基盤の VM のため、キャッシュのサイズが大きいほど帯域幅も増えます。 詳細については、[次の表](#cache-performance)を参照してください。
* **スループット**:Premium レベルでは、提供されているうちで最大のスループットが提供されます。 キャッシュ サーバーまたはクライアントが帯域幅の限界に達した場合、クライアント側でタイムアウトが発生する場合があります。 詳細については、後の表を参照してください。
* **高可用性/SLA**:Azure Cache for Redis では、Standard/Premium キャッシュについて、少なくとも 99.9% の可用性を保証しています。 SLA の詳細については、[Azure Cache for Redis の価格](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)についてのページを参照してください。 SLA は、Cache エンドポイントへの接続のみをカバーします。 SLA は、データ損失からの保護には対応していません。 Premium レベルの Redis データの保持機能を使用して、データ損失に対する復元性を高めることをお勧めします。
* **Redis データの永続化**:Premium レベルでは、Azure Storage アカウント内のキャッシュ データを永続化できます。 Basic/Standard のキャッシュでは、データはすべてメモリ内にのみ格納されます。 基になるインフラストラクチャで問題が発生すると、データが失われる可能性があります。 Premium レベルの Redis データの保持機能を使用して、データ損失に対する復元性を高めることをお勧めします。 Azure Cache for Redis では、Redis 永続化の RDB オプションと AOF オプション (近日提供予定) が用意されています。 詳細については、[Premium Azure Cache for Redis の永続化の構成方法](cache-how-to-premium-persistence.md)についてのページを参照してください。
* **Redis クラスター**:Premium サービス レベルで利用可能な Redis クラスタリングを使用すると、120 GB を超えるキャッシュを作成したり、複数の Redis ノード間でデータをシャード化したりできます。 各ノードは、高可用性対応のプライマリ/レプリカ キャッシュのペアで構成されています。 詳細については、[Premium Azure Cache for Redis のクラスタリングの構成方法](cache-how-to-premium-clustering.md)に関するページを参照してください。
* **セキュリティとネットワークの分離の強化**:Azure Virtual Network (VNET) のデプロイにより、Azure Cache for Redis のセキュリティと分離が強化されると共に、サブネット、アクセス制御ポリシー、アクセスをさらに制限する他の機能も提供されます。 詳細については、[Premium Azure Cache for Redis の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)に関するページを参照してください。
* **Redis の構成**:Standard レベルと Premium レベルのどちらでも、キースペース通知のために Redis を構成できます。
* **最大クライアント接続数**:Premium レベルでは、Redis に接続できる最大数のクライアントが提供されます。キャッシュのサイズが大きいほど、接続の数は多くなります。 クラスタリングでは、クラスター化されたキャッシュで使用できる接続の数は増加しません。 詳細については、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)に関するページを参照してください。
* **Redis サーバー専用コア**:Premium レベルでは、すべてのキャッシュ サイズに Redis 専用のコアがあります。 Basic/Standard レベルでは、C1 サイズ以上に Redis サーバー専用コアがあります。
* **Redis はシングル スレッドです**。したがって、3 つ以上のコアを使用しても 2 つのコアを使用する場合と比べて追加のメリットはありません。ただし、一般に、VM のサイズが大きいほど、小さなサイズよりも多くの帯域幅を利用できます。 キャッシュ サーバーまたはクライアントが帯域幅の制限に達すると、クライアント側でタイムアウトが発生します。
* **パフォーマンスの向上**:Premium レベルのキャッシュは、高速プロセッサを備えたハードウェアにデプロイされ、Basic レベルや Standard レベルと比べて優れたパフォーマンスを発揮します。 Premium レベルのキャッシュは、スループットが高く、待機時間が低くなっています。

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure Cache for Redis のパフォーマンス
次の表に、Azure Cache for Redis のエンドポイントに対して IaaS VM から `redis-benchmark.exe` を使用して、Standard および Premium キャッシュのさまざまなサイズをテストした際に測定された最大帯域幅を示します。 SSL のスループットについては、Azure Cache for Redis エンドポイントに接続するため、Redis ベンチマークは stunnel と共に使用されています。

>[!NOTE] 
>これらの値は保証された値ではなく、これらの値の SLA もありません。これらの値は、標準的な値と考えてください。 アプリケーションに最適なキャッシュ サイズを特定するには、アプリケーションに対してロード テストを実行する必要があります。
>定期的に新しい結果を公表しているため、これらの数字は変わる可能性があります。
>

この表からは次のような結論が得られます。

* キャッシュのサイズが同じ場合のスループットは、Standard レベルより Premium レベルの方が高くなります。 たとえば、P1 のスループットは、6 GB キャッシュでは 180,000 要求/秒 (RPS) であるのに対し、C3 では 100,000 RPS となります。
* Redis クラスタリングでは、クラスターのシャード (ノード) の数を増やすと、スループットもそれに比例して増加する。 たとえば、10 個のシャードから成る P4 クラスターを作成すると、利用可能なスループットは 400,000 * 10 = 4 百万 RPS になります。
* キー サイズを大きくしたときのスループットは、Standard レベルより Premium レベルのほうが高い。

| Pricing tier | Size | CPU コア数 | 使用可能な帯域幅 | 1 KB 値サイズ | 1 KB 値サイズ |
| --- | --- | --- | --- | --- | --- |
| **Standard のキャッシュ サイズ** | | |**メガビット/秒 (Mb/s) / メガバイト/秒 (MB/s)** |**1 秒あたりの要求数 (RPS) 非 SSL** |**1 秒あたりの要求数 (RPS) SSL** |
| C0 | 250 MB | 共有 | 100 / 12.5  |  15,000 |   7,500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2.5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100,000 |  90,000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60,000 |  55,000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **Premium のキャッシュ サイズ** | |**シャードあたりの CPU コア数** | **メガビット/秒 (Mb/s) / メガバイト/秒 (MB/s)** |**1 秒あたりの要求数 (RPS) 非 SSL、シャードあたり** |**1 秒あたりの要求数 (RPS) SSL、シャードあたり** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

stunnel の設定や `redis-benchmark.exe` などの Redis ツールのダウンロードの詳細については、「[Redis コマンドの実行方法](#cache-commands) 」セクションを参照してください。

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>キャッシュを配置するリージョン
最大限のパフォーマンスと最短の待機時間を実現するには、キャッシュ クライアント アプリケーションと同じリージョンに Azure Cache for Redis を配置します。

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Azure Cache for Redis の課金方法
Azure Cache for Redis の価格は[ここ](https://azure.microsoft.com/pricing/details/cache/)に記載されています。 価格ページには、1 時間単位の価格が表示されます。 キャッシュは、キャッシュが作成された時間から削除された時間までの期間に関して、分単位で課金されます。 キャッシュの課金を停止または一時停止するオプションはありません。

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Azure Cache for Redis を Azure Government Cloud、Azure China Cloud、または Microsoft Azure Germany で使用できるか
はい。Azure Cache for Redis は、Azure Government Cloud、Azure China 21Vianet Cloud、Microsoft Azure Germany で使用できます。 ただし、Azure Cache for Redis のアクセスと管理を行うための URL については、これらのクラウドと、Azure パブリック クラウドとで異なります。

| クラウド   | Redis の DNS サフィックス            |
|---------|---------------------------------|
| パブリック  | *.redis.cache.windows.net       |
| 米国政府  | *. redis.cache.usgovcloudapi.net |
| ドイツ | *.redis.cache.cloudapi.de       |
| 中国   | *.redis.cache.chinacloudapi.cn  |

その他のクラウドで Azure Cache for Redis を使用するときの考慮事項の詳細については、次のリンクを参照してください。

- [Azure Government データベース - Azure Cache for Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet Cloud - Azure Cache for Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

Azure Government Cloud、Azure China 21Vianet Cloud、Microsoft Azure Germany での PowerShell を使用した Azure Cache for Redis の利用の詳細については、[他のクラウドに接続する方法 - Azure Cache for Redis PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds) に関するページを参照してください。

<a name="cache-configuration"></a>

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
  * 問題の肥大化 - ある問題が発生し、再試行によって問題が肥大化して、解決しないことがあります。 問題の肥大化が発生した場合は、Microsoft Patterns & Practices グループ発行の「[再試行に関する一般的なガイダンス](../best-practices-retry-general.md)」に説明されている指数バックオフ再試行アルゴリズムの使用を検討する必要があります。
* **タイムアウト値**
  * ワークロードを考慮したうえで値を適宜設定します。 大きな値を保存する場合は、タイムアウトを大きな値に設定します。
  * StackExchange.Redis が再接続できるように、 `AbortOnConnectFail` を false に設定します。
  * アプリケーションに対して ConnectionMultiplexer インスタンスを 1 つ使用します。 「 [ConnectionMultiplexer クラスを使用してキャッシュに接続する](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)」に示されているように、LazyConnection を使用して、Connection プロパティから返される単一のインスタンスを作成できます。
  * 診断用には、 `ConnectionMultiplexer.ClientName` プロパティを、アプリ インスタンスの一意な名前に設定します。
  * カスタム ワークロードに対しては複数の `ConnectionMultiplexer` インスタンスを使用します。
      * アプリケーションの負荷が変化する場合は、このモデルに従うことができます。 例:
      * 1 つのマルチプレクサーを使用して、サイズの大きなキーを処理できます。
      * 1 つのマルチプレクサーを使用して、サイズの小さなキーを処理できます。
      * 使用する ConnectionMultiplexer ごとに、異なる接続タイムアウト値と再試行ロジックを設定できます。
      * 診断を容易にするために、各マルチプレクサーで `ClientName` プロパティを設定します。
      * このガイダンスにより、`ConnectionMultiplexer` あたりの待機時間が合理化される場合があります。

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>使用可能な Azure Cache for Redis クライアントについて
Redis のメリットの 1 つが、クライアントが多数存在しており、さまざまな開発言語を多数サポートしている点です。 現在のクライアントの一覧については、 [Radis クライアント](https://redis.io/clients)に関するページをご覧ください。 さまざまな言語とクライアントのチュートリアルについては、[Azure Cache for Redis の使用方法](cache-dotnet-how-to-use-azure-redis-cache.md)に関するページとその関連記事を目次から見つけて参照してください。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Azure Cache for Redis のローカル エミュレーターの有無について
Azure Cache for Redis のローカル エミュレーターがなくても、ローカル コンピューターの [Redis コマンド ライン ツール](https://github.com/MSOpenTech/redis/releases/)から、redis-server.exe の MSOpenTech のバージョンを実行して接続し、以下の例のように、ローカル キャッシュ エミュレーターと同じような使い心地を得ることができます。

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


必要に応じて、オンラインの Azure Cache for Redis の[既定のキャッシュ設定](cache-configure.md#default-redis-server-configuration)とより正確に一致するように、[redis.conf](https://redis.io/topics/config) ファイルを構成します。

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Redis コマンドの実行方法
[Azure Cache for Redis でサポートされない Redis コマンド](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)に関するセクションに示されているコマンドを除き、[Redis コマンド](https://redis.io/commands#)のページに示されているすべてのコマンドを使用できます。 Redis コマンドを実行するにはオプションがいくつかあります。

* Standard または Premium キャッシュがある場合は、 [Redis コンソール](cache-configure.md#redis-console)を使用して Redis コマンドを実行できます。 Redis コンソールは、Azure Portal で Redis コマンドを安全に実行するための方法です。
* Redis コマンド ライン ツールを使用することもできます。 これらを使用するには、次の手順を実行します。
* [Redis コマンド ライン ツール](https://github.com/MSOpenTech/redis/releases/)をダウンロードします。
* `redis-cli.exe`を使用してキャッシュに接続します。 次の例に示すように、-h スイッチを使用してキャッシュ エンドポイントを渡し、-a を使用してキーを渡します。
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Redis コマンド ライン ツールは SSL ポートを使用できません。ただし、`stunnel` などのユーティリティを使用すると、ツールを SSL ポートに安全に接続することができます。詳細については、ブログ記事「[Azure Cache for Redis で Redis コマンドライン ツールを使用する方法](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool)」の記事を参照してください。
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>他のいくつかの Azure サービスと異なり Azure Cache for Redis の MSDN クラス ライブラリ リファレンスが提供されない理由
Microsoft Azure Cache for Redis は、広く支持されているオープン ソースの Azure Cache for Redis がベースとなっています。 これには、多くのプログラミング言語に対するさまざまな [Redis クライアント](https://redis.io/clients) によってアクセスできます。 各クライアントは、[Redis コマンド](https://redis.io/commands)を使用して Azure Cache for Redis インスタンスを呼び出す独自の API を持ちます。

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
>

Azure Cache for Redis を PhpRedis クライアントで PHP セッションのキャッシュとして使用する方法の詳細については、[PHP セッション ハンドラー](https://github.com/phpredis/phpredis#php-session-handler)に関するページを参照してください。

### <a name="what-are-redis-databases"></a>Redis データベースとは

Redis データベースとは、単に同じ Redis インスタンス内でデータを論理的に切り離したものです。 キャッシュ メモリは、すべてのデータベースで共有され、特定のデータベースの実際のメモリ使用量は、そのデータベースに格納されているキー/値によって異なります。 たとえば、C6 キャッシュは 53 GB のメモリを備え、P5 の場合は 120 GB になります。 この 53 GB/120 GB すべてを 1 つのデータベースに配置することも、複数のデータベースに分割することもできます。 

> [!NOTE]
> クラスタリングを有効にして Premium Azure Cache for Redis を使用すると、使用できるのはデータベース 0 だけになります。 これは Redis に固有の制限事項です。Azure Cache for Redis の制限事項ではありません。 詳細については、「 [クラスタリングを使用するためにクライアント アプリケーションを変更する必要がありますか](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Redis への接続に非 SSL ポートを有効にする必要がある状況
Redis サーバーはネイティブで SSL をサポートしませんが、Azure Cache for Redis では SSL がサポートされます。 Azure Cache for Redis に接続しようとしていて、クライアントが StackExchange.Redis のように SSL をサポートしている場合は、SSL を使用する必要があります。

>[!NOTE]
>既定では、新しい Azure Cache for Redis インスタンスの SSL 以外のポートは無効になっています。 クライアントが SSL をサポートしていない場合は、[Azure Cache for Redis でのキャッシュの構成](cache-configure.md)に関するページの「[アクセス ポート](cache-configure.md#access-ports)」セクションの指示に従って、非 SSL ポートを有効にする必要があります。
>
>

`redis-cli` などの Redis ツールは SSL ポートを使用できません。ただし、`stunnel` などのユーティリティを使用すると、ツールを SSL ポートに安全に接続することができます。詳細については、ブログ記事「[Announcing ASP.NET Session State Provider for Redis Preview Release (Redis 向け ASP.NET セッション状態プロバイダー プレビュー リリースの発表)](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)」を参照してください。

Redis ツールのダウンロードの詳細については、「 [Redis コマンドの実行方法](#cache-commands) 」セクションを参照してください。

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
* 独自のパフォーマンス テストを作成する前に、 `redis-benchmark.exe` を使用して実現可能なスループットを確認してください。 `redis-benchmark` では SSL はサポートされていないため、テストを行うには、[Azure Portal で非 SSL ポートを有効にする](cache-configure.md#access-ports)必要があります。 例については、「 [キャッシュのベンチマークを実行およびテストする方法](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* テストに使用するクライアント VM は、Azure Cache for Redis インスタンスと同じリージョンにある必要があります。
* Dv2 VM シリーズはハードウェアが強力であり、最良の結果が得られるため、クライアントにはこれらのシリーズを使用することをお勧めします。
* クライアント VM については、コンピューティング能力と帯域幅がテスト対象のキャッシュと同等以上であるものを選択してください。
* Windows を使用している場合は、クライアント コンピューターで VRSS を有効にしてください。 [詳細についてはこちらをご覧ください](https://technet.microsoft.com/library/dn383582.aspx)。
* Premium レベルでは、Redis インスタンスが CPU およびネットワークの両方が優れたハードウェアで実行されるため、ネットワーク待機時間およびスループットが改善します。

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>一般的な Redis コマンドの使用に関するいくつかの考慮事項

* 完了するのに時間がかかる特定の Redis コマンドについては、その影響を完全に理解しないまま使用するのは避けてください。 たとえば、運用環境で [KEYS](https://redis.io/commands/keys) コマンドを実行しないでください。 キーの数によっては、復帰に時間がかかる可能性があります。 Redis はシングル スレッド サーバーであり、一度に 1 つずつコマンドを処理します。 KEYS の後に他のコマンドが発行されている場合、それらのコマンドは Redis によって KEYS コマンドが処理されるまで処理されません。 [redis.io のサイト](https://redis.io/commands/) に、Redis でサポートされる各操作の時間計算量の詳細が記載されています。 各コマンドをクリックして、操作ごとの時間計算量を確認してください。
* キー サイズ - 小さなキー/値と大きなキー/値のどちらを使用するか。 これはシナリオによって異なります。 ご利用のシナリオで、より大きなキーが必要な場合は、ConnectionTimeout を調整してから、値を再度試して再試行ロジックを調整することができます。 Redis サーバーの観点からは、値が小さいほど、パフォーマンスは向上します。
* これらの考慮事項は、サイズの大きな値を Redis に格納できないという意味ではありません。次の点を考慮する必要があります。 待機時間は長くなります。 サイズの大きなデータ セットとサイズの小さなデータ セットがある場合は、前の「[StackExchange.Redis 構成オプションについて](#cache-configuration)」に説明したように、それぞれ異なるタイムアウト値と再試行回数が構成された複数の ConnectionMultiplexer インスタンスを使用できます。

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>キャッシュのベンチマークを実行およびテストする方法
* [キャッシュ診断の有効化](cache-how-to-monitor.md#enable-cache-diagnostics)によってキャッシュの正常性を[監視](cache-how-to-monitor.md)できるようにします。 Azure Portal でメトリックを表示できますが、任意のツールを使用して、メトリックを [ダウンロードして確認](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) することも可能です。
* redis-benchmark.exe を使用して Redis サーバーのロード テストを実行できます。
* ロード テスト用のクライアントと Azure Cache for Redis が同じリージョン内にあることを確認します。
* redis-cli.exe を使用し、INFO コマンドを使用してキャッシュを監視します。
* 負荷が高いことが原因でメモリの断片化が発生している場合は、キャッシュのサイズをスケール アップする必要があります。
* Redis ツールのダウンロードの詳細については、「 [Redis コマンドの実行方法](#cache-commands) 」セクションを参照してください。

以下のコマンドは、redis-benchmark.exe の使用例です。 正確な結果を得るために、以下のコマンドはキャッシュと同じリージョンにある VM で実行してください。

* 1 k ペイロードを使用してパイプライン SET 要求をテストする

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* 1 k ペイロードを使用してパイプライン GET 要求をテストする。
  注:まず上の SET テストを実行し、キャッシュにデータを格納してください。

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>ThreadPool 拡大の重要な詳細情報
CLR ThreadPool には、"Worker" スレッドと "I/O Completion Port" (IOCP) スレッドの 2 種類があります。

* `Task.Run(…)` メソッドや `ThreadPool.QueueUserWorkItem(…)` メソッドの処理などには、Worker スレッドが使用されます。 これらのスレッドは、バック グラウンド スレッドで作業が発生する必要がある場合に、CLR でさまざまなコンポーネントによっても使用されます。
* IOCP スレッドは、ネットワークからの読み取りの場合など、非同期 IO が発生する場合に使用されます。

スレッド プールは、各種のスレッドについて「最小」設定に達するまで、新しい worker スレッドまたは I/O 完了スレッドをオンデマンドで (スロットルなしで) 提供します。 既定では、スレッドの最小数はシステム上のプロセッサの数に設定されます。

既存の (ビジー) スレッドの数がスレッドの "最小" 数に達すると、ThreadPool は新しいスレッドを挿入する速度を、500 ミリ秒ごとに 1 スレッドへとスロットルします。 通常、ご利用のシステムで、IOCP スレッドを必要とする作業のバーストが取得された場合、その作業は高速に処理されます。 ただし、作業のバーストが構成済みの「最小」設定を超えた場合は、次の 2 つの状態のうちどちらかが発生するまで ThreadPool が待機するので、多少の遅延が生じます。

1. 既存のスレッドの 1 つが空き状態になり、作業を処理する。
2. 既存のスレッドが 500 ミリ秒間空いた状態にならないと、新しいスレッドが作成されます。

基本的に、これは、ビジー スレッド数が最小スレッド数より大きい場合、ネットワーク トラフィックがアプリケーションによって処理される前に 500 ミリ秒の遅延が生じる可能性があることを意味しています。 また、既存のスレッドが (私の記憶によると) 15 秒を超えてアイドル状態になると、そのスレッドはクリーンアップされ、拡大と縮小のこのサイクルが繰り返されることがあります。

StackExchange.Redis (ビルド 1.0.450 以降) からのエラー メッセージの例を見れば、ThreadPool の統計情報が出力されていることがわかります (IOCP と WORKER に関する下記の詳細を参照)。

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

前の例では、IOCP スレッドについて、6 つのビジー スレッドが存在し、システムは 4 つの最小スレッドを許容するように構成されていることがわかります。 この場合、6 > 4 であることから、クライアントは 500 ミリ秒の遅延を 2 回検出している可能性があります。

IOCP スレッドまたは WORKER スレッドの拡大がスロットルされた場合、StackExchange.Redis がタイムアウトになる可能性があることに注意してください。

### <a name="recommendation"></a>推奨

この情報に基づき、顧客が IOCP スレッドと WORKER スレッドの最小構成値を既定値よりも大きく設定することを強くお勧めします。 あるアプリケーションでは適切な値であっても別のアプリケーションで高すぎたり低すぎたりする可能性があるため、これをどのような値にする必要があるかについて画一的なガイダンスを提供することはできません。 この設定は複雑なアプリケーションの他の部分のパフォーマンスにも影響を与える可能性があるので、各顧客は特定のニーズに合わせてこの設定を調整する必要があります。 適切な値として、まず 200 または 300 に設定し、テストして必要に応じて調整します。

この設定を構成する方法

* この設定は、`global.asax.cs` 内の [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) メソッドを使用してプログラムによって変更することをお勧めします。 例:

```cs
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

* 最小スレッド数の設定は、通常、`%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` にある `Machine.config` 内の `<processModel>` 構成要素の下にある [*minIoThreads* または *minWorkerThreads* 構成設定](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx)を使用して指定することもできます。 **この方法で最小スレッド数を設定する方法は、一般的はにお勧めできません。これはシステム全体の設定だからです。**

  > [!NOTE]
  > この構成要素で指定される値は、 "*コアごと*" の設定となります。 たとえば、4 コア マシンをお持ちの場合で、実行時の *minIoThreads* 設定を 200 にしたい場合は、`<processModel minIoThreads="50"/>` を使用します。
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>StackExchange.Redis を使用するときにサーバー GC を有効にしてクライアントでのスループットを向上させる
StackExchange.Redis を使用するときにサーバー GC を有効にすると、クライアントが最適化され、パフォーマンスとスループットを向上させることができます。 サーバー GC とそれを有効にする方法の詳細については、次の記事を参照してください。

* [サーバー GC を有効にするには](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [ガベージ コレクションの基礎](/dotnet/standard/garbage-collection/fundamentals)
* [ガベージ コレクションとパフォーマンス](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>接続のパフォーマンスに関する考慮事項

各価格レベルには、クライアント接続、メモリ、および帯域幅についてさまざまな制限があります。 各キャッシュのサイズが特定の接続数*まで*許容される一方で、Redis への各接続はそれにオーバーヘッドが関連付けられています。 このようなオーバーヘッドの例には、TLS/SSL 暗号化の結果としての CPU とメモリの使用量があります。 指定したキャッシュ サイズの最大接続数の上限は、負荷が低いキャッシュを想定しています。 接続オーバーヘッドからの読み込みに*加えて*、クライアントの操作からの読み込みがシステムの容量を超える場合、現在のキャッシュ サイズが接続数の上限を超えていない場合でも、キャッシュ容量の問題が発生する可能性があります。

各レベルの異なる接続制限について詳しくは、[Azure Cache for Redis の価格](https://azure.microsoft.com/pricing/details/cache/)についてのページを参照してください。 接続と他の既定の構成について詳しくは、「[既定の Redis サーバー構成](cache-configure.md#default-redis-server-configuration)」をご覧ください。

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>キャッシュの正常性とパフォーマンスの監視方法
Microsoft Azure Cache for Redis のインスタンスは、[Azure Portal](https://portal.azure.com) で監視できます。 メトリックの表示、メトリック グラフのスタート画面へのピン留め、監視グラフの日付と時刻の範囲のカスタマイズ、グラフのメトリックの追加と削除、特定の条件が満たされた場合のアラートの設定を行うことができます。 詳細については、[Azure Cache for Redis の監視](cache-how-to-monitor.md)に関するページを参照してください。

Azure Cache for Redis の **[リソース] メニュー**にも、キャッシュの監視およびトラブルシューティングのためのツールがいくつか含まれています。

* **[問題の診断と解決]** では、一般的な問題と、その問題を解決するための戦略に関する情報を確認できます。
* **[リソース正常性]** ではリソースが監視され、そのリソースが意図したとおりに動いているかどうかが示されます。 Azure Resource Health サービスの詳細については、「 [Azure Resource Health の概要](../resource-health/resource-health-overview.md)」を参照してください。
* **[新しいサポート要求]** には、キャッシュのサポート要求を開くためのオプションが用意されています。

これらのツールによって、Azure Cache for Redis インスタンスの正常性を監視し、キャッシュ アプリケーションを管理できます。 「[Azure Redis Cache の構成方法](cache-configure.md)」の「サポートおよびトラブルシューティング設定」を参照してください。

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>タイムアウトが発生する理由
タイムアウトは、Redis との対話に使用されているクライアントで発生します。 Redis サーバーに送信されたコマンドは、キューに格納されます。コマンドは、最終的に Redis サーバーによって取得され、実行されます。 ただし、この処理中にクライアントがタイムアウトすることがあり、その場合は呼び出し元では例外が発生します。 タイムアウトの問題のトラブルシューティングについては、「[クライアント側のトラブルシューティング](cache-how-to-troubleshoot.md#client-side-troubleshooting)」および「[StackExchange.Redis のタイムアウトの例外](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions)」を参照してください。

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>クライアントがキャッシュから切断される理由
キャッシュが切断される一般的な理由のいくつかを次に示します。

* クライアント側の原因
  * クライアント アプリケーションが再デプロイされた。
  * クライアント アプリケーションがスケーリング操作を実行した。
    * Cloud Services または Web Apps では、これは自動スケーリングが原因である場合があります。
  * クライアント側のネットワーク レイヤーが変更された。
  * クライアントで、またはクライアントとサーバー間のネットワーク ノードで一時的なエラーが発生した。
  * 帯域幅のしきい値制限に達した。
  * CPU バインド型の操作の完了に時間がかかった。
* サーバー側の原因
  * Standard キャッシュ プランで、Azure Cache for Redis サービスがプライマリ ノードからセカンダリ ノードへのフェールオーバーを開始した。
  * Azure により、キャッシュがデプロイされているインスタンスに修正プログラムが適用されていた。
    * Redis サーバーの更新または VM の一般的なメンテナンスの場合もこれに該当します。

### <a name="which-azure-cache-offering-is-right-for-me"></a>どの Azure Cache を利用すればよいですか。
> [!IMPORTANT]
> 昨年 [お知らせ](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)したとおり、**Azure Managed Cache Service と Azure In-Role Cache サービス**は 2016 年 11 月 30 日で提供が終了しました。 [Azure Cache for Redis](https://azure.microsoft.com/services/cache/) の使用をお勧めします。 移行については、[Managed Cache Service から Azure Cache for Redis への移行](cache-migrate-to-redis.md)についてのページを参照してください。
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis は、一般公開されていて、最大サイズは 120 GB です。可用性の SLA は 99.9% です。 新しい [Premium レベル](cache-premium-tier-intro.md)は、最大 1.2 TB のサイズを提供し、クラスタリング、VNET、および永続化を 99.9% の SLA でサポートします。

Azure Cache for Redis により、お客様は Microsoft によって管理されている、セキュリティで保護された専用の Azure Cache for Redis を使用できるようになります。 このサービスでは、Redis が提供する豊富な機能セットとエコシステムを利用し、Microsoft による信頼性の高いホスティングと監視を受けられます。

キーと値のペアのみを処理する従来のキャッシュとは異なり、Redis は、データ型の性能が高いため人気があります。 また、Redis は、このようなデータに対するアトミックな操作 (文字列の付加、ハッシュ内の値のインクリメント、リストへのプッシュ、積集合、和集合、および差集合の計算、並べ替えられた集合内で最高ランクのメンバーの取得など) の実行もサポートしています。 その他の機能として、トランザクションのサポート、パブリッシュ/サブスクライブ、Lua スクリプト、有効期限が制限されたキー、Redis を従来のキャッシュのように動作させるための構成設定があります。

Redis を成功させているもう 1 つの重要な側面は、その周りに構築される健全で活発なオープン ソース エコシステムです。 また、その環境を複数の言語で使用できる多様な Redis クライアントに反映します。 このエコシステムと幅広いクライアントにより、Azure 内部に構築するほぼすべてのワークロードで Azure Cache for Redis を使用できます。

Azure Cache for Redis の使用を開始することの詳細については、「[Azure Cache for Redis の使用方法](cache-dotnet-how-to-use-azure-redis-cache.md)」と、[Azure Cache for Redis のドキュメント](index.md)に関するページを参照してください。

### <a name="managed-cache-service"></a>Managed Cache Service
[Managed Cache Service は 2016 年 11 月 30 日に終了しました。](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

アーカイブされたドキュメントを参照するには、[アーカイブされた Managed Cache Service に関するドキュメント](/previous-versions/azure/azure-services/dn386094(v=azure.100))を参照してください。

### <a name="in-role-cache"></a>In-Role Cache
[In-Role Cache は 2016 年 11 月 30 日に終了しました。](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

アーカイブされたドキュメントを参照するには、[アーカイブされた In-Role Cache に関するドキュメント](/previous-versions/azure/azure-services/dn386103(v=azure.100))を参照してください。

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
