---
title: クイック スタート:C# を使用して接続する - Azure Database for MySQL
description: このクイックスタートでは、Azure Database for MySQL に接続してデータを照会するために使用できる、C# (.NET) コード サンプルを紹介します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 3b90d8819b5d327c3ccd143257198c7ec8538f03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535828"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql"></a>クイック スタート:.NET (C#) を使用して Azure Database for MySQL に接続してデータを照会する

このクイックスタートでは、C# アプリケーションを使用して Azure Database for MySQL に接続する方法を紹介します。 ここでは、SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法を説明します。 

## <a name="prerequisites"></a>前提条件
このクイックスタートでは、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free)。
- [Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) を使用して、Azure Database for MySQL シングル サーバーを作成します <br/> [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) を使用して、Azure Database for PostgreSQL の単一サーバーを作成します (まだない場合)。
- パブリック アクセスとプライベート アクセスのどちらを使用しているかに基づいて、次の **いずれか** のアクションを実行して、接続を有効にします。

|アクション| 接続方法|ハウツー ガイド|
|:--------- |:--------- |:--------- |
| **ファイアウォール規則を構成する** | パブリック | [ポータル](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
| **サービス エンドポイントを構成する** | パブリック | [ポータル](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
| **プライベート リンクを構成する** | プライベート | [ポータル](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [データベースと管理者以外のユーザーを作成する](./howto-create-users.md)

[問題がある場合は、お知らせください](https://aka.ms/mysql-doc-feedback)

## <a name="create-a-c-project"></a>C# プロジェクトの作成
コマンド プロンプトで、次のコマンドを実行します。

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>接続情報の取得
Azure Database for MySQL に接続するために必要な接続情報を取得します。 完全修飾サーバー名とログイン資格情報が必要です。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、作成したサーバー (例: **mydemoserver**) を検索します。
3. サーバー名をクリックします。
4. サーバーの **[概要]** パネルから、 **[サーバー名]** と **[サーバー管理者ログイン名]** を書き留めます。 パスワードを忘れた場合も、このパネルからパスワードをリセットすることができます。
 :::image type="content" source="./media/connect-csharp/1_server-overview-name-login.png" alt-text="Azure Database for MySQL サーバー名":::

## <a name="step-1-connect-and-insert-data"></a>手順 1: データの接続と挿入
接続を確立し、SQL ステートメントの `CREATE TABLE` と `INSERT INTO` を使用してデータを読み込むには、次のコードを使用します。 コードは、`MySqlConnection` クラスの以下のメソッドを使用します。
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) は、MySQL への接続を確立します。
- [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) は、CommandText プロパティを設定します
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) は、データベース コマンドを実行します。 

`Server`、`Database`、`UserID`、`Password` の各パラメーターの値は、サーバーとデータベースを作成するときに指定した値に置き換えてください。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[問題がある場合は、お知らせください](https://aka.ms/mysql-doc-feedback)


## <a name="step-2-read-data"></a>手順 2:データの読み取り

接続を確立し、`SELECT` SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 コードは、`MySqlConnection` クラスと以下のメソッドを使用します。
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) は、MySQL への接続を確立します。
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) は、CommandText プロパティを設定します。
- [ExecuteReaderAsync()](/dotnet/api/system.data.common.dbcommand.executereaderasync) は、データベース コマンドを実行します。 
- [ReadAsync()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) は、結果のレコードに進みます。 GetInt32 と GetString を使用して、レコード内の値を解析します。


`Server`、`Database`、`UserID`、`Password` の各パラメーターの値は、サーバーとデータベースを作成するときに指定した値に置き換えてください。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[問題がある場合は、お知らせください](https://aka.ms/mysql-doc-feedback)

## <a name="step-3-update-data"></a>手順 3:データの更新
接続を確立し、`UPDATE` SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 コードは、`MySqlConnection` クラスと以下のメソッドを使用します。
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) は、MySQL への接続を確立します。 
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) は、CommandText プロパティを設定します
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) は、データベース コマンドを実行します。 


`Server`、`Database`、`UserID`、`Password` の各パラメーターの値は、サーバーとデータベースを作成するときに指定した値に置き換えてください。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```
[問題がある場合は、お知らせください](https://aka.ms/mysql-doc-feedback)

## <a name="step-4-delete-data"></a>手順 4:データの削除
接続を確立し、`DELETE` SQL ステートメントを使用してデータを削除するには、次のコードを使用します。 

コードは、`MySqlConnection` クラスと以下のメソッドを使用します
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) は、MySQL への接続を確立します。
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) は、CommandText プロパティを設定します。
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) は、データベース コマンドを実行します。 


`Server`、`Database`、`UserID`、`Password` の各パラメーターの値は、サーバーとデータベースを作成するときに指定した値に置き換えてください。 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで使用したすべてのリソースをクリーンアップするには、次のコマンドを使用してリソース グループを削除します。

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [ポータルを使用して Azure Database for MySQL サーバーを管理する](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLI を使用して Azure Database for MySQL サーバーを管理する](./how-to-manage-single-server-cli.md)

[お探しの情報が見つからない場合は、お知らせください。](https://aka.ms/mysql-doc-feedback)
