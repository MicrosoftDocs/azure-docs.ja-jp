---
title: "Visual Studio と .NET を使用して Azure SQL Database に照会する | Microsoft Docs"
description: "このトピックでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータベースに照会するプログラムを Visual Studio で作成する方法について説明します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/05/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 105dab17823a7e7f6957a604833f4ecad35c14bd
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="use-net-c-with-visual-studio-to-connect-and-query-an-azure-sql-database"></a>.NET (C#) と Visual Studio で Azure SQL Database に接続してデータベースに照会する

このクイック スタート チュートリアルでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータを照会する C# プログラムを Visual Studio と [.NET Framework](https://www.microsoft.com/net/) を使って作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタート チュートリアルを完了するには、以下のものが必要です。

- Azure SQL Database。 このクイック スタートでは、次のいずれかのクイック スタートで作成したリソースを使用します。 

   - [DB の作成 - ポータル](sql-database-get-started-portal.md)
   - [DB の作成 - CLI](sql-database-get-started-cli.md)
   - [DB の作成 - PowerShell](sql-database-get-started-powershell.md)

- このクイック スタート チュートリアルに使用するコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。
- [Visual Studio Community 2017、Visual Studio Professional 2017、Visual Studio Enterprise 2017 のいずれか](https://www.visualstudio.com/downloads/)のインストール。

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの **[概要]** ページで、次の図に示すように、完全修飾サーバー名を確認します。 サーバー名をポイントすると、**[コピーするにはクリックします]** オプションが表示されます。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Azure SQL Database サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示します。 必要に応じてパスワードをリセットしてください。

5. **[データベース接続文字列の表示]** をクリックします。

6. 完全な **ADO.NET** 接続文字列を確認します。

    ![ADO.NET の接続文字列](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> このチュートリアルを実行するコンピューターのパブリック IP アドレスに対してファイアウォール規則を設定しておく必要があります。 別のコンピューターから実行する場合または別のパブリック IP アドレスがある場合は、[Azure Portal でサーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)を作成してください。 
>
  
## <a name="create-a-new-visual-studio-project"></a>新しい Visual Studio プロジェクトの作成

1. Visual Studio で **[ファイル]**、**[新規作成]**、**[プロジェクト]** の順に選択します。 
2. **[新しいプロジェクト]** ダイアログで **[Visual C#]** を展開します。
3. **[コンソール アプリケーション]** を選択し、プロジェクト名に「*sqltest*」と入力します。
4. **[OK]** をクリックして新しいプロジェクトを作成し、Visual Studio で開きます。
4. ソリューション エクスプローラーで、**[sqltest]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。 
5. **[参照]** で ```System.Data.SqlClient``` を探し、見つかったらそれを選択します。
6. **[System.Data.SqlClient]** ページで **[インストール]** をクリックします。
7. インストールが完了したら変更を確認し、**[OK]** をクリックして **[プレビュー]** ウィンドウを閉じます。 
8. **[ライセンスへの同意]** ウィンドウが表示された場合は **[同意する]** をクリックします。

## <a name="insert-code-to-query-sql-database"></a>SQL Database に照会するコードの挿入
1. **Program.cs** に切り替えます (または必要に応じて開きます)。

2. **Program.cs** の内容を次のコードで置き換えます。サーバー、データベース、ユーザー、パスワードには、実際の値を追加してください。

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
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

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

1. **F5** キーを押してアプリケーションを実行します。
2. 先頭から 20 行が返されることを確認して、アプリケーション ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- Windows/Linux/macOS で [.NET Core を使い Azure SQL Database に接続してデータベースに照会](sql-database-connect-query-dotnet-core.md)する方法についての情報を入手します。  
- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)を把握します。
- [SSMS で初めての Azure SQL Database を設計](sql-database-design-first-database.md)する方法や [.NET で初めての Azure SQL Database を設計](sql-database-design-first-database-csharp.md)する方法についての情報を入手します。
- .NET の詳細については、[.NET のドキュメント](https://docs.microsoft.com/dotnet/)を参照してください。

