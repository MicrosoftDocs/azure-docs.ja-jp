---
title: SQL Server または Azure SQL Database に接続する - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps を使用してワークフローを自動化することにより、オンプレミスまたはクラウド内の SQL データベースにアクセスしてそれを管理する方法です
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 804a913d17c3151d07a1ecf229e2db148dc45558
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050757"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>SQL Server または Azure SQL Database に接続する - Azure Logic Apps

この記事では、SQL Server コネクタを使用してロジック アプリの中から SQL データベースのデータにアクセスする方法を示します。 ロジック アプリを作成することによって、SQL データとリソースを管理するタスク、プロセス、ワークフローを自動化できます。 コネクタは、[オンプレミスの SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation)と[クラウド内の Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)の両方で動作します。 

SQL データベースや Dynamics CRM Online などの他のシステム内のイベントによってトリガーされたときに実行されるロジック アプリを作成できます。 ロジック アプリは、データの取得、挿入、削除のほか、SQL クエリやストアド プロシージャを実行することもできます。 たとえば、Dynamics CRM Online の新しいレコードを自動的に確認し、新しいレコード用の項目を SQL データベースに追加した後、電子メール アラートを送信するロジック アプリをビルドできます。

Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。 コネクタ固有の技術情報については、[SQL Server コネクタ リファレンス](https://docs.microsoft.com/connectors/sql/)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* SQL データベースにアクセスする必要があるロジック アプリ。 SQL トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。 

* [Azure SQL データベース](../sql-database/sql-database-get-started-portal.md)または [SQL Server データベース](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  テーブルには、ロジック アプリが操作を呼び出したときに結果を返すことができるように、データが含まれている必要があります。 Azure SQL Database を作成する場合は、用意されているサンプル データベースを使用できます。 

* SQL サーバー名、データベース名、ユーザー名およびパスワード。 ロジックを承認して SQL Server にアクセスできるようにするには、これらの資格情報が必要です。 

  * Azure SQL Database の場合、これらの詳細は、接続文字列か、Azure Portal の SQL Database のプロパティで確認できます。

    "Server=tcp:<*yourServerName*>.database.windows.net,1433;Initial Catalog=<*yourDatabaseName*>;Persist Security Info=False;User ID=<*yourUserName*>;Password=<*yourPassword*>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

  * SQL Server の場合、これらの詳細は、接続文字列で確認できます。 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

* SQL Server などのオンプレミス システムにロジック アプリを接続する前に、[オンプレミス データ ゲートウェイをセットアップする](../logic-apps/logic-apps-gateway-install.md)必要があります。 これにより、ロジック アプリの SQL 接続を作成するときに、ゲートウェイを選択できます。

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>SQL トリガーを追加する

Azure Logic Apps では、すべてのロジック アプリは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から起動されます。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。 トリガーが発生するたびに、Logic Apps エンジンによってロジック アプリ インスタンスが作成され、アプリのワークフローが開始されます。

1. Azure Portal または Visual Studio で、Logic Apps デザイナーを開いて、空のロジック アプリを作成します。 この例では、Azure Portal を使用します。

2. 検索ボックスに、フィルターとして「sql server」と入力します。 トリガーの一覧から、使用する SQL トリガーを選択します。 

   この例では、このトリガーを選択します。**SQL Server - アイテムの作成時**

   ![［SQL Server - When an item is created］\(SQL Server - 項目が作成されたとき\) トリガーの選択](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. 接続の詳細の入力を求められたら、[SQL 接続を今すぐ作成](#create-connection)します。 
   接続が既に存在する場合は、使用するテーブルを **［テーブル名］** の一覧から選択します。

   ![テーブルの選択](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. **［間隔］** プロパティと **［頻度］** を設定します。これらは、ロジック アプリがテーブルをチェックする頻度を指定します。

   この例では、選択したテーブルのみをチェックし、それ以外は何もしません。 
   反応を見るために、望みのタスクを実行するアクションを追加します。 
   
   たとえば、テーブルの新しい項目を表示するには、そのテーブルと同じフィールドを持つファイルを作成し、電子メール アラートを送信するアクションなどを追加します。 
   このコネクタまたは他のコネクタのその他のアクションについては、[Logic Apps コネクタ](../connectors/apis-list.md)に関する記事を参照してください。

5. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。 

   この手順によって、ロジック アプリが自動的に有効化され、Azure に発行されます。 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>SQL アクションを追加する

Azure Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、トリガーまたは別のアクションに続くワークフロー内のステップです。 この例では、ロジック アプリは、[繰り返しトリガー](../connectors/connectors-native-recurrence.md)で起動され、SQL データベースから行を取得するアクションを呼び出します。

1. Azure Portal または Visual Studio で、Logic Apps デザイナーでロジック アプリを開きます。 この例では、Azure Portal を使用します。

2. Logic Apps デザイナーのトリガーまたはアクションで、 **[新しいステップ]**  >  **[アクションの追加]** を選択します。

   ![[新しいステップ] > [アクションの追加] の選択](./media/connectors-create-api-sqlazure/add-action.png)
   
   既存のステップの間にアクションを追加するには、接続矢印の上にマウスを移動します。 
   表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

2. 検索ボックスに、フィルターとして「sql server」と入力します。 アクションの一覧から、使用する SQL アクションを選択します。 

   この例では、1 つのレコードを取得する次の操作を選択します。 **[SQL Server - Get row]\(SQL Server - 行の取得\)**

   ![「sql server」入力、[SQL Server - Get row]\(SQL Server - 行の取得\) 選択](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. 接続の詳細の入力を求められたら、[SQL 接続を今すぐ作成](#create-connection)します。 
   接続が存在する場合は、 **［テーブル名］** を入力し、使用するレコードの **［行 ID］** を入力します。

   ![テーブル名と行 ID の入力](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   この例では、選択したテーブルから 1 行のみを返し、それ以外は何もしません。 
   この行のデータを表示するには、後でレビューするための行のフィールドが含まれるファイルを作成し、そのファイルをクラウドのストレージ アカウントに格納するその他のアクションを追加します。 このコネクタまたは他のコネクタのその他のアクションについては、[Logic Apps コネクタ](../connectors/apis-list.md)に関する記事を参照してください。

4. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>データベースに接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>一括データを処理します。

サイズが大きすぎてコネクタが同時にすべての結果を返さない結果セットを操作する場合、または、結果セットのサイズと構造を詳細に制御する場合があります。 このような大きな結果セットを処理する方法は、いくつかあります。

* 結果を、より小さなセットとして管理しやすくするには、*改ページ位置の自動修正*をオンします。 詳細は、「[Get bulk data, records, and items by using pagination](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)」(改ページ位置の自動修正を使用した一括データ、レコードおよび項目) を参照してください。

* 希望どおりの結果を編成するストアド プロシージャを作成します。

  複数の行を取得または挿入する場合、ロジック アプリは、こちらの[制限](../logic-apps/logic-apps-limits-and-config.md)の中で "[*until ループ*](../logic-apps/logic-apps-control-flow-loops.md#until-loop)" を使用することで、行を反復処理できます。 
  ただし、ロジック アプリは、数千から数百万の行がある非常に大きなレコード セットを処理する場合があります。このような場合は、データベースへの呼び出しコストを最小限にする必要があります。

  代わりに、SQL インスタンスで実行され、**SELECT - ORDER BY**ステートメントを使用して、望みどおりの方法で結果を整理する "[*ストアド プロシージャ*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)" を作成できます。 
  このソリューションでは、結果のサイズと構造を詳細に制御できます。 
  ロジック アプリは、SQL Server コネクタの **［ストアド プロシージャの実行］** アクションを使用して、ストアド プロシージャを呼び出します。

  ソリューションの詳細については、次の記事を参照してください。

  * [Logic Apps での一括データ転送に対する SQL の改ページ処理](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY Clause](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>コネクタ固有の詳細

このコネクタのトリガー、アクション、および制限に関する技術情報については、[コネクタ リファレンス](/connectors/sql/)を参照してください。 

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。

