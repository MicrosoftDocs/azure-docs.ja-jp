---
title: Python を使用してデータベースを照会する
description: このトピックでは、Azure SQL Database のデータベースに接続して Transact-SQL ステートメントでデータベースにクエリを実行するプログラムを Python で作成する方法について説明します。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 5257e8730830c6b51808d5d014bc3bdec6036bfe
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843965"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>クイック スタート:Python を使用して Azure SQL Database または Azure SQL Managed Instance 内のデータベースに対してクエリを実行する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

このクイックスタートでは、Python を使用して Azure SQL Database または Azure SQL Managed Instance に接続し、T-SQL ステートメントを使用してデータに対してクエリを実行します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

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

- [Python](https://python.org/downloads) 3 および関連するソフトウェア

  # <a name="macos"></a>[macOS](#tab/macos)

  Homebrew と Python、ODBC ドライバーと SQLCMD、および SQL Server 用の Python ドライバーをインストールするには、[macOS での SQL Server を使用した Python アプリの作成](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)に関するページの手順 **1.2**、**1.3**、および **2.1** を使用します。

  詳細については、[macOS の Microsoft ODBC ドライバー](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)に関するページを参照してください。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Python とその他の必要なパッケージをインストールするには、`sudo apt-get install python python-pip gcc g++ build-essential` を使用します。

  ODBC ドライバー、SQLCMD、および SQL Server 用の Python ドライバーをインストールするには、[pyodbc Python 開発用の環境の構成](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)に関するページを参照してください。

  詳細については、[Linux の Microsoft ODBC ドライバー](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)に関するページを参照してください。

  # <a name="windows"></a>[Windows](#tab/windows)

  Python、ODBC ドライバー、SQLCMD、および SQL Server 用の Python ドライバーをインストールするには、[pyodbc Python 開発用の環境の構成](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)に関するページを参照してください。

  詳細については、[Microsoft ODBC ドライバー](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)に関するページを参照してください。

---

> [!IMPORTANT]
> この記事のスクリプトは、**Adventure Works** データベースを使用するように記述されています。

> [!NOTE]
> オプションで、Azure SQL Managed Instance の使用を選択できます。
>
> 作成して構成するには、[Azure portal](../managed-instance/instance-create-quickstart.md)、[PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)、または [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) を使用して、[オンプレミス](../managed-instance/point-to-site-p2s-configure.md)または [VM](../managed-instance/connect-vm-instance-configure.md) 接続を設定します。
>
> データを読み込む方法については、[Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ファイルの [BACPAC を使用した復元](database-import.md)に関するページを参照するか、[Wide World Importers データベースの復元](../managed-instance/restore-sample-database-quickstart.md)に関するページを参照してください。

Python と Azure SQL Database のデータベースの詳細については、「[Python 用 Azure SQL Database ライブラリ](/python/api/overview/azure/sql)」、[pyodbc リポジトリ](https://github.com/mkleehammer/pyodbc/wiki/)、および [pyodbc のサンプル](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)を参照してください。

## <a name="get-server-connection-information"></a>サーバーの接続情報を取得する

Azure SQL Database のデータベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、Azure SQL Database のデータベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、Azure SQL Managed Instance または Azure VM 上の SQL Server の場合は **[ホスト]** の横の完全修飾サーバー名 (または IP アドレス) を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

> [!NOTE]
> Azure VM 上の SQL Server の接続情報については、[SQL Server インスタンスへの接続](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)に関するページをご覧ください。

## <a name="create-code-to-query-your-database"></a>データベースに対してクエリを実行するコードを作成する 

1. 任意のテキスト エディターで新しいファイル (*sqltest.py*) を作成します。  
   
1. 次のコードを追加します。 \<server>、\<database>、\<username>、\<password> は、使用する実際の値で置き換えてください。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
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

1. 上位 20 のカテゴリ/製品行が返されることを確認し、コマンド ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database で最初のデータベースを設計する](design-first-database-tutorial.md)
- [SQL Server 用 Microsoft Python ドライバー](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python デベロッパー センター](https://azure.microsoft.com/develop/python/?v=17.23h)

