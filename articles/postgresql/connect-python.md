---
title: クイック スタート:Python を使用して接続する - Azure Database for PostgreSQL - Single Server
description: このクイックスタートでは、Azure Database for PostgreSQL - Single Server に接続してデータを照会するために使用できる、Python コード サンプルを紹介します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: db94a82112f2670facd4d89178f11653c5316c36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998942"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>クイック スタート:Python を使用して Azure Database for PostgreSQL - Single Server に接続し、データにクエリを実行する

このクイックスタートでは、Azure Database for PostgreSQL 単一サーバー上のデータベースに接続し、SQL ステートメントを実行して macOS、Ubuntu Linux、または Windows で Python を使用したクエリを実行する方法について説明します。

> [!TIP]
> PostgreSQL を使用する Django アプリケーションを構築する場合は、[PostgreSQL を使用した Django Web アプリのデプロイ](../app-service/tutorial-python-postgresql-app.md)に関するチュートリアルを参照してください。


## <a name="prerequisites"></a>前提条件
このクイックスタートでは、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free)。
- [Azure portal](./quickstart-create-server-database-portal.md) または <br/> [Azure CLI](./quickstart-create-server-database-azure-cli.md) を使用して、Azure Database for PostgreSQL の単一サーバーを作成します (まだない場合)。
- パブリック アクセスとプライベート アクセスのどちらを使用しているかに基づいて、次の **いずれか** のアクションを実行して、接続を有効にします。

  |アクション| 接続方法|ハウツー ガイド|
  |:--------- |:--------- |:--------- |
  | **ファイアウォール規則を構成する** | パブリック | [ポータル](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **サービス エンドポイントを構成する** | パブリック | [ポータル](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **プライベート リンクを構成する** | プライベート | [ポータル](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7.9 以降または 3.4 以降。

- 最新の [pip](https://pip.pypa.io/en/stable/installing/) パッケージ インストーラー。
- ターミナルまたはコマンド プロンプト ウィンドウで `pip install psycopg2` を使用して [psycopg2](https://pypi.python.org/pypi/psycopg2/) をインストールします。 詳細については、[`psycopg2` のインストール方法](http://initd.org/psycopg/docs/install.html)を参照してください。

## <a name="get-database-connection-information"></a>データベース接続情報を取得する
Azure Database for PostgreSQL データベースに接続するには、完全修飾サーバー名とログイン資格情報が必要です。 この情報は Azure portal から取得できます。

1. [Azure portal](https://portal.azure.com/) で、ご利用の Azure Database for PostgreSQL サーバー名を検索して選択します。
1. サーバーの **[概要]** ページで、完全修飾 **サーバー名** と **管理者ユーザー名** をコピーします。 完全修飾 **サーバー名** は常に *\<my-server-name>.postgres.database.azure.com* の形式になり、**管理者ユーザー名** は常に *\<my-admin-username>@\<my-server-name>* の形式になります。

   また、管理者パスワードも必要です。 忘れた場合は、このページでリセットできます。

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Azure Database for PostgreSQL サーバーの名前":::

> [!IMPORTANT]
>  次の値を置き換えます。
>   - `<server-name>` および `<admin-username>` を、Azure portal からコピーした値に指定します。
>   - `<admin-password>` をサーバー パスワードに指定します。
>   - `<database-name>`: サーバーの作成時に、*postgres* という名前の既定のデータベースが自動的に作成されています。 このデータベースの名前を変更することも、SQL コマンドを使用して[新しいデータベースを作成する](https://www.postgresql.org/docs/9.0/sql-createdatabase.html)こともできます。

## <a name="step-1-connect-and-insert-data"></a>手順 1: データの接続と挿入
次のコード例では、以下を使用して Azure Database for PostgreSQL データベースに接続します
-  [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) 関数。次に、SQL **INSERT** ステートメントを使用してデータを読み込みます。
- [cursor. execute](http://initd.org/psycopg/docs/cursor.html#execute) 関数は、データベースに対して SQL クエリを実行します。

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

コードが正常に実行されると、次の出力が生成されます。

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="コマンドライン 出力":::


[問題がある場合は、お知らせください](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>手順 2:データの読み取り
次のコード例では、Azure Database for PostgreSQL データベースに接続して、以下を使用します
- [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) と SQL **SELECT** ステートメントを使用してデータを読み取ります。
- [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) は、クエリを受け取り、反復処理する結果セットを返します

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[問題がある場合は、お知らせください](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>手順 3:データの更新
次のコード例では、[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) と SQL **UPDATE** ステートメントを使用してデータを更新します。

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[問題がある場合は、お知らせください](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>手順 5: データの削除
次のコード例では、[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) と **DELETE** SQL ステートメントを実行して、先ほど挿入した inventory 項目を削除します。

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[問題がある場合は、お知らせください](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで使用したすべてのリソースをクリーンアップするには、次のコマンドを使用してリソース グループを削除します。

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [ポータルを使用して Azure Database for MySQL サーバーを管理する](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLI を使用して Azure Database for MySQL サーバーを管理する](./how-to-manage-server-cli.md)<br/>

[お探しの情報が見つからない場合は、お知らせください。](https://aka.ms/postgres-doc-feedback)
