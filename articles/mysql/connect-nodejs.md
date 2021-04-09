---
title: クイック スタート:Node.js を使用して接続する - Azure Database for MySQL
description: このクイックスタートでは、Azure Database for MySQL に接続してデータを照会するために使用できる、Node.js コード サンプルをいくつか紹介します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 12/11/2020
ms.openlocfilehash: 6a9134e13e3145daea1eed81c4aa8795a0a49950
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588235"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>クイック スタート:Node.js を使用して Azure Database for MySQL に接続してデータを照会する

このクイックスタートでは、Node.js を使用して Azure Database for MySQL に接続します。 Mac、Ubuntu Linux、Windows の各プラットフォームから SQL ステートメントを使用して、データベース内のデータを照会、挿入、更新、削除できます。 

このトピックでは、Node.js を使用した開発には慣れているものの、Azure Database for MySQL の使用は初めてであるユーザーを想定しています。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure Database for MySQL サーバー。 [Azure portal を使用して Azure Database for MySQL サーバーを作成](quickstart-create-mysql-server-database-using-azure-portal.md)するか、[Azure CLI を使用して Azure Database for MySQL サーバーを作成](quickstart-create-mysql-server-database-using-azure-cli.md)してください。

> [!IMPORTANT] 
> [Azure portal](./howto-manage-firewall-using-portal.md) または [Azure CLI](./howto-manage-firewall-using-cli.md) を使用して、接続元の IP アドレスにサーバーのファイアウォール規則が追加されていることを確認します。

## <a name="install-nodejs-and-the-mysql-connector"></a>Node.js と MySQL コネクタのインストール

プラットフォームに応じて、該当するセクションの手順に従って [Node.js](https://nodejs.org) をインストールしてください。 npm を使用して [mysql](https://www.npmjs.com/package/mysql) パッケージとその依存関係をプロジェクト フォルダーにインストールしてください。

### <a name="windows"></a>Windows

1. [Node.js ダウンロード ページ](https://nodejs.org/en/download/)にアクセスし、必要な Windows インストーラー オプションを選択します。
2. ローカル プロジェクト フォルダーを作成します (例: `nodejsmysql`)。 
3. コマンド プロンプトを開き、ディレクトリをプロジェクト フォルダーに変更します (例: `cd c:\nodejsmysql\`)。
4. NPM ツールを実行して、mysql ライブラリをプロジェクト フォルダーにインストールします。

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. `npm list` の出力テキストを確認してインストール結果を検証します。 バージョン番号は、新しいパッチのリリースに伴って変わる場合があります。

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. 次のコマンドを実行して、**Node.js** と、Node.js 用のパッケージ マネージャー **npm** をインストールします。

   ```bash
    # Using Ubuntu
    curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
    sudo apt-get install -y nodejs

    # Using Debian, as root
    curl -sL https://deb.nodesource.com/setup_14.x | bash -
    apt-get install -y nodejs
   ```

2. 次のコマンドを実行して、`mysqlnodejs` というプロジェクト フォルダーを作成し、そのフォルダーに mysql パッケージをインストールします。

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. npm list の出力テキストを確認してインストール結果を検証します。 バージョン番号は、新しいパッチのリリースに伴って変わる場合があります。

### <a name="macos"></a>macOS

1. [Node.js ダウンロード ページ](https://nodejs.org/en/download/)にアクセスし、macOS インストーラーを選択します。

2. 次のコマンドを実行して、`mysqlnodejs` というプロジェクト フォルダーを作成し、そのフォルダーに mysql パッケージをインストールします。

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. `npm list` の出力テキストを確認してインストール結果を検証します。 バージョン番号は、新しいパッチのリリースに伴って変わる場合があります。

## <a name="get-connection-information"></a>接続情報の取得

Azure Database for MySQL に接続するために必要な接続情報を取得します。 完全修飾サーバー名とログイン資格情報が必要です。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. Azure portal の左側のメニューにある **[すべてのリソース]** を選択し、作成したサーバー (例: **mydemoserver**) を検索します。
3. サーバー名を選択します。
4. サーバーの **[概要]** パネルから、 **[サーバー名]** と **[サーバー管理者ログイン名]** を書き留めます。 パスワードを忘れた場合も、このパネルからパスワードをリセットすることができます。
 :::image type="content" source="./media/connect-nodejs/server-name-azure-database-mysql.png" alt-text="Azure Database for MySQL サーバー名":::

## <a name="running-the-javascript-code-in-nodejs"></a>Node.js での JavaScript コードの実行

1. JavaScript コードをテキスト ファイルに貼り付け、そのファイルを .js というファイル拡張子でプロジェクト フォルダーに保存します (例: C:\nodejsmysql\createtable.js や /home/username/nodejsmysql/createtable.js)。
2. コマンド プロンプトまたは Bash シェルを開き、ディレクトリをプロジェクト フォルダーに変更します (`cd nodejsmysql`)。
3. アプリケーションを実行するには、node コマンドに続けてファイル名を入力します (例: `node createtable.js`)。
4. Windows で環境変数 PATH に node アプリケーションが追加されていない場合、node アプリケーションを起動するには完全パスを使用する必要があります (例: `"C:\Program Files\nodejs\node.exe" createtable.js`)。

## <a name="connect-create-table-and-insert-data"></a>接続、テーブルの作成、データの挿入

接続し、SQL ステートメント **CREATE TABLE** および **INSERT INTO** を使用してデータを読み込むには、次のコードを使用します。

MySQL サーバーとやり取りするには、[mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) メソッドを使用します。 サーバーへの接続を確立するには、[connect()](https://github.com/mysqljs/mysql#establishing-connections) 関数を使用します。 MySQL データベースに対して SQL クエリを実行するには、[query()](https://github.com/mysqljs/mysql#performing-queries) 関数を使用します。 

`host`、`user`、`password`、`database` の各パラメーターの値は、サーバーとデータベースを作成するときに指定した値に置き換えてください。

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }
});

function queryDatabase(){
    conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
        if (err) throw err; 
        console.log('Dropped inventory table if existed.');
    })
        conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Created inventory table.');
    })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
        else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
    function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.end(function (err) { 
    if (err) throw err;
    else  console.log('Done.') 
    });
};
```

## <a name="read-data"></a>データの読み取り

接続し、**SELECT** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 

MySQL サーバーとやり取りするには、[mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) メソッドを使用します。 サーバーとの接続を確立するには、[connect()](https://github.com/mysqljs/mysql#establishing-connections) メソッドを使用します。 MySQL データベースに対して SQL クエリを実行するには、[query()](https://github.com/mysqljs/mysql#performing-queries) メソッドを使用します。 クエリの結果は、results 配列を使って保存します。

`host`、`user`、`password`、`database` の各パラメーターの値は、サーバーとデータベースを作成するときに指定した値に置き換えてください。

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }
    });

function readData(){
    conn.query('SELECT * FROM inventory', 
        function (err, results, fields) {
            if (err) throw err;
            else console.log('Selected ' + results.length + ' row(s).');
            for (i = 0; i < results.length; i++) {
                console.log('Row: ' + JSON.stringify(results[i]));
            }
            console.log('Done.');
        })
    conn.end(
        function (err) { 
            if (err) throw err;
            else  console.log('Closing connection.') 
    });
};
```

## <a name="update-data"></a>データの更新

接続し、**UPDATE** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 

MySQL サーバーとやり取りするには、[mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) メソッドを使用します。 サーバーとの接続を確立するには、[connect()](https://github.com/mysqljs/mysql#establishing-connections) メソッドを使用します。 MySQL データベースに対して SQL クエリを実行するには、[query()](https://github.com/mysqljs/mysql#performing-queries) メソッドを使用します。 

`host`、`user`、`password`、`database` の各パラメーターの値は、サーバーとデータベースを作成するときに指定した値に置き換えてください。

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>データの削除

接続し、**DELETE** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 

MySQL サーバーとやり取りするには、[mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) メソッドを使用します。 サーバーとの接続を確立するには、[connect()](https://github.com/mysqljs/mysql#establishing-connections) メソッドを使用します。 MySQL データベースに対して SQL クエリを実行するには、[query()](https://github.com/mysqljs/mysql#performing-queries) メソッドを使用します。 

`host`、`user`、`password`、`database` の各パラメーターの値は、サーバーとデータベースを作成するときに指定した値に置き換えてください。

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
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
> [エクスポートとインポートを使用したデータベースの移行](./concepts-migrate-import-export.md)
