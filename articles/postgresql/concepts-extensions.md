---
title: Azure Database for PostgreSQL - Single Server での PostgreSQL 拡張機能の使用
description: Azure Database for PostgreSQL - Single Server で拡張機能を使用して、データベースの機能を拡張する方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 412ce3c5245f3f22bfb03740a0451670dc6a90a7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448108"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server の PostgreSQL 拡張機能
PostgreSQL では拡張機能を使用してデータベースの機能を拡張することができます。 拡張機能により、関連する複数の SQL オブジェクトを 1 つのパッケージにまとめて、1 つのコマンドでデータベースに読み込んだり、データベースから削除したりできます。 データベースに読み込まれた拡張機能は、組み込み機能と同じように動作します。 PostgreSQL 拡張機能の詳細については、「 [Packaging Related Objects into an Extension (拡張機能への関連オブジェクトのパッケージ化)](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)」をご覧ください。

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL 拡張機能の使用方法
PostgreSQL 拡張機能を使用するには、その拡張機能がデータベースにインストールされている必要があります。 特定の拡張機能をインストールするには、psql ツールから  [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html)  コマンドを実行して、パッケージ化されたオブジェクトをデータベースに読み込みます。

Azure Database for PostgreSQL で現在サポートされている主要な拡張機能のサブセットを次に示します。 ここにない拡張機能はサポートされていません。Azure Database for PostgreSQL サービスでは、独自の拡張機能を作成することはできません。

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Azure Database for PostgreSQL でサポートされる拡張機能
Azure Database for PostgreSQL で現在サポートされている標準的な PostgreSQL 拡張機能を次の表に示します。 この情報は、`SELECT * FROM pg_available_extensions;`を実行して確認することもできます。

### <a name="data-types-extensions"></a>データ型の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張機能** | **説明** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | 自動暗号化パスワードのデータ型を提供します。 |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | 大文字と小文字が区別されない文字列型を提供します。 |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | 多次元キューブのデータ型を提供します。 |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | キー/値のペアのセットを格納するデータ型を提供します。 |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | 国際対応の製品番号規格のデータ型を提供します。 |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | 階層ツリー状の構造体のデータ型を提供します。 |

### <a name="functions-extensions"></a>関数の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張機能** | **説明** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | 地球の表面にある大圏距離を計算するための手段を提供します。 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 文字列間の類似点と相違点を特定する関数を提供します。 |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | 整数の null を含まない配列を操作する関数と演算子を提供します。 |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 暗号化関数を提供します。 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 時刻または ID によってパーティション テーブルを管理します。 |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | trigram 一致に基づいて英数字テキストの類似性を特定する関数と演算子を提供します。 |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | クロス集計を含む、テーブル全体を操作する関数を提供します。 |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 汎用一意識別子 (UUID) を生成します。 (この拡張機能に関する注意事項は下記を参照)。 |
> | [orafce](https://github.com/orafce/orafce) | 商用データベースからエミュレートされている関数およびパッケージのサブセットを提供します。 |

### <a name="full-text-search-extensions"></a>フルテキスト検索の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張機能** | **説明** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | 整数に対するテキスト検索ディクショナリのテンプレートを提供します。 |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | 語彙からアクセント記号 (分音記号) を削除するテキスト検索辞書。 |

### <a name="index-types-extensions"></a>インデックス型の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張機能** | **説明** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | 特定のデータ型に対して B ツリーのような動作を実装するサンプル GIN 演算子クラスを提供します。 |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | B ツリーを実装する GiST インデックス演算子クラスを提供します。 |

### <a name="language-extensions"></a>言語の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張機能** | **説明** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL 読み込み可能な手続き型言語。 |
> | [plv8](https://plv8.github.io/) | ストアド プロシージャ、トリガーなどに使用できる PostgreSQL の Javascript 言語拡張機能。 |

### <a name="miscellaneous-extensions"></a>その他の拡張機能

> [!div class="mx-tableFixed"]
> | **拡張機能** | **説明** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | リアルタイムで共有バッファー キャッシュの動作を確認する手段を提供します。 |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 関係データをバッファー キャッシュに読み込む方法を提供します。 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | サーバーで実行されるすべての SQL ステートメントの実行統計を追跡する手段を提供します。 (この拡張機能に関する注意事項は下記を参照)。 |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 行レベルのロックに関する情報を表示するための手段を提供します。 |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | タプル レベルの統計を表示するための手段を提供します。 |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 外部 PostgreSQL サーバーに格納されているデータへのアクセスに使用される外部データ ラッパーです。 (この拡張機能に関する注意事項は下記を参照)。|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | CPU やディスク コストのない仮定のインデックスを作成する手段の提供。 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | データベース セッション内から他の PostgreSQL データベースへの接続をサポートするモジュール。 (この拡張機能に関する注意事項は下記を参照)。 |


### <a name="postgis-extensions"></a>PostGIS 拡張機能

> [!div class="mx-tableFixed"]
> | **拡張機能** | **説明** |
> |---|---|
> | [PostGIS](https://www.postgis.net/)、postgis\_topology、postgis\_tiger\_geocoder、postgis\_sfcgal | PostgreSQL の空間および地理なオブジェクト。 |
> | address\_standardizer、address\_standardizer\_data\_us | 構成要素へのアドレスの解析に使用されます。 ジオコーディング アドレス正規化の手順をサポートするために使用されます。 |
> | [pgrouting](https://pgrouting.org/) | PostGIS/PostgreSQL 地理空間データベースを拡張して、地理空間ルーティング機能を提供します。 |


### <a name="time-series-extensions"></a>時系列拡張機能

> [!div class="mx-tableFixed"]
> | **拡張機能** | **説明** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | 高速の取り込みとクエリに対応した自動パーティション分割をサポートする時系列 SQL データベース。 時間指向の分析関数、最適化を提供し、時系列ワークロードに合わせて PostgreSQL を拡大縮小します。 TimescaleDB は、[Timescale, Inc.](https://www.timescale.com/) によって開発された、この会社の登録商標です。(この拡張機能に関する注意事項は下記を参照)。 |


## <a name="pgstatstatements"></a>pg_stat_statements
SQL ステートメントの実行の統計を追跡する手段を提供するために、[Pg\_stat\_ステートメント拡張子](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html)が、すべての Azure Database for PostgreSQL サーバーにプリロードされています。
ステートメントをコントロールする設定`pg_stat_statements.track`は、拡張機能と既定値によって`top`にカウントされ、クライアントが直接発行したすべてのステートメントがすべて追跡されます。 その他の 2 つの追跡レベルは`none`と`all`です。 この設定は、[Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)または[Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)を通じてサーバーのパラメーターとして構成可能です。

各 SQL ステートメントをログに記録する時はPg_stat_statements が提供するクエリの実行情報とサーバーのパフォーマンスに与える影響にトレードオフがあります。 pg_stat_statements 拡張機能を使用していない場合、`pg_stat_statements.track`を`none`に設定することをお勧めします。 一部のサード パーティ監視サービスがクエリ パフォーマンスの分析情報を実行するために Pg_stat_statements に依存することがありますので、そのようなケースに該当するかどうかを確認してください。

## <a name="dblink-and-postgresfdw"></a>dblink および postgres_fdw
dblink および postgres_fdw を使用して、1 つの PostgreSQL サーバーから別のサーバーに、または同一サーバー内の別のデータベースに接続できます。 受信側サーバーでは、ファイアウォールを経由した送信元サーバーからの接続を許可している必要があります。 これらの拡張機能を使用して Azure Database for PostgreSQL サーバー間を接続する場合、[Azure サービスへのアクセスを許可] を [オン] に設定することで実現できます。 拡張機能を使用して同じサーバーにループバックする場合にも、これは必要になります。 [Azure サービスへのアクセスを許可] 設定は、Postgres サーバー向けの Azure portal ページ内の [接続のセキュリティ ] の下にあります。 [Azure サービスへのアクセスを許可] を [オン] にすると、すべての Azure IP がホワイトリストに登録されます。

現時点では、Azure Database for PostgreSQL からの送信接続は、他の Azure Database for PostgreSQL サーバーへの接続を除き、サポートされていません。

## <a name="uuid"></a>uuid
uuid-ossp 拡張機能の `uuid_generate_v4()` を使用する予定の場合、パフォーマンス上の利点について、pgcrypto 拡張機能の `gen_random_uuid()` と比較することを検討してください。


## <a name="timescaledb"></a>TimescaleDB
TimescaleDB は、PostgreSQL の拡張機能としてパッケージされた時系列データベースです。 TimescaleDB は、時間指向の分析関数、最適化を提供し、時系列ワークロードに合わせて PostgreSQL を拡大縮小します。

[TimescaleDB の詳細を参照](https://docs.timescale.com/latest) ([Timescale, Inc.](https://www.timescale.com/) の登録商標)

### <a name="installing-timescaledb"></a>TimescaleDB をインストールする
TimescaleDB をインストールするには、それをサーバーの共有プリロード ライブラリに含める必要があります。 Postgres の `shared_preload_libraries` パラメーターへの変更を有効にするには、**サーバーの再起動**が必要です。 [Azure portal](howto-configure-server-parameters-using-portal.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用してパラメーターを変更できます。

> [!NOTE]
> TimescaleDB は、Azure Database for PostgreSQL バージョン 9.6 および 10 で有効にできます。

[Azure portal](https://portal.azure.com/) を使用して以下を実行します。

1. Azure Database for PostgreSQL サーバーを選択します。

2. サイドバーから、 **[サーバー パラメーター]** を選択します。

3. `shared_preload_libraries` パラメーターを検索します。

4. **[TimescaleDB]** を選択します。

5. **[保存]** を選択して変更を保存します。 変更が保存されると通知を受け取ります。 

6. 通知後、サーバーを**再起動**してこれらの変更を適用します。 サーバーを再起動する方法については、[Azure Database for PostgreSQL サーバーの再起動](howto-restart-server-portal.md)に関するページを参照してください。


次に、Postgres データベースで TimescaleDB を有効にすることができます。 データベースに接続して、以下のコマンドを実行します。
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> エラーが表示される場合は、shared_preload_libraries を保存した後に[サーバーを再起動した](howto-restart-server-portal.md)ことを確認します。 

次に、[一から](https://docs.timescale.com/getting-started/creating-hypertables) TimescaleDB ハイパーテーブルを作成するか、[PostgreSQL 内の既存の時系列データ](https://docs.timescale.com/getting-started/migrating-data)を移行することができます。


## <a name="next-steps"></a>次の手順
使用する拡張機能が見つからない場合は、お知らせください。 [フィードバック フォーラム](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)で、既存のリクエストに投票することや、新しいフィードバック リクエストを作成することができます。
