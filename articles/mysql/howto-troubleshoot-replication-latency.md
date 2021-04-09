---
title: レプリケーション待機時間のトラブルシューティング - Azure Database for MySQL
description: Azure Database for MySQL の読み取りレプリカを使用して、レプリケーション待機時間のトラブルシューティングを行う方法を説明します。
keywords: mysql, トラブルシューティング, レプリケーション待機時間 (秒)
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 01/13/2021
ms.openlocfilehash: 92513a8c24b5106e3a59c8cfa4d743e900b957bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98249773"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Azure Database for MySQL のレプリケーション待ち時間のトラブルシューティング

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

[読み取りレプリカ](concepts-read-replicas.md)機能を使用すると、Azure Database for MySQL サーバーから、読み取り専用のレプリカ サーバーにデータをレプリケートできます。 読み取りおよびレポート クエリをアプリケーションから複数のレプリカ サーバーにルーティングすることによって、ワークロードをスケールアウトできます。 このセットアップにより、ソース サーバーの負荷が軽減されます。 また、スケーリングにより、アプリケーションの全体的なパフォーマンスと待機時間も改善されます。

レプリカは、MySQL エンジンのネイティブなバイナリ ログ (binlog) ファイルの位置に基づくレプリケーション テクノロジを使用して、非同期で更新されます。 詳細については、[MySQL の binlog ファイルの位置に基づくレプリケーションの構成の概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)に関するページを参照してください。

セカンダリ読み取りレプリカでのレプリケーションのラグは、いくつかの要因に依存します。 このような要因には以下のものが含まれますが、これらだけではありません。

- ネットワーク待機時間。
- ソース サーバーでのトランザクションの量。
- ソース サーバーとセカンダリ読み取りレプリカ サーバーのコンピューティング レベル。
- ソース サーバーとセカンダリ サーバーで実行されているクエリ。

この記事では、Azure Database for MySQL でレプリケーション待機時間のトラブルシューティングを行う方法について説明します。 レプリカ サーバーでレプリケーション待機時間が長くなるいくつかの一般的な原因についても説明します。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "_スレーブ_" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。
>

## <a name="replication-concepts"></a>レプリケーションの概念

バイナリ ログが有効になっている場合、コミットされたトランザクションはソース サーバーによってバイナリ ログに書き込まれます。 バイナリ ログはレプリケーションに使用されます。 それは、新しくプロビジョニングされるサーバーのうち、サポートされるストレージが 16 TB までのもののすべてにおいて既定で有効になります。 各レプリカ サーバーで 2 つのスレッドが実行されます。 1 つのスレッドは "*IO スレッド*" で、もう 1 つは "*SQL スレッド*" です。

- IO スレッドにより、ソース サーバーへの接続と、更新されたバイナリ ログの要求が行われます。 このスレッドは、バイナリ ログの更新を受け取ります。 それらの更新は、レプリカ サーバーの "*リレー ログ*" と呼ばれるローカル ログに保存されます。
- SQL スレッドによって、リレー ログが読み取られた後、レプリカ サーバーにデータの変更が適用されます。

## <a name="monitoring-replication-latency"></a>レプリケーション待機時間の監視

Azure Database for MySQL により、[Azure Monitor](concepts-monitoring.md) でレプリケーション ラグ (秒単位) のメトリックが提供されます。 このメトリックは、読み取りレプリカ サーバーでのみ使用できます。 それは、MySQL で使用できる seconds_behind_master メトリックによって計算されます。 

レプリケーション待機時間が増加する原因を理解するには、[MySQL Workbench](connect-workbench.md) または [Azure Cloud Shell](https://shell.azure.com) を使用してレプリカ サーバーに接続します。 そして、次のコマンドを実行します。

>[!NOTE]
> コード内で、例の値を実際のレプリカ サーバー名と管理ユーザー名に置き換えます。 管理者ユーザー名には、Azure Database for MySQL の `@\<servername>` が必要です。

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Cloud Shell ターミナルでのエクスペリエンスは次のようになります。

```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI
Type "help" to learn about Cloud Shell

user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64796
Server version: 5.6.42.0 Source distribution

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

同じ Cloud Shell ターミナルで、次のコマンドを実行します。

```
mysql> SHOW SLAVE STATUS;
```

一般的な出力を次に示します。
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="レプリケーション待機時間の監視":::

出力には大量の情報が含まれています。 通常は、次の表で説明する行だけに注目する必要があります。

|メトリック|説明|
|---|---|
|Slave_IO_State| IO スレッドの現在の状態を表します。 通常、ソース (マスター) サーバーが同期している場合、状態は "Waiting for master to send event" (マスターによるイベントの送信を待機中) です。 "Connecting to master" (マスターに接続中) などの状態は、レプリカがソース サーバーへの接続を失ったことを示します。 ソース サーバーが実行されていることを確認するか、ファイアウォールによって接続がブロックされているかどうかを調べます。|
|Master_Log_File| ソース サーバーの書き込み先のバイナリ ログ ファイルを表します。|
|Read_Master_Log_Pos| ソース サーバーによって書き込みが行われているバイナリ ログ ファイル内の場所を示します。|
|Relay_Master_Log_File| レプリカ サーバーによってソース サーバーから読み取りが行われているバイナリ ログ ファイルを表します。|
|Slave_IO_Running| IO スレッドが実行されているかどうかを示します。 値は `Yes` である必要があります。 値が `NO` の場合は、レプリケーションが中断されている可能性があります。|
|Slave_SQL_Running| SQL スレッドが実行されているかどうかを示します。 値は `Yes` である必要があります。 値が `NO` の場合は、レプリケーションが中断されている可能性があります。|
|Exec_Master_Log_Pos| レプリカによって適用されている Relay_Master_Log_File の位置を示します。 待機時間がある場合、この位置シーケンスが Read_Master_Log_Pos より小さくなるはずです。|
|Relay_Log_Space|リレー ログのサイズの上限を示します。 `relay_log_space_limit` のように `SHOW GLOBAL VARIABLES` のクエリを実行することによって、サイズを確認できます。|
|Seconds_Behind_Master| レプリケーション待機時間を秒単位で表示します。|
|Last_IO_Errno|IO スレッドのエラー コードを表示します (存在する場合)。 これらのコードの詳細については、[MySQL サーバーのエラー メッセージ リファレンス](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)に関するページを参照してください。|
|Last_IO_Error| IO スレッドのエラー メッセージを表示します (存在する場合)。|
|Last_SQL_Errno|SQL スレッドのエラー コードを表示します (存在する場合)。 これらのコードの詳細については、[MySQL サーバーのエラー メッセージ リファレンス](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)に関するページを参照してください。|
|Last_SQL_Error|SQL スレッドのエラー メッセージを表示します (存在する場合)。|
|Slave_SQL_Running_State| 現在の SQL スレッドの状態を示します。 この状態では、`System lock` は正常です。 また、`Waiting for dependent transaction to commit` という状態が表示される場合も正常です。 この状態は、コミットされたトランザクションがソース サーバーによって更新されるのをレプリカが待機していることを示します。|

Slave_IO_Running が `Yes` で、Slave_SQL_Running が `Yes` の場合、レプリケーションは正常に実行されています。 

次に、Last_IO_Errno、Last_IO_Error、Last_SQL_Errno、Last_SQL_Error を確認します。  これらのフィールドには、SQL スレッドの停止の原因となった最新のエラーのエラー番号とエラー メッセージが表示されます。 エラー番号が `0` でメッセージが空の場合は、エラーがないことを意味します。 [MySQL サーバーのエラー メッセージ リファレンス](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)に関するページでエラー コードを調べることにより、0 以外のエラー値を調査します。

## <a name="common-scenarios-for-high-replication-latency"></a>レプリケーション待機時間が長い場合の一般的なシナリオ

以下のセクションでは、レプリケーションの待機時間が長くなることがよくあるシナリオについて説明します。

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>ソース サーバーでのネットワーク待機時間または高い CPU 使用率

次のような値が表示される場合、レプリケーション待機時間の原因は、長いネットワーク待機時間、またはソース サーバーでの高い CPU 使用率である可能性があります。

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

この場合、IO スレッドは実行中であり、ソース サーバーを待機しています。 ソース サーバーは既に、番号が 20 のバイナリ ログ ファイルに書き込んでいます。 レプリカは、ファイル番号 10 までしか受信していません。 このシナリオでレプリケーション待機時間が長くなる主な要因は、ネットワークの速度またはソース サーバーでの高い CPU 使用率です。  

Azure においては、通常、リージョン内のネットワーク待機時間はミリ秒単位で測定されます。 リージョン間の場合の待機時間の範囲は、ミリ秒から秒です。

ほとんどの場合、IO スレッドとソース サーバーの間の接続遅延の原因は、ソース サーバーでの高い CPU 使用率です。 IO スレッドの処理が遅くなっています。 この問題は、Azure Monitor を使用して、ソース サーバーでの CPU の使用率とコンカレント接続の数を調べることにより検出できます。

ソース サーバーの CPU 使用率が高くない場合、問題はネットワーク待機時間である可能性があります。 ネットワーク待機時間が突然異常に高くなった場合は、[Azure の状態ページ](https://status.azure.com/status)で既知の問題や停止を確認します。 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>ソース サーバーでのトランザクションの大量バースト

次のような値が表示される場合、レプリケーション待機時間の原因として可能性が高いのは、ソース サーバーでのトランザクションの大量バーストです。 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

レプリカはソース サーバーより遅れてバイナリ ログを取得できることが出力で示されています。 しかし、レプリカの IO スレッドは、リレー ログ領域が既にいっぱいであることを示しています。

ネットワークの速度は遅延の原因ではありません。 レプリカは追い付こうとしています。 しかし、更新されたバイナリ ログのサイズがリレー ログ領域の上限を超えています。

この問題のトラブルシューティングを行うには、ソース サーバーで[低速クエリ ログ](concepts-server-logs.md)を有効にします。 低速クエリ ログを使用して、ソース サーバーで長い時間実行されているトランザクションを特定します。 その後、特定されたクエリを調整して、サーバーでの待機時間を短縮します。 

この種のレプリケーション待機時間は、通常、ソース サーバーでのデータの読み込みが原因で発生します。 ソース サーバーで週単位または月単位のデータの読み込みがある場合、レプリケーション待機時間は避けられません。 ソース サーバーでデータの読み込みが完了した後、レプリカ サーバーは最終的に追い付きます。

### <a name="slowness-on-the-replica-server"></a>レプリカ サーバーのパフォーマンスの低下

次のような値が表示される場合は、レプリカ サーバーで問題が発生している可能性があります。

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

このシナリオでは、IO スレッドと SQL スレッドの両方が正常に実行されていることが出力で示されます。 ソース サーバーで書き込まれているのと同じバイナリ ログ ファイルが、レプリカによって読み取られています。 ただし、レプリカ サーバーでの多少の待機時間は、ソース サーバーからの同じトランザクションを反映しています。

以下のセクションでは、この種の待機時間の一般的な原因について説明します。

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>テーブルに主キーまたは一意キーがない

Azure Database for MySQL では行ベースのレプリケーションが使用されます。 ソース サーバーにより、バイナリ ログにイベントが書き込まれて、個々のテーブル行での変更が記録されます。 その後、SQL スレッドにより、それらの変更が、レプリカ サーバー上の対応するテーブル行にレプリケートされます。 テーブルに主キーまたは一意キーがない場合、変更を適用するために、SQL スレッドによってターゲット テーブルのすべての行がスキャンされます。 このスキャンにより、レプリケーション待機時間が発生する可能性があります。

MySQL の主キーは、NULL 値を含むことができないために高速のクエリ パフォーマンスが保証される、関連付けられたインデックスです。 InnoDB ストレージ エンジンを使用している場合、テーブル データは、主キーに基づいて超高速な検索と並べ替えを行えるよう物理的に編成されます。

レプリカ サーバーを作成する前に、ソース サーバーのテーブルに主キーを追加することをお勧めします。 ソース サーバーに主キーを追加した後、レプリケーション待機時間が向上するように、読み取りレプリカを再作成します。

ソース サーバーで主キーがないテーブルを調べるには、次のクエリを使用します。

```sql
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="long-running-queries-on-the-replica-server"></a>レプリカ サーバーでの実行時間の長いクエリ

レプリカ サーバーでのワークロードにより、SQL スレッドが IO スレッドより遅れることがあります。 レプリカ サーバー上の実行時間の長いクエリは、レプリケーション待機時間が長くなる一般的な原因の 1 つです。 この問題のトラブルシューティングを行うには、レプリカ サーバーで[低速クエリ ログ](concepts-server-logs.md)を有効にします。

低速クエリにより、リソースの消費が増加したり、サーバーの速度が低下することがあり、そのためにレプリカはソース サーバーに追いつけなくなります。 このシナリオでは、低速のクエリを調整します。 クエリを高速化すると、SQL スレッドをブロックするものがなくなり、レプリケーション待機時間が大幅に短縮されます。

#### <a name="ddl-queries-on-the-source-server"></a>ソース サーバーでの DDL クエリ

ソース サーバーで [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) のようなデータ定義言語 (DDL) コマンドを実行すると、時間がかかる場合があります。 DDL コマンドの実行中に、他の何千ものクエリがソース サーバーで並列に実行される可能性があります。 

DDL がレプリケートされるときは、データベースの整合性を維持するため、MySQL エンジンによって単一のレプリケーション スレッドで DDL が実行されます。 このタスクが実行されている間、他のすべてのレプリケートされたクエリはブロックされ、レプリカ サーバーで DDL 操作が完了するまで待機する必要があります。 オンライン DDL 操作でも、この遅延が発生します。 DDL 操作により、レプリケーション待機時間が増加します。

ソース サーバーで[低速クエリ ログ](concepts-server-logs.md)を有効にした場合、ソース サーバーで実行された DDL コマンドを調べることで、この待機時間の問題を検出できます。 インデックスの削除、名前変更、作成を行うときは、ALTER TABLE に INPLACE アルゴリズムを使用できます。 テーブルのデータをコピーし、テーブルを再構築することが、必要になる場合があります。

通常、INPLACE アルゴリズムに対してはコンカレント DML がサポートされています。 ただし、操作を準備して実行するときに、テーブルに対する排他的なメタデータ ロックを簡単に取得できます。 したがって、CREATE INDEX ステートメントの場合、ALGORITHM 句と LOCK 句を使用して、テーブルをコピーする方法と、読み取りと書き込みのコンカレンシーのレベルに、影響を与えることができます。 その場合でも、FULLTEXT インデックスまたは SPATIAL インデックスを追加することにより、DML 操作を防ぐことができます。

次の例では、ALGORITHM 句と LOCK 句を使用してインデックスを作成します。

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

残念ながら、ロックを必要とする DDL ステートメントの場合、レプリケーション待機時間を回避することはできません。 可能性のある影響を軽くするには、夜間など、ピーク以外の時間帯にこれらの種類の DDL 操作を実行します。

#### <a name="downgraded-replica-server"></a>ダウングレードされたレプリカ サーバー

Azure Database for MySQL の場合、ソース サーバーと同じサーバー構成が読み取りレプリカで使用されます。 作成された後で、レプリカ サーバーの構成を変更できます。

レプリカ サーバーをダウングレードすると、ワークロードによるリソースの消費量が増え、その結果、レプリケーション待機時間が発生する可能性があります。 この問題を検出するには、Azure Monitor を使用して、レプリカ サーバーの CPU とメモリの消費量を調べます。

このシナリオの場合、レプリカ サーバーの構成を、ソース サーバーの値以上の値に維持することをお勧めします。 このように構成すると、レプリカはソース サーバーより遅れることがなくなります。

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>ソース サーバーのパラメーターの調整によるレプリケーション待機時間の改善

Azure Database for MySQL の場合、レプリケーションは既定で、レプリカ上の並列スレッドで実行されるように最適化されます。 ソース サーバーでの高コンカレンシー ワークロードのためにレプリカ サーバーが遅れる場合、ソース サーバーでパラメーター binlog_group_commit_sync_delay を構成することにより、レプリケーション待機時間を改善できます。

binlog_group_commit_sync_delay パラメーターにより、バイナリ ログ ファイルを同期する前にバイナリ ログ コミットが待機するマイクロ秒数が制御されます。 このパラメーターの利点は、トランザクションがコミットされるたびに直ちに適用されるのではなく、ソース サーバーからバイナリ ログの更新が一括送信されることです。 この遅延により、レプリカでの IO が減少し、パフォーマンスを向上させることができます。

binlog_group_commit_sync_delay パラメーターを 1000 程度に設定すると効果がある場合があります。 その後、レプリケーション待機時間を監視します。 このパラメーターを設定するときは慎重に行い、高コンカレンシー ワークロードに対してのみ使用します。

> [!IMPORTANT]
> レプリカ サーバーでは、binlog_group_commit_sync_delay パラメーターを 0 にすることをお勧めします。 これが推奨されるのは、ソース サーバーとは異なり、レプリカ サーバーの同時実行性が高くないためであり、レプリカ サーバーで binlog_group_commit_sync_delay の値を増やすと、レプリケーションのラグが誤って増加する可能性があります。

多数のシングルトン トランザクションが含まれる低コンカレンシーのワークロードの場合は、binlog_group_commit_sync_delay を設定すると待機時間が長くなる可能性があります。 コミットされるトランザクションが少ない場合でも、一括バイナリ ログ更新に対する IO スレッドの待機により、待機時間が増加する可能性があります。

## <a name="next-steps"></a>次の手順

[MySQL binlog レプリケーションの概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)を確認します。
