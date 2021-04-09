---
title: .NET Core を使用してデータベースに接続し、クエリを実行する
description: このトピックでは、.NET Core を使用して Azure SQL Database または Azure SQL Managed Instance のデータベースに接続し、Transact-SQL ステートメントを使用してデータベースに対してクエリを実行するプログラムを作成する方法について説明します。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 1d25f43ef5a694d8b94710055bf1be72a7fcb45c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705219"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>クイックスタート: .NET Core (C#) を使用してデータベースのクエリを実行する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

このクイックスタートでは、[.NET Core](https://www.microsoft.com/net/) と C# コードを使用してデータベースに接続します。 その後、Transact-SQL ステートメントを実行して、データの照会を行います。

> [!TIP]
> 次の Microsoft Learn モジュールは、[Azure SQL Database のデータベースに対してクエリを実行する ASP.NET アプリケーションを開発および構成する](/learn/modules/develop-app-that-queries-azure-sql/)方法を無料で学習する際に役立ちます

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [お使いオペレーティング システム用の .NET Core](https://www.microsoft.com/net/core) がインストールされていること。
- クエリを実行できるデータベース。 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>新しい .NET Core プロジェクトを作成する

1. コマンド プロンプトを開き、**sqltest** という名前のフォルダーを作成します。 このフォルダーに移動して、次のコマンドを実行します。

    ```cmd
    dotnet new console
    ```

    このコマンドにより、初期 C# コード ファイル (**Program.cs**)、XML 構成ファイル (**sqltest.csproj**)、必要なバイナリなど、新しいアプリ プロジェクト ファイルが作成されます。

2. テキスト エディターで **sqltest.csproj** を開き、`<Project>` タグの間に次の XML を貼り付けます。 この XML により、依存関係として `System.Data.SqlClient` が追加されます。

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Azure SQL Database のデータベースに対してクエリを実行するコードを挿入する

1. テキスト エディターで、**Program.cs** を開きます。

2. その内容を次のコードで置き換え、サーバー、データベース、ユーザー名、パスワードに実際の値を追加します。

> [!NOTE]
> ADO.NET の接続文字列を使用するには、コードでサーバー、データベース、ユーザー名、パスワードを設定している 4 行を、次の行に置き換えます。 文字列で、ユーザー名とパスワードを設定します。
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

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

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>コードの実行

1. プロンプトで、次のコマンドを実行します。

   ```cmd
   dotnet restore
   dotnet run
   ```

2. 行が返されていることを確認します。

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. **Enter** キーを押してアプリケーション ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)
- [.NET Framework と Visual Studio を使用して Azure SQL Database または Azure SQL Managed Instance に接続してクエリを実行する](connect-query-dotnet-visual-studio.md)方法について学習します。  
- [SSMS を使用して初めてのデータベースを設計する](design-first-database-tutorial.md)方法や、[C# と ADO.NET を使用してデータベースを設計して接続する](design-first-database-csharp-tutorial.md)方法について学習します。
- .NET の詳細については、[.NET のドキュメント](/dotnet/)を参照してください。
