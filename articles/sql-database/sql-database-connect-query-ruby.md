---
title: "Ruby を使用して Azure SQL Database に接続する | Microsoft Docs"
description: "このクイック スタートのコード サンプルを使用して、Ruby で最新のアプリケーションを構築し、Azure SQL Database を使用してクラウドの強力なリレーショナル データベースでバックアップします。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 03/28/2017
ms.author: andrela;sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 8e01a717cbef6b5f6d26a8191c44e249bf7a9567
ms.lasthandoff: 03/30/2017


---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Azure SQL Database: Ruby を使って接続とデータの照会を行う

[Ruby](https://Ruby.org) を使用して Azure SQL Database に接続し、クエリを実行します。 このクイック スタートでは、Ruby を使用して Azure SQL Database に接続し、照会、挿入、更新、削除の各ステートメントを実行する方法について詳しく説明します。

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

- [DB の作成 - ポータル](sql-database-get-started-portal.md)
- [DB の作成 - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>開発環境の設定

次のセクションでは、既存の Mac OS および Linux (Ubuntu) 開発環境で Azure SQL Database を使用するように設定する方法について説明します。

### <a name="mac-os"></a>**Mac OS**
ターミナルを開き、Ruby スクリプトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**brew**、**FreeTDS**、および **TinyTDS** をインストールします。

```ruby
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
ターミナルを開き、Ruby スクリプトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**FreeTDS** と **TinyTDS** をインストールします。

```ruby
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="get-connection-information"></a>接続情報の取得

Azure Portal で接続文字列を取得します。 その接続文字列は Azure SQL データベースに接続するために使用します。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの **[要点]** ウィンドウで、完全修飾サーバー名を確認します。

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="connection strings" style="width: 780px;" />
    

## <a name="select-data"></a>データの選択
[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数と [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL ステートメントを使用して、Azure SQL Database のデータを照会します。 TinyTDS::Client 関数は、クエリを受け入れて結果セットを返します。 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds)を使用することにより、結果セットが反復処理されます。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="insert-data"></a>データを挿入する
[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数と [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL ステートメントを使用して、Azure SQL Database にデータを挿入します。

この例では、[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) ステートメントを安全に実行し、[[SQL インジェクション]](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) の脆弱性からアプリケーションを保護するパラメーターを渡し、自動生成された[プライマリ キー](https://msdn.microsoft.com/library/ms179610.aspx)値を取得する方法について説明しています。    
  
Azure で TinyTDS を使用するには、いくつかの `SET` ステートメントを実行して現在のセッションが特定の情報を処理する方法を変更することをお勧めします。 推奨 `SET` ステートメントは、コード サンプルに用意されています。 たとえば、 `SET ANSI_NULL_DFLT_ON` は、列の NULL 値が許容されることが明示的に宣言されていない場合でも、作成された新しい列が NULL 値を持つことを許可します。  
  
Microsoft SQL Server の [datetime](http://msdn.microsoft.com/library/ms187819.aspx) 形式に合うように、[strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 関数を使用して対応する日付時刻形式にキャストします。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON"")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## <a name="update-data"></a>データの更新
[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数と [UPDATE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL ステートメントを使用して、Azure SQL Database のデータを更新します。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## <a name="delete-data"></a>データの削除
[TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) 関数と [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL ステートメントを使用して、Azure SQL Database のデータを削除します。

```ruby
require 'tiny_tds'
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## <a name="next-steps"></a>次のステップ
* [SQL Database の開発の概要](sql-database-develop-overview.md)の確認。
* [Ruby Driver for SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/) の詳細。
* [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)すべてを確認します。

