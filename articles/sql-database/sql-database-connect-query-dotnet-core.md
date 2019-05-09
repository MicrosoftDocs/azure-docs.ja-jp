---
title: .NET Core を使用して Azure SQL Database に照会する | Microsoft Docs
description: このトピックでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータベースに照会するプログラムを .NET Core で作成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 460f79eb0679dc7d71bc929e4e790b68bd0f6774
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447388"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>クイック スタート:.NET Core (C#) を使用して Azure SQL データベースに照会する

このクイック スタートでは、[.NET Core](https://www.microsoft.com/net/) と C# コードを使用して、Azure SQL Database に接続します。 その後、Transact-SQL ステートメントを実行して、データの照会を行います。

## <a name="prerequisites"></a>前提条件

このチュートリアルには、次のものが必要です。

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

- [お使いオペレーティング システム用の .NET Core](https://www.microsoft.com/net/core) がインストールされていること。

> [!NOTE]
> このクイック スタートでは、*mySampleDatabase* データベースを使用します。 別のデータベースを使いたい場合は、データベース参照を変更し、C# コードの `SELECT` クエリを変更する必要があります。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、単一データベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、マネージド インスタンスの場合は **[ホスト]** の横の完全修飾サーバー名を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

## <a name="get-adonet-connection-information-optional"></a>ADO.NET の接続情報を取得する (省略可能)

1. **mySampleDatabase** のページに移動し、**[設定]** で **[接続文字列]** を選択します。

2. 完全な **ADO.NET** 接続文字列を確認します。

    ![ADO.NET の接続文字列](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. 使用する場合は、**ADO.NET** の接続文字列をコピーします。
  
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

## <a name="insert-code-to-query-sql-database"></a>SQL Database に照会するコードの挿入

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

2. 先頭の 20 行が返されることを確認します。

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. **Enter** キーを押してアプリケーション ウィンドウを閉じます。

## <a name="next-steps"></a>次の手順

- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)
- [.NET Framework と Visual Studio で Azure SQL Database に接続してデータベースに照会](sql-database-connect-query-dotnet-visual-studio.md)する方法について学習します。  
- [SSMS を使用して初めての Azure SQL データベースを設計する](sql-database-design-first-database.md)方法や、[C# と ADO.NET で Azure SQL データベースを設計して接続する](sql-database-design-first-database-csharp.md)方法について学習します。
- .NET の詳細については、[.NET のドキュメント](https://docs.microsoft.com/dotnet/)を参照してください。
