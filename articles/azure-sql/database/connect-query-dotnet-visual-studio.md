---
title: .NET および C# と Visual Studio を使用してクエリを実行する
description: Visual Studio を使用して、Azure SQL Database または Azure SQL Managed Instance のデータベースに接続してクエリを実行する C# アプリを作成します。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 8fe541432366d3c2ac1dc1470fea66d328f79780
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213042"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>クイック スタート:Visual Studio で .NET および C# を使用して、Azure SQL Database または Azure SQL Managed Instance のデータベースに接続してクエリを実行します
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

このクイック スタートでは、Visual Studio で [.NET Framework](https://www.microsoft.com/net/) と C# コードを使用し、Transact-SQL ステートメントを使って Azure SQL Database 内のデータベースに対してクエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure SQL Database 内のデータベース。 以下のいずれかのクイック スタートを使用して、Azure SQL Database でデータベースを作成し、構成できます。

  | アクション | SQL Database | SQL Managed Instance | Azure VM 上の SQL Server |
  |:--- |:--- |:---|:---|
  | 作成| [ポータル](single-database-create-quickstart.md) | [ポータル](../managed-instance/instance-create-quickstart.md) | [ポータル](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 構成 | [サーバーレベルの IP ファイアウォール規則](firewall-create-server-level-portal-quickstart.md)| [VM からの接続](../managed-instance/connect-vm-instance-configure.md)|
  |||[オンプレミスからの接続](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server への接続](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |データの読み込み|クイック スタートごとに読み込まれる Adventure Works|[Wide World Importers を復元する](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers を復元する](../managed-instance/restore-sample-database-quickstart.md) |
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) の [BACPAC](database-import.md) ファイルから Adventure Works を復元またはインポートする| [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) の [BACPAC](database-import.md) ファイルから Adventure Works を復元またはインポートする|
  |||

  > [!IMPORTANT]
  > この記事のスクリプトは、Adventure Works データベースを使用するように記述されています。 SQL Managed Instance では、Adventure Works データベースをインスタンス データベースにインポートするか、Wide World Importers データベースを使用するようにこの記事のスクリプトを修正する必要があります。

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community、Professional、または Enterprise エディション。

## <a name="get-server-connection-information"></a>サーバーの接続情報を取得する

データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[SQL Database]** または **[SQL Managed Instance]** ページに移動します。

3. **[概要]** ページで、Azure SQL Database のデータベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、Azure SQL Managed Instance または Azure VM 上の SQL Server の場合は **[ホスト]** の横の完全修飾サーバー名 (または IP アドレス) を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

> [!NOTE]
> Azure VM 上の SQL Server の接続情報については、[SQL Server インスタンスへの接続](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)に関するページをご覧ください。

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Azure SQL Database のデータベースに対してクエリを実行するコードを作成する

1. Visual Studio で、新しいプロジェクトを作成します。 
   
1. **[新しいプロジェクト]** ダイアログで、 **[Visual C#]** 、 **[コンソール アプリ (.NET Framework)]** の順に選択します。
   
1. プロジェクトの名前に「*sqltest*」と入力して、 **[OK]** を選択します。 新しいプロジェクトが作成されます。 
   
1. **[プロジェクト]**  >  **[NuGet パッケージの管理]** の順に選択します。 
   
1. **[NuGet パッケージ マネージャー]** で **[参照]** タブを選択し、**Microsoft.Data.SqlClient** を検索して選択します。
   
1. **[Microsoft.Data.SqlClient]** ページで **[インストール]** を選択します。 
   - プロンプトが表示されたら、 **[OK]** を選択してインストールを続行します。 
   - **[ライセンスへの同意]** ウィンドウが表示された場合は **[同意する]** を選択します。
   
1. インストールが完了すると、 **[NuGet パッケージ マネージャー]** を閉じることができます。 
   
1. コード エディターで **Program.cs** の内容を次のコードに置き換えます。 `<server>`、`<username>`、`<password>`、`<database>` は実際の値に置き換えます。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>コードの実行

1. アプリを実行するには、 **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、ツールバーの **[開始]** を選択するか、**F5** キーを押します。
1. データベースの上位 20 のカテゴリ/製品行が返されていることを確認し、アプリ ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- Windows/Linux/macOS 上で [.NET Core を使用して Azure SQL Database 内のデータベースに接続してクエリを実行する](connect-query-dotnet-core.md)方法について学びます。  
- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)を把握します。
- [SSMS を使用して Azure SQL Database に初めてのデータベースを設計](design-first-database-tutorial.md)する方法や [.NET を使用して Azure SQL Database に初めてのデータベースを設計](design-first-database-csharp-tutorial.md)する方法について学びます。
- .NET の詳細については、[.NET のドキュメント](https://docs.microsoft.com/dotnet/)を参照してください。
- 再試行ロジックの例:[ADO.NET を使用して Azure SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-ado-net-a78n]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

