---
title: 論理デコード - Azure Database for PostgreSQL - Single Server
description: Azure Database for PostgreSQL - Single Server での変更データ キャプチャの論理デコードと wal2json について説明します
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 363c003a915763a7ab1165c2e0d8f945bc3dd510
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213688"
---
# <a name="logical-decoding"></a>論理デコード
 
[PostgreSQL で論理デコード](https://www.postgresql.org/docs/current/logicaldecoding.html)を使用すると、データの変更を外部のコンシューマーにストリーミングできます。 論理デコードは、イベント ストリーミングおよび変更データ キャプチャのシナリオでよく使用されます。

論理デコードでは、出力プラグインを使用して、Postgres の先行書き込みログ (WAL) を読み取り可能な形式に変換します。 Azure Database for PostgreSQL には、[wal2json](https://github.com/eulerto/wal2json)、[test_decoding](https://www.postgresql.org/docs/current/test-decoding.html)、および pgoutput という出力プラグインが用意されています。 pgoutput は、Postgres バージョン 10 以上の Postgres で使用できます。

Postgres の論理デコードのしくみの概要については、[弊社のブログ](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421)を参照してください。 

> [!NOTE]
> 論理デコードは、Azure Database for PostgreSQL - Single Server のパブリック プレビューです。


## <a name="set-up-your-server"></a>サーバーのセットアップ 
論理デコードと[読み取りレプリカ](concepts-read-replicas.md)はどちらも、情報を Postgres 書き込み先行ログ (WAL) に依存しています。 これらの 2 つの機能には、Postgres とは異なるレベルのログが必要です。 論理デコードには、読み取りレプリカよりも高いレベルのログが必要です。

適切なレベルのログを構成するには、Azure レプリケーション サポート パラメーターを使用します。 Azure レプリケーション サポートには、次の 3 つの設定オプションがあります。

* **オフ** - 最小限の情報を WAL に格納します。 この設定は、ほとんどの Azure Database for PostgreSQL サーバーでは使用できません。  
* **レプリカ** - **[オフ]** よりも冗長です。 これは、[読み取りレプリカ](concepts-read-replicas.md)を機能させるために必要な最小レベルのログです。 ほとんどのサーバーでは、この設定が既定値です。
* **論理** - **[レプリカ]** よりも冗長です。 これは、論理デコードを機能させるための最小レベルのログです。 読み取りレプリカはこの設定でも機能します。

このパラメーターを変更した後、サーバーを再起動する必要があります。 内部的には、このパラメーターによって、Postgres のパラメーター `wal_level`、`max_replication_slots`、および `max_wal_senders` が設定されます。

### <a name="using-azure-cli"></a>Azure CLI の使用

1. Replication_support を `logical` に設定します。
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. サーバーを再起動して変更を適用します。
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

### <a name="using-azure-portal"></a>Azure Portal の使用

1. Azure レプリケーションサポートを **[論理]** に設定します。 **[保存]** を選択します。

   ![[Azure Database for PostgreSQL] - [レプリケーション] - [Azure レプリケーションのサポート]](./media/concepts-logical/replication-support.png)

2. サーバーを再起動して変更を適用するには、 **[はい]** を選択します。

   ![[Azure Database for PostgreSQL] - [レプリケーション] - 再起動の確認](./media/concepts-logical/confirm-restart.png)


## <a name="start-logical-decoding"></a>論理デコードを開始する

論理デコードは、ストリーミング プロトコルまたは SQL インターフェイスを介して使用できます。 どちらの方法でも[レプリケーション スロット](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS)を使用します。 スロットは、単一のデータベースからの変更のストリームを表します。

レプリケーション スロットを使用するには、Postgres のレプリケーション特権が必要です。 現時点では、レプリケーション特権は、サーバーの管理者ユーザーのみが使用できます。 

### <a name="streaming-protocol"></a>ストリーミング プロトコル
多くの場合、ストリーミング プロトコルを使用して変更を行うことをお勧めします。 独自のコンシューマー/コネクタを作成したり、[Debezium](https://debezium.io/) のようなツールを使用したりできます。 

[pg_recvlogical によるストリーミング プロトコルの例](https://github.com/eulerto/wal2json#pg_recvlogical)については、wal2json のドキュメントを参照してください。


### <a name="sql-interface"></a>SQL インターフェイス
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


## <a name="monitoring-slots"></a>スロットの監視

論理デコードは監視する必要があります。 未使用のレプリケーション スロットはすべてドロップしてください。 変更がコンシューマーによって読み取られるまで、スロットは Postgres WAL ログおよび関連のあるシステム カタログを保持します。 コンシューマーが失敗した場合、または適切に構成されていない場合は、未使用のログが蓄積され、ストレージがいっぱいになります。 また、未使用のログによって、トランザクション ID の折り返しリスクが増加します。 どちらの状況でも、サーバーを使用できなくなる可能性があります。 このため、論理レプリケーション スロットを継続的に使用することが重要になります。 論理レプリケーション スロットが使用されなくなった場合は、すぐにドロップしてください。

pg_replication_slots ビューの「active」列には、コンシューマーがスロットに接続されているかどうかが示されます。
```SQL
SELECT * FROM pg_replication_slots;
```

*[使用済みストレージ]* と *[レプリカの最大ラグ]* メトリックで[アラートを設定](howto-alert-on-metric.md)し、値が通常のしきい値を超えたら通知するようにします。 

> [!IMPORTANT]
> 未使用のレプリケーション スロットはドロップする必要があります。 これを行わなければ、サーバーを使用できなくなる可能性があります。

## <a name="how-to-drop-a-slot"></a>スロットをドロップする方法
レプリケーション スロットをアクティブに使用していない場合は、ドロップする必要があります。

SQL を使用して `test_slot` という名前のレプリケーション スロットを削除するには以下を行ってください。
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> 論理デコードの使用を停止した場合は、azure.replication_support を `replica` または `off` に戻します。 `logical` によって保持される WAL 情報はより詳細なので、論理デコードが使用されていない場合は無効にする必要があります。 

 
## <a name="next-steps"></a>次のステップ

* Postgres のドキュメントを参照して、[論理デコードの詳細を学びます](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)。
* 論理デコードについて質問がある場合は、[当社のチーム](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)までご連絡ください。
* [読み取りレプリカ](concepts-read-replicas.md)の詳細を参照してください。

