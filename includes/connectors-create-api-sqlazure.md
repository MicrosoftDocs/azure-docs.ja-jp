---
title: インクルード ファイル
description: インクルード ファイル
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: da03c5247b8ebe0a3305b08a05d661264497663f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181428"
---
* Azure SQL Database を使用している場合は、「[Azure SQL Database に接続する](#connect-azure-sql-db)」の手順に従ってください。 

* SQL Server を使用している場合は、「[SQL Server への接続](#connect-sql-server)」の手順に従ってください。

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Azure SQL Database に接続する

1. SQL のトリガーまたはアクションから接続情報を求められた場合は、以下の手順に従います。

   1. 接続の名前を作成します。

   2. SQL Server を選択し、データベースを選択します。 

      データベースの一覧は、SQL Server を選択した後にのみ表示されます。
 
   3. サーバーのユーザー名とパスワードを入力します。

      この情報は、Azure ポータルの、SQL データベースのプロパティまたは次の接続文字列にあります。 
      
      "User ID=<*yourUserName*>"
      <br>
      "Password=<*yourPassword*>"

   この例は、トリガーの場合の接続情報を示していますが、アクションでも次の手順を使用できます。

   ![Azure SQL Database 接続を作成する](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   アスタリスク (*) は、必要な値を示しています。

   | プロパティ | 値 | 詳細 | 
   |----------|-------|---------| 
   | 接続名 | <*my-sql-connection*> | 接続の名前 | 
   | SQL Server 名 | <*my-sql-server*> | SQL Server の名前 |
   | SQL データベース名 | <*my-sql-database*>  | SQL データベースの名前 | 
   | ユーザー名 | <*my-sql-username*> | データベースにアクセスするためのユーザー名 |
   | パスワード | <*my-sql-password*> | データベースにアクセスするためのパスワード | 
   |||| 

2. 操作が完了したら、 **[作成]** を選択します。

3. 接続を作成した後で、[SQL トリガーの追加](#add-sql-trigger)または[ SQL アクションの追加](#add-sql-action)を続けて実行します。

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>SQL Server への接続

ゲートウェイを選択する前に、既に[データ ゲートウェイを設定](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)していることを確認してください。 こうすれば、接続を作成するときにゲートウェイの一覧で、ゲートウェイが表示されます。

1. SQL のトリガーまたはアクションから接続情報を求められた場合は、以下の手順に従います。

   1. トリガーまたはアクションで、 **[オンプレミスのデータ ゲートウェイ経由で接続]** を選択し、SQL Server のオプションが表示されるようにします。

   2. 接続の名前を作成します。

   3. SQL Server のアドレスを指定し、データベースの名前を指定します。
   
      接続文字列で、この情報を確認できます。 
      
      * "Server=<*yourServerAddress*>"
      * "Database=<*yourDatabaseName*>"

   4. サーバーのユーザー名とパスワードを入力します。

      接続文字列で、この情報を確認できます。 
      
      * "User ID=<*yourUserName*>"
      * "Password=<*yourPassword*>"

   5. SQL server で、Windows 認証または基本認証を使用する場合は、認証の種類を選択します。

   6. 以前に作成した、オンプレミス データ ゲートウェイの名前を選択します。
   
      ゲートウェイが一覧に表示されない場合は、正しく[ゲートウェイを設定](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)していることを確認します。

   この例は、トリガーの場合の接続情報を示していますが、アクションでも次の手順を使用できます。

   ![SQL Server の接続の作成](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   アスタリスク (*) は、必要な値を示しています。

   | プロパティ | 値 | 詳細 | 
   |----------|-------|---------| 
   | オンプレミスのゲートウェイ経由で接続 | SQL Server の設定で最初にこのオプションを選択します。 | | 
   | 接続名 | <*my-sql-connection*> | 接続の名前 | 
   | SQL Server 名 | <*my-sql-server*> | SQL Server の名前 |
   | SQL データベース名 | <*my-sql-database*>  | SQL データベースの名前 |
   | ユーザー名 | <*my-sql-username*> | データベースにアクセスするためのユーザー名 |
   | パスワード | <*my-sql-password*> | データベースにアクセスするためのパスワード | 
   | 認証の種類 | Windows または基本 | 省略可能:SQL Server で使用される認証の種類 | 
   | ゲートウェイ | <*my-data-gateway*> | オンプレミス データ ゲートウェイの名前 | 
   |||| 

2. 操作が完了したら、 **[作成]** を選択します。 

3. 接続を作成した後で、[SQL トリガーの追加](#add-sql-trigger)または[ SQL アクションの追加](#add-sql-action)を続けて実行します。
