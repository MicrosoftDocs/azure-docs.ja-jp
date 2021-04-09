---
title: SQL 変換の適用
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の Apply SQL Transformation (SQL 変換の適用) モジュールを使用して入力データセットに対して SQLite クエリを実行し、データを変換する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: c66fbe59fd5b2660d02bfca285f78666d64569fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94555602"
---
# <a name="apply-sql-transformation"></a>SQL 変換の適用

この記事では、Azure Machine Learning デザイナーのモジュールについて説明します。

Apply SQL Transformation (SQL 変換の適用) モジュールを使用すると、次のことを実行できます。
  
-   結果のテーブルを作成し、データセットを移植可能なデータベースに保存する。  
  
-   データ型に対してカスタム変換を実行するか、集計を作成する。  
  
-   SQL クエリ ステートメントを実行してデータをフィルター処理または変更し、クエリ結果をデータ テーブルとして返す。  

> [!IMPORTANT]
> このモジュールで使用される SQL エンジンは **SQLite** です。 SQLite 構文の詳細については、「[SQLite によって認識される SQL](https://www.sqlite.org/index.html)」を参照してください。
> このモジュールは、メモリ DB 内にある SQLite にデータをバンプします。そのため、このモジュールの実行には多くのメモリが必要となり、`Out of memory` エラーが発生する可能性があります。 お使いのコンピューターに十分な RAM があることを確認してください。

## <a name="how-to-configure-apply-sql-transformation"></a>Apply SQL Transformation (SQL 変換の適用) を構成する方法  

このモジュールは、最大 3 つのデータセットを入力として受け取ることができます。 各入力ポートに接続されているデータセットを参照する場合は、`t1`、`t2`、`t3` の名前を使用する必要があります。 テーブル番号は、入力ポートのインデックスを示しています。  

2 つのテーブルを結合する方法を示すサンプル コードを次に示します。 t1 と t2 は、**Apply SQL Transformation** の左と中央の入力ポートに接続された 2 つのデータセットです。

```sql
SELECT t1.*
    , t3.Average_Rating
FROM t1 join
    (SELECT placeID
        , AVG(rating) AS Average_Rating
    FROM t2
    GROUP BY placeID
    ) as t3
on t1.placeID = t3.placeID
```
  
残りのパラメーターは、SQLite 構文を使用する SQL クエリです。 **SQL スクリプト** テキスト ボックスに複数の行を入力する場合は、セミコロンを使用して各ステートメントを終了します。 そうしないと、改行はスペースに変換されます。  

このモジュールでは、SQLite 構文のすべての標準ステートメントがサポートされます。 サポートされていないステートメントの一覧については、「[テクニカル ノート](#technical-notes)」セクションを参照してください。

##  <a name="technical-notes"></a>テクニカル ノート  

このセクションには、実装の詳細、ヒント、よく寄せられる質問への回答が含まれています。

-   ポート 1 では、常に入力が必要です。  
  
-   空白またはその他の特殊文字を含む列識別子の場合、`SELECT` 句または `WHERE` 句で列を参照するときに、必ず列識別子を角かっこまたは二重引用符で囲んでください。  
  
### <a name="unsupported-statements"></a>サポートされていないステートメント  

SQLite では ANSI SQL 標準の多くがサポートされていますが、商用リレーショナル データベース システムでサポートされている多くの機能が含まれていません。 詳細については、「[SQLite によって認識される SQL](http://www.sqlite.org/lang.html)」を参照してください。 また、SQL ステートメントを作成するときは、次の制限事項に注意してください。  
  
- SQLite は、ほとんどのリレーショナル データベース システムのように列に型を割り当てるのではなく、値に対して動的型付けを使用します。 弱い型付けであるため、暗黙的な型変換が可能です。  
  
- `LEFT OUTER JOIN` は実装されていますが、`RIGHT OUTER JOIN` または `FULL OUTER JOIN` は実装されていません。  

- `RENAME TABLE` ステートメントと `ADD COLUMN` ステートメントは `ALTER TABLE` コマンドで使用できますが、`DROP COLUMN`、`ALTER COLUMN`、`ADD CONSTRAINT` などの他の句はサポートされていません。  
  
- SQLite 内でビューを作成することはできますが、その後、ビューは読み取り専用になります。 ビューに対して `DELETE`、`INSERT`、または `UPDATE` ステートメントを実行することはできません。 ただし、ビューに対して `DELETE`、`INSERT`、または `UPDATE` を試行したときに起動するトリガーを作成し、そのトリガーの本体で他の操作を実行できます。  
  

SQLite の公式サイトで提供されているサポートされていない関数の一覧に加え、次の Wiki では、サポートされていないその他の機能の一覧が提供されています。[SQLite - サポートされていない SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
