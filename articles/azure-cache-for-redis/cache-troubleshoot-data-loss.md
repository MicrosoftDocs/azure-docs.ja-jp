---
title: Azure Cache for Redis でのデータ損失のトラブルシューティング
description: 部分的なキーの損失、キーの有効期限、完全なキーの損失など、Azure Cache for Redis のデータ損失に関する問題を解決する方法について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 6db036752bab7b84b72a37b148eaec7aa5765ef3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92538597"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Azure Cache for Redis でのデータ損失のトラブルシューティング

この記事では、Azure Cache for Redis で発生する可能性がある実際のデータ損失または認識されたデータ損失を診断する方法について説明します。

> [!NOTE]
> このガイドのトラブルシューティング手順のいくつかには、Redis コマンドを実行し、さまざまなパフォーマンス メトリックを監視する手順が含まれています。 詳細および手順については、「 [追加情報](#additional-information) 」セクションの記事を参照してください。
>

## <a name="partial-loss-of-keys"></a>部分的なキーの損失

Azure Cache for Redis では、キーがメモリに格納された後でランダムに削除されることはありません。 ただし、有効期限や削除のポリシー、および明示的なキーの削除コマンドに応じて、キーの削除が行われます。 Premium または Standard の Azure Cache for Redis インスタンスでプライマリ ノードに書き込まれたキーが、レプリカですぐには使用可能にならない可能性もあります。 データは、非同期および非ブロッキング方式でプライマリからレプリカにレプリケートされます。

キャッシュからキーが消失していることに気付いた場合は、次の考えられる原因を確認してください。

| 原因 | 説明 |
|---|---|
| [キーの有効期限](#key-expiration) | 設定されているタイムアウトが原因でキーが削除されます。 |
| [キーの強制削除](#key-eviction) | メモリ不足のためにキーが削除されます。 |
| [キーの削除](#key-deletion) | 明示的な削除コマンドによってキーが削除されます。 |
| [非同期レプリケーション](#async-replication) | データ レプリケーションの遅延のため、レプリカでキーが使用できません。 |

### <a name="key-expiration"></a>キーの有効期限

Azure Cache for Redis では、キーにタイムアウトが割り当てられ、その期間が経過するとキーは自動的に削除されます。 Redis キーの有効期限の詳細については、[EXPIRE](https://redis.io/commands/expire) コマンドのドキュメントを参照してください。 タイムアウト値は、[SET](https://redis.io/commands/set)、[SETEX](https://redis.io/commands/setex)、[GETSET](https://redis.io/commands/getset)、およびその他の **\*STORE** コマンドを使用して設定することもできます。

有効期限が切れたキーの数に関する統計を取得するには、[INFO](https://redis.io/commands/info) コマンドを使用します。 `Stats` セクションに、有効期限が切れたキーの総数が示されます。 `Keyspace` セクションには、タイムアウトが設定されているキーの数と、平均タイムアウト値に関する追加情報が提供されます。

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

さらに、キャッシュの診断メトリックを参照して、キーが消失したタイミングと、有効期限が切れたキーの急増との間に相関関係があるかどうかを確認することもできます。 キースペース通知や **MONITOR** を使用してこれらの種類の問題をデバッグする方法については、[Redis キースペースの消失のデバッグ](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)に関する記事の付録を参照してください。

### <a name="key-eviction"></a>キーの強制削除

Azure Cache for Redis には、データを格納するためのメモリ領域が必要です。 必要に応じて、使用可能なメモリを解放するためにキーが消去されます。 [INFO](https://redis.io/commands/info) コマンドの **used_memory** または **used_memory_rss** の値が、構成されている **maxmemory** 設定に近づくと、Azure Cache for Redis は [キャッシュ ポリシー](https://redis.io/topics/lru-cache)に基づいて、メモリからのキーの強制削除を開始します。

[INFO](https://redis.io/commands/info) コマンドを使用して、強制削除されたキーの数を監視できます。

```
# Stats

evicted_keys:13224
```

さらに、キャッシュの診断メトリックを参照して、キーが消失したタイミングと、強制削除されたキーの急増との間に相関関係があるかどうかを確認することもできます。 キースペース通知や **MONITOR** を使用してこれらの種類の問題をデバッグする方法については、[Redis キースペースの消失のデバッグ](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)に関する記事の付録を参照してください。

### <a name="key-deletion"></a>キーの削除

Redis クライアントは、[DEL](https://redis.io/commands/del) コマンドまたは [HDEL](https://redis.io/commands/hdel) コマンドを発行して、Azure Cache for Redis から明示的にキーを削除することができます。 [INFO](https://redis.io/commands/info) コマンドを使用して、削除操作の回数を追跡できます。 **DEL** コマンドまたは **HDEL** コマンドが呼び出された場合は、`Commandstats` セクションに一覧表示されます。

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>非同期レプリケーション

Standard レベルまたは Premium レベルの Azure Cache for Redis インスタンスは、プライマリ ノードと少なくとも 1 つのレプリカを使用して構成されます。 バックグラウンド プロセスを使用して、データがプライマリからレプリカに非同期的にコピーされます。 [redis.io](https://redis.io/topics/replication) Web サイトでは、Redis データ レプリケーションの一般的な動作について説明しています。 クライアントが Redis に頻繁に書き込みを行うシナリオでは、このレプリケーションが瞬時に行われることが保証されないため、部分的なデータ損失が発生する可能性があります。 たとえば、クライアントがキーをプライマリに書き込んだ "*後*"、バックグラウンド プロセスがそのキーをレプリカに送信する "*前*" にプライマリがダウンした場合、キーは、レプリカが新しいプライマリとして引き継いだときに消失します。

## <a name="major-or-complete-loss-of-keys"></a>大部分または完全なキーの損失

ほとんどまたはすべてのキーがキャッシュから消失している場合は、次の考えられる原因を確認してください。

| 原因 | 説明 |
|---|---|
| [キーのフラッシュ](#key-flushing) | キーは手動で削除されました。 |
| [データベースの選択が正しくない](#incorrect-database-selection) | Azure Cache for Redis が既定以外のデータベースを使用するように設定されています。 |
| [Redis インスタンスのエラー](#redis-instance-failure) | Redis サーバーが使用できません。 |

### <a name="key-flushing"></a>キーのフラッシュ

クライアントは、[FLUSHDB](https://redis.io/commands/flushdb) コマンドを呼び出して、*1 つの* データベース内のすべてのキーを削除することも、[FLUSHALL](https://redis.io/commands/flushall) を呼び出して Redis キャッシュ内の *すべての* データベースからすべてのキーを削除することもできます。 キーがフラッシュされているかどうかを確認するには、[INFO](https://redis.io/commands/info) コマンドを使用します。 `Commandstats` セクションには、**FLUSH** コマンドが呼び出されたかどうかが示されます。

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>データベースの選択が正しくない

Azure Cache for Redis では、既定で **db0** データベースが使用されます。 別のデータベース (たとえば、**db1**) に切り替えて、そこからキーを読み取ろうとした場合、Azure Cache for Redis ではそれらは検出されません。 各データベースは論理的に独立したユニットであり、異なるデータセットを保持しています。 [SELECT](https://redis.io/commands/select) コマンドを使用して、他の使用可能なデータベースを使用し、そのそれぞれでキーを検索してください。

### <a name="redis-instance-failure"></a>Redis インスタンスのエラー

Redis は、インメモリ データ ストアです。 データは、Redis キャッシュをホストする物理マシンまたは仮想マシン上に保持されます。 Basic レベルの Azure Cache for Redis インスタンスは、1 つの仮想マシン (VM) 上でのみ実行されます。 その VM がダウンすると、キャッシュに格納されているすべてのデータが失われます。 

Standard レベルと Premium レベルのキャッシュでは、レプリケートされた構成で 2 つの VM を使用することにより、データ損失に対してより高い回復性が提供されます。 そのようなキャッシュのプライマリ ノードで障害が発生すると、レプリカ ノードが自動的に引き継いでデータを提供します。 これらの VM は、障害用と更新用の別々のドメインに配置され、両方が同時に使用できなくなる可能性を最小限に抑えます。 ただし、それでも大規模なデータセンターの停止が発生した場合は、それらの VM が同時にダウンする可能性があります。 これらのまれなケースでは、データが失われます。

これらのインフラストラクチャ障害に対するデータの保護を強化するには、[Redis のデータ永続化](https://redis.io/topics/persistence)と [geo レプリケーション](./cache-how-to-geo-replication.md)を使用することを検討してください。

## <a name="additional-information"></a>関連情報

- [Azure Cache for Redis のサーバー側の問題に関するトラブルシューティング](cache-troubleshoot-server.md)
- [最適なサービス レベルを選択する](cache-overview.md#choosing-the-right-tier)
- [Azure Cache for Redis を監視する方法](cache-how-to-monitor.md)
- [Redis コマンドの実行方法](cache-development-faq.md#how-can-i-run-redis-commands)