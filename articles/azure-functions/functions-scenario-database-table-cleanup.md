---
title: スケジュールされたクリーンアップ タスクを Azure Functions で実行する
description: Azure Functions を使用して、Azure SQL Database に接続し、定期的に行をクリーンアップするタスクをスケジュールします。
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/02/2019
ms.openlocfilehash: 0b5e255d7d108eb063ece4e5489a8762261a0bed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88207258"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Azure Functions を使用して Azure SQL Database に接続する

この記事では、Azure Functions を使用して Azure SQL Database または Azure SQL Managed Instance に接続するスケジュール済みジョブを作成する方法を示します。 この関数コードは、データベース内のテーブル内の行をクリーンアップします。 この新しい C# 関数は、Visual Studio 2019 の定義済みタイマー トリガー テンプレートに基づいて作成されます。 このシナリオを実現するには、別途データベースの接続文字列を関数アプリのアプリ設定として設定する作業が必要となります。 Azure SQL Managed Instance の場合は、Azure Functions から接続できるように[パブリック エンドポイントを有効にする](../azure-sql/managed-instance/public-endpoint-configure.md)必要があります。 このシナリオではデータベースに対する一括操作を使用しています。 

C# 関数を初めて使用する場合は、[Azure Functions C# 開発者向けリファレンス](functions-dotnet-class-library.md)をお読みください。

## <a name="prerequisites"></a>前提条件

+ 「[Visual Studio を使用して初めての関数を作成する](functions-create-your-first-function-visual-studio.md)」記事の手順が完了しており、バージョン 2.x 以降のランタイムを対象としたローカル関数アプリを作成しています。 また、プロジェクトを Azure の関数アプリに発行している必要があります。

+ この記事では、AdventureWorksLT サンプル データベースの **SalesOrderHeader** テーブルに対して一括クリーンアップ操作を実行する Transact-SQL コマンドの例を取り上げています。 AdventureWorksLT サンプル データベースを作成するには、[Azure portal を使用して Azure SQL Database でデータベースを作成する方法](../azure-sql/database/single-database-create-quickstart.md)に関する記事の手順を実行します。

+ このクイック スタートに使用するコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](../azure-sql/database/firewall-create-server-level-portal-quickstart.md)を追加している必要があります。 このルールは、ローカル コンピューターから SQL Database インスタンスにアクセスできるようにするために必要です。  

## <a name="get-connection-information"></a>接続情報の取得

[Azure portal を使用して Azure SQL Database でデータベースを作成する方法](../azure-sql/database/single-database-create-quickstart.md)に関する記事の手順で作成したデータベースの接続文字列を取得する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 左側のメニューから **[SQL データベース]** を選択し、 **[SQL データベース]** ページで目的のデータベースをクリックします。

1. **[Settings]** \(設定) の下の **[接続文字列]** を選択し、完全な **ADO.NET** の接続文字列をコピーします。 Azure SQL Managed Instance の場合は、パブリック エンドポイントの接続文字列をコピーします。

    ![ADO.NET の接続文字列をコピーします。](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>接続文字列の設定

Function App は、Azure での関数の実行をホストします。 セキュリティ上、接続文字列をはじめとするシークレットは、関数アプリの設定に保存することをお勧めします。 アプリケーション設定を使用して、コードによって誤って接続文字列が公開されることを防ぎます。 関数アプリのアプリ設定には、Visual Studio から直接アクセスできます。

以前アプリを Azure に発行している必要があります。 まだ行っていない場合は、[[Publish your function app to Azure]](functions-develop-vs.md#publish-to-azure)\(関数アプリを Azure に発行) します。

1. ソリューション エクスプローラーで関数アプリ プロジェクトを右クリックし、 **[Publish]** \(発行)  >  **[Edit Azure App Service settings]** \(Azure App Service の設定を編集する) を選択します。 **[設定の追加]** を選択し、 **[新しいアプリ設定名]** で型 `sqldb_connection` を選択して **[OK]** を選択します。

    ![関数アプリのアプリケーション設定。](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. 新しい **sqldb_connection** の設定で、前のセクションでコピーした接続文字列を **[Local]** \(ローカル) フィールドに貼り付け、`{your_username}` および `{your_password}` プレースホルダーを実際の値で置き換えます。 **[ローカルから値を挿入する]** を選択し、更新した値を **[Remote]** \(リモート) フィールドにコピーして **[OK]** を選択します。

    ![SQL 接続文字列設定の追加。](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    接続文字列は、Azure で暗号化されて格納されます (**リモート**)。 シークレットの漏えいを防ぐため、.gitignore ファイルなどを使用して local.settings.json プロジェクト ファイル (**ローカル**) をソース管理から除外します。

## <a name="add-the-sqlclient-package-to-the-project"></a>SqlClient パッケージをプロジェクトに追加する

SqlClient ライブラリを含む NuGet パッケージを追加する必要があります。 SQL Database への接続には、このデータ アクセス ライブラリが必要です。

1. Visual Studio 2019 で、ローカル関数アプリ プロジェクトを開きます。

1. ソリューション エクスプローラーで関数アプリ プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[参照]** タブで ```System.Data.SqlClient``` を探し、見つかったらそれを選択します。

1. **[System.Data.SqlClient]** ページでバージョン `4.5.1` を選択し、 **[インストール]** をクリックします。

1. インストールが完了したら変更を確認し、 **[OK]** をクリックして **[プレビュー]** ウィンドウを閉じます。

1. **[ライセンスへの同意]** ウィンドウが表示された場合は **[同意する]** をクリックします。

それでは、SQL Database に接続する C# 関数のコードを追加しましょう。

## <a name="add-a-timer-triggered-function"></a>タイマーでトリガーされる関数を追加する

1. ソリューション エクスプローラーで関数アプリ プロジェクトを右クリックし、 **[Add]** \(追加) >  **[新しい Azure 関数]** を選択します。

1. **Azure Functions** テンプレートを選択し、新しい項目に `DatabaseCleanup.cs` などの名前を付けて、 **[Add]** \(追加) を選択します。

1. **[新しい Azure 関数]** ダイアログ ボックスで、 **[タイマー トリガー]** 、 **[OK]** を選択します。 このダイアログで、タイマー トリガー関数のコード ファイルが作成されます。

1. 新しいコード ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. 既存の `Run` 関数を次のコードに置き換えます。

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    この関数は 15 秒間隔で実行され、出荷日に基づいて `Status` 列を更新します。 タイマー トリガーの詳細については、「[Azure Functions のタイマー トリガー](functions-bindings-timer.md)」を参照してください。

1. **F5** キーを押して関数アプリを起動します。 Visual Studio の背後で [[Azure Functions Core Tools]](functions-develop-local.md) 実行ウィンドウが開きます。

1. 起動後 15 秒で、関数が実行されます。 出力を確認し、更新された **SalesOrderHeader** テーブルの行数に注意してください。

    ![関数のログを表示します。](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    最初の実行では、32 行のデータを更新する必要があります。 以降の実行では、`UPDATE` ステートメントで行が選択されるように SalesOrderHeader テーブルのデータに変更を加えた場合を除き、データ行は更新されません。

[この関数を発行する](functions-develop-vs.md#publish-to-azure)予定がある場合は、`TimerTrigger` 属性を 15 秒ごとよりも適切な [cron スケジュール](functions-bindings-timer.md#ncrontab-expressions)に変更してください。

## <a name="next-steps"></a>次のステップ

次に、使用方法を学習します。 Logic Apps で Functions を使用して、その他のサービスと統合します。

> [!div class="nextstepaction"]
> [Logic Apps と統合される関数を作成する](functions-twitter-email.md)

関数の詳細については、次の記事をご覧ください。

+ [Azure Functions 開発者向けリファレンス](functions-reference.md)  
  関数のコーディングとトリガーおよびバインドの定義に関するプログラマ向けリファレンスです。
+ [Azure Functions のテスト](functions-test-a-function.md)  
  関数をテストするための各種ツールと手法について説明します。  
