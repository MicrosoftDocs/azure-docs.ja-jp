---
title: "スケジュールされたクリーンアップ タスクを Azure Functions で実行する | Microsoft Docs"
description: "イベント タイマーに従って実行される C# 関数を Azure Functions で作成します。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/26/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: b873a7d0ef9efa79c9a173a8bfd3522b12522322
ms.openlocfilehash: c0b4a963275dae5bbf203388cb61086393803b15


---
# <a name="use-azure-functions-to-perform-a-scheduled-clean-up-task"></a>スケジュールされたクリーンアップ タスクを Azure Functions で実行する
このトピックでは、Azure Functions を使用して C# の新しい関数を作成する方法を紹介します。作成するのは、イベント タイマーに従ってデータベース テーブル内の行をクリーンアップする関数です。 この新しい関数は、Azure Functions ポータルで定義済みテンプレートに基づいて作成されます。 このシナリオを実現するには、別途データベースの接続文字列を関数アプリの App Service 設定として設定する作業が必要となります。 

## <a name="prerequisites"></a>前提条件
関数を作成するには、アクティブな Azure アカウントを用意する必要があります。 Azure アカウントがない場合は、 [無料アカウントを利用できます](https://azure.microsoft.com/free/)。

このトピックでは、SQL Database の *TodoItems* というテーブルに対して一括クリーンアップ操作を実行する Transact-SQL コマンドの例を取り上げています。 この TodoItems テーブルは、 [Azure App Service Mobile Apps のクイック スタート チュートリアル](../app-service-mobile/app-service-mobile-ios-get-started.md)で作成したものです。 サンプル データベースを使用してもかまいませんが、別のテーブルを使う場合はコマンドを適宜変更する必要があります。

Mobile Apps バックエンドで使用する接続文字列は、ポータルから **[すべての設定]** > **[アプリケーション設定]** > **[接続文字列]** > **[接続文字列の値の表示]** > **[MS_TableConnectionString]** の順に選択して取得できます。 また、ポータルから **[すべての設定]** > **[プロパティ]** > **[データベース接続文字列の表示]** > **[ADO.NET (SQL 認証)]** の順に選択して直接 SQL Database から接続文字列を取得することもできます。

このシナリオではデータベースに対する一括操作を使用しています。 Mobile Apps のテーブルにおける CRUD 操作を関数で個別に処理する場合は、Mobile Table バインドを使用する必要があります。

## <a name="set-a-sql-database-connection-string-in-the-function-app"></a>関数アプリにおける SQL Database の接続文字列の設定
関数アプリは Azure での関数の実行をホストします。 接続文字列をはじめとするシークレットは、関数アプリの設定に保存することをお勧めします。 そうすれば、最終的に関数のコードが置かれるリポジトリから意図せずシークレットが漏えいするのを防ぐことができます。 

1. [Azure Functions ポータル](https://functions.azure.com/signin) に移動し、Azure アカウントでサインインします。
2. 使用する既存の関数アプリがある場合は、**[Your function apps (関数アプリ)]** から選択し、**[開く]** をクリックします。 新しい関数アプリを作成するには、アプリ用に一意の **[名前]** を入力するか、生成された名前をそのまま使用し、希望する **[リージョン]** を選択して **[Create + get started (作成 + 開始)]** をクリックします。 
3. 関数アプリで、**[Function App Settings (関数アプリの設定)]** > **[Go to App Service Settings (App Service の設定に移動)]** の順にクリックします。 
   
    ![関数アプリの設定のブレード](./media/functions-create-an-event-processing-function/functions-app-service-settings.png)
4. 関数アプリで **[すべての設定]** をクリックし、下へスクロールして **[アプリケーション設定]** に移動します。次に、**[接続文字列]** の **[名前]** に `sqldb_connection` と入力し、**[値]** に接続文字列を貼り付けます。その後 **[保存]** をクリックし、関数アプリのブレードを閉じて Functions ポータルに戻ります。
   
    ![App Service setting connection string](./media/functions-create-an-event-processing-function/functions-app-service-settings-connection-strings.png)

それでは、SQL Database に接続する C# 関数のコードを追加しましょう。

## <a name="create-a-timer-triggered-function-from-the-template"></a>タイマーでトリガーされる関数をテンプレートから作成する
1. 関数アプリで **[+ 新しい関数]** > **[TimerTrigger - C#]** > **[作成]** の順にクリックします。 既定のスケジュールで (1 分おきに) 実行される関数が既定の名前で作成されます。 
   
    ![Create a new timer-triggered function](./media/functions-create-an-event-processing-function/functions-create-new-timer-trigger.png)
2. **[開発]** タブの **[コード]** ウィンドウで、既存の関数コードの一番上に次のアセンブリ参照を追加します。
    ```cs
        #r "System.Configuration"
        #r "System.Data"
    ```

3. 次の `using` ステートメントを関数に追加します。
    ```cs
        using System.Configuration;
        using System.Data.SqlClient;
        using System.Threading.Tasks;
    ```

4. 既存の **Run** 関数を次のコードに置き換えます。
    ```cs
        public static async Task Run(TimerInfo myTimer, TraceWriter log)
        {
            var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
            using (SqlConnection conn = new SqlConnection(str))
            {
                conn.Open();
                var text = "DELETE from dbo.TodoItems WHERE Complete='True'";
                using (SqlCommand cmd = new SqlCommand(text, conn))
                {
                    // Execute the command and log the # rows deleted.
                    var rows = await cmd.ExecuteNonQueryAsync();
                    log.Info($"{rows} rows were deleted");
                }
            }
        }
    ```

5. **[保存]** をクリックして、次に関数が実行されるようすを **[ログ]** ウィンドウで観察し、TodoItems テーブルから削除された行数をメモします。
6. (省略可) [Mobile Apps クイック スタート アプリ](../app-service-mobile/app-service-mobile-ios-get-started.md)を使用して、新たにいくつかの項目を "完了済み" に設定します。その後 **[ログ]** ウィンドウに戻り、次回関数が実行されたときに、同数の行が削除されるようすを観察してください。 

## <a name="next-steps"></a>次のステップ
Azure Functions の詳細については、次のトピックを参照してください。

* [Azure Functions 開発者向けリファレンス](functions-reference.md)  
   関数のコーディングとトリガーおよびバインドの定義に関するプログラマ向けリファレンスです。
* [Azure Functions のテスト](functions-test-a-function.md)  
   関数をテストするための各種ツールと手法について説明します。
* [Azure Functions のスケーリング方法](functions-scale.md)  
  Azure Functions で利用できるサービス プラン (従量課金プランを含む) と、適切なプランを選択する方法について説明します。  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO5-->


