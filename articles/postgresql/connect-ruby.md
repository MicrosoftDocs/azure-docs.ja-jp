---
title: "Ruby を使用して Azure Database for PostgreSQL に接続する | Microsoft Docs"
description: "このクイックスタートでは、Azure Database for PostgreSQL に接続してデータを照会するために使用できる、Ruby コード サンプルを紹介します。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/30/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: a04188aed40a46e8cb64dfeb1230fb9b3c6ab679
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017

---

# <a name="azure-database-for-postgresql-use-ruby-to-connect-and-query-data"></a>Azure Database for PostgreSQL: Ruby を使用した接続とデータの照会
このクイックスタートでは、[Ruby](https://www.ruby-lang.org) アプリケーションを使用して Azure Database for PostgreSQL に接続する方法を紹介します。 ここでは、SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法を説明します。 この記事では、Ruby を使用した開発には慣れているものの、Azure Database for PostgreSQL の使用は初めてであるユーザーを想定しています。

## <a name="prerequisites"></a>前提条件
このクイックスタートでは、次のいずれかのガイドで作成されたリソースを出発点として使用します。
- [DB の作成 - ポータル](quickstart-create-server-database-portal.md)
- [DB の作成 - Azure CLI](quickstart-create-server-database-azure-cli.md)

## <a name="install-ruby"></a>Ruby のインストール
Ruby を自分のマシンにインストールします。 

### <a name="windows"></a>Windows
- 最新バージョンの [Ruby](http://rubyinstaller.org/downloads/) をダウンロードしてインストールします。
- MSI インストーラーの完了画面で、[Run 'ridk install' to install MSYS2 and development toolchain]\('ridk install' を実行して MSYS2 と開発ツールチェーンをインストールする\) チェック ボックスをオンにします。 **[完了]** をクリックして、次のインストーラーを起動します。
- Windows インストーラー用 RubyInstaller2 が起動します。 「2」と入力して、MSYS2 リポジトリ更新をインストールします。 完了してインストール プロンプトに戻ったら、コマンド ウィンドウを閉じます。
- スタート メニューから新しいコマンド プロンプト (cmd) を起動します。
- `ruby -v` で Ruby のインストールをテストして、インストールされているバージョンを確認します。
- `gem -v` で Gem のインストールをテストして、インストールされているバージョンを確認します。
- コマンド `gem install pg` を実行して、Gem を使用して Ruby 用の PostgreSQL モジュールをビルドします。

### <a name="macos"></a>MacOS
- コマンド `brew install ruby` を実行して、Homebrew で Ruby をインストールします。 その他のインストール オプションについては、Ruby の [インストール ドキュメント](https://www.ruby-lang.org/en/documentation/installation/#homebrew)を参照してください。
- `ruby -v` で Ruby のインストールをテストして、インストールされているバージョンを確認します。
- `gem -v` で Gem のインストールをテストして、インストールされているバージョンを確認します。
- コマンド `gem install pg` を実行して、Gem を使用して Ruby 用の PostgreSQL モジュールをビルドします。

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- コマンド `sudo apt-get install ruby-full` を実行して Ruby をインストールします。 その他のインストール オプションについては、Ruby の [インストール ドキュメント](https://www.ruby-lang.org/en/documentation/installation/)を参照してください。
- `ruby -v` で Ruby のインストールをテストして、インストールされているバージョンを確認します。
- コマンド `sudo gem update --system` を実行して、Gem の最新の更新プログラムをインストールします。
- `gem -v` で Gem のインストールをテストして、インストールされているバージョンを確認します。
- `sudo apt-get install build-essential` コマンドを実行して、gcc や make などのビルド ツールをインストールします。
- コマンド `sudo apt-get install libpq-dev` を実行して、PostgreSQL ライブラリをインストールします。
- コマンド `sudo gem install pg` を実行して、Gem で Ruby pg モジュールをビルドします。

## <a name="run-ruby-code"></a>Ruby コードの実行 
- コードをテキスト ファイルに保存し、そのファイルを .rb というファイル拡張子でプロジェクト フォルダーに保存します (例: `C:\rubypostgres\read.rb` または `/home/username/rubypostgres/read.rb`)。
- このコードを実行するには、コマンド プロンプトまたは Bash シェルを起動します。 `cd rubypostgres` でディレクトリをプロジェクト フォルダーに変更し、コマンド `ruby read.rb` を入力してアプリケーションを実行します。

## <a name="get-connection-information"></a>接続情報の取得
Azure Database for PostgreSQL に接続するために必要な接続情報を取得します。 完全修飾サーバー名とログイン資格情報が必要です。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、作成したサーバー (例: **mypgserver-20170401**) を検索します。
3. サーバー名 **[mypgserver-20170401]** をクリックします。
4. サーバーの **[概要]** ページを選択します。 **[サーバー名]** と **[サーバー管理者ログイン名]** の値を書き留めておきます。
 ![Azure Database for PostgreSQL - サーバー管理者ログイン](./media/connect-ruby/1-connection-string.png)
5. サーバーのログイン情報を忘れた場合は、**[概要]** ページに移動して、サーバー管理者ログイン名を確認します。 必要に応じて、パスワードをリセットします。

## <a name="connect-and-create-a-table"></a>接続とテーブルの作成
接続し、**CREATE TABLE** SQL ステートメントでテーブルを作成してから、**INSERT INTO** SQL ステートメントでそのテーブルに行を追加するには、次のコードを使用します。

このコードでは、[new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) コンストラクターを使用した [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) オブジェクトを使用して、Azure Database for PostgreSQL に接続します。 次に、[exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) メソッドを呼び出して、DROP、CREATE TABLE、INSERT INTO の各コマンドを実行します。 [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error) クラスを使用して、エラーをチェックします。 その後、[close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) メソッドを呼び出して、終了する前に接続を閉じます。

`host`、`database`、`user`、`password` の各文字列は、実際の値に置き換えてください。 
```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="read-data"></a>データの読み取り
接続し、**SELECT** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 

このコードでは、[new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) コンストラクターを使用した [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) オブジェクトを使用して、Azure Database for PostgreSQL に接続します。 次に、[exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) メソッドを呼び出して SELECT コマンドを実行します。その際、結果は結果セットに保持されます。 結果セットのコレクションは `resultSet.each do` ループを使用して反復処理され、現在の行の値が `row` 変数に保持されます。 [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error) クラスを使用して、エラーをチェックします。 その後、[close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) メソッドを呼び出して、終了する前に接続を閉じます。

`host`、`database`、`user`、`password` の各文字列は、実際の値に置き換えてください。 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="update-data"></a>データの更新
接続し、**UPDATE** SQL ステートメントを使用してデータを更新するには、次のコードを使用します。

このコードでは、[new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) コンストラクターを使用した [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) オブジェクトを使用して、Azure Database for PostgreSQL に接続します。 次に、[exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) メソッドを呼び出し、UPDATE コマンドを実行します。 [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error) クラスを使用して、エラーをチェックします。 その後、[close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) メソッドを呼び出して、終了する前に接続を閉じます。

`host`、`database`、`user`、`password` の各文字列は、実際の値に置き換えてください。 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>データの削除
接続し、**DELETE** SQL ステートメントを使用してデータを削除するには、次のコードを使用します。 

このコードでは、[new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) コンストラクターを使用した [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) オブジェクトを使用して、Azure Database for PostgreSQL に接続します。 次に、[exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) メソッドを呼び出し、DELETE コマンドを実行します。 [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error) クラスを使用して、エラーをチェックします。 その後、[close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) メソッドを呼び出して、終了する前に接続を閉じます。

`host`、`database`、`user`、`password` の各文字列は、実際の値に置き換えてください。 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [エクスポートとインポートを使用したデータベースの移行](./howto-migrate-using-export-and-import.md)

