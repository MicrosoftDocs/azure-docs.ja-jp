---
title: Visual Studio で .NET および C# を使用して SQL Database に対してクエリを実行する | Microsoft Docs
description: Visual Studio を使用して、Azure SQL Database に接続し、それに対して Transact-SQL ステートメントを使用してクエリを実行する C# アプリを作成します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 4472ff10c42cd9163693e7316b6bdaef50258db6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569232"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>クイック スタート:Visual Studio で .NET と C# を使用して Azure SQL データベースに接続してクエリを実行する

このクイック スタートでは、Visual Studio で [.NET Framework](https://www.microsoft.com/net/) と C# コードを使用し、Azure SQL データベースに対して Transact-SQL ステートメントを使用してクエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

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

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community、Professional、または Enterprise エディション。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、単一データベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、マネージド インスタンスの場合は **[ホスト]** の横の完全修飾サーバー名を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。 

## <a name="create-code-to-query-the-sql-database"></a>SQL データベースに対してクエリを実行するコードを作成する

1. Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。 
   
1. **[新しいプロジェクト]** ダイアログで、 **[Visual C#]** 、 **[コンソール アプリ (.NET Framework)]** の順に選択します。
   
1. プロジェクトの名前に「*sqltest*」と入力して、 **[OK]** を選択します。 新しいプロジェクトが作成されます。 
   
1. **[プロジェクト]**  >  **[NuGet パッケージの管理]** の順に選択します。 
   
1. **[NuGet パッケージ マネージャー]** で **[参照]** タブを選択し、**System.Data.SqlClient** を検索して選択します。
   
1. **[System.Data.SqlClient]** ページで **[インストール]** を選択します。 
   - プロンプトが表示されたら、 **[OK]** を選択してインストールを続行します。 
   - **[ライセンスへの同意]** ウィンドウが表示された場合は **[同意する]** を選択します。
   
1. インストールが完了すると、 **[NuGet パッケージ マネージャー]** を閉じることができます。 
   
1. コード エディターで **Program.cs** の内容を次のコードに置き換えます。 `<server>`、`<username>`、`<password>`、`<database>` は実際の値に置き換えます。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
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
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
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

## <a name="next-steps"></a>次の手順

- Windows/Linux/macOS 上で [.NET Core を使用して Azure SQL データベースに接続し、クエリを実行する](sql-database-connect-query-dotnet-core.md)方法について学びます。  
- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)を把握します。
- [SSMS で初めての Azure SQL データベースを設計](sql-database-design-first-database.md)する方法や [.NET で初めての Azure SQL データベースを設計](sql-database-design-first-database-csharp.md)する方法についての情報を入手します。
- .NET の詳細については、[.NET のドキュメント](https://docs.microsoft.com/dotnet/)を参照してください。
- 再試行ロジックの例:[ADO.NET を使用して SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-ado-net-a78n]。


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

