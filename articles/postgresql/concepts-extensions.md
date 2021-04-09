---
title: 拡張子 - Azure Database for PostgreSQL - Single Server
description: Azure Database for PostgreSQL - Single Server での PostgreSQL 拡張機能の使用の可用性について学びます。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 78395873457f9fe53d45dfbfd94aa9ccdccd614d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92485462"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server の PostgreSQL 拡張機能
PostgreSQL では拡張機能を使用してデータベースの機能を拡張することができます。 拡張機能により、関連する複数の SQL オブジェクトを単一のパッケージにまとめて、単一のコマンドでデータベースに対する読み込みや削除を行うことができます。 データベースに読み込まれた後、拡張機能は組み込み機能と同じように機能します。

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL 拡張機能の使用方法
PostgreSQL 拡張機能を使用するには、その拡張機能がデータベースにインストールされている必要があります。 特定の拡張機能をインストールするには、psql ツールから [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) コマンドを実行して、パッケージ化されたオブジェクトをデータベースに読み込みます。

Azure Database for PostgreSQL でサポートされる主要な拡張機能のサブセットを次に示します。 この情報は、`SELECT * FROM pg_available_extensions;`を実行して確認することもできます。 ここに示した以外の拡張機能はサポートされていません。 Azure Database for PostgreSQL で独自の拡張機能を作成することはできません。

## <a name="postgres-11-extensions"></a>Postgres 11 の拡張機能

Postgres バージョン 11 を搭載した Azure Database for PostgreSQL サーバーでは、次の拡張機能を使用できます。 

> [!div class="mx-tableFixed"]
> | **拡張子**| **拡張機能のバージョン** | **説明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 構成要素へのアドレスの解析に使用されます。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US データセットの例|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | GIN で一般的なデータ型のインデックスを作成するためのサポート|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | GiST で一般的なデータ型のインデックスを作成するためのサポート|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 大文字と小文字を区別しない文字列のデータ型|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 多次元キューブのデータ型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | データベース内から他の PostgreSQL データベースに接続する|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 整数のテキスト検索辞書テンプレート|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 地表面上の大圏距離を計算する|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 文字列間の類似点と相違点を特定する|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | (キー、値) ペアのセットを格納するためのデータ型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | PostgreSQL の仮定のインデックス|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 整数の 1 次元配列に対する関数、演算子、およびインデックスのサポート|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 国際対応の製品番号規格のデータ型|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 階層ツリー状の構造体のデータ型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 商用 RDBMS から関数とパッケージのサブセットをエミュレートする関数と演算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 監査機能を提供する|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 暗号化関数|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting の拡張機能|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 行レベルのロック情報を表示する|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | タプルレベルの統計情報を表示する|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 共有バッファー キャッシュを確認する|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | 時刻または ID によってパーティション テーブルを管理するための拡張機能|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | 関係データをプレウォームする|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 実行されたすべての SQL ステートメントの実行統計情報を追跡する|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | trigram に基づくテキストの類似性の測定とインデックス検索|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 手続き型言語|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) の信頼された手続き型言語|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS ジオメトリ、地理、およびラスターの空間型と関数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 関数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger ジオコーダとリバース ジオコーダ|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS トポロジの空間型と関数|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | リモート PostgreSQL サーバー用の外部データ ラッパー|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | クロス集計を含む、テーブル全体を操作する関数|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | 時系列データに対するスケーラブルな挿入と複雑なクエリを可能にします|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | アクセントを削除するテキスト検索辞書|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | 汎用一意識別子 (UUID) を生成する|

## <a name="postgres-10-extensions"></a>Postgres 10 の拡張機能 

Postgres バージョン 10 を搭載した Azure Database for PostgreSQL サーバーでは、次の拡張機能を使用できます。

> [!div class="mx-tableFixed"]
> | **拡張子**| **拡張機能のバージョン** | **説明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 構成要素へのアドレスの解析に使用されます。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US データセットの例|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | GIN で一般的なデータ型のインデックスを作成するためのサポート|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | GiST で一般的なデータ型のインデックスを作成するためのサポート|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | 自動暗号化パスワードのデータ型|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | 大文字と小文字を区別しない文字列のデータ型|
> |[cube](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | 多次元キューブのデータ型|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | データベース内から他の PostgreSQL データベースに接続する|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | 整数のテキスト検索辞書テンプレート|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | 地表面上の大圏距離を計算する|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | 文字列間の類似点と相違点を特定する|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | (キー、値) ペアのセットを格納するためのデータ型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL の仮定のインデックス|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | 整数の 1 次元配列に対する関数、演算子、およびインデックスのサポート|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | 国際対応の製品番号規格のデータ型|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | 階層ツリー状の構造体のデータ型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 商用 RDBMS から関数とパッケージのサブセットをエミュレートする関数と演算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | 監査機能を提供する|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | 暗号化関数|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting の拡張機能|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | 行レベルのロック情報を表示する|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | タプルレベルの統計情報を表示する|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | 共有バッファー キャッシュを確認する|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 時刻または ID によってパーティション テーブルを管理するための拡張機能|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | 関係データをプレウォームする|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | 実行されたすべての SQL ステートメントの実行統計情報を追跡する|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | trigram に基づくテキストの類似性の測定とインデックス検索|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL 手続き型言語|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) の信頼された手続き型言語|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS ジオメトリ、地理、およびラスターの空間型と関数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL 関数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS Tiger ジオコーダとリバース ジオコーダ|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS トポロジの空間型と関数|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | リモート PostgreSQL サーバー用の外部データ ラッパー|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | クロス集計を含む、テーブル全体を操作する関数|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 時系列データに対するスケーラブルな挿入と複雑なクエリを可能にします|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | アクセントを削除するテキスト検索辞書|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | 汎用一意識別子 (UUID) を生成する|

## <a name="postgres-96-extensions"></a>Postgres 9.6 の拡張機能 

Postgres バージョン 9.6 を搭載した Azure Database for PostgreSQL サーバーでは、次の拡張機能を使用できます。

> [!div class="mx-tableFixed"]
> | **拡張子**| **拡張機能のバージョン** | **説明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | 構成要素へのアドレスの解析に使用されます。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Address Standardizer US データセットの例|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | GIN で一般的なデータ型のインデックスを作成するためのサポート|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | GiST で一般的なデータ型のインデックスを作成するためのサポート|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | 自動暗号化パスワードのデータ型|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | 大文字と小文字を区別しない文字列のデータ型|
> |[cube](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | 多次元キューブのデータ型|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | データベース内から他の PostgreSQL データベースに接続する|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | 整数のテキスト検索辞書テンプレート|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | 地表面上の大圏距離を計算する|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | 文字列間の類似点と相違点を特定する|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | (キー、値) ペアのセットを格納するためのデータ型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL の仮定のインデックス|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | 整数の 1 次元配列に対する関数、演算子、およびインデックスのサポート|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | 国際対応の製品番号規格のデータ型|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | 階層ツリー状の構造体のデータ型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 商用 RDBMS から関数とパッケージのサブセットをエミュレートする関数と演算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | 監査機能を提供する|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | 暗号化関数|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting の拡張機能|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | 行レベルのロック情報を表示する|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | タプルレベルの統計情報を表示する|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | 共有バッファー キャッシュを確認する|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 時刻または ID によってパーティション テーブルを管理するための拡張機能|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | 関係データをプレウォームする|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | 実行されたすべての SQL ステートメントの実行統計情報を追跡する|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | trigram に基づくテキストの類似性の測定とインデックス検索|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL 手続き型言語|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) の信頼された手続き型言語|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS ジオメトリ、地理、およびラスターの空間型と関数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL 関数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS Tiger ジオコーダとリバース ジオコーダ|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS トポロジの空間型と関数|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | リモート PostgreSQL サーバー用の外部データ ラッパー|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | クロス集計を含む、テーブル全体を操作する関数|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 時系列データに対するスケーラブルな挿入と複雑なクエリを可能にします|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | アクセントを削除するテキスト検索辞書|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | 汎用一意識別子 (UUID) を生成する|

## <a name="postgres-95-extensions"></a>Postgres 9.5 の拡張機能 

Postgres バージョン 9.5 を搭載した Azure Database for PostgreSQL サーバーでは、次の拡張機能を使用できます。

> [!div class="mx-tableFixed"]
> | **拡張子**| **拡張機能のバージョン** | **説明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | 構成要素へのアドレスの解析に使用されます。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Address Standardizer US データセットの例|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | GIN で一般的なデータ型のインデックスを作成するためのサポート|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | GiST で一般的なデータ型のインデックスを作成するためのサポート|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | 自動暗号化パスワードのデータ型|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | 大文字と小文字を区別しない文字列のデータ型|
> |[cube](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | 多次元キューブのデータ型|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | データベース内から他の PostgreSQL データベースに接続する|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | 整数のテキスト検索辞書テンプレート|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | 地表面上の大圏距離を計算する|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | 文字列間の類似点と相違点を特定する|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | (キー、値) ペアのセットを格納するためのデータ型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL の仮定のインデックス|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | 整数の 1 次元配列に対する関数、演算子、およびインデックスのサポート|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | 国際対応の製品番号規格のデータ型|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | 階層ツリー状の構造体のデータ型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 商用 RDBMS から関数とパッケージのサブセットをエミュレートする関数と演算子|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | 監査機能を提供する|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | 暗号化関数|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting の拡張機能|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | 行レベルのロック情報を表示する|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | タプルレベルの統計情報を表示する|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | 共有バッファー キャッシュを確認する|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 時刻または ID によってパーティション テーブルを管理するための拡張機能|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | 関係データをプレウォームする|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | 実行されたすべての SQL ステートメントの実行統計情報を追跡する|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | trigram に基づくテキストの類似性の測定とインデックス検索|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL 手続き型言語|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS ジオメトリ、地理、およびラスターの空間型と関数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL 関数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS Tiger ジオコーダとリバース ジオコーダ|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS トポロジの空間型と関数|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | リモート PostgreSQL サーバー用の外部データ ラッパー|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | クロス集計を含む、テーブル全体を操作する関数|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | アクセントを削除するテキスト検索辞書|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | 汎用一意識別子 (UUID) を生成する|


## <a name="pg_stat_statements"></a>pg_stat_statements
[pg_stat_statements 拡張機能](https://www.postgresql.org/docs/current/pgstatstatements.html)は、すべての Azure Database for PostgreSQL サーバーにプリロードされ、SQL ステートメントの実行統計情報を追跡する手段が提供されています。
ステートメントをコントロールする設定`pg_stat_statements.track`は、拡張機能と既定値によって`top`にカウントされ、クライアントが直接発行したすべてのステートメントがすべて追跡されます。 その他の 2 つの追跡レベルは`none`と`all`です。 この設定は、[Azure portal](./howto-configure-server-parameters-using-portal.md)または[Azure CLI](./howto-configure-server-parameters-using-cli.md)を通じてサーバーのパラメーターとして構成可能です。

各 SQL ステートメントをログに記録する時はPg_stat_statements が提供するクエリの実行情報とサーバーのパフォーマンスに与える影響にトレードオフがあります。 pg_stat_statements 拡張機能を使用していない場合、`pg_stat_statements.track`を`none`に設定することをお勧めします。 一部のサード パーティ監視サービスがクエリ パフォーマンスの分析情報を実行するために Pg_stat_statements に依存することがありますので、そのようなケースに該当するかどうかを確認してください。

## <a name="dblink-and-postgres_fdw"></a>dblink および postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) および [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) を使用して、1 つの PostgreSQL サーバーから別のサーバーに、または同一サーバー内の別のデータベースに接続できます。 受信側サーバーでは、ファイアウォールを経由した送信元サーバーからの接続を許可している必要があります。 これらの拡張機能を使用して Azure Database for PostgreSQL サーバー間を接続する場合、[Azure サービスへのアクセスを許可] を [オン] に設定することで実現できます。 拡張機能を使用して同じサーバーにループバックする場合にも、これは必要になります。 [Azure サービスへのアクセスを許可] 設定は、Postgres サーバー向けの Azure portal ページ内の [接続のセキュリティ ] の下にあります。 [Azure サービスへのアクセスを許可] を [オン] にすると、すべての Azure IP が許可リストに設定されます。

現時点では、Azure Database for PostgreSQL からの送信接続は、同じリージョンにある他の Azure Database for PostgreSQL サーバーへの接続を除き、サポートされていません。

## <a name="uuid"></a>uuid
[uuid-ossp 拡張機能](https://www.postgresql.org/docs/current/uuid-ossp.html)の `uuid_generate_v4()` を使用する予定の場合、パフォーマンス上の利点について、[pgcrypto 拡張機能](https://www.postgresql.org/docs/current/pgcrypto.html)の `gen_random_uuid()` と比較することを検討してください。

## <a name="pgaudit"></a>pgAudit
[pgAudit 拡張機能](https://github.com/pgaudit/pgaudit/blob/master/README.md)では、セッションとオブジェクトの監査ログが提供されます。 Azure Database for PostgreSQL でこの拡張機能を使用する方法については、[監査の概念に関する記事](concepts-audit.md)を参照してください。 

## <a name="pg_prewarm"></a>pg_prewarm
pg_prewarm 拡張機能により、リレーショナル データがキャッシュに読み込まれます。 キャッシュをプレウォームすると、再起動後に最初に実行したときのクエリの応答時間が向上します。 Postgres 10 以降では、プレウォームは [prewarm 関数](https://www.postgresql.org/docs/10/pgprewarm.html)を使用して手動で実行されます。

Postgres 11 以降では、プレウォームが[自動的に](https://www.postgresql.org/docs/current/pgprewarm.html)発生するように構成することができます。 `shared_preload_libraries` パラメーターの一覧に pg_prewarm を含め、サーバーを再起動して変更を適用する必要があります。 パラメーターは、[Azure portal](howto-configure-server-parameters-using-portal.md)、[CLI](howto-configure-server-parameters-using-cli.md)、REST API、または ARM テンプレートから設定できます。 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB は、PostgreSQL の拡張機能としてパッケージされた時系列データベースです。 TimescaleDB は、時間指向の分析関数、最適化を提供し、時系列ワークロードに合わせて PostgreSQL を拡大縮小します。

[Timescale, Inc.](https://www.timescale.com/)の登録商標である[TimescaleDBの詳細をご覧ください](https://docs.timescale.com/latest)。 Azure Database for PostgreSQL には、TimescaleDB [Apache-2 エディション](https://www.timescale.com/legal/licenses)が用意されています。

### <a name="installing-timescaledb"></a>TimescaleDB をインストールする
TimescaleDB をインストールするには、それをサーバーの共有プリロード ライブラリに含める必要があります。 Postgres の `shared_preload_libraries` パラメーターへの変更を有効にするには、**サーバーの再起動** が必要です。 [Azure portal](howto-configure-server-parameters-using-portal.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用してパラメーターを変更できます。

[Azure portal](https://portal.azure.com/) を使用して以下を実行します。

1. Azure Database for PostgreSQL サーバーを選択します。

2. サイドバーから、 **[サーバー パラメーター]** を選択します。

3. `shared_preload_libraries` パラメーターを検索します。

4. **[TimescaleDB]** を選択します。

5. **[保存]** を選択して変更を保存します。 変更が保存されると通知を受け取ります。 

6. 通知後、サーバーを **再起動** してこれらの変更を適用します。 サーバーを再起動する方法については、[Azure Database for PostgreSQL サーバーの再起動](howto-restart-server-portal.md)に関するページを参照してください。


次に、Postgres データベースで TimescaleDB を有効にすることができます。 データベースに接続して、以下のコマンドを実行します。
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> エラーが表示される場合は、shared_preload_libraries を保存した後に[サーバーを再起動した](howto-restart-server-portal.md)ことを確認します。 

次に、[一から](https://docs.timescale.com/getting-started/creating-hypertables) TimescaleDB ハイパーテーブルを作成するか、[PostgreSQL 内の既存の時系列データ](https://docs.timescale.com/getting-started/migrating-data)を移行することができます。

### <a name="restoring-a-timescale-database"></a>タイムスケールデータベースを復元する
Pg_dump と pg_restore を使用してタイムスケールデータベースを復元するには、次の2つのヘルパープロシージャをデータベースで実行する必要があります：`timescaledb_pre_restore()` と `timescaledb_post restore()`。

まず、コピー先データベースを指定します：

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

これで、元のデータベースで pg_dump を実行し、pg_restore を実行することができます。 復元後、復元されたデータベースで、必ず次のコマンドを実行します：

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>次のステップ
使用する拡張機能が見つからない場合は、お知らせください。 [フィードバック フォーラム](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)で、既存のリクエストに投票することや、新しいフィードバック リクエストを作成することができます。