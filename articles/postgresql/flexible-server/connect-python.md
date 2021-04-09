---
title: クイック スタート:Python を使用して接続する - Azure Database for PostgreSQL - フレキシブル サーバー
description: このクイックスタートでは、Azure Database for PostgreSQL - フレキシブル サーバーに接続してデータのクエリを実行するために使用できる、Python コード サンプルをいくつか紹介します。
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 89dc36a9b1b1fee9ad10d55945c7fc17bf72f476
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90946191"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>クイック スタート:Python を使用して Azure Database for PostgreSQL - フレキシブル サーバーに接続し、データのクエリを実行する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

このクイックスタートでは、Python を使用して Azure Database for PostgreSQL - フレキシブル サーバーに接続します。 Mac、Ubuntu Linux、Windows の各プラットフォームから SQL ステートメントを使用して、データベース内のデータを照会、挿入、更新、削除できます。 

この記事では、Python を使用した開発には慣れているものの、Azure Database for PostgreSQL - フレキシブル サーバーの使用は初めてであるユーザーを想定しています。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* Azure Database for PostgreSQL - フレキシブル サーバー。 フレキシブル サーバーの作成については、「[Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバーを作成する](./quickstart-create-server-portal.md)」を参照してください。
* [Python](https://www.python.org/downloads/) 2.7.9 以降または 3.4 以降。
* 最新の [pip](https://pip.pypa.io/en/stable/installing/) パッケージ インストーラー。

## <a name="preparing-your-client-workstation"></a>クライアント ワークステーションを準備する
- "*プライベート アクセス (VNet 統合)* " を指定してフレキシブル サーバーを作成した場合は、サーバーと同じ VNet 内のリソースからサーバーに接続する必要があります。 仮想マシンを作成して、フレキシブル サーバーと共に作成された VNet に追加できます。 「[Azure CLI を使用した Azure Database for PostgreSQL - フレキシブル サーバー仮想ネットワークの作成と管理](./how-to-manage-virtual-network-cli.md)」を参照してください。
- "*パブリック アクセス (使用できる IP アドレス)* " を指定してフレキシブル サーバーを作成した場合は、サーバー上のファイアウォール規則のリストにローカル IP アドレスを追加できます。 「[Azure CLI を使用した Azure Database for PostgreSQL - フレキシブル サーバー ファイアウォール規則の作成と管理](./how-to-manage-firewall-cli.md)」を参照してください。

## <a name="install-the-python-libraries-for-postgresql"></a>PostgreSQL 用 Python ライブラリのインストール
[psycopg2](https://pypi.python.org/pypi/psycopg2/) モジュールを使用すると、PostgreSQL データベースに接続してクエリを実行でき、このモジュールは、Linux、macOS、または Windows の [wheel](https://pythonwheels.com/) パッケージとして使用できます。 すべての依存関係を含んだバイナリ バージョンのモジュールをインストールしてください。 `psycopg2` のインストールと要件の詳細については、「[インストール](http://initd.org/psycopg/docs/install.html)」を参照してください。 

`psycopg2`をインストールするには、ターミナルまたはコマンド プロンプトを開き、コマンド `pip install psycopg2` を実行します。

## <a name="get-database-connection-information"></a>データベース接続情報を取得する
Azure Database for PostgreSQL - フレキシブル サーバーに接続するには、完全修飾サーバー名とログイン資格情報が必要です。 この情報は Azure portal から取得できます。

1. [Azure portal](https://portal.azure.com/) で、フレキシブル サーバーの名前を探して選択します。 
2. サーバーの **[概要]** ページで、完全修飾 **サーバー名** と **管理者ユーザー名** をコピーします。 完全修飾 **サーバー名** は常に *\<my-server-name>.postgres.database.azure.com* の形式になります。

   また、管理者パスワードも必要です。 忘れた場合は、概要ページでリセットできます。 

   <!--![Azure Database for PostgreSQL server name](./media/connect-python/1-connection-string.png)-->

## <a name="how-to-run-the-python-examples"></a>Python の例を実行する方法

この記事の各コード例では、次のことを行います。

1. テキスト エディターで新しいファイルを作成します。 

1. ファイルにコード例を追加します。 コードで、次のように置き換えます。
   - `<server-name>` および `<admin-username>` を、Azure portal からコピーした値に指定します。
   - `<admin-password>` をサーバー パスワードに指定します。
   - `<database-name>` を、Azure Database for PostgreSQL - フレキシブル サーバー データベースの名前に置き換えます。 サーバーの作成時に、*postgres* という名前の既定のデータベースが自動的に作成されています。 このデータベースの名前を変更することも、SQL コマンドを使用して新しいデータベースを作成することもできます。 

1. ファイルに *.py* 拡張子を付けてプロジェクト フォルダーに保存します。たとえば、*postgres-insert.py* のように指定します。 Windows の場合は、ファイルを保存するときに UTF-8 エンコードを選択するようにしてください。 

1. ファイルを実行するには、コマンドライン インターフェイスでプロジェクト フォルダーに移動し、「`python`」と入力して、その後にファイル名を入力します (例: `python postgres-insert.py`)。

## <a name="create-a-table-and-insert-data"></a>テーブルを作成してデータを挿入する
次のコード例では、[psycopg2.connect](http://initd.org/psycopg/docs/connection.html) 関数を使用して Azure Database for PostgreSQL - フレキシブル サーバー データベースに接続し、SQL **INSERT** ステートメントを使用してデータを読み込みます。 [cursor. execute](http://initd.org/psycopg/docs/cursor.html#execute) 関数は、データベースに対して SQL クエリを実行します。 

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

![コマンドライン 出力](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>データの読み取り
次のコード例では、Azure Database for PostgreSQL - フレキシブル サーバー データベースに接続し、[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) と SQL **SELECT** ステートメントを使用してデータを読み取ります。 この関数はクエリを受け取り、[cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) を使用して反復処理する結果セットを返します。 

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
# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()
# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>データの更新
次のコード例では、Azure Database for PostgreSQL - フレキシブル サーバー データベースに接続し、[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) と SQL **UPDATE** ステートメントを使用してデータを更新します。 

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
# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>データの削除
次のコード例では、Azure Database for PostgreSQL - フレキシブル サーバー データベースに接続し、[cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) と SQL **DELETE** ステートメントを使用して、前に挿入したインベントリ項目を削除します。 

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
# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [ダンプと復元を使用してデータベースを移行する](../howto-migrate-using-dump-and-restore.md)