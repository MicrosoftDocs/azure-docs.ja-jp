---
title: PostgreSQL 拡張機能を使用する
description: PostgreSQL 拡張機能を使用する
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e247e372237572586e5a4647d24d9ed6067ea823
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104949789"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループで PostgreSQL 拡張機能を使用する

PostgreSQL は、拡張機能で使用する場合に最適です。 実際、独自の Hyperscale 機能の主要素は、Microsoft 提供の `citus` 拡張機能です。これは既定でインストールされ、Postgres で、複数ノード間のデータの透過的なシャード化を可能にします。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>サポートされる拡張機能
標準の [`contrib`](https://www.postgresql.org/docs/12/contrib.html) 拡張機能と以下の拡張機能は、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのコンテナーに既にデプロイされています。
- [`citus`](https://github.com/citusdata/citus)、v: 9.4。 [Citus Data](https://www.citusdata.com/) の Citus 拡張機能は、PostgreSQL エンジンに Hyperscale 機能を提供するものであるため、既定で読み込まれます。 Azure Arc PostgreSQL Hyperscale サーバーグループからの Citus 拡張機能の削除は、サポートされていません。
- [`pg_cron`](https://github.com/citusdata/pg_cron)、v: 1.2
- [`pgaudit`](https://www.pgaudit.org/)、v: 1.4
- plpgsql、v: 1.0
- [`postgis`](https://postgis.net)、v: 3.0.2
- [`plv8`](https://plv8.github.io/)、v: 2.3.14

時間が経過して変化があったときには、この一覧に対する更新が投稿されます。

> [!IMPORTANT]
> サーバー グループに上記以外の拡張機能を導入することもできますが、このプレビューではシステムに機能が存続しません。 つまり、システムの再起動後にこれは使用できなくなり、再度導入する必要が生じます。

このガイドでは、次の 2 つの拡張機能を使用するシナリオについて説明します。
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>shared_preload_libraries への追加と作成が必要な拡張機能はどれか

|Extensions   |shared_preload_libraries に追加する必要がある  |作成する必要がある |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |いいえ       |はい        |
|`pg_audit`     |はい       |はい        |
|`plpgsql`      |はい       |はい        |
|`postgis`      |いいえ       |はい        |
|`plv8`      |いいえ       |はい        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>拡張機能を shared_preload_libraries に追加する
shared_preload_libraries の詳細については、[こちら](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES)の PostgreSQL ドキュメントを参照してください。
- この手順は、`contrib` に含まれる拡張機能については必要ありません
- この手順は、shared_preload_libraries で事前に読み込む必要がない拡張機能については必要ありません。 これらの拡張機能については、次の段落「[拡張機能の作成](#create-extensions)」を飛ばし進むことができます。

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>サーバー グループの作成時に拡張機能を追加する
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>既に存在するインスタンスに拡張機能を追加する
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>shared_preload_libraries に追加された拡張機能の一覧を表示する
次のコマンドのいずれかを実行します。

### <a name="with-an-azdata-cli-command"></a>azdata CLI コマンドを使用する
```console
azdata arc postgres server show -n <server group name>
```
出力をスクロールし、サーバー グループの仕様の engine\extensions セクションを確認します。 次に例を示します。
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>kubectl を使用する
```console
kubectl describe postgresql-12s/postgres02
```
出力をスクロールし、サーバー グループの仕様の engine\extensions セクションを確認します。 次に例を示します。
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>拡張機能の作成
任意のクライアント ツールを使用してサーバー グループに接続し、標準の PostgreSQL クエリを実行します。
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>作成された拡張機能の一覧を表示する
任意のクライアント ツールを使用してサーバー グループに接続し、標準の PostgreSQL クエリを実行します。
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>拡張機能を削除する
任意のクライアント ツールを使用してサーバー グループに接続し、標準の PostgreSQL クエリを実行します。
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>`PostGIS` 拡張機能
`PostGIS` 拡張機能を `shared_preload_libraries` に追加する必要はありません。
MIT の Department of Urban Studies & Planning から[サンプル データ](http://duspviz.mit.edu/tutorials/intro-postgis/)を取得します。 必要に応じて、`apt-get install unzip` を実行して unzip をインストールします。

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

データベースに接続し、`PostGIS` 拡張機能を作成しましょう。

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> `postgis` パッケージ (`postgis_raster`、`postgis_topology`、`postgis_sfcgal`、`fuzzystrmatch` など) の拡張機能のいずれかを使用する場合は、PostGIS 拡張機能を最初に作成してから、その他の拡張機能を作成する必要があります。 例: `CREATE EXTENSION postgis`、`CREATE EXTENSION postgis_raster`。

スキーマを作成します。

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

ここでは、coffee_shops テーブルを分散させることで、`PostGIS` をスケールアウト機能と組み合わせることができます。

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

データを読み込みましょう。

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

`geom` フィールドに、`PostGIS` `geometry` データ型の適切にエンコードされた緯度と経度を入力します。

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

これで、MIT に最も近いコーヒー ショップ (77 Massachusetts Ave at 42.359055, -71.093500) を一覧表示できるようになりました。

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>`pg_cron` 拡張機能

ここでは、`pg_cron` を shared_preload_libraries に追加することで、それを PostgreSQL サーバー グループ上で有効にしましょう。

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

サーバー グループは、拡張機能のインストールを完了するために再起動します。 これには 2 ～ 3 分かかることがあります。

これでもう一度接続し、`pg_cron` 拡張機能を作成することができます。

```sql
CREATE EXTENSION pg_cron;
```

テスト目的で、先ほどの `coffee_shops` テーブルからランダムな名前を取得するテーブル `the_best_coffee_shop` を作成し、テーブルの内容を挿入しましょう。

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

`cron.schedule` といくつかの SQL ステートメントを使用して、ランダムなテーブル名を取得 (分散クエリの結果を格納するために一時テーブルを使用することに注意) し、`the_best_coffee_shop` に格納できます。

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

これで、1 分ごとに、別の名前を取得するようになります。

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

構文の詳細については、[pg_cron README](https://github.com/citusdata/pg_cron) を参照してください。


## <a name="next-steps"></a>次のステップ
- [`plv8`](https://plv8.github.io/) に関するドキュメントを読む
- [`PostGIS`](https://postgis.net/) に関するドキュメントを読む
- [`pg_cron`](https://github.com/citusdata/pg_cron) に関するドキュメントを読む