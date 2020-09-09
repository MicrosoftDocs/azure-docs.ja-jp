---
title: Azure Data Studio (プレビュー) を使用して Synapse SQL に接続する
description: Azure Data Studio (プレビュー) を使用して Azure Synapse Analytics の Synapse SQL に接続し、クエリを実行します。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 78807db19b413bb642e8dcf61e480d6d954b6178
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059561"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Azure Data Studio (プレビュー) を使用して Synapse SQL に接続する

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

[Azure Data Studio (プレビュー)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用して Azure Synapse Analytics の Synapse SQL に接続し、クエリを実行することができます。 

## <a name="connect"></a>接続する

Synapse SQL に接続するには、Azure Data Studio を開いて **[新しい接続]** を選択します。

![Azure Data Studio を開きます](./media/get-started-azure-data-studio/1-start.png)

**[接続の種類]** に **[Microsoft SQL Server]** を選択します。

この接続には、次のパラメーターが必要になります。

* **サーバー:** `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net 形式のサーバー
* **データベース:** データベース名

> [!NOTE]
> **SQL オンデマンド (プレビュー)** を使用する場合、URL は次のようになります。
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net
>
> **SQL プール**を使用する場合、URL は次のようになります。
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

**[認証の種類]** として、 **[Windows 認証]** 、 **[Azure Active Directory]** 、 **[SQL ログイン]** のいずれかを選択します。

認証の種類として **[SQL ログイン]** を使用する場合は、ユーザー名とパスワードのパラメーターを追加します。

* **ユーザー:** サーバーのユーザー。`<User>` 形式で指定します。
* **パスワード:** ユーザーに関連付けられているパスワード

Azure Active Directory を使用するには、必要な認証の種類を選択する必要があります。

![AAD 認証](./media/get-started-azure-data-studio/3-aad-auth.png)

次のスクリーンショットは、 **[Windows 認証]** の **[接続の詳細]** を示しています。

![Windows 認証](./media/get-started-azure-data-studio/3-windows-auth.png)

次のスクリーンショットは、 **[SQL ログイン]** を使用した **[接続の詳細]** を示しています。

![SQL ログイン](./media/get-started-azure-data-studio/2-database-details.png)

ログインに成功すると、次のようなダッシュボードが表示されます。![ダッシュボード](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>クエリ

接続後、Synapse SQL のインスタンスに対し、サポートされている [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ステートメントを使用してクエリを実行することができます。 最初に、ダッシュボード ビューから **[新しいクエリ]** を選択します。

![新しいクエリ](./media/get-started-azure-data-studio/5-new-query.png)

たとえば、SQL オンデマンドを使用して [Parquet ファイルを照会](query-parquet-files.md)するには、次の Transact-SQL ステートメントを使用します。

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>次のステップ 
Synapse SQL に接続する他の方法を詳しく見てみましょう。 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)
 
