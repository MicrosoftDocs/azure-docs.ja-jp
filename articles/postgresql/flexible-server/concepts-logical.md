---
title: 論理レプリケーションと論理デコード - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure Database for PostgreSQL - フレキシブル サーバーでの論理レプリケーションと論理デコードの使用について説明します
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b6689220873aaeb65337ba480e346e5d2c8020ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91707865"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーでの論理レプリケーションと論理デコード

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

Azure Database for PostgreSQL - フレキシブル サーバーでは、Postgres バージョン 11 に対する PostgreSQL の論理レプリケーションと論理デコードの機能がサポートされています。

## <a name="comparing-logical-replication-and-logical-decoding"></a>論理レプリケーションと論理デコードの比較
論理レプリケーションと論理デコードには、似ている点がいくつかあります。 両方とも
* Postgres からデータをレプリケートできます
* 変更のソースとして[先書きログ (WAL)](https://www.postgresql.org/docs/current/wal.html) を使用します
* データを送信するために[論理レプリケーション スロット](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)を使用します。 スロットは、変更のストリームを表します。
* テーブルの [REPLICA IDENTITY プロパティ](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY)を使用して、送信できる変更を特定します
* DDL の変更はレプリケートされません


2 つのテクノロジには次のような違いがあります。論理レプリケーション 
* レプリケートするテーブルまたはテーブルのセットを指定できます
* PostgreSQL のインスタンス間でデータをレプリケートします

論理デコード 
* データベース内のすべてのテーブルから変更を抽出します 
* PostgreSQL のインスタンス間でデータを直接送信することはできません


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>論理レプリケーションと論理デコードの前提条件

1. サーバー パラメーター `wal_level` を `logical` に設定します。
2. サーバーを再起動して `wal_level` の変更を適用します。
3. PostgreSQL のインスタンスで、接続しているリソースからのネットワーク トラフィックが許可されていることを確認します。
4. 管理者ユーザーのレプリケーションのアクセス許可を付与します。
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>論理レプリケーションと論理デコードの使用

### <a name="logical-replication"></a>論理レプリケーション
論理レプリケーションでは、"パブリッシャー" と "サブスクライバー" という用語を使用します。 
* パブリッシャーは、データの送信 **元** である PostgreSQL データベースです。 
* サブスクライバーは、データの送信 **先** である PostgreSQL データベースです。

論理レプリケーションを試すために使用できるサンプル コードを次に示します。

1. パブリッシャー データベースに接続します。 テーブルを作成し、データをいくつか追加します。
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. テーブルのパブリケーションを作成します。
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. サブスクライバー データベースに接続します。 パブリッシャーと同じスキーマでテーブルを作成します。
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. 前に作成したパブリケーションに接続するサブスクリプションを作成します。
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. これで、サブスクライバーのテーブルに対してクエリを実行できるようになります。 パブリッシャーからデータを受信したことがわかります。
   ```SQL
   SELECT * FROM basic;
   ```

パブリッシャーのテーブルに行を追加し、サブスクライバーで変更を表示することができます。

[論理レプリケーション](https://www.postgresql.org/docs/current/logical-replication.html)の詳細については、PostgreSQL のドキュメントを参照してください。

### <a name="logical-decoding"></a>論理デコード
論理デコードは、ストリーミング プロトコルまたは SQL インターフェイスを介して使用できます。 

#### <a name="streaming-protocol"></a>ストリーミング プロトコル
多くの場合、ストリーミング プロトコルを使用して変更を行うことをお勧めします。 独自のコンシューマーやコネクタを作成したり、[Debezium](https://debezium.io/) のようなサードパーティのサービスを使用したりできます。 

[pg_recvlogical によるストリーミング プロトコルの例](https://github.com/eulerto/wal2json#pg_recvlogical)については、wal2json のドキュメントを参照してください。

#### <a name="sql-interface"></a>SQL インターフェイス 
次の例では、SQL インターフェイスを wal2json プラグインと共に使用します。
 
1. スロットを作成します。
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL コマンドを実行します。 次に例を示します。
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. 変更を使用します。
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   出力は以下のようになります。
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. 使用が完了したら、スロットをドロップします。
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

[論理デコード](https://www.postgresql.org/docs/current/logicaldecoding.html)の詳細については、PostgreSQL のドキュメントを参照してください。


## <a name="monitoring"></a>監視
論理デコードは監視する必要があります。 未使用のレプリケーション スロットはすべてドロップしてください。 変更が読み取られるまで、スロットには Postgres WAL ログおよび関連するシステム カタログが保持されます。 サブスクライバーまたはコンシューマーが失敗した場合、または適切に構成されていない場合は、ログが消費されないまま蓄積され、ストレージがいっぱいになります。 また、未使用のログによって、トランザクション ID の折り返しリスクが増加します。 どちらの状況でも、サーバーを使用できなくなる可能性があります。 このため、論理レプリケーション スロットを継続的に使用することが重要になります。 論理レプリケーション スロットが使用されなくなった場合は、すぐにドロップしてください。

pg_replication_slots ビューの「active」列には、コンシューマーがスロットに接続されているかどうかが示されます。
```SQL
SELECT * FROM pg_replication_slots;
```

値が大きくなって通常のしきい値を超えたときに通知を受け取るには、フレキシブル サーバーのメトリック **Maximum Used Transaction IDs (使用されるトランザクション ID の最大数)** および **Storage Used (使用済みストレージ)** に [アラートを設定します](howto-alert-on-metrics.md)。 

## <a name="limitations"></a>制限事項
* **読み取りレプリカ** - Azure Database for PostgreSQL の読み取りレプリカは、現在のところ、フレキシブル サーバーではサポートされていません。
* **スロットと HA フェールオーバー** - プライマリ サーバー上の論理レプリケーション スロットは、セカンダリ AZ 内のスタンバイ サーバーでは使用できません。 これは、サーバーでゾーン冗長高可用性オプションが使用されている場合に当てはまります。 スタンバイ サーバーへのフェールオーバーが発生した場合、論理レプリケーション スロットは、スタンバイ上で使用可能になりません。

## <a name="next-steps"></a>次のステップ
* [ネットワーク オプション](concepts-networking.md)の詳細を確認する
* フレキシブル サーバーで使用できる[拡張機能](concepts-extensions.md)について確認する
* [高可用性](concepts-high-availability.md)の詳細を確認する

