---
title: データベースの破損を解決する - Azure Database for MySQL
description: この記事では、Azure Database for MySQL でデータベースの破損の問題を解決する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766883"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Azure Database for MySQL でデータベースの破損をトラブルシューティングする
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

データベースが破損すると、アプリケーションのダウンタイムが発生する可能性があります。 また、データが損失しないうちに破損の問題を解決することも重要です。 データベースの破損が発生すると、サーバー ログに次のエラーが表示されます: `InnoDB: Database page corruption on disk or a failed.`

この記事では、データベースまたはテーブルの破損の問題を解決する方法について説明します。 Azure Database for MySQL では InnoDB エンジンが使用されます。 この機能により、破損チェックと修復操作が自動化されます。 InnoDB では、読み取られるすべてのページでチェックサムを実行することで、破損したページが確認されます。 チェックサムの不一致が見つかった場合は、MySQL サーバーが自動的に停止されます。

データベースの破損の問題を迅速に軽減するには、次のオプションを試してください。

## <a name="restart-your-mysql-server"></a>MySQL サーバーを再起動する

通常、データベースまたはテーブルが破損していることがわかるのは、アプリケーションがそのテーブルまたはデータベースにアクセスしたときです。 InnoDB にはクラッシュ復旧メカニズムがあり、サーバーの再起動時にほとんどの問題を解決できます。 したがって、サーバーを再起動すると、データベースが異常な状態になる原因になったクラッシュからサーバーを復旧するのに役立ちます。

## <a name="use-the-dump-and-restore-method"></a>ダンプして復元する方法を使用する

"*ダンプして復元する*" 方法を使用して破損の問題を解決することをお勧めします。 この方法の手順は次のとおりです。
1. 破損したテーブルにアクセスします。
1. mysqldump ユーティリティを使用して、テーブルの論理バックアップを作成します。 このバックアップには、テーブル構造とその中のデータが保持されます。
1. データベースにテーブルを再読み込みします。

### <a name="back-up-your-database-or-tables"></a>データベースまたはテーブルをバックアップする

> [!Important]
> - クライアント コンピューターからサーバーにアクセスするために、ファイアウォール規則が構成されていることを確認します。 詳細については、[単一サーバーでのファイアウォール規則の構成](howto-manage-firewall-using-portal.md)および[フレキシブル サーバーでのファイアウォール規則の構成](flexible-server/how-to-connect-tls-ssl.md)に関するページを参照してください。
> - SSL が有効になっている場合は、mysqldump で SSL オプション `--ssl-cert` を使用します。

mysqldump を使用してコマンド ラインからバックアップ ファイルを作成します。 次のコマンドを実行します。

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

パラメーターについて次に説明します。
- `[ssl-cert=/path/to/pem]`:SSL 証明書のパス。 クライアント コンピューター上の SSL 証明書をダウンロードし、コマンドでそのパスを設定します。 SSL が無効になっている場合は、このパラメーターを使用しないでください。
- `[host]`:Azure Database for MySQL サーバー。
- `[uname]`:サーバーの管理者ユーザーの名前。
- `[pass]`:管理者ユーザーのパスワード。
- `[dbname]`:データベースの名前。
- `[backupfile.sql]`:データベース バックアップのファイル名。

> [!Important]
> - 単一サーバーの場合は、形式 `admin-user@servername` を使用して、次のコマンドの `myserveradmin` を置き換えます。
> - フレキシブル サーバーの場合は、形式 `admin-user` を使用して、次のコマンドの `myserveradmin` を置き換えます。

特定のテーブルが破損している場合は、バックアップするデータベース内の特定のテーブルを選択します。
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

1 つ以上のデータベースをバックアップするには、`--database` スイッチを使用し、スペースで区切ってデータベース名の一覧を指定します。

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>データベースまたはテーブルを復元する

次の手順では、データベースまたはテーブルを復元する方法について説明します。 バックアップ ファイルを作成したら、mysql ユーティリティを使用して、テーブルまたはデータベースを復元できます。 次のコマンドを実行します。

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
これは、mysqldump で作成されたバックアップ ファイルから `testdb` を復元する例です。 

> [!Important]
> - 単一サーバーの場合は、形式 `admin-user@servername` を使用して、次のコマンドの `myserveradmin` を置き換えます。
> - フレキシブル サーバーの場合は、形式 ```admin-user``` を使用して、次のコマンドの `myserveradmin` を置き換えます。 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>次の手順
上記の手順を実行しても問題が解決しない場合は、いつでもサーバー全体を復元できます。
- [Azure Database for MySQL でサーバーを復元する - 単一サーバー](howto-restore-server-portal.md)
- [Azure Database for MySQL でサーバーを復元する - フレキシブル サーバー](flexible-server/how-to-restore-server-portal.md)



