---
title: Ruby を使用して Azure SQL Database に照会する | Microsoft Docs
description: このトピックでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータベースを照会するプログラムを Ruby で作成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 05f3213383c526944a8a1cf51fb92d5186ac7434
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449026"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>クイック スタート:Ruby を使用して Azure SQL Database に照会する

このクイック スタートでは、[Ruby](https://www.ruby-lang.org) を使って Azure SQL データベースに接続した後、Transact-SQL ステートメントを使ってデータのクエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- Azure SQL Database。 以下のいずれかのクイック スタートを使用して、Azure SQL Database でデータベースを作成し、構成できます。

  || 単一データベース | マネージド インスタンス |
  |:--- |:--- |:---|
  | Create| [ポータル](sql-database-single-database-get-started.md) | [ポータル](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 構成 | [サーバーレベルの IP ファイアウォール規則](sql-database-server-level-firewall-rule.md)| [VM からの接続](sql-database-managed-instance-configure-vm.md)|
  |||[オンサイトからの接続](sql-database-managed-instance-configure-p2s.md)
  |データを読み込む|クイック スタートごとに読み込まれる Adventure Works|[Wide World Importers を復元する](sql-database-managed-instance-get-started-restore.md)
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) の [BACPAC](sql-database-import.md) ファイルから Adventure Works を復元またはインポートする|
  |||

  > [!IMPORTANT]
  > この記事のスクリプトは、Adventure Works データベースを使用するように記述されています。 マネージド インスタンスの場合は、Adventure Works データベースをインスタンス データベースにインポートするか、Wide World Importers データベースを使用するようにこの記事のスクリプトを修正する必要があります。
  
- ご使用のオペレーティング システムに対応した Ruby とそれに関連するソフトウェア:
  
  - **MacOS**: Homebrew、rbenv と ruby-build、Ruby、FreeTDS、TinyTDS をインストールします。 「[Create Ruby apps using SQL Server on macOS (macOS での SQL Server を使用した Ruby アプリの作成)](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)」の手順 1.2、1.3、1.4、1.5、2.1 をご覧ださい。
  
  - **Ubuntu**:Ruby に対する前提条件、rbenv と ruby-build、Ruby、FreeTDS、TinyTDS をインストールします。 「[Create Ruby apps using SQL Server on Ubuntu (Ubuntu での SQL Server を使用した Ruby アプリの作成)](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)」の手順 1.2、1.3、1.4、1.5、2.1 をご覧ださい。
  
  - **Windows**:Ruby、Ruby Devkit、TinyTDS をインストールします。 「[Ruby 開発用に開発環境を構成する](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)」をご覧ください。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、単一データベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、マネージド インスタンスの場合は **[ホスト]** の横の完全修飾サーバー名を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。 

## <a name="create-code-to-query-your-sql-database"></a>コードを作成して、SQL データベースのクエリを実行する

1. 任意のテキストまたはコード エディターで新しいファイル (*sqltest.rb*) を作成します。
   
1. 次のコードを追加します。 `<server>`、`<database>`、`<username>`、`<password>` を Azure SQL データベースからの値に置き換えます。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
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
   
1. データベースの上位 20 のカテゴリ/製品行が返されていることを確認します。 

## <a name="next-steps"></a>次の手順
- [最初の Azure SQL データベースの設計](sql-database-design-first-database.md)。
- [TinyTDS の GitHub リポジトリ](https://github.com/rails-sqlserver/tiny_tds)。
- [TinyTDS についての問題の報告と質問](https://github.com/rails-sqlserver/tiny_tds/issues)。
- [SQL Server 用 Ruby ドライバー](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)。
