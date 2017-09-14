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
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: a3d5cb909dbfcb00f3fbfa0165bb6cd58eb18688
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Azure SQL Database コネクタの概要
Azure SQL Database コネクタを使用して、テーブル内のデータを管理する組織のワークフローを作成します。 

SQL Database では次のことを行います。

* 顧客データベースに新しい顧客を追加するか、注文データベースで注文を更新することで、ワークフローを構築します。
* データ行の取得、新しい行の挿入、行の削除を行うアクションを使用します。 たとえば、Dynamics CRM Online にレコードが作成されると (トリガー)、Azure SQL Database に行を挿入します (アクション)。 

このトピックでは、ロジック アプリ内で SQL Database コネクタを使用する方法を説明し、アクションの一覧を示します。

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
   > この例では、テーブルから 1 行が返されます。 この行のデータを確認するには、テーブルのフィールドを使用してファイルを作成する別のアクションを追加してください。 たとえば、FirstName フィールドと LastName フィールドを使用してクラウド ストレージ アカウントに新しいファイルを作成する OneDrive アクションを追加します。 
   > 
   > 
5. ツール バーの左上隅にある **[保存]** を選択して変更を保存します。 ロジック アプリが保存され、場合によっては、自動的に有効になります。

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/sql/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。 

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。 [API の一覧](apis-list.md)で、Logic Apps で使用できる他のコネクタを確認してください。


