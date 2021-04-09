---
title: データイン レプリケーションの構成 － Azure Database for MariaDB
description: この記事では、Azure Database for MariaDB でデータイン レプリケーションをセットアップする方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: 67e4da13d6954342b9979eb57a35c812cb63bb3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665125"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Azure Database for MariaDB でのデータイン レプリケーションの構成

この記事では、ソース サーバーとレプリカ サーバーを構成することによって、Azure Database for MariaDB で[データイン レプリケーション](concepts-data-in-replication.md)をセットアップする方法について説明します。 この記事は、MariaDB サーバーと MariaDB データベースに関して、ある程度の使用経験があることを前提としています。

[データイン レプリケーション](concepts-data-in-replication.md)により、Azure Database for MariaDB サービスでレプリカを作成するために、オンプレミス、仮想マシン (VM)、またはクラウド データベース サービスのソース MariaDB サーバーからデータが同期されます。 データイン レプリケーションは、MariaDB のネイティブなバイナリ ログ (binlog) ファイルの位置ベースのレプリケーションに基づいています。 binlog レプリケーションの詳細については、[binlog レプリケーションの概要](https://mariadb.com/kb/en/library/replication-overview/)に関する記事を参照してください。

この記事の手順を実行する前に、データイン レプリケーションの[制限事項と要件](concepts-data-in-replication.md#limitations-and-considerations)を確認してください。

> [!NOTE]
> ソース サーバーがバージョン 10.2 以降の場合、[グローバル トランザクション ID](https://mariadb.com/kb/en/library/gtid/) を使用してデータイン レプリケーションをセットアップすることをお勧めします。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "_スレーブ_" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>レプリカとして使用する MariaDB サーバーを作成する

1. 新規の Azure Database for MariaDB サーバー (たとえば、replica.mariadb.database.azure.com) を作成します。 このサーバーは、データイン レプリケーションにおけるレプリカ サーバーになります。

    サーバーの作成については、「[Azure portal を使用した Azure Database for MariaDB サーバーの作成](quickstart-create-mariadb-server-database-using-azure-portal.md)」をご覧ください。

   > [!IMPORTANT]
   > Azure Database for MariaDB サーバーは、汎用とメモリ最適化のどちらかの価格レベルで作成する必要があります。

2. 同一のユーザー アカウントとそれに対応する権限を作成します。

    レプリカ サーバーにソース サーバーのユーザー アカウントはレプリケートされません。 レプリカ サーバーへのアクセス権をユーザーに付与するには、新しく作成した Azure Database for MariaDB サーバーですべてのアカウントとそれらに対応する権限を手動で作成する必要があります。

3. ソース サーバーの IP アドレスをレプリカのファイアウォール規則に追加します。 

   [Azure portal](howto-manage-firewall-portal.md) または [Azure CLI](howto-manage-firewall-cli.md) を使用してファイアウォール規則を更新します。

## <a name="configure-the-source-server"></a>ソース サーバーを構成する

次の手順では、オンプレミス、VM、またはクラウド データベース サービスでホストされる MariaDB サーバーをデータイン レプリケーション用に準備し、構成します。 MariaDB サーバーは、データイン レプリケーションにおけるソースになります。

1. 続行する前に、[プライマリ サーバーの要件](concepts-data-in-replication.md#requirements)を確認してください。 

2. 確実に、ソース サーバーでポート 3306 での受信と送信の両方のトラフィックが許可されていて、ソース サーバーに **パブリック IP アドレス** がある、または DNS にパブリックにアクセス可能である、あるいは完全修飾ドメイン名 (FQDN) があるかのいずれかであるようにします。 

   別のマシンでホストされている MySQL のコマンド ラインや、Azure portal で使用可能な [Azure Cloud Shell](../cloud-shell/overview.md) などのツールから接続を試行することで、ソース サーバーへの接続性をテストします。

   組織に厳密なセキュリティ ポリシーがあり、ソース サーバー上のすべての IP アドレスで Azure からソース サーバーへの通信が許可されない場合は、次のコマンドを使用して Azure Database for MariaDB サーバーの IP アドレスを確認できます。

   1. MySQL コマンド ラインなどのツールを使用して Azure Database for MariaDB にサインインします。
   2. 下のクエリを実行します。

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      出力例を次に示します。

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. MySQL コマンド ラインを終了します。
   4. ping ユーティリティで以下を実行して、IP アドレスを取得します。

      ```bash
      ping <output of step 2b>
      ```

      次に例を示します。

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. 前のステップで出力された IP アドレスがポート 3306 に含まれるように、ソース サーバーのファイアウォール規則を構成します。

   > [!NOTE]
   > この IP アドレスは、メンテナンスまたはデプロイ操作により変更される可能性があります。 この接続方法は、ポート 3306 上ですべての IP アドレスを許可することができないお客様のみを対象としています。

3. バイナリ ログを有効にします。

    プライマリでバイナリ ログが有効になっているかどうか確認するために、次のコマンドを入力します。

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   変数 [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) で値 `ON` が返された場合、サーバーでバイナリ ログが有効になっています。

   `log_bin` で値 `OFF` が返された場合、**my.cnf** ファイルを編集して、`log_bin=ON` によってバイナリ ログを有効にします。 変更を有効にするためにサーバーを再起動します。

4. ソース サーバーの設定を構成します。

    データイン レプリケーションの場合、ソース サーバーとレプリカ サーバーの間でパラメーター `lower_case_table_names` を一致させる必要があります。 Azure Database for MariaDB では、`lower_case_table_names` パラメーターは既定では `1` に設定されています。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. 新しいレプリケーション ロールを作成し、権限をセットアップします。

   レプリケーションの権限を持つように構成されたユーザー アカウントをソース サーバーに作成します。 SQL コマンドまたは MySQL Workbench を使用してアカウントを作成できます。 SSL を使用してレプリケートを行う場合は、ユーザー アカウントの作成時にこれを指定する必要があります。

   ソース サーバーにユーザー アカウントを追加する方法については、[MariaDB のドキュメント](https://mariadb.com/kb/en/library/create-user/)を参照してください。

   次のコマンドを使用することで、新しいレプリケーション ロールは、ソース自体をホストするマシンだけでなく、任意のマシンからソースにアクセスできます。 このアクセスでは、ユーザーを作成するコマンドに **syncuser\@'%'** を指定します。

   MariaDB のドキュメントの詳細については、[アカウント名の指定](https://mariadb.com/kb/en/library/create-user/#account-names)に関するページをご覧ください。

   **SQL コマンド**

   - SSL を使用したレプリケーション

       すべてのユーザー接続で SSL を要求するには、次のコマンドを入力してユーザーを作成します。

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - SSL を使用しないレプリケーション

       必ずしもすべての接続に SSL が必要でない場合は、次のコマンドを入力してユーザーを作成します。

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   MySQL Workbench でレプリケーション ロールを作成するには、 **[Management]\(管理\)** ウィンドウで **[Users and Privileges]\(ユーザーと権限\)** を選択します。 次に、 **[アカウントの追加]** を選択します。

   ![[Users and Privileges]\(ユーザーと権限\)](./media/howto-data-in-replication/users_privileges.png)

   **[Login Name]\(ログイン名\)** フィールドにユーザー名を入力します。

   ![同期ユーザー](./media/howto-data-in-replication/syncuser.png)

   **[Administrative Roles]\(管理者ロール\)** パネルを選択し、 **[Global Privileges]\(グローバル権限\)** の一覧から **[Replication Slave]\(レプリケーション スレーブ\)** を選択します。 **[Apply]\(適用\)** を選択して、レプリケーション ロールを作成します。

   ![[Replication Slave]\(レプリケーション スレーブ\)](./media/howto-data-in-replication/replicationslave.png)

6. ソース サーバーを読み取り専用モードに設定します。

   データベースをダンプする前に、サーバーを読み取り専用モードにする必要があります。 読み取り専用モードの間、ソースで書き込みトランザクションを処理することはできません。 ビジネスに影響が出ないようにするために、ピーク時以外の時間帯に読み取り専用期間をスケジュールしてください。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. 現在のバイナリ ログ ファイルの名前とオフセットを取得します。

   現在のバイナリ ログ ファイルの名前とオフセットを調べるために、コマンド [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) を実行します。

   ```sql
   show master status;
   ```

   結果は次の表のようになります。

   ![master status の結果](./media/howto-data-in-replication/masterstatus.png)

   バイナリ ファイルの名前は、この後の手順で使用するためメモしてください。

8. GTID 位置 (オプション、GTID を使用したレプリケーションに必要) を取得します。

   関数 [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) を実行して、対応する binlog ファイルの名前とオフセットの GTID 位置を取得します。
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```

## <a name="dump-and-restore-the-source-server"></a>ソース サーバーのダンプと復元

1. ソース サーバーからすべてのデータベースをダンプします。

   mysqldump を使用して、ソース サーバーからすべてのデータベースをダンプします。 MySQL ライブラリとテスト ライブラリをダンプする必要はありません。

    詳細については、「[ダンプと復元](howto-migrate-dump-restore.md)」をご覧ください。

2. ソース サーバーを読み取り書き込みモードに設定します。

   データベースのダンプ後、ソース MariaDB サーバーを読み取り書き込みモードに戻します。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. ダンプ ファイルを新しいサーバーに復元します。

   Azure Database for MariaDB サービスに作成したサーバーにダンプ ファイルを復元します。 ダンプ ファイルを MariaDB サーバーに復元する方法については、「[ダンプと復元](howto-migrate-dump-restore.md)」をご覧ください。

   大きいダンプ ファイルは、レプリカ サーバーと同じリージョンにある Azure 内の VM にアップロードしてください。 それを VM から Azure Database for MariaDB サーバーに復元します。

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>ソースとレプリカのサーバーをリンクしてデータイン レプリケーションを開始する

1. ソース サーバーを設定します。

   データイン レプリケーションの機能は、すべてストアド プロシージャによって実現されています。 「[データイン レプリケーションのストアド プロシージャ](reference-stored-procedures.md)」で、すべてのプロシージャをご覧いただけます。 ストアド プロシージャは、MySQL シェルまたは MySQL Workbench で実行できます。

   2 つのサーバーをリンクさせてレプリケーションを開始するには、Azure DB for MariaDB サービスのターゲット レプリカ サーバーにサインインします。 次に、Azure DB for MariaDB サーバーで `mysql.az_replication_change_master` または `mysql.az_replication_change_master_with_gtid` ストアド プロシージャを使用して、外部インスタンスをソース サーバーとして設定します。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   または

   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: ソース サーバーのホスト名
   - master_user: ソース サーバーのユーザー名
   - master_password: ソース サーバーのパスワード
   - master_log_file: `show master status` を実行することによって得たバイナリ ログ ファイルの名前
   - master_log_pos: `show master status` を実行することによって得たバイナリ ログの位置
   - master_gtid_pos: `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);` を実行することによって得た GTID の位置
   - master_ssl_ca:CA 証明書のコンテキスト。 SSL を使用しない場合は、空の文字列を渡します。*


    *master_ssl_ca パラメーターを変数として渡すことをお勧めします。 詳細については、次の例を参照してください。

   **使用例**

   - SSL を使用したレプリケーション

       次のコマンドを実行して、変数 `@cert` を作成します。

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       SSL を使用したレプリケーションを、ドメイン companya.com にホストされたソース サーバーと Azure Database for MariaDB にホストされたレプリカ サーバーとの間でセットアップします。 このストアド プロシージャはレプリカで実行します。

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```

   - SSL を使用しないレプリケーション

       SSL を使用しないレプリケーションを、ドメイン companya.com にホストされたソース サーバーと Azure Database for MariaDB にホストされたレプリカ サーバーとの間でセットアップします。 このストアド プロシージャはレプリカで実行します。

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. レプリケーションを開始します。

   `mysql.az_replication_start` ストアド プロシージャを呼び出してレプリケーションを開始します。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. レプリケーションの状態を確認します。

   レプリカ サーバーで [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) コマンドを呼び出して、レプリケーションの状態を確認します。

   ```sql
   show slave status;
   ```

   `Slave_IO_Running` と `Slave_SQL_Running` が状態 `yes` で、`Seconds_Behind_Master` の値が `0` の場合、レプリケーションは機能しています。 `Seconds_Behind_Master` は、レプリカの遅れの程度を示しています。 値が `0` ではない場合、レプリカで更新が処理されています。

4. 対応するサーバー変数を更新して、データイン レプリケーションをより安全にします (GTID なしのレプリケーションでのみ必要)。

    MariaDB ではネイティブのレプリケーション制限があるため、GTID なしのレプリケーション シナリオでは、[`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) および [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) 変数を設定する必要があります。

    レプリカ サーバーの `sync_master_info` および `sync_relay_log_info` 変数を調べて、データイン レプリケーションが安定していることを確認し、変数を `1` に設定します。

## <a name="other-stored-procedures"></a>その他のストアド プロシージャ

### <a name="stop-replication"></a>レプリケーションの停止

ソースとレプリカのサーバー間でレプリケーションを停止するには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>レプリケーション関係の解除

ソースとレプリカのサーバーの関係を解除するには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>レプリケーション エラーのスキップ

レプリケーション エラーをスキップし、レプリケーションを許可するには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>次のステップ

Azure Database for MariaDB の[データイン レプリケーション](concepts-data-in-replication.md)について確認します。
