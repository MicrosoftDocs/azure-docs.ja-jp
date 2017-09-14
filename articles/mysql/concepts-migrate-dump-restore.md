---
title: "Azure Database for MySQL でのダンプと復元を使用した MySQL データベースの移行 | Microsoft Docs"
description: "この記事では、mysqldump、MySQL Workbench、PHPMyAdmin などのツールを使用して、Azure Database for MySQL でデータベースをバックアップして復元する一般的な 2 つの方法について説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 8606067a8e82c6314ab931eb4816d45755a8e04f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017

---

# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>ダンプと復元を使用した Azure Database for MySQL への MySQL データベースの移行
この記事では、Azure Database for MySQL でデータベースをバックアップして復元する一般的な 2 つの方法について説明します
- コマンド ラインからのダンプと復元 (mysqldump を使用) 
- PHPMyAdmin を使用したダンプと復元 

## <a name="before-you-begin"></a>開始する前に
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for MySQL サーバー - Azure Portal を使用して作成](quickstart-create-mysql-server-database-using-azure-portal.md)
- コンピューターにインストールされている [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) コマンド ライン ユーティリティ
- ダンプおよび復元コマンドを実行する、MySQL Workbench ([MySQL Workbench のダウンロード](https://dev.mysql.com/downloads/workbench/))、Toad、Navicat、またはサード パーティ製の他の MySQL ツール

## <a name="use-common-tools"></a>一般的なツールの使用
MySQL Workbench、mysqldump、Toad、Navicat などの一般的なユーティリティとツールを使用して、Azure Database for MySQL にリモートで接続し、データを復元します。 こうしたツールは、インターネットに接続されたクライアント コンピューターで、Azure Database for MySQL に接続するときに使用します。 SSL で暗号化された接続を使ってセキュリティを強化するために、[Azure Database for MySQL での SSL 接続の構成](concepts-ssl-connection-security.md)に関するページもご覧ください。 Azure Database for MySQL に移行するとき、ダンプ ファイルをクラウドの特別な場所に移動する必要はありません。 

## <a name="common-uses-for-dump-and-restore"></a>ダンプと復元の一般的な使用法
いくつかの一般的なシナリオでは、mysqldump や mysqlpump などの MySQL ユーティリティを使用して、データベースをダンプし、Azure MySQL Database に読み込むことができます。 他のシナリオでは、代わりに[インポートとエクスポート](concepts-migrate-import-export.md)の方法を使用できます。

- データベース全体を移行するときは、データベース ダンプを使用します。 この推奨事項は、大量の MySQL データを移動するときや、ライブ サイトやライブ アプリケーションのためにサービスの中断を最小限に抑える必要がある場合に有効です。 
-  Azure Database for MySQL にデータを読み込むときは、データベースのすべてのテーブルで InnoDB ストレージ エンジンが使用されていることを確認します。 Azure Database for MySQL でサポートされるのは InnoDB ストレージ エンジンだけであるため、代替のストレージ エンジンはサポートされません。 テーブルが他のストレージ エンジンで構成されている場合は、Azure Database for MySQL に移行する前に、それらのテーブルを InnoDB エンジン形式に変換します。
   たとえば、MyISAM テーブルを使用する WordPress または WebApp がある場合は、Azure Database for MySQL に復元する前に InnoDB 形式に移行して、それらのテーブルを変換しておきます。 新しいテーブルを作成するときに、`ENGINE=InnoDB` 句によって使用するエンジンを設定し、復元前にデータを互換性のあるテーブルに転送します。 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- 互換性の問題を回避するために、データベースをダンプするときに、ダンプ元とダンプ先のシステムで同じバージョンの MySQL が使用されていることを確認します。 たとえば、既存の MySQL サーバーがバージョン 5.7 の場合は、バージョン 5.7 を実行するように構成された Azure Database for MySQL に移行する必要があります。 `mysql_upgrade` コマンドは、Azure Database for MySQL サーバーでは機能しないのでサポートされていません。 MySQL のバージョン間でアップグレードする必要がある場合は、まず、独自の環境の下位バージョンのデータベースを上位バージョンの MySQL にダンプまたはエクスポートします。 次に、Azure Database for MySQL への移行を実行する前に、`mysql_upgrade` を実行します。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項
パフォーマンスを最適化するには、大規模なデータベースをダンプするときに、次の考慮事項に注意してください。
-   データベースをダンプするときに、mysqldump で `exclude-triggers` オプションを使用します。 データの復元中にトリガー コマンドが実行されないように、ダンプ ファイルからトリガーを除外します。 
-   非常に大規模なデータベースをダンプするときは、mysqldump で `single-transaction` オプションを使用しないようにします。 1 つのトランザクションで多数のテーブルをダンプすると、復元時に余分なストレージ リソースやメモリ リソースが使用され、パフォーマンスの遅延やリソースの制約が発生する可能性があります。
-   SQL での読み込み時に複数値挿入を使用して、データベースをダンプするときのステートメント実行のオーバーヘッドを最小限に抑えます。 mysqldump ユーティリティによって生成されるダンプ ファイルを使用すると、複数値挿入が既定で有効になります。 
-  データベースをダンプするときに、mysqldump で `order-by-primary` オプションを使用します。このオプションを使用すると、主キー順にデータがスクリプト化されます。
-   データをダンプするときに、mysqldump で `disable-keys` オプションを使用して、読み込み前に外部キー制約を無効にします。 外部キーのチェックを無効にすると、パフォーマンスが向上します。 読み込み後に制約を有効にし、データを検証して参照整合性を確認します。
-   パーティション テーブルを適宜使用します。
-   データを並列で読み込みます。 リソースの上限に達するような過剰な並列処理を避け、Azure Portal で使用可能なメトリックを使用してリソースを監視します。 
-   データベースをダンプするときに、mysqlpump で `defer-table-indexes` オプションを使用します。このオプションを使用すると、テーブル データが読み込まれてからインデックスが作成されます。

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>mysqldump を使用したコマンド ラインからのバックアップ ファイルの作成
ローカルのオンプレミス サーバーまたは仮想マシンで既存の MySQL データベースをバックアップするには、次のコマンドを実行します。 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

指定するパラメーターは次のとおりです。
- [uname]: データベースのユーザー名 
- [pass]: データベースのパスワード (-p とパスワードの間にスペースがありません) 
- [dbname]: データベースの名前 
- [backupfile.sql]: データベース バックアップのファイル名 
- [--opt]: mysqldump オプション 

たとえば、MySQL サーバー上の、ユーザー名が "testuser" で、パスワードが設定されていない "testdb" という名前のデータベースを、testdb_backup.sql ファイルにバックアップするには、次のコマンドを使用します。 このコマンドは、`testdb` データベースを `testdb_backup.sql` というファイルにバックアップします。このファイルには、データベースを再作成するために必要なすべての SQL ステートメントが含まれます。 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
バックアップするデータベースのテーブルを選択するには、テーブル名をスペースで区切って指定します。 たとえば、"testdb" の table1 テーブルと table2 テーブルだけをバックアップするには、次のように指定します。 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

複数のデータベースを一度にバックアップするには、--database スイッチを使用して、データベース名をスペースで区切って指定します。 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
サーバーのすべてのデータベースを一度にバックアップするには、--all databases オプションを使用します。
```
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Azure Database for MySQL ターゲット サーバーでのデータベースの作成
データを移行する Azure Database for MySQL ターゲット サーバーに空のデータベースを作成します。 データベースは、MySQL Workbench、Toad、Navicat などのツールを使用して作成します。 データベースの名前は、ダンプされたデータが含まれるデータベースと同じにすることも、別の名前でデータベースを作成することもできます。

接続するために、Azure Database for MySQL のプロパティ ページで接続情報を見つけます。
![Azure Portal で接続情報を見つける](./media/concepts-migrate-dump-restore/1_server-properties-name-login.png)

接続情報を MySQL Workbench に追加します。
![MySQL Workbench の接続文字列](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>コマンド ラインまたは MySQL Workbench を使用した MySQL データベースの復元
ターゲット データベースを作成したら、mysql コマンドまたは MySQL Workbench を使用して、ダンプ ファイルから新しく作成された特定のデータベースにデータを復元できます。
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
この例では、Azure Database for MySQL ターゲット サーバーに新しく作成されたデータベースにデータを復元します。
```bash
$ mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>PHPMyAdmin を使用したエクスポート
エクスポートには一般的なツールである phpMyAdmin を使用できます。このツールは、既にローカル環境にインストールされている可能性があります。 PHPMyAdmin を使用して MySQL データベースをエクスポートするには、次の操作を行います。
- phpMyAdmin を開きます。
- データベースを選択します。 左側の一覧でデータベース名をクリックします。 
- **[エクスポート]** リンクをクリックします。 新しいページが表示され、データベースのダンプが表示されます。
- [エクスポート] 領域で **[すべて選択]** リンクをクリックして、データベースのテーブルを選択します。 
- [SQL options]\(SQL オプション\) 領域で、適切なオプションをクリックします。 
- **[名前を付けて保存]** ファイル オプションと、対応する圧縮オプションをクリックし、**[実行]** をクリックします。 ファイルをローカルに保存するよう求めるダイアログ ボックスが表示されます。

## <a name="import-using-phpmyadmin"></a>PHPMyAdmin を使用したインポート
データベースのインポート操作は、エクスポートと似ています。 次の操作を実行してください。
- phpMyAdmin を開きます。 
- phpMyAdmin セットアップ ページで、**[追加]** をクリックして Azure Database for MySQL サーバーを追加します。 接続の詳細とログイン情報を入力します。
- データベースを作成して適切な名前を付けたら、画面の左側でそのデータベースを選択します。 既存のデータベースを再作成するには、データベース名をクリックし、テーブル名の横のすべてのチェック ボックスをオンにします。**[ドロップ]** を選択して既存のテーブルを削除します。 
- **[SQL]** リンクをクリックします。表示されたページで、SQL コマンドを入力したり、SQL ファイルをアップロードしたりできます。 
- **参照**ボタンを使用して、データベース ファイルを検索します。 
- **[実行]** をクリックすると、バックアップがエクスポートされ、SQL コマンドが実行された後、データベースが再作成されます。

## <a name="next-steps"></a>次のステップ
[Azure Database for MySQL にアプリケーションを接続する](./howto-connection-string.md)

