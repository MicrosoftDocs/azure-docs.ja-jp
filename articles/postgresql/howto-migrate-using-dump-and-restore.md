---
title: ダンプと復元 - Azure Database for PostgreSQL - Single Server
description: PostgreSQL データベースをダンプ ファイルに抽出し、Azure Database for PostgreSQL - Single Server で pg_dump によって作成されたファイルから復元する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 16166183b56b371fe8338894f83dbacf2e659c53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563557"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>ダンプと復元を使用した PostgreSQL データベースの移行
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) を使用して PostgreSQL データベースをダンプ ファイルに抽出し、[pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) を使用して、pg_dump によって作成されたアーカイブ ファイルから PostgreSQL データベースを復元することができます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- アクセスとその下のデータベースを許可するファイアウォール規則のある [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) および [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) コマンド ライン ユーティリティのインストール

以下の手順に従って、PostgreSQL データベースをダンプおよび復元します。

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>読み込まれるデータを含む pg_dump を使用してダンプ ファイルを作成する
オンプレミスまたは VM の既存の PostgreSQL データベースをバックアップするには、次のコマンドを実行します。
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
たとえば、ローカル サーバーとその中に **testdb** というデータベースがあるとします
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>pg_restore を使用して対象の Azure Database for PostgreSQL にデータを復元する
ターゲット データベースを作成すると、pg_restore コマンドおよび -d、--dbname パラメータを使用して、ダンプ ファイルからターゲット データベースにデータを復元できます。
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

--no-owner パラメーターを指定すると、復元中に作成されるすべてのオブジェクトは、--username に指定されたユーザーによって所有されます。 詳細については、[pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) の公式 PostgreSQL ドキュメントを参照してください。

> [!NOTE]
> お使いの PostgreSQL サーバーで TLS または SSL 接続を必要とする場合 (Azure Database for PostgreSQL サーバーでは既定でオン) は、pg_restore ツールが TLS で接続するように、環境変数 `PGSSLMODE=require` を設定します。 TLS を使用しない場合、エラー `FATAL:  SSL connection is required. Please specify SSL options and retry.` が表示される場合があります。
>
> Windows コマンド ラインで、コマンド `SET PGSSLMODE=require` を実行してから、pg_restore コマンドを実行します。 Linux または Bash では、コマンド `export PGSSLMODE=require` を実行してから、pg_restore コマンドを実行します。
>

この例では、対象サーバー **mydemoserver.postgres.database.azure.com** 上のデータベース **mypgsqldb** に、ダンプ ファイル **testdb.dump** からデータを復元します。

この **pg_restore** を **単一サーバー** で使用する方法の例を次に示します。

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```
この **pg_restore** を **フレキシブル サーバー** で使用する方法の例を次に示します。

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```
---

## <a name="optimizing-the-migration-process"></a>移行プロセスの最適化

既存の PostgreSQL データベースを Azure Database for PostgreSQL サービスに移行する方法の 1 つは、ソース側のデータベースをバックアップし、そのバックアップを Azure に復元することです｡ 移行に要する時間を最小限に抑えるには、バックアップと復元のコマンドに次のパラメーターを使用することを検討してください｡

> [!NOTE]
> 構文についての詳細は、[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) と [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) の記事を参照してください｡
>

### <a name="for-the-backup"></a>バックアップ
- バックアップと平行して復元を行うことで処理をスピードアップできるよう､バックアップには -FC スイッチを使用します｡ 次に例を示します。

    ```bash
    pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
    ```

### <a name="for-the-restore"></a>復元
- バックアップ ファイルは､移行先の Azure Database for PostgreSQL サーバーと同じリージョンにある Azure VM に移動し、その VM から pg_restore を実行することで､ネットワーク待機時間を短縮することをお勧めします。 また､VM は [accelerated networking](../virtual-network/create-vm-accelerated-networking-powershell.md) を有効にした状態で作成することもお勧めします。

- 既定では、すでにその設定になっているはずですが､ダンプ ファイルを開いて､データの insert 後に create index ステートメントがあることを確認してください｡ そのようになっていない場合は、データの insert の後に create index ステートメントを移動します。

- -fc および -j *#* スイッチを付けて復元することで､復元を並列処理します。 *#* ターゲット サーバーのコアの数です。 *#* をターゲット サーバーのコア数の 2 倍に設定することで､その影響を確認することもできます｡ 次に例を示します。

この **pg_restore** を **単一サーバー** で使用する方法の例を次に示します。
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
```
この **pg_restore** を **フレキシブル サーバー** で使用する方法の例を次に示します。
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
 ```

- ダンプ ファイルを編集することもできます｡先頭に *set synchronous_commit = off;* コマンド､最後に *set synchronous_commit = on;* コマンドを追加してください｡ アプリによってデータが変更される前の､最後にオンにするようにしないと､後続データが失われることがあります。

- ターゲットの Azure Database for PostgreSQL サーバーで、復元の前に以下を行うことを検討してください。
    - クエリ パフォーマンスの追跡をオフにする。これら統計は移行中は必要ないからです。 これを行うには、pg_stat_statements.track、pg_qs.query_capture_mode、および pgms_wait_sampling.query_capture_mode を NONE に設定します。

    - 移行の速度を上げるために、32 vCore メモリ最適化のようなハイ コンピューティングとハイ メモリの SKU を使用する。 復元が完了したら、希望する SKU に簡単に戻すことができます。 SKU が大きいほど、pg_restore コマンドの対応する `-j` パラメーターを増やすことで、より多くの並行処理を実現できます。

    - ターゲット サーバーの IOPS を上げると、復元のパフォーマンスが向上する可能性がある。 サーバーのストレージ サイズを増やすことで、より高い IOPS をプロビジョニングできます。 この設定は元に戻せませんが、IOPS を高くすることで、将来実際のワークロードにメリットがあるかどうか検討してください。

これらのコマンドは､運用環境で使用する前にテスト環境でテスト､検証してください。

## <a name="next-steps"></a>次の手順
- エクスポートとインポートを使用して PostgreSQL データベースを移行するには、「[エクスポートとインポートを使用した PostgreSQL の移行](howto-migrate-using-export-and-import.md)」を参照してください。
- Azure Database for PostgreSQL へのデータベースの移行については、「[Database Migration Guide](https://aka.ms/datamigration)」 (データベースの移行ガイド) を参照してください。
