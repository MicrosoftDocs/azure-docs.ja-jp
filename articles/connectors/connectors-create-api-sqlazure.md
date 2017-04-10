---
title: "ロジック アプリに Azure SQL Database コネクタを追加する | Microsoft Docs"
description: "Azure SQL Database コネクタと REST API パラメーターの概要"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: ce3a622db8667df8b3f1d1391c2aa0d7e1e012a5
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Azure SQL Database コネクタの概要
Azure SQL Database コネクタを使用して、テーブル内のデータを管理する組織のワークフローを作成します。 

SQL Database では次のことを行います。

* 顧客データベースに新しい顧客を追加するか、注文データベースで注文を更新することで、ワークフローを構築します。
* データ行の取得、新しい行の挿入、行の削除を行うアクションを使用します。 たとえば、Dynamics CRM Online にレコードが作成されると (トリガー)、Azure SQL Database に行を挿入します (アクション)。 

このトピックでは、ロジック アプリ内で SQL Database コネクタを使用する方法を説明し、アクションの一覧を示します。

> [!NOTE]
> 本記事は、一般公開された Logic Apps の一般公開 (GA) を対象としています。 
> 
> 

Logic Apps の詳細については、「[Logic Apps とは](../logic-apps/logic-apps-what-are-logic-apps.md)」と[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関するページを参照してください。

## <a name="connect-to-azure-sql-database"></a>Azure SQL Database に接続する
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "*接続*" を作成します。 接続により、ロジック アプリと別のサービスとの接続が実現します。 たとえば、SQL Database に接続するには、まず SQL Database "*接続*" を作成します。 接続を作成するには、接続対象のサービスへのアクセスに通常使用する資格情報を入力します。 そのため、SQL Database の場合は、SQL Database の資格情報を入力して接続を作成します。 

#### <a name="create-the-connection"></a>接続の作成
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>トリガーを使用する
このコネクタにはトリガーがありません。 定期実行のトリガー、HTTP Webhook トリガー、他のコネクタで使用可能なトリガーなど、他のトリガーを使用してロジック アプリを起動します。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事に例が記載されています。

## <a name="use-an-action"></a>アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

1. プラス記号を選択します。 **[アクションの追加]**、**[条件の追加]**、**[More (その他)]** のいずれかのオプションという複数の選択肢があります。
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. **[アクションの追加]**を選択します。
3. テキスト ボックスに「sql」と入力して、使用可能なすべてのアクションの一覧を取得します。
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. この例では、**[SQL Server - Get row (SQL Server - 行を取得する)]** を選択します。 接続が既に存在する場合は、**[テーブル名]** ボックスの一覧でテーブル名を選択し、**[行 ID]** に返される ID を入力します。
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    接続情報の入力を求められたら、詳細を入力して接続を作成します。 これらのプロパティについては、このトピックの「[接続の作成](connectors-create-api-sqlazure.md#create-the-connection)」を参照してください。 
   
   > [!NOTE]
   > この例では、テーブルから&1; 行が返されます。 この行のデータを確認するには、テーブルのフィールドを使用してファイルを作成する別のアクションを追加してください。 たとえば、FirstName フィールドと LastName フィールドを使用してクラウド ストレージ アカウントに新しいファイルを作成する OneDrive アクションを追加します。 
   > 
   > 
5. ツール バーの左上隅にある **[保存]** を選択して変更を保存します。 ロジック アプリが保存され、場合によっては、自動的に有効になります。

## <a name="technical-details"></a>技術的な詳細
## <a name="sql-database-actions"></a>SQL Database アクション
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 SQL Database コネクタには、次のアクションがあります。 

| [操作] | 説明 |
| --- | --- |
| [ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure) |SQL でストアド プロシージャを実行します。 |
| [GetRow](connectors-create-api-sqlazure.md#get-row) |SQL テーブルから&1; 行を取得します。 |
| [GetRows](connectors-create-api-sqlazure.md#get-rows) |SQL テーブルから複数の行を取得します。 |
| [InsertRow](connectors-create-api-sqlazure.md#insert-row) |SQL テーブルに新しい行を挿入します。 |
| [DeleteRow](connectors-create-api-sqlazure.md#delete-row) |SQL テーブルから行を削除します。 |
| [GetTables](connectors-create-api-sqlazure.md#get-tables) |SQL データベースからテーブルを取得します。 |
| [UpdateRow](connectors-create-api-sqlazure.md#update-row) |SQL テーブルの既存の行を更新します。 |

### <a name="action-details"></a>アクションの詳細
このセクションでは、必須または任意の入力プロパティ、コネクタに関連付けられた対応する出力など、各アクションに関する具体的な詳細について説明します。

#### <a name="execute-stored-procedure"></a>ストアド プロシージャの実行
SQL でストアド プロシージャを実行します。  

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| procedure * |プロシージャ名 |実行するストアド プロシージャの名前 |
| parameters * |入力パラメーター |パラメーターは動的で、選択したストアド プロシージャに基づいています。 <br/><br/> たとえば、Adventure Works サンプル データベースを使用している場合は、*ufnGetCustomerInformation* ストアド プロシージャを選択します。 **Customer ID** 入力パラメーターが表示されます。 「6」または他の顧客 ID の&1; つを入力します。 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
ProcedureResult: ストアド プロシージャの実行結果を示します。

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| OutputParameters |オブジェクト |出力パラメーターの値 |
| ReturnCode |integer |プロシージャのリターン コード |
| ResultSets |オブジェクト |結果セット |

#### <a name="get-row"></a>行を取得する
SQL テーブルから&1; 行を取得します。  

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table * |テーブル名 |SQL テーブルの名前 |
| id * |行 ID |取得する行の一意識別子 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

#### <a name="get-rows"></a>行を取得する
SQL テーブルから複数の行を取得します。  

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |テーブル名 |SQL テーブルの名前 |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |
| $filter |Filter Query (フィルター クエリ) |エントリ数を制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
ItemsList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

#### <a name="insert-row"></a>行を挿入する
SQL テーブルに新しい行を挿入します。  

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |テーブル名 |SQL テーブルの名前 |
| item* |行 |SQL の指定されたテーブルに挿入する行 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

#### <a name="delete-row"></a>行を削除する
SQL テーブルから行を削除します。  

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |テーブル名 |SQL テーブルの名前 |
| id* |行 ID |削除する行の一意識別子 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
なし。

#### <a name="get-tables"></a>テーブルを取得する
SQL データベースからテーブルを取得します。  

この呼び出しには、パラメーターはありません。 

##### <a name="output-details"></a>出力の詳細
TablesList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

#### <a name="update-row"></a>行を更新する
SQL テーブルの既存の行を更新します。  

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |テーブル名 |SQL テーブルの名前 |
| id* |行 ID |更新する行の一意識別子 |
| item* |行 |更新された値のある行 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

### <a name="http-responses"></a>HTTP 応答
他のアクションを呼び出すとき、特定の応答を受け取る場合があります。 次の表に、これらの応答とその説明を示します。  

| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。 [API の一覧](apis-list.md)で、Logic Apps で使用できる他のコネクタを確認してください。


