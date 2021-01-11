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
ms.openlocfilehash: f1908e243b7cb1def2eac8a1d46d5f087a25f8c6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936403"
---
# <a name="quickstart-use-net-core-c-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>クイック スタート:.NET Core (C#) を使用して Azure SQL Database または Azure SQL Managed Instance のデータベースに対してクエリを実行する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

このクイックスタートでは、[.NET Core](https://www.microsoft.com/net/) と C# コードを使用してデータベースに接続します。 その後、Transact-SQL ステートメントを実行して、データの照会を行います。

> [!TIP]
> 次の Microsoft Learn モジュールは、[Azure SQL Database のデータベースに対してクエリを実行する ASP.NET アプリケーションを開発および構成する](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)方法を無料で学習する際に役立ちます

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
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

- [お使いオペレーティング システム用の .NET Core](https://www.microsoft.com/net/core) がインストールされていること。

> [!NOTE]
> このクイック スタートでは、*mySampleDatabase* データベースを使用します。 別のデータベースを使いたい場合は、データベース参照を変更し、C# コードの `SELECT` クエリを変更する必要があります。

## <a name="get-server-connection-information"></a>サーバーの接続情報を取得する

Azure SQL Database のデータベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、Azure SQL Database のデータベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、Azure SQL Managed Instance または Azure VM 上の SQL Server の場合は **[ホスト]** の横の完全修飾サーバー名 (または IP アドレス) を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

> [!NOTE]
> Azure VM 上の SQL Server の接続情報については、[SQL Server インスタンスへの接続](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)に関するページをご覧ください。

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>ADO.NET の接続情報を取得する (省略可能 - SQL Database のみ)

1. **mySampleDatabase** のページに移動し、 **[設定]** で **[接続文字列]** を選択します。

2. 完全な **ADO.NET** 接続文字列を確認します。

    ![ADO.NET の接続文字列](./media/connect-query-dotnet-core/adonet-connection-string2.png)

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

## <a name="next-steps"></a>次のステップ

- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)
- [.NET Framework と Visual Studio を使用して Azure SQL Database または Azure SQL Managed Instance に接続してクエリを実行する](connect-query-dotnet-visual-studio.md)方法について学習します。  
- [SSMS を使用して初めてのデータベースを設計する](design-first-database-tutorial.md)方法や、[C# と ADO.NET を使用してデータベースを設計して接続する](design-first-database-csharp-tutorial.md)方法について学習します。
- .NET の詳細については、[.NET のドキュメント](https://docs.microsoft.com/dotnet/)を参照してください。
