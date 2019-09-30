---
title: Python を使用して Azure SQL Database に照会する | Microsoft Docs
description: このトピックでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータベースに照会するプログラムを Python で作成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 659f6333d16f84cc35be45c45b7a7119e53fd0d0
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764296"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>クイック スタート:Python を使用して Azure SQL データベースのクエリを実行する

 このクイック スタートでは、[Python](https://python.org) を使って Azure SQL データベースに接続した後、Transact-SQL ステートメントを使ってデータを照会する方法について説明します。 SDK の詳細については、[リファレンス](https://docs.microsoft.com/python/api/overview/azure/sql) ドキュメント、[pyodbc GitHub リポジトリ](https://github.com/mkleehammer/pyodbc/wiki/)、[pyodbc サンプル](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)を確認してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下のものが必要です。

- Azure SQL データベース。 以下のいずれかのクイック スタートを使用して、Azure SQL Database でデータベースを作成し、構成できます。

  || 単一データベース | マネージド インスタンス |
  |:--- |:--- |:---|
  | 作成| [ポータル](sql-database-single-database-get-started.md) | [ポータル](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 構成 | [サーバーレベルの IP ファイアウォール規則](sql-database-server-level-firewall-rule.md)| [VM からの接続](sql-database-managed-instance-configure-vm.md)|
  |||[オンサイトからの接続](sql-database-managed-instance-configure-p2s.md)
  |データを読み込む|クイック スタートごとに読み込まれる Adventure Works|[Wide World Importers を復元する](sql-database-managed-instance-get-started-restore.md)
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) の [BACPAC](sql-database-import.md) ファイルから Adventure Works を復元またはインポートする|
  |||

  > [!IMPORTANT]
  > この記事のスクリプトは、Adventure Works データベースを使用するように記述されています。 マネージド インスタンスの場合は、Adventure Works データベースをインスタンス データベースにインポートするか、Wide World Importers データベースを使用するようにこの記事のスクリプトを修正する必要があります。
  
- ご使用のオペレーティング システムに対応した Python とそれに関連するソフトウェア:
  
  - **MacOS**:Homebrew と Python をインストールし、ODBC ドライバーと SQLCMD をインストールした後、SQL Server 用の Python ドライバーをインストールします。 [macOS で SQL Server を使用した Python アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)に関する記事の手順 1.2、1.3、および 2.1 を参照してください。 詳しくは、「[Linux および macOS に Microsoft ODBC Driver インストールする](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)」をご覧ください。

  - **Ubuntu**:`sudo apt-get install python python-pip gcc g++ build-essential` を使用して、Python とその他の必要なパッケージをインストールします。 ODBC ドライバー、SQLCMD、および SQL Server 用 Python ドライバーをダウンロードしてインストールします。 手順については、「[pyodbc Python 開発用に開発環境を構成する](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)」をご覧ください。

  - **Windows**:Python、ODBC ドライバー、SQLCMD、および SQL Server 用 Python ドライバーをインストールします。 手順については、「[pyodbc Python 開発用に開発環境を構成する](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)」をご覧ください。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、単一データベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、マネージド インスタンスの場合は **[ホスト]** の横の完全修飾サーバー名を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

## <a name="create-code-to-query-your-sql-database"></a>コードを作成して、SQL データベースのクエリを実行する 

1. 任意のテキスト エディターで新しいファイル (*sqltest.py*) を作成します。  
   
1. 次のコードを追加します。 \<server>、\<database>、\<username>、\<password> を独自の値に置き換えます。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトで、次のコマンドを実行します。

   ```cmd
   python sqltest.py
   ```

1. 上位 20 カテゴリ/製品の行が返されることを確認し、コマンド ウィンドウを閉じます。

## <a name="next-steps"></a>次の手順

- [最初の Azure SQL データベースの設計](sql-database-design-first-database.md)
- [SQL Server 用 Microsoft Python ドライバー](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python デベロッパー センター](https://azure.microsoft.com/develop/python/?v=17.23h)

