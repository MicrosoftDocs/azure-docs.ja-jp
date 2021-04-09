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
ms.openlocfilehash: 1d8859f4790610e72ad517f74bbbbf0cf77d9316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705204"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>クイックスタート: Visual Studio で .NET と C# を使用してデータベースに接続してクエリを実行する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

このクイックスタートでは、Visual Studio で [.NET Framework](https://www.microsoft.com/net/) と C# コードを使用し、Transact-SQL ステートメントを使って Azure SQL または Synapse SQL 内のデータベースに対してクエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community、Professional、または Enterprise エディション。
- クエリを実行できるデータベース。

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

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
   
1. コード エディターで **Program.cs** の内容を次のコードに置き換えます。 `<your_server>`、`<your_username>`、`<your_password>`、`<your_database>` は実際の値に置き換えます。
   
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
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
1. データベース名と照合順序が返されていることを確認し、アプリ ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- Windows/Linux/macOS 上で [.NET Core を使用して Azure SQL Database 内のデータベースに接続してクエリを実行する](connect-query-dotnet-core.md)方法について学びます。  
- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)を把握します。
- [SSMS を使用して Azure SQL Database に初めてのデータベースを設計](design-first-database-tutorial.md)する方法や [.NET を使用して Azure SQL Database に初めてのデータベースを設計](design-first-database-csharp-tutorial.md)する方法について学びます。
- .NET の詳細については、[.NET のドキュメント](/dotnet/)を参照してください。
- 再試行ロジックの例:[ADO.NET を使用して Azure SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-ado-net-a78n]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
