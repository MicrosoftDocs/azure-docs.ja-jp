---
title: PostgreSQL データベースから Azure Arc 対応 PostgreSQL Hyperscale サーバー グループにデータを移行する
titleSuffix: Azure Arc enabled database services
description: PostgreSQL データベースから Azure Arc 対応 PostgreSQL Hyperscale サーバー グループにデータを移行する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d9cbfc30b10373ad2a4f4304987dac426b5dcabe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643577"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループに PostgreSQL データベースを移行する

このドキュメントでは、既存の PostgreSQL データベース (Azure Arc 対応 Data Services でホストされていないもの) を Azure Arc 対応 PostgreSQL Hyperscale サーバー グループに移行する手順について説明します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>考慮事項

Azure Arc 対応 PostgreSQL Hyperscale サーバー グループは、PostgreSQL のコミュニティ バージョンであり、CitusData 拡張機能が有効な状態で実行されます。 したがって、Azure Arc の外部にある PostgreSQL で動作するすべてのツールが、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループで動作するはずです。


そのため、現在 Postgres で使用している一連のツールを使用して、次のことができます。
1. Azure Arc の外部でホストされているインスタンスから Postgres データベースをバックアップする
2. Azure Arc 対応 PostgreSQL Hyperscale サーバー グループにそれを復元する

ユーザーは次のことを行うことができます。
- サーバー パラメーターをリセットする
- セキュリティ コンテキストをリセットする: ユーザーやロールの再作成、アクセス許可のリセット...

このバックアップと復元操作を行うには、Postgres のバックアップと復元を実行できる任意のツールを使用できます。 次に例を示します。
- Azure Data Studio とその Postgres 拡張機能
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>例
`pgAdmin` ツールを用いたそれらの手順について説明します。
次のセットアップを検討します。
- **ソース:**  
    オンプレミスのベア メタル サーバー上で実行されている、JEANYDSRV という名前の Postgres サーバー。 これはバージョン 12 で、それによってホストされている MyOnPremPostgresDB という名前のデータベースには 1 行だけのテーブル T1 が含まれます :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="Migrate-source":::

- **ターゲット:**  
    Azure Arc 環境で実行されている、postgres01 という名前の Postgres サーバー。 バージョン 12 です。 標準の Postgres データベース以外のデータベースはありません。  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Migrate-destination":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>オンプレミスのソース データベースのバックアップを作成する

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migrate-source-backup":::

それを構成します。
1. ファイル名を指定します。**MySourceBackup**
2. 形式を **カスタム** に設定します
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="Migrate-source-backup-configure":::

バックアップが正常に完了します。  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migrate-source-backup-completed":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの移行先システムに空のデータベースを作成する

> [!NOTE]
> `pgAdmin` ツールで Postgres インスタンスを登録するには、Kubernetes クラスターでのインスタンスのパブリック IP を使用し、ポートとセキュリティ コンテキストを適切に設定する必要があります。 これらの詳細は、次のコマンドを実行して `psql` エンドポイントの行で確認できます。

```console
azdata arc postgres endpoint list -n postgres01
```
次のような出力が返されます。
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

移行先データベースに **RESTORED_MyOnPremPostgresDB** という名前を付けます。

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migrate-destination-db-create" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Arc セットアップでデータベースを復元する

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migratre-db-restore":::

復元を構成します。
1. 復元するバックアップが含まれているファイルを指定します。**MySourceBackup**
2. 形式は **カスタムまたは tar** に設定したままにします
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="Migrate-db-restore-configure":::

3. **[復元]** をクリックします。  

   復元が成功します。  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migrate-db-restore-completed":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループで、データベースが正常に復元されたことを確認します

次のいずれかの方法を使用します。

**`pgAdmin` から:**  

Azure Arc のセットアップでホストされている Postgres インスタンスを展開します。 復元したデータベースのテーブルが表示され、データを選択すると、オンプレミスのインスタンスと同じ行が表示されます。

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migrate-db-restore-verification":::

**Azure Arc セットアップ内の `psql` から:**  

Arc セットアップ内では、`psql` を使用して Postgres インスタンスに接続し、データベース コンテキストを `RESTORED_MyOnPremPostgresDB` に設定して、データのクエリを実行できます。

1. `psql` の接続文字列から役に立つエンド ポイントの一覧を表示します。

   ```console
   azdata arc postgres endpoint list -n postgres01
   [
     {
       "Description": "PostgreSQL Instance",
       "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. `psql` の接続文字列から、`-d` パラメーターを使用してデータベース名を指定します。 次のコマンドでは、パスワードの入力を求められます。

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` が接続します。

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. テーブルを選択すると、オンプレミスの Postgres インスタンスから復元したデータが表示されます。

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - Azure Arc 対応 PostgreSQL Hyperscale をスケールアウトして、PostgreSQL Hyperscale サーバー グループのワーカー ノード全体にデータをシャードおよび分散することによるパフォーマンス上の利点はあまりありません。 「[次のステップ](#next-steps)」を参照してください。
>
> - 現在、オンプレミスまたはその他のクラウドで実行されている既存の Postgres インスタンスを "Azure Arc にオンボードする" ことはできません。 つまり、既存の Postgres インスタンスに何らかの種類の "Azure Arc エージェント" をインストールして、Azure Arc で Postgres セットアップ対応にすることはできません。代わりに、新しい Postgres インスタンスを作成し、そこにデータを転送する必要があります。 上記の方法を使用してこれを行うことも、任意の ETL ツールを使用することもできます。

## <a name="next-steps"></a>次のステップ

- 複数の PostgreSQL Hyperscale ノードにデータを分散し、Azure Database for PostgreSQL Hyperscale のすべての利点を活用するために、次の Azure Database for PostgreSQL Hyperscale の概念と使い方に関するガイドを参照してください。
    * [ノードとテーブル](../../postgresql/concepts-hyperscale-nodes.md)
    * [アプリケーションの種類の決定](../../postgresql/concepts-hyperscale-app-type.md)
    * [ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)
    * [テーブルの配布と変更](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [マルチテナント データベースを設計する](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [リアルタイム分析ダッシュボードを設計する](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> \* これらのドキュメントで、**Azure portal へのサインイン** および **Azure Database for PostgreSQL - Hyperscale (Citus) の作成** に関するセクションはスキップしてください。 Azure Arc デプロイの残りの手順を実装します。 これらのセクションは Azure クラウドで PaaS サービスとして提供される Azure Database for PostgreSQL Hyperscale (Citus) に固有のものですが、ドキュメントの他の部分は Azure Arc 対応 PostgreSQL Hyperscale に直接適用できます。

- [Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアウト](scale-out-postgresql-hyperscale-server-group.md)
