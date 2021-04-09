---
title: 拡張機能 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) で拡張機能を使用して、データベースの機能を拡張する方法について説明します
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 000f8a1457298901dcfc94bc5e0923e94ba35dc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620904"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) での PostgreSQL 拡張機能

PostgreSQL では拡張機能を使用してデータベースの機能を拡張することができます。 拡張機能により、関連する複数の SQL オブジェクトを 1 つのパッケージにまとめて、1 つのコマンドでデータベースに読み込んだり、データベースから削除したりできます。 データベースに読み込まれた後、拡張機能は組み込み機能と同じように機能します。 PostgreSQL 拡張機能の詳細については、[拡張機能への関連オブジェクトのパッケージ化](https://www.postgresql.org/docs/current/static/extend-extensions.html)に関するページを参照してください。

## <a name="use-postgresql-extensions"></a>PostgreSQL 拡張機能を使用する

PostgreSQL 拡張機能を使用するには、その拡張機能がデータベースにインストールされている必要があります。 特定の拡張機能をインストールするには、psql ツールから [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) コマンドを実行して、パッケージ化されたオブジェクトをデータベースに読み込みます。

Azure Database for PostgreSQL - Hyperscale (Citus) で現在サポートされている主要な拡張機能のサブセットを以下に記載しています。 リストされているもの以外の拡張機能はサポートされていません。 Azure Database for PostgreSQL では、独自の拡張機能を作成することはできません。

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Azure Database for PostgreSQL でサポートされる拡張機能

Azure Database for PostgreSQL で現在サポートされている標準的な PostgreSQL 拡張機能を次の表に示します。 この情報は、`SELECT * FROM pg_available_extensions;`を実行して確認することもできます。

### <a name="data-types-extensions"></a>データ型の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張子** | **説明** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | 大文字と小文字が区別されない文字列型を提供します。 |
> | [cube](https://www.postgresql.org/docs/current/static/cube.html) | 多次元キューブのデータ型を提供します。 |
> | [hll](https://github.com/citusdata/postgresql-hll) | HyperLogLog データ構造を提供します。 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | キーと値のペアのセットを格納するデータ型を提供します。 |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | 国際対応の製品番号規格のデータ型を提供します。 |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | ラージ オブジェクトのメンテナンス。 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | 階層ツリー状の構造体のデータ型を提供します。 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | 線分または浮動小数点の間隔を表すデータ型。 |
> | [tdigest](https://github.com/tvondra/tdigest) | 分位点やトリム平均などのランクベースの統計をオンラインで集計するためのデータ型。 |
> | [topn](https://github.com/citusdata/postgresql-topn/) | トップ-N JSONB の型。 |

### <a name="full-text-search-extensions"></a>フルテキスト検索の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張子** | **説明** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | 整数に対するテキスト検索ディクショナリのテンプレートを提供します。 |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | 拡張されたシノニム処理のためのテキスト検索ディクショナリのテンプレート。 |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | 語彙からアクセント記号 (分音記号) を削除するテキスト検索辞書。 |

### <a name="functions-extensions"></a>関数の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張子** | **説明** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | 自動増分するフィールドの関数。 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | 地球の表面にある大圏距離を計算するための手段を提供します。 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | 文字列間の類似点と相違点を特定する関数を提供します。 |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | テーブルを変更したユーザーを追跡するための関数。 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | 整数のアグリゲーターと列挙子 (廃止)。 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | 整数の null を含まない配列を操作する関数と演算子を提供します。 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | 最終変更時刻を追跡するための関数。 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 時刻または ID によってパーティション テーブルを管理します。 |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | trigram 一致に基づいて英数字テキストの類似性を特定する関数と演算子を提供します。 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | 暗号化関数を提供します。 |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | 参照整合性を実装するための関数 (廃止)。 |
> | session\_analytics | hstore 配列のクエリを実行するための関数。 |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | クロス集計を含む、テーブル全体を操作する関数を提供します。 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | 変更通知をトリガーします。 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | タイム トラベルを実装するための関数。 |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | 汎用一意識別子 (UUID) を生成します。 |

### <a name="hyperscale-citus-extensions"></a>Hyperscale (Citus) の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張子** | **説明** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus 分散型データベース。 |

### <a name="index-types-extensions"></a>インデックス型の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張子** | **説明** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom アクセス メソッド - シグネチャ ファイルに基づくインデックス。 |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | 特定のデータ型に対して B ツリーのような動作を実装するサンプル GIN 演算子クラスを提供します。 |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | B ツリーを実装する GiST インデックス演算子クラスを提供します。 |

### <a name="language-extensions"></a>言語拡張機能

> [!div class="mx-tableFixed"]
> | **拡張子** | **説明** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL 読み込み可能な手続き型言語。 |

### <a name="miscellaneous-extensions"></a>その他の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張子** | **説明** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL の管理関数。 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | 関係の整合性を検証するための関数。 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | データベース セッション内から他の PostgreSQL データベースへの接続をサポートするモジュール。 この拡張機能については、「dblink and postgres_fdw」のセクションを参照してください。 |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | フラット ファイルのアクセスのための外部データ ラッパー。 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | 低レベルでデータベース ページの内容を検査します。 |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | リアルタイムで共有バッファー キャッシュの動作を確認する手段を提供します。 |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | PostgreSQL のジョブ スケジューラ。 |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | 空き領域マップ (FSM) を確認します。 |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | 関係データをバッファー キャッシュに読み込む方法を提供します。 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | サーバーで実行されるすべての SQL ステートメントの実行統計を追跡する手段を提供します。 この拡張機能については、「pg_stat_statements」のセクションを参照してください。 |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | 可視性マップ (VM) とページレベルの可視性情報を調べます。 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | 行レベルのロックに関する情報を表示するための手段を提供します。 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | タプル レベルの統計を表示するための手段を提供します。 |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | 外部 PostgreSQL サーバーに格納されているデータへのアクセスに使用される外部データ ラッパーです。 この拡張機能については、「dblink and postgres_fdw」のセクションを参照してください。|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | TLS または SSL 証明書に関する情報。 |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | 行数を制限として受け取る TABLESAMPLE メソッド。 |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | ミリ秒単位の時間を制限として受け取る TABLESAMPLE メソッド。 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath のクエリの実行と XSLT。 |


### <a name="postgis-extensions"></a>PostGIS 拡張機能

> [!div class="mx-tableFixed"]
> | **拡張子** | **説明** |
> |---|---|
> | [PostGIS](https://www.postgis.net/)、postgis\_topology、postgis\_tiger\_geocoder、postgis\_sfcgal | PostgreSQL の空間および地理なオブジェクト。 |
> | address\_standardizer、address\_standardizer\_data\_us | 構成要素へのアドレスの解析に使用されます。 ジオコーディング アドレス正規化の手順をサポートするために使用されます。 |
> | postgis\_sfcgal | PostGIS SFCGAL 関数。 |
> | postgis\_tiger\_geocoder | PostGIS Tiger ジオコーダとリバース ジオコーダ。 |
> | postgis\_topology | PostGIS トポロジの空間型と関数。 |


## <a name="pg_stat_statements"></a>pg_stat_statements
すべての Azure Database for PostgreSQL サーバーには、SQL ステートメントの実行の統計を追跡する手段として、[pg\_stat\_statements 拡張機能](https://www.postgresql.org/docs/current/pgstatstatements.html)がプリロードされています。

この拡張機能によって追跡されるステートメントは、`pg_stat_statements.track` 設定で制御されます。 既定では `top` に設定され、クライアントによって直接発行されたすべてのステートメントが追跡の対象となります。 その他の 2 つの追跡レベルは`none`と`all`です。 この設定は、[Azure portal](./howto-configure-server-parameters-using-portal.md)または[Azure CLI](./howto-configure-server-parameters-using-cli.md)を通じてサーバーのパラメーターとして構成可能です。

pg_stat_statements から得られるクエリの実行情報と、各 SQL ステートメントがログに記録されることによるサーバーのパフォーマンスへの影響との間には、トレードオフがあります。 pg_stat_statements 拡張機能を使用していない場合は、`pg_stat_statements.track` を `none` に設定することをお勧めします。 一部のサード パーティ監視サービスがクエリ パフォーマンスの分析情報を生成するために pg_stat_statements に依存することがあるため、そのようなケースに該当するかどうかを確認してください。

## <a name="dblink-and-postgres_fdw"></a>dblink および postgres_fdw

PostgreSQL サーバーから別の PostgreSQL サーバーへの接続、または同じサーバー内の別のデータベースへの接続には、dblink および postgres\_fdw を使用します。  受信側サーバーでは、ファイアウォールを経由した送信元サーバーからの接続を許可している必要があります。  Azure Database for PostgreSQL サーバーまたは Hyperscale (Citus) サーバー グループ間の接続にこれらの拡張機能を使用する場合、 **[Allow Azure services and resources to access this server group (or server)]\(Azure サービスおよびリソースにこのサーバー グループ (またはサーバー) へのアクセスを許可する\)** をオンに設定します。  拡張機能を使用して同じサーバーにループバックする場合にも、この設定を [オン] にする必要があります。
**[Allow Azure services and resources to access this server group]\(Azure サービスおよびリソースにこのサーバー グループへのアクセスを許可する\)** の設定は、Hyperscale (Citus) サーバー グループの Azure portal ページの **[ネットワーク]** の下にあります。  現時点では、Azure Database for PostgreSQL Single サーバーと Hyperscale (Citus) からのアウトバウンド接続は、他の Azure Database for PostgreSQL サーバーと Hyperscale (Citus) サーバー グループに接続する場合を除き、サポートされていません。