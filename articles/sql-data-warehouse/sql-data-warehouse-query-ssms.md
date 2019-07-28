---
title: Azure SQL Data Warehouse への接続 (SSMS) | Microsoft Docs
description: SQL Server Management Studio (SSMS) を使用して Azure SQL Data Warehouse に接続し、クエリを実行します。
services: sql-data-warehouse
author: xiaoyul-preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 64ea7c175b733f974eba6c081ee2c98814cbcda2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873715"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) で SQL Data Warehouse に接続する
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

SQL Server Management Studio (SSMS) を使用して Azure SQL Data Warehouse に接続し、クエリを実行します。 

## <a name="prerequisites"></a>前提条件
このチュートリアルを使用するには、次のものが必要です。

* 既存の SQL Data Warehouse。 作成方法については、[SQL Data Warehouse の作成][Create a SQL Data Warehouse]に関するページを参照してください。
* SQL Server Management Studio (SSMS) をインストールしている。 [SSMS をインストール][Install SSMS]していない場合はインストールします (無料)。
* 完全修飾 SQL サーバー名。 これを特定するには、[SQL Data Warehouse への接続][Connect to SQL Data Warehouse]に関するページを参照してください。

## <a name="1-connect-to-your-sql-data-warehouse"></a>1.SQL Data Warehouse への接続
1. SSMS を開きます。
2. オブジェクト エクスプローラーを開きます。 これを行うには、[**ファイル]** 、 >  **[オブジェクト エクスプローラーを接続]** の順に選択します。
   
    ![[SQL Server オブジェクト エクスプローラー]][1]
3. [サーバーへの接続] ウィンドウのフィールドに入力します。
   
    ![[サーバーへの接続]][2]
   
   * **[サーバー名]** : 前の手順で特定した**サーバー名**を入力します。
   * **[認証]** : **[SQL Server 認証]** または **[Active Directory 統合認証]** を選択します。
   * **[ユーザー名]** と **[パスワード]** : 先ほど [SQL Server 認証] を選択した場合は、ユーザー名とパスワードを入力します。
   * **[接続]** をクリックします。
4. 確認のために、Azure SQL Server を展開します。 サーバーに関連付けられているデータベースが表示されます。 AdventureWorksDW を展開すると、サンプル データベース内のテーブルが表示されます。
   
    ![Explore AdventureWorksDW][3]

## <a name="2-run-a-sample-query"></a>2.サンプル クエリの実行
これで、データベースへの接続が確立されました。次はクエリを記述してみましょう。

1. SQL Server オブジェクト エクスプローラーでデータベースを右クリックします。
2. **[新しいクエリ]** を選択します。 新しいクエリ ウィンドウが開きます。
   
    ![[新しいクエリ]][4]
3. 次の TSQL クエリをクエリ ウィンドウにコピーします。
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. クエリを実行します。 そのためには、`Execute` をクリックするか、ショートカット キー (`F5`) を使用します。
   
    ![Run query][5]
5. クエリ結果を確認します。 この例では、FactInternetSales テーブルに 60,398 行が含まれています。
   
    ![Query results][6]

## <a name="next-steps"></a>次の手順
これで接続してクエリを実行することができます。[Power BI でデータを視覚化][visualizing the data with PowerBI]してみてください。

Azure Active Directory 認証を使用するために環境を構成する方法については、[SQL Data Warehouse の認証][Authenticate to SQL Data Warehouse]に関するページを参照してください。

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
