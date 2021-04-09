---
title: SSMS を使用して専用 SQL プール (旧称 SQL DW) に接続する
description: SQL Server Management Studio (SSMS) を使用して Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) に接続し、クエリを実行します。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b142c88b0003281237dad125080930c0dd4d3bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673605"
---
# <a name="connect-to-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) を使用して Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) に接続する

> [!div class="op_single_selector"]
>
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

SQL Server Management Studio (SSMS) を使用して専用 SQL プール (旧称 SQL DW) に接続し、クエリを実行します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを使用するには、次のものが必要です。

* 既存の専用 SQL プール。 作成するには、[専用 SQL プール (旧称 SQL DW) の作成](create-data-warehouse-portal.md)に関するページを参照してください。
* SQL Server Management Studio (SSMS) をインストールしている。 [SSMS をダウンロード](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)していない場合はダウンロードします (無料)。
* 完全修飾 SQL サーバー名。 この情報については、[専用 SQL プール (旧称 SQL DW)](sql-data-warehouse-connect-overview.md) に関するページを参照してください。

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1.専用 SQL プール (旧称 SQL DW) への接続

1. SSMS を開きます。
2. **[ファイル]**  >  **[オブジェクト エクスプローラーを接続]** を選択してオブジェクト エクスプローラーを開きます。

    ![[SQL Server オブジェクト エクスプローラー]](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. [サーバーへの接続] ウィンドウのフィールドに入力します。

   ![[サーバーへの接続]](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **[サーバー名]** : 前の手順で特定した **サーバー名** を入力します。
   * **[認証]** : **[SQL Server 認証]** または **[Active Directory 統合認証]** を選択します。
   * **[ユーザー名]** と **[パスワード]** : 先ほど [SQL Server 認証] を選択した場合は、ユーザー名とパスワードを入力します。
   * **[Connect]** をクリックします。
4. 確認のために、Azure SQL Server を展開します。 サーバーに関連付けられているデータベースが表示されます。 AdventureWorksDW を展開すると、サンプル データベース内のテーブルが表示されます。

   ![Explore AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2.サンプル クエリの実行

これで、データベースへの接続が確立されました。次はクエリを記述してみましょう。

1. SQL Server オブジェクト エクスプローラーでデータベースを右クリックします。
2. **[新しいクエリ]** を選択します。 新しいクエリ ウィンドウが開きます。

   ![[新しいクエリ]](./media/sql-data-warehouse-query-ssms/new-query.png)
3. 次の T-SQL クエリをクエリ ウィンドウにコピーします。

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. `Execute` をクリックしてクエリを実行するか、ショートカット キー `F5` を使用します。

   ![Run query](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. クエリ結果を確認します。 この例では、FactInternetSales テーブルに 60,398 行が含まれています。

   ![Query results](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>次のステップ

これで接続してクエリを実行することができます。[Power BI でデータを視覚化](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)してみてください。 Azure Active Directory 認証を使用するために環境を構成する方法については、[専用 SQL プールの認証](sql-data-warehouse-authentication.md)に関するページを参照してください。