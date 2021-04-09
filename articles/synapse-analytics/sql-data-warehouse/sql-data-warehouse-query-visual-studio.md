---
title: VSTS を使用して専用 SQL プール (旧称 SQL DW) に接続する
description: Visual Studio を使用して Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) にクエリを実行する
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0baf2396b7c5af103f0b3aa223d0bccf725babbe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584144"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Visual Studio および SSDT を使用して Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) に接続する

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Visual Studio を使用して、わずか数分で Azure Synapse 内の専用 SQL プール (旧称 SQL DW) に対してクエリを実行します。 この方法では、Visual Studio 2019 の SQL Server Data Tools (SSDT) 拡張機能が使用されます。 

## <a name="prerequisites"></a>前提条件
このチュートリアルを使用するには、次のものが必要です。

* 既存の専用 SQL プール (旧称 SQL DW)。 作成するには、[専用 SQL プール (旧称 SQL DW) の作成](create-data-warehouse-portal.md)に関するページを参照してください。
* Visual Studio 用の SSDT。 Visual Studio を使用している場合は、既に Visual Studio 用の SSDT を持っている可能性があります。 インストールの手順とオプションの詳細については、 [Visual Studio と SSDT のインストール](sql-data-warehouse-install-visual-studio.md)に関するページを参照してください。
* 完全修飾 SQL サーバー名。 この情報については、[専用 SQL プール (旧称 SQL DW) への接続](sql-data-warehouse-connect-overview.md)に関するページを参照してください。

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1.専用 SQL プール (旧称 SQL DW) への接続
1. Visual Studio 2019 を開きます。
2. **[表示]**  >  **[SQL Server オブジェクト エクスプローラー]** を選択して SQL Server オブジェクト エクスプローラーを開きます。
   
    ![[SQL Server オブジェクト エクスプローラー]](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. **[SQL Server の追加]** アイコンをクリックします。
   
    ![[SQL Server の追加]](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. [サーバーへの接続] ウィンドウのフィールドに入力します。
   
    ![[サーバーへの接続]](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **[サーバー名]** : 前の手順で特定した **サーバー名** を入力します。
   * **[認証]** : **[SQL Server 認証]** または **[Active Directory 統合認証]** を選択します。
   * **[ユーザー名]** と **[パスワード]** : 先ほど [SQL Server 認証] を選択した場合は、ユーザー名とパスワードを入力します。
   * **[Connect]** をクリックします。
5. 確認のために、Azure SQL Server を展開します。 サーバーに関連付けられているデータベースが表示されます。 AdventureWorksDW を展開すると、サンプル データベース内のテーブルが表示されます。
   
    ![Explore AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2.サンプル クエリの実行
これで、データベースへの接続が確立されました。次はクエリを記述してみましょう。

1. SQL Server オブジェクト エクスプローラーでデータベースを右クリックします。
2. **[新しいクエリ]** を選択します。 新しいクエリ ウィンドウが開きます。
   
    ![[新しいクエリ]](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. 次の T-SQL クエリをクエリ ウィンドウにコピーします。
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 緑色の矢印をクリックしてクエリを実行するか、ショートカット キー `CTRL`+`SHIFT`+`E` を使用します。
   
    ![Run query](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. クエリ結果を確認します。 この例では、FactInternetSales テーブルに 60,398 行が含まれています。
   
    ![Query results](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>次のステップ
これで接続してクエリを実行することができます。[Power BI でデータを視覚化](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)してみてください。

Azure Active Directory 認証を使用するために環境を構成する方法については、[専用 SQL プール (旧称 SQL DW) の認証](sql-data-warehouse-authentication.md)に関するページを参照してください。