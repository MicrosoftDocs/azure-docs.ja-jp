---
title: "Azure Database for PostgreSQL でダンプおよび復元する方法 | Microsoft Docs"
description: "ダンプ ファイルに PostgreSQL データベースを抽出し、Azure Database for PostgreSQL で pg_dump によって作成されたアーカイブ ファイルから PostgreSQL データベースを復元する方法について説明します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/14/2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8a399ca2f529568dbe5ffd4b58c3f0f69248b71d
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>ダンプと復元を使用した PostgreSQL データベースの移行
[pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) を使用して PostgreSQL データベースをダンプ ファイルに抽出し、[pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) を使用して、pg_dump によって作成されたアーカイブ ファイルから PostgreSQL データベースを復元することができます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- アクセスとその下のデータベースを許可するファイアウォール規則のある [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) および [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) コマンド ライン ユーティリティのインストール

以下の手順に従って、PostgreSQL データベースをダンプおよび復元します。

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>読み込まれるデータを含む pg_dump を使用してダンプ ファイルを作成する
オンプレミスまたは VM の既存の PostgreSQL データベースをバックアップするには、次のコマンドを実行します。
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
たとえば、ローカル サーバーとその中に **testdb** というデータベースがあるとします
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>pg_restore を使用して対象の Azure Database for PostrgeSQL にデータを復元する
ターゲット データベースを作成したら、pg_restore コマンドおよび -d、--dbname パラメータを使用して、ダンプ ファイルからターゲット データベースにデータを復元できます。
```bash
pg_restore -v –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
この例では、対象サーバー **mypgserver-20170401.postgres.database.azure.com** 上のデータベース **mypgsqldb** に、ダンプ ファイル **testdb.dump** からデータを復元します。
```bash
pg_restore -v --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>次のステップ
- エクスポートとインポートを使用して PostgreSQL データベースを移行するには、「[Migrate your PostgreSQL database using export and import (エクスポートとインポートを使用した PostgreSQL の移行)](howto-migrate-using-export-and-import.md)」を参照してください。
