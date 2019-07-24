---
title: Azure Database for PostgreSQL でのインポートとエクスポートを使用したデータベースの移行
description: PostgreSQL データベースをスクリプト ファイルに抽出し、そのファイルから対象のデータベースにデータをインポートする方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: ecd7dc225379fc9d3eda6fb2e80e3c47a73db49b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547628"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>エクスポートとインポートを使用した PostgreSQL データベースの移行
[pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) を使用することで、PostgreSQL データベースをスクリプト ファイルに抽出できます。また、[psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) を使用することで、そのファイルから対象のデータベースにデータをインポートできます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- 内部へのアクセスとデータベース化を許可するファイアウォール規則を備えた [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)
- インストールされた [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) コマンド ライン ユーティリティ
- インストールされた [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) コマンド ライン ユーティリティ

次の手順に従って PostgreSQL データベースのエクスポートとインポートを行います。

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>pg_dump を使用して読み込むデータが含まれたスクリプト ファイルを作成する
オンプレミスまたは VM 内にある既存の PostgreSQL データベースを SQL スクリプト ファイルにエクスポートするには、既存の環境で次のコマンドを実行します。
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
たとえば、ローカル サーバーとその中に **testdb** というデータベースがあるとします。
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>対象の Azure Database for PostgreSQL にデータをインポートする
psql コマンド ラインと --dbname パラメーター (-d) を使用して、Azure Database for PostgreSQL サーバーにデータをインポートし、SQL ファイルからデータを読み込みます。
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
この例は、psql ユーティリティと前の手順で作成した **testdb.sql** という名前のスクリプト ファイルを使って、対象サーバー **mydemoserver.postgres.database.azure.com** のデータベース **mypgsqldb** にデータをインポートします。
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>次の手順
- ダンプと復元を使用して PostgreSQL データベースを移行するには、「[ダンプと復元を使用した PostgreSQL データベースの移行](howto-migrate-using-dump-and-restore.md)」をご覧ください。
- Azure Database for PostgreSQL へのデータベースの移行については、「[Database Migration Guide](https://aka.ms/datamigration)」 (データベースの移行ガイド) を参照してください。 
