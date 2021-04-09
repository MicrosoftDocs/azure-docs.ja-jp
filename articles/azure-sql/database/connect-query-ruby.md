---
title: Ruby を使用してクエリを実行する
description: このトピックでは、データベースに接続するプログラムを Ruby を使用して作成し、Transact-SQL ステートメントを使用してデータベースに対してクエリを実行する方法について説明します。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 0434a99d28b4f71594e0ca9ce312087dee5b0ee2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92672537"
---
# <a name="quickstart-use-ruby-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>クイック スタート:Ruby を使用して Azure SQL Database または Azure SQL Managed Instance のデータベースに対してクエリを実行する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

このクイックスタートでは、[Ruby](https://www.ruby-lang.org) を使ってデータベースに接続した後、Transact-SQL ステートメントを使ってデータのクエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- データベース。 以下のいずれかのクイックスタートを使用すると、データベースを作成し、構成できます。

  | アクション | SQL Database | SQL Managed Instance | Azure VM 上の SQL Server |
  |:--- |:--- |:---|:---|
  | 作成| [ポータル](single-database-create-quickstart.md) | [ポータル](../managed-instance/instance-create-quickstart.md) | [ポータル](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 構成 | [サーバーレベルの IP ファイアウォール規則](firewall-create-server-level-portal-quickstart.md)| [VM からの接続](../managed-instance/connect-vm-instance-configure.md)|
  |||[オンプレミスからの接続](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server インスタンスに接続する](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |データの読み込み|クイック スタートごとに読み込まれる Adventure Works|[Wide World Importers を復元する](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers を復元する](../managed-instance/restore-sample-database-quickstart.md) |
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) の [BACPAC](database-import.md) ファイルから Adventure Works を復元またはインポートする| [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) の [BACPAC](database-import.md) ファイルから Adventure Works を復元またはインポートする|
  |||

  > [!IMPORTANT]
  > この記事のスクリプトは、Adventure Works データベースを使用するように記述されています。 SQL マネージド インスタンスの場合は、Adventure Works データベースをインスタンス データベースにインポートするか、Wide World Importers データベースを使用するようにこの記事のスクリプトを修正する必要があります。
  
- ご使用のオペレーティング システムに対応した Ruby とそれに関連するソフトウェア:
  
  - **macOS**:Homebrew、rbenv と ruby-build、Ruby、FreeTDS、TinyTDS をインストールします。 「[Create Ruby apps using SQL Server on macOS (macOS での SQL Server を使用した Ruby アプリの作成)](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)」の手順 1.2、1.3、1.4、1.5、2.1 をご覧ださい。
  
  - **Ubuntu**:Ruby に対する前提条件、rbenv と ruby-build、Ruby、FreeTDS、TinyTDS をインストールします。 「[Create Ruby apps using SQL Server on Ubuntu (Ubuntu での SQL Server を使用した Ruby アプリの作成)](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)」の手順 1.2、1.3、1.4、1.5、2.1 をご覧ださい。
  
  - **Windows**:Ruby、Ruby Devkit、TinyTDS をインストールします。 「[Ruby 開発用に開発環境を構成する](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)」をご覧ください。

## <a name="get-server-connection-information"></a>サーバーの接続情報を取得する

Azure SQL Database のデータベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、Azure SQL Database のデータベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、Azure SQL Managed Instance または Azure VM 上の SQL Server の場合は **[ホスト]** の横の完全修飾サーバー名 (または IP アドレス) を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

> [!NOTE]
> Azure VM 上の SQL Server の接続情報については、[SQL Server インスタンスへの接続](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)に関するページをご覧ください。

## <a name="create-code-to-query-your-database-in-azure-sql-database"></a>Azure SQL Database のデータベースに対してクエリを実行するコードを作成する

1. 任意のテキストまたはコード エディターで新しいファイル (*sqltest.rb*) を作成します。

1. 次のコードを追加します。 `<server>`、`<database>`、`<username>`、`<password>` を Azure SQL Database のデータベースからの値で置き換えます。

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

## <a name="next-steps"></a>次のステップ
- [Azure SQL Database で最初のデータベースを設計する](design-first-database-tutorial.md)
- [TinyTDS の GitHub リポジトリ](https://github.com/rails-sqlserver/tiny_tds)
- [TinyTDS についての問題の報告と質問](https://github.com/rails-sqlserver/tiny_tds/issues)
- [SQL Server 用 Ruby ドライバー](/sql/connect/ruby/ruby-driver-for-sql-server/)