---
title: クイック スタート:Python を使用して接続する - Azure Database for MySQL
description: このクイックスタートでは、Azure Database for MySQL に接続してデータを照会するために使用できる、Python コード サンプルをいくつか紹介します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: a4391ecb7175b0e473b47cc3de43fd113795bc6b
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889027"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>クイック スタート:Python を使用して Azure Database for MySQL に接続してデータを照会する

このクイックスタートでは、Python を使用して Azure Database for MySQL に接続します。 Mac、Ubuntu Linux、Windows の各プラットフォームから SQL ステートメントを使用して、データベース内のデータを照会、挿入、更新、削除できます。 

## <a name="prerequisites"></a>前提条件
このクイックスタートでは、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free)。
- [Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) を使用して、Azure Database for MySQL シングル サーバーを作成します <br/> [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) を使用して、Azure Database for PostgreSQL の単一サーバーを作成します (まだない場合)。
- パブリック アクセスとプライベート アクセスのどちらを使用しているかに基づいて、次の **いずれか** のアクションを実行して、接続を有効にします。

   |アクション| 接続方法|ハウツー ガイド|
   |:--------- |:--------- |:--------- |
   | **ファイアウォール規則を構成する** | パブリック | [ポータル](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
   | **サービス エンドポイントを構成する** | パブリック | [ポータル](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
   | **プライベート リンクを構成する** | プライベート | [ポータル](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [データベースと管理者以外のユーザーを作成する](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>Python と MySQL コネクタのインストール

次の手順を使用して、お使いのコンピューターに Python と Python 用 MySQL コネクタをインストールします。 

> [!NOTE]
> このクイックスタートでは、[MySQL Connector/Python 開発者ガイド](https://dev.mysql.com/doc/connector-python/en/)を使用しています。

1. ご使用の OS に [Python 3.7 以上](https://www.python.org/downloads/)をダウンロードしてインストールします。 MySQL コネクタで必要となるため、必ず Python を `PATH` に追加します。
   
2. コマンド プロンプトまたは `bash` シェルを開き、大文字の V スイッチを指定して `python -V` を実行して、Python のバージョンを確認します。
   
3. `pip` パッケージ インストーラーは、Python の最新バージョンに含まれています。 `pip install -U pip` を実行して、`pip` を最新バージョンに更新します。 
   
   `pip` がインストールされていない場合は、`get-pip.py` を使用してダウンロードおよびインストールできます。 詳細については、「[インストール](https://pip.pypa.io/en/stable/installing/)」を参照してください。 
   
4. `pip` を使用して、Python 用 MySQL コネクタとその依存関係をインストールします。
   
   ```bash
   pip install mysql-connector-python
   ```
   
[問題がある場合は、お知らせください](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>接続情報の取得

Azure portal から Azure Database for MySQL に接続するために必要な接続情報を取得します。 サーバー名、データベース名、およびログイン資格情報が必要です。

1. [Azure portal](https://portal.azure.com/) にサインインします。
   
1. ポータルの検索バーで、作成した Azure Database for MySQL サーバー (たとえば、**mydemoserver**) を検索して選択します。
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL サーバー名":::
   
1. サーバーの **[概要]** ページから、 **[サーバー名]** と **[サーバー管理者ログイン名]** を書き留めます。 パスワードを忘れた場合も、このページからパスワードをリセットすることができます。
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL サーバー名 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>手順 1:テーブルを作成してデータを挿入する

次のコードを使用して、サーバーとデータベースに接続し、テーブルを作成した後、**INSERT** SQL ステートメントを使用してデータを読み込みます。コードによって mysql.connector ライブラリがインポートされ、以下のメソッドが使用されます。
- [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) 関数は、config コレクションの[引数](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html)を使用して、Azure Database for MySQL に接続します。 
- [cursor. execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) メソッドは、MySQL データベースに対して SQL クエリを実行します。 
- [cursor.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) は、カーソルの使用が終了したときに使用されます。
- [conn.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) は、接続を閉じます。

> [!IMPORTANT]
> - SSL は、既定で有効になっています。 ローカル環境から接続するには、[DigiCertGlobalRootG2 SSL 証明書](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)をダウンロードする必要がある場合があります。
> - `<mydemoserver>`、`<myadmin>`、`<mypassword>`、および `<mydatabase>` の各プレースホルダーを、実際の MySQL サーバーとデータベースの値に置き換えます。

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [mysql.connector.ClientFlag.SSL],
  'ssl_ca': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

[問題がある場合は、お知らせください](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>手順 2:データの読み取り

接続し、**SELECT** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 コードは mysql.connector ライブラリをインポートし、[cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) メソッドを使用して MySQL データベースに対して SQL クエリを実行します。 

このコードでは、[fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) メソッドを使用してデータ行を読み取り、結果セットをコレクション行に保持し、`for` 反復子を使用して行をループ処理します。

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>手順 3:データの更新

接続し、**UPDATE** SQL ステートメントを使用してデータを更新するには、次のコードを使用します。 コードは mysql.connector ライブラリをインポートし、[cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) メソッドを使用して MySQL データベースに対して SQL クエリを実行します。 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>手順 4:データの削除

接続し、**DELETE** SQL ステートメントを使用してデータを削除するには、次のコードを使用します。 コードは mysql.connector ライブラリをインポートし、[cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) メソッドを使用して MySQL データベースに対して SQL クエリを実行します。 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
```

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
> [CLI を使用して Azure Database for MySQL サーバーを管理する](./how-to-manage-single-server-cli.md)

[お探しの情報が見つからない場合は、お知らせください。](https://aka.ms/mysql-doc-feedback)
