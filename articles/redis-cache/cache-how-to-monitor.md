---
title: "Azure Redis Cache を監視する方法 | Microsoft Docs"
description: "Azure Redis Cache のインスタンスの正常性とパフォーマンスを監視する方法を学習する"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: a1c7afab747b917ae979a41e63739a4f726265fc
ms.contentlocale: ja-jp
ms.lasthandoff: 05/27/2017

---
# <a name="how-to-monitor-azure-redis-cache"></a>Azure Redis Cache の監視方法
Azure Redis Cache は [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) を使用して、キャッシュのインスタンスを監視するためのオプションを提供します。 メトリックの表示、メトリック グラフのスタート画面へのピン留め、監視グラフの日付と時刻の範囲のカスタマイズ、グラフのメトリックの追加と削除、特定の条件が満たされた場合のアラートの設定を行うことができます。 これらのツールによって、Azure Redis Cache インスタンスの正常性を監視し、キャッシュ アプリケーションを管理できます。

Azure Redis Cache インスタンスのメトリックが Redis [INFO](http://redis.io/commands/info) コマンドを約 1 分に 2 回使用して収集され、自動的に 30 日間格納されるため (異なる保持ポリシーを構成するには[キャッシュ メトリックのエクスポート](#export-cache-metrics)に関する記事をご覧ください)、それらをメトリック グラフで表示してアラート ルールで評価できます。 各キャッシュ メトリックで使用される各種 INFO コマンドの詳細については、「 [使用可能なメトリックとレポート期間](#available-metrics-and-reporting-intervals)」を参照してください。

<a name="view-cache-metrics"></a>

キャッシュ メトリックを確認するには、[Azure Portal](https://portal.azure.com) で対象のキャッシュ インスタンスに[移動](cache-configure.md#configure-redis-cache-settings)します。  Azure Redis Cache の **[概要]** ブレードと **[Redis メトリック]** ブレードには組み込みのグラフがいくつか用意されています。 各グラフは、メトリックの追加や削除、レポート期間の変更など、カスタマイズすることができます。

![[Redis メトリック]](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>事前に構成されているメトリック グラフを表示する

**[概要]** ブレードには、事前に構成されている次の監視グラフが用意されています。

* [監視グラフ](#monitoring-charts)
* [使用状況グラフ](#usage-charts)

### <a name="monitoring-charts"></a>監視グラフ
**[概要]** ブレードの **[監視]** セクションには、**[ヒット数とミス数]**、**[取得数と設定数]**、**[接続数]**、および **[コマンド合計数]** のグラフがあります。

![監視グラフ](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>使用状況グラフ
**[概要]** ブレードの **[使用状況]** セクションには、**[Redis サーバーの負荷]**、**[メモリ使用量]**、**[ネットワーク帯域幅]**、および **[CPU 使用率]** のグラフのほか、キャッシュ インスタンスの **[価格レベル]** も表示されます。

![使用状況グラフ](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**[価格レベル]** にはキャッシュの価格レベルが表示され、キャッシュを別の価格レベルに [スケーリング](cache-how-to-scale.md) するために使用できます。

## <a name="view-metrics-with-azure-monitor"></a>Azure Monitor でメトリックを表示する
Azure Monitor を使用して Redis のメトリックを表示し、カスタム グラフを作成するには、**[リソース] メニュー**で **[メトリック]** をクリックし、目的のメトリック、レポート間隔、グラフの種類などを使用してグラフをカスタマイズします。

![[Redis メトリック]](./media/cache-how-to-monitor/redis-cache-monitor.png)

Azure Monitor を使用してメトリックを操作する方法について詳しくは、「[Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)」をご覧ください。

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>キャッシュ メトリックをエクスポートする
既定では、Azure Monitor のキャッシュ メトリックは [30 日間格納](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive)され、その後削除されます。 キャッシュ メトリックを 30 日を超えて保持するには、[ストレージ アカウントを指定](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)し、対象のキャッシュ メトリックの**[リテンション期間 (日数)]** ポリシーを指定します。 

対象のキャッシュ メトリックのストレージ アカウントを構成するには:

1. **[Redis キャッシュ]** ブレードの **[リソース] メニュー**から **[診断]** をクリックします。
2. **[オン]** をクリックします。
3. **[ストレージ アカウントへのアーカイブ]** をオンにします。
4. キャッシュ メトリックを格納するストレージ アカウントを選択します。
5. **[1 分]** チェック ボックスをオンにして **[リテンション期間 (日数)]** ポリシーを指定します。 リテンション期間ポリシーを適用せず、データを永続的に保持する場合は、**[リテンション期間 (日数)]** を **0** に設定します。
6. [ **Save**] をクリックします。

![Redis 診断](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>キャッシュ メトリックをストレージにアーカイブする以外に、[イベント ハブにストリーミングしたり、Log Analytics に送信したり](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics)できます。
>
>

メトリックにアクセスするには、この記事に説明されているようにメトリックを Azure Portal に表示するか、[Azure Monitor Metrics REST API](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api) を使用してアクセスすることもできます。

> [!NOTE]
> ストレージ アカウントを変更すると、以前に構成されたストレージ アカウント内のデータは引き続きダウンロードできますが、Azure ポータルには表示されなくなります。  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>使用可能なメトリックとレポート期間
キャッシュ メトリックは、**[過去 1 時間]**、**[今日]**、**[過去 1 週間]**、**[カスタム]** などの期間でレポートが作成されます。 各メトリック グラフの **[メトリック]** ブレードには、グラフ内の各メトリックの平均値、最小値、および最大値が表示され、一部のメトリックでは指定のレポート期間における合計が表示されます。 

各メトリックには 2 つのバージョンがあります。 1 つ目のメトリックは、キャッシュ全体と、[クラスタリング](cache-how-to-premium-clustering.md)を使用するキャッシュのパフォーマンスを測定し、名前に `(Shard 0-9)` が含まれる 2 つ目のバージョンのメトリックは、キャッシュ内の 1 つのシャードのパフォーマンスを測定します。 たとえば、キャッシュに 4 つのシャードがある場合、`Cache Hits` はキャッシュ全体の総ヒット数で、`Cache Hits (Shard 3)` はキャッシュの当該シャードのヒット数のみとなります。

> [!NOTE]
> キャッシュがアイドル状態で、アクティブなクライアント アプリケーションに接続されていない場合でも、接続されているクライアント、メモリの使用状況、実行中の操作などのキャッシュ アクティビティが表示されることがあります。 これは Azure Redis Cache インスタンスの処理中に普通に発生するアクティビティです。
> 
> 

| [メトリック] | Description |
| --- | --- |
| キャッシュ ヒット数 |指定したレポート期間中に、成功したキー検索の数。 これは Redis [INFO](http://redis.io/commands/info) コマンドの `keyspace_hits` にマッピングされます。 |
| キャッシュ ミス数 |指定したレポート期間中に、失敗したキー検索の数。 これは Redis INFO コマンドの `keyspace_misses` にマッピングされます。 キャッシュ ミスは必ずしもキャッシュに問題があるということではありません。 たとえば、キャッシュ アサイド プログラミング パターンを使用する場合、アプリケーションはまず項目をキャッシュから検索します。 項目が見つからなかった (キャッシュ ミス) 場合は、データベースから項目を取得して、次回使用するためにキャッシュに追加されます。 キャッシュ ミスは、キャッシュ アサイド プログラミング パターンでは普通の動作です。 キャッシュ ミスの数が予想よりも大きい場合は、キャッシュにデータを入力またはキャッシュからデータを読み取るアプリケーション ロジックを確認してください。 メモリ不足のためにキャッシュからアイテムが削除される場合はキャッシュ ミスとしてカウントされますが、メモリの負荷の監視には `Used Memory` または `Evicted Keys` のメトリックが適しています。 |
| 接続されているクライアント数 |指定したレポート期間中に、キャッシュに接続されるクライアントの数。 これは Redis INFO コマンドの `connected_clients` にマッピングされます。 [接続の上限](cache-configure.md#default-redis-server-configuration) に達すると、それ以降のキャッシュへの接続の試行は失敗します。 アクティブなクライアント アプリケーションがない場合でも、内部処理や接続によって接続されたクライアントのインスタンスが少数見つかることがあります。 |
| 削除されたキー数 |指定したレポート期間中に、 `maxmemory` の制限によってキャッシュから削除された項目の数。 これは Redis INFO コマンドの `evicted_keys` にマッピングされます。 |
| 期限切れキー数 |指定したレポート期間中に、期限が切れたキャッシュの項目の数。 この値は Redis INFO コマンドの `expired_keys` にマッピングされます。 |
| 合計キー数  | 過去のレポート期間におけるキャッシュ内のキーの最大数。 これは Redis INFO コマンドの `keyspace` にマッピングされます。 基礎となるメトリック システムの制限により、クラスタリングが有効になっているキャッシュでキー合計が返すキーの最大数は、レポート期間中にキーの数が最大のシャードのキーの最大数になります。  |
| 取得数 |指定したレポート期間中に、キャッシュから実行された取得操作の数。 この値は、Redis INFO のすべてのコマンド (`cmdstat_get`、`cmdstat_hget`、`cmdstat_hgetall`、`cmdstat_hmget`、`cmdstat_mget`、`cmdstat_getbit`、および `cmdstat_getrange`) からの値の合計で、レポート期間中のキャッシュ ヒット数とキャッシュ ミス数の合計と同じです。 |
| Redis サーバーの負荷 |Redis サーバーの処理がビジーで、アイドル状態でメッセージを待機していないサイクルの割合。 このカウンタが 100 に達すると、Redis サーバーのパフォーマンスが上限に達したことを意味し、これ以上 CPU を高速で処理できないことを表します。 Redis サーバーの負荷が高い場合は、クライアントでタイムアウトの例外が表示されます。 この場合は、スケールアップまたはデータを複数のキャッシュにパーティション分割することを検討してください。 |
| 設定数 |指定したレポート期間中に、キャッシュから実行された設定操作の数。 この値は、Redis INFO のすべてのコマンド (`cmdstat_set`、`cmdstat_hset`、`cmdstat_hmset`、`cmdstat_hsetnx`、`cmdstat_lset`、`cmdstat_mset`、`cmdstat_msetnx`、`cmdstat_setbit`、`cmdstat_setex`、`cmdstat_setrange`、および `cmdstat_setnx`) からの値の合計です。 |
| 合計処理数 |指定したレポート期間中に、キャッシュ サーバーによって処理されたコマンドの合計数。 この値は Redis INFO コマンドの `total_commands_processed` にマッピングされます。 Azure Redis Cache をパブリッシュ/サブスクライブにのみ使用する場合、`Cache Hits`、`Cache Misses`、`Gets`、または `Sets` のメトリックは存在しませんが、パブリッシュ/サブスクライブ処理のキャッシュの使用状況を反映する `Total Operations` メトリックが用意されています。 |
| メモリ使用量 |指定したレポート期間中にキャッシュ内のキー/値のペアで使用されるキャッシュ メモリの量 (MB)。 この値は Redis INFO コマンドの `used_memory` にマッピングされます。 これには、メタデータや断片化は含まれません。 |
| RSS メモリ使用量 |指定したレポート期間中に使用されるキャッシュ メモリの量 (MB)。これには断片化とメタデータが含まれます。 この値は Redis INFO コマンドの `used_memory_rss` にマッピングされます。 |
| CPU |指定したレポート期間中に Azure Redis Cache で使用される CPU の割合。 この値は、オペレーティング システム `\Processor(_Total)\% Processor Time` パフォーマンス カウンターにマッピングされます。 |
| キャッシュの読み取り |指定したレポート期間中にキャッシュから読み取られた、メガバイト単位での 1 秒あたりのデータ量 (MB/秒)。 この値は、キャッシュをホストする仮想マシンをサポートするネットワーク インターフェイス カードから派生し、Redis 固有のものではありません。 **この値は、このキャッシュで使用されるネットワーク帯域幅に対応しています。サーバー側のネットワーク帯域幅の制限に対してアラートを設定する場合は、この `Cache Read` カウンターを使用してアラートを作成します。キャッシュのさまざまな価格レベルとサイズで観測された帯域幅の制限値については、[こちらの表](cache-faq.md#cache-performance)を参照してください。** |
| キャッシュの書き込み |指定したレポート期間中にキャッシュに書き込まれた、メガバイト単位での 1 秒あたりのデータ量 (MB/秒)。 この値は、キャッシュをホストする仮想マシンをサポートするネットワーク インターフェイス カードから派生し、Redis 固有のものではありません。 この値は、クライアントからキャッシュに送信されるデータのネットワーク帯域幅に対応しています。 |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>アラート
メトリックとアクティビティ ログに基づいてアラートを受け取るように設定できます。 Azure Monitor では、アラートがトリガーされたときに次の処理を実行するように構成することができます。

* 電子メール通知を送信する
* Webhook を呼び出す
* Azure Logic App を呼び出す

対象のキャッシュのアラート ルールを構成するには、**[リソース] メニュー**で **[アラート ルール]** をクリックします。

![監視](./media/cache-how-to-monitor/redis-cache-monitoring.png)

アラートを構成して使用する方法について詳しくは、[アラートの概要](../monitoring-and-diagnostics/insights-alerts-portal.md)に関する記事をご覧ください。

## <a name="activity-logs"></a>アクティビティ ログ
アクティビティ ログは、Azure Redis Cache インスタンスで実行された操作に関する情報を提供します。 以前は "監査ログ" や "操作ログ" と呼ばれていました。 アクティビティ ログを使用すると、Azure Redis Cache インスタンスで発生した書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。 

> [!NOTE]
> アクティビティ ログには、読み取り (GET) 操作は含まれません。
>
>

対象のキャッシュのアクティビティ ログを表示するには、**[リソース] メニュー**で **[アクティビティ ログ]** をクリックします。

アクティビティ ログについて詳しくは、「[Azure アクティビティ ログの概要](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)」をご覧ください。












