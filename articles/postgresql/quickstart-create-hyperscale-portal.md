---
title: 分散テーブルを作成する – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) で分散型テーブルを作成し、クエリを実行するためのクイックスタート。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 02e009e6fff2e717693d1579d409199ab179d941
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973419"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>クイック スタート:Azure portal で Azure Database for PostgreSQL - Hyperscale (Citus) を作成する

Azure Database for PostgreSQL は、高可用性の PostgreSQL データベースをクラウドで実行、管理、スケールできるマネージド サービスです。 このクイック スタートでは、Azure portal を使用して Azure Database for PostgreSQL - Hyperscale (Citus) サーバー グループを作成する方法について説明します。 ノード間でテーブルをシャード化したり、サンプル データを取り込んだり、複数のノードでクエリを実行したり、分散データをいろいろ試してみます。

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>テーブルを作成して配布する

psql を使用し、ハイパースケール コーディネーター ノードに接続したら、基本的なタスクの一部を完了できます。

ハイパースケール サーバー内には、3 種類のテーブルがあります。

- 分散またはシャード化されたテーブル (パフォーマンスと並列処理のための拡張を支援する目的で分散されます)
- 参照テーブル (複数のコピーを保持)
- ローカル テーブル (多くの場合、内部管理テーブルとして使用)

このクイック スタートでは、分散テーブルとこれをよく理解することに重点を置きます。

これから使用するデータ モデルはシンプルです。GitHub のユーザーとイベント データを使用します。 イベントには、フォーク作成や組織に関連する git コミットなどがあります。

psql 経由で接続できたら、テーブルを作成しましょう。 psql コンソールで次を実行します。

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`github_events` の `payload` フィールドには JSONB データ型があります。 JSONB は Postgres におけるバイナリ形式の JSON データ型です。 データ型により、柔軟なスキーマを 1 つの列に簡単に格納できます。

Postgres では、その中にあるすべてのキーと値にインデックスを作成するこの型で `GIN` インデックスを作成できます。 インデックスがあると、さまざまな条件でペイロードに簡単にクエリを実行できます。 それでは、データを読み込む前にインデックスをいくつか作成してみましょう。 psql で:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

次に、コーディネーター ノードの Postgres テーブルを選択し、worker 間でシャード化するように Hyperscale に指示します。 その際、シャード化するためのキーを指定し、各テーブルにクエリを実行します。 現在の例では、`user_id` でイベントとユーザーのテーブルをシャード化します。

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

データを読み込む準備ができました。 psql でも、シェルアウトして、ファイルをダウンロードします。

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

次に、ファイルから分散テーブルに、データを読み込みます。

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>クエリを実行する

次は面白い部分です。クエリをいくつか実際に実行します。 単純な `count (*)` から初めて読み込んだデータの量を確認しましょう。

```sql
SELECT count(*) from github_events;
```

問題なく動作しました。 この種の集計にはすぐに戻りますが、今は他のクエリをいくつか見てみましょう。 JSONB `payload` 列内に、データが数多くありますが、これはイベントの種類によって異なります。 `PushEvent` イベントには、プッシュ用の個別コミットが多く含まれるサイズがあります。 これを使用し、時間あたりのコミット総数を見つけることができます。

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

これまでのところ、クエリには github\_events だけが関係していましたが、この情報を github\_users と組み合わせることができます。 ユーザーとイベントを両方、同じ ID (`user_id`) でシャード化したため、ユーザー ID が一致する両テーブルの行は同じデータベース ノードと[同じ場所に配置](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation)され、簡単に結合できます。

`user_id` で結合した場合、Hyperscale は、worker ノードで並列実行するため、結合実行をシャードにプッシュできます。 たとえば、リポジトリを最も多く作成したユーザーを見つけましょう。

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

前の手順では、サーバー グループ内に Azure リソースを作成しました。 これらのリソースが将来不要であると思われる場合は、サーバー グループを削除します。 サーバー グループの **[概要]** ページで、 **[削除]** ボタンを押します。 ポップアップ ページでメッセージが表示されたら、サーバー グループの名前を確認し、最後の **[削除]** ボタンをクリックします。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Hyperscale (Citus) サーバー グループのプロビジョニング方法を学習しました。 そのサーバー グループに psql で接続し、スキーマを作成して、データを分散しました。

次に、チュートリアルに従い、拡張性の高いマルチテナント アプリケーションを構築します。
> [!div class="nextstepaction"]
> [マルチテナント データベースを設計する](https://aka.ms/hyperscale-tutorial-multi-tenant)
