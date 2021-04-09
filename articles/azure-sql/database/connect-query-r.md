---
title: Azure SQL Database Machine Learning Services (プレビュー) で R を使用してデータベースに対してクエリを実行する
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: この記事では、Azure SQL Database Machine Learning Services で R スクリプトを使用して、Azure SQL Database 内のデータベースに接続し、Transact-SQL ステートメントを使用してクエリを実行する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, sstein
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2e32a4abeae78aa7105f21ecffbb18c2eae841a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185625"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>クイック スタート:Azure SQL Database Machine Learning Services (プレビュー) で R を使用してデータベースに対してクエリを実行する 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このクイックスタートでは、Azure SQL Database Machine Learning Services で R を使用して Azure SQL Database 内のデータベースに接続し、T-SQL ステートメントを使用してデータに対してクエリを実行します。

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure SQL Database](single-database-create-quickstart.md)
- R が有効な [Machine Learning Services](../managed-instance/machine-learning-services-overview.md)。
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> この記事のスクリプトは、**Adventure Works** データベースを使用するように記述されています。

Machine Learning Services と R は、データベース内の R スクリプトを実行するために使用される、Azure SQL Database の機能です。 詳細については、[R プロジェクト](https://www.r-project.org/)に関するページを参照してください。

## <a name="get-the-sql-server-connection-information"></a>SQL Server の接続情報を取得する

Azure SQL Database のデータベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[SQL Database]** または **[SQL Managed Instance]** ページに移動します。

3. **[概要]** ページで、Azure SQL Database 内のデータベースの **[サーバー名]** の横にある完全修飾サーバー名、または Azure SQL Managed Instance 内のマネージド インタンスの **[ホスト]** の横にある完全修飾サーバー名を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

## <a name="create-code-to-query-your-database"></a>データベースに対してクエリを実行するコードを作成する

1. **SQL Server Management Studio** を開き、データベースに接続します。

   接続についてご不明な点がある場合は、「[クイック スタート: SQL Server Management Studio を使用して Azure SQL Database 内のデータベースに接続しクエリを実行する](connect-query-ssms.md)」を参照してください。

1. R スクリプト全体を [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) ストアド プロシージャに渡します。

   このスクリプトは、`@script` 引数を通して渡されます。 `@script`引数内のすべては、有効な R コードである必要があります。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > エラーが発生する場合は、Machine Learning Services (R を使用) のパブリック プレビューがデータベースで有効になっていない可能性があります。 前述の「[前提条件](#prerequisites)」を参照してください。

## <a name="run-the-code"></a>コードの実行

1. [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) ストアド プロシージャを実行します。

1. 上位 20 カテゴリ/製品の行が **[メッセージ]** ウィンドウに返されることを確認します。

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database で最初のデータベースを設計する](design-first-database-tutorial.md)
- [Azure SQL Database の Machine Learning Services と R (プレビュー)](../managed-instance/machine-learning-services-overview.md)
- [Azure SQL Database Machine Learning Services (プレビュー) で簡単な R スクリプトを作成して実行する](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context)