---
title: "Ruby を使用して Azure SQL Database に照会する | Microsoft Docs"
description: "このトピックでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータベースを照会するプログラムを Ruby で作成する方法について説明します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 07/14/2017
ms.author: carlrab
ms.openlocfilehash: 7322b1f36017adb32735c4c1e1f0af73bff70c97
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-ruby-to-query-an-azure-sql-database"></a>Ruby を使用して Azure SQL Database に照会する

このクイック スタート チュートリアルでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータを照会するプログラムを [Ruby](https://www.ruby-lang.org) で作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタート チュートリアルを完了するには、次の前提条件を満たしておく必要があります。

- Azure SQL Database。 このクイック スタートでは、次のいずれかのクイック スタートで作成したリソースを使用します。 

   - [DB の作成 - ポータル](sql-database-get-started-portal.md)
   - [DB の作成 - CLI](sql-database-get-started-cli.md)
   - [DB の作成 - PowerShell](sql-database-get-started-powershell.md)

- このクイック スタート チュートリアルに使用するコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。
- ご使用のオペレーティング システムに対応した Ruby とそれに関連するソフトウェアをインストール済みであること。
    - **MacOS**: Homebrew をインストールし、rbenv と ruby-build をインストールした後、Ruby と FreeTDS を順にインストールします。 [手順 1.2.、手順 1.3.、手順 1.4.、手順 1.5.](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/) を参照してください。
    - **Ubuntu**: Ruby の前提条件をインストールし、rbenv と ruby-build をインストールした後、Ruby と FreeTDS を順にインストールします。 [手順 1.2.、手順 1.3.、手順 1.4.、手順 1.5.](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/) を参照してください。

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの **[概要]** ウィンドウで、完全修飾サーバー名を確認します。 サーバー名をポイントすると、**[コピーするにはクリックします]** オプションが表示されます (下図参照)。

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Azure SQL Database サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示し、必要に応じて、パスワードをリセットします。

> [!IMPORTANT]
> このチュートリアルを実行するコンピューターのパブリック IP アドレスに対してファイアウォール規則を設定しておく必要があります。 別のコンピューターから実行する場合または別のパブリック IP アドレスがある場合は、[Azure Portal でサーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)を作成してください。 

## <a name="insert-code-to-query-sql-database"></a>SQL Database に照会するコードの挿入

1. 任意のテキスト エディターで新しいファイル (**sqltest.rb**) を作成します。

2. その内容を次のコードで置き換えます。サーバー、データベース、ユーザー、パスワードには、実際の値を追加してください。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトで、次のコマンドを実行します。

   ```bash
   ruby sqltest.rb
   ```

2. 先頭から 20 行が返されることを確認して、アプリケーション ウィンドウを閉じます。


## <a name="next-steps"></a>次のステップ
- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [TinyTDS の GitHub リポジトリ](https://github.com/rails-sqlserver/tiny_tds)
- [TinyTDS についての問題の報告と質問](https://github.com/rails-sqlserver/tiny_tds/issues)
- [SQL Server 用 Ruby ドライバー](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
