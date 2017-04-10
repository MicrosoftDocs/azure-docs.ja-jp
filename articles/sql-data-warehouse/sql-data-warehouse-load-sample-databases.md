---
title: "SQL Data Warehouse へのサンプル データのロード | Microsoft Docs"
description: "SQL Data Warehouse へのサンプル データのロード"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e338ecf8-cfee-419b-b7b6-98108d381c62
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: cc5ec6022cede019541d697905aa068b88d25ee4
ms.lasthandoff: 03/22/2017



---
# <a name="load-sample-data-into-sql-data-warehouse"></a>SQL Data Warehouse へのサンプル データのロード
次の簡単な手順で Adventure Works サンプル データベースをロードしてクエリを実行します。 これらのスクリプトでは、まず sqlcmd を使用して、テーブルとビューを作成する SQL を実行します。 テーブルが作成されると、スクリプトは bcp を使用してデータを読み込みます。  まだ sqlcmd と bcp をインストールしていない場合は、リンクに従って [bcp をインストール][install bcp]し、[sqlcmd をインストール][install sqlcmd]します。

## <a name="load-sample-data"></a>サンプル データの読み込み
1. [SQL Data Warehouse の Adventure Works サンプル スクリプト][Adventure Works Sample Scripts for SQL Data Warehouse]の zip ファイルをダウンロードします。
2. ダウンロードした zip からローカル コンピューターのディレクトリにファイルを抽出します。
3. 抽出したファイル aw_create.bat を編集し、ファイルの先頭にある以下の変数を設定します。  "=" とパラメーターの間にスペースを入れないようにします。  編集内容の例を次に示します。
   
    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```
4. Windows コマンド プロンプトから編集した aw_create.bat を実行します。  編集バージョンの aw_create.bat を保存したディレクトリにいることを確認します。
   このスクリプトでは、次のことが行われます。
   
   * データベースに既に存在する Adventure Works のテーブルまたはビューを削除します
   * Adventure Works のテーブルとビューを作成します
   * bcp を使用して Adventure Works の各テーブルをロードします
   * Adventure Works の各テーブルの行数を検証します
   * Adventure Works の各テーブルのすべての列の統計情報を収集します

## <a name="query-sample-data"></a>サンプル データのクエリ
SQL Data Warehouse にサンプル データをロードしたら、いくつかのクエリをすぐに実行できます。  クエリを実行するには、Azure SQL DW に新しく作成した Adventure Works データベースに Visual Studio と SSDT を使用して接続します (詳しくは、[Visual Studio を使用したクエリ][query with Visual Studio]に関するドキュメントを参照)。

従業員のすべての情報を取得する簡単な SELECT ステートメントの例:

```sql
SELECT * FROM DimEmployee;
```

GROUP BY などのコンストラクトを使用する、より複雑なクエリを実行して、日ごとの総売上金額の合計を参照する例:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SELECT と WHERE 句を使用して、ある日付以前の注文をフィルター処理する例:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL Data Warehouse は、SQL Server がサポートするほぼすべての T-SQL 構造をサポートします。  相違点については、[コードの移行][migrate code]ドキュメントをご覧ください。

## <a name="next-steps"></a>次のステップ
サンプル データをクエリしたので、SQL Data Warehouse の[開発][develop]、[ロード][load]、[移行][migrate]の方法をご確認ください。

<!--Image references-->

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[query with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrate code]: sql-data-warehouse-migrate-code.md
[install bcp]: sql-data-warehouse-load-with-bcp.md
[install sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works Sample Scripts for SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip

