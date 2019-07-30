---
title: Azure SQL Database Machine Learning Services (プレビュー) に R パッケージを追加する
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: この記事では、Azure SQL Database Machine Learning Services (プレビュー) にまだインストールされていない R パッケージをインストールする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ms.openlocfilehash: f82408a6aaa7cf3a492f3036a6db5d8666b6f160
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598056"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Azure SQL Database Machine Learning Services (プレビュー) に R パッケージを追加する

この記事では、Azure SQL Database Machine Learning Services (プレビュー) に R パッケージを追加する方法について説明します。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

- お使いのローカル コンピューターに [R](https://www.r-project.org) および [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) をインストールします。 R は、Windows、MacOS、Linux 向けに提供されています。 この記事は、Windows を使用していることを前提としています。

- この記事には、[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) または [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) を使用して Azure SQL Database で R スクリプトを実行する例が含まれています。 R スクリプトは他のデータベース管理ツールやクエリ ツールを使用して実行できますが、この例では Azure Data Studio または SSMS を前提としています。
   
> [!NOTE]
> Azure Data Studio または SSMS で **sp_execute_external_script** を使用して R スクリプトを実行することによりパッケージをインストールすることはできません。 この記事で述べているように、パッケージのインストールと削除は、R コマンドラインと RStudio を使用してのみ実行できます。 パッケージがインストールされたら、**sp_execute_external_script** を使用して R スクリプトでパッケージの機能にアクセスすることができます。

## <a name="list-r-packages"></a>R パッケージを一覧表示する

Microsoft では、Azure SQL Database に Machine Learning Services と共にプレインストールされる R パッケージを多数提供しています。
Azure Data Studio または SSMS で次のコマンドを実行すると、インストール済みの R パッケージの一覧を表示できます。

1. Azure Data Studio または SSMS を開き、Azure SQL Database に接続します。

1. 次のコマンドを実行します。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

出力は次のようになります。

**結果**

![インストールされている R のパッケージ](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>sqlmlutils を使用してパッケージを追加する

お使いの Azure SQL Database にまだインストールされていないパッケージを使用する必要がある場合は、[sqlmlutils](https://github.com/Microsoft/sqlmlutils) を使用してインストールすることができます。 **sqlmlutils** は、ユーザーが SQL デーベース (SQL Server および Azure SQL Database) と対話し、R または Python のクライアントから SQL で R または Python のコードを実行するのを支援するように設計されたパッケージです。 現時点では、**sqlmlutils** の R バージョンのみが Azure SQL Database でサポートされています。

次の例では、文字列の書式設定と補間を実行できる **[glue](https://cran.r-project.org/web/packages/glue/)** パッケージをインストールします。 次の手順では、**sqlmlutils** と **RODBCext** (**sqlmlutils** の前提条件) をインストールして、**glue** パッケージを追加します。

### <a name="install-sqlmlutils"></a>**sqlmlutils** をインストールする

1. [https://github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist ) からローカル コンピューターに、最新の **sqlmlutils** zip ファイルをダウンロードします。 ファイルを解凍する必要はありません。

1. **コマンド プロンプト**を開き、次のコマンドを実行して **RODBCext** と **sqlmlutils** をローカル コンピューターにインストールします。 完全パスを、ダウンロードした **sqlmlutils** zip ファイルに置き換えます (この例は、ファイルがドキュメント フォルダーにあることを前提としています)。
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    表示される出力は次のようになります。

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > "'R' は、内部コマンドまたは外部コマンド、操作可能なプログラムまたはバッチ ファイルとして認識されません" というエラーを受け取る場合は、R.exe のパスが Windows の **PATH** 環境変数に含まれていないことを示している可能性があります。 該当するパスを環境変数に追加するか、コマンド プロンプトで該当するフォルダーに移動して (例: `cd C:\Program Files\R\R-3.5.3\bin`)、コマンドを再試行することができます。

### <a name="add-the-package"></a>パッケージを追加する

1. RStudio を開いて、新しい **R Script** ファイルを作成します。 

1. 次の R コードを使用し、**sqlmlutils** を使って **glue** パッケージをインストールします。 自分の Azure SQL Database 接続情報を置き換えます。

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > **scope** には、**PUBLIC** または **PRIVATE** を指定できます。 パブリック スコープは、全ユーザーが使用できるパッケージをデータベース管理者がインストールする場合に適しています。 プライベート スコープを指定した場合、パッケージを利用できるのは、それをインストールしたユーザーに限られます。 スコープを指定しなかった場合の既定のスコープは **PRIVATE** です。

### <a name="verify-the-package"></a>パッケージを確認する

RStudio で次の R スクリプトを実行して、**glue** パッケージがインストールされていることを確認します。 前の手順で定義したのと同じ**接続**を使用します。

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**結果**

![RTestData テーブルの内容](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>パッケージを使用する

パッケージがインストールされたら、**sp_execute_external_script** を介して R スクリプトで使用することができます。

1. Azure Data Studio または SSMS を開き、Azure SQL Database に接続します。

1. 次のコマンドを実行します。

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    **[メッセージ]** タブに次の結果が表示されます。

    **結果**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>パッケージを削除する

パッケージを削除する場合は、RStudio で次の R スクリプトを実行します。 前に定義したのと同じ**接続**を使用します。

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Azure SQL Database に R パッケージをインストールするもう 1 つの方法は、**CREATE EXTERNAL LIBRARY** T-SQL ステートメントを使用してバイト ストリームから R パッケージをアップロードするというものです。 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 参照ドキュメント内の「[バイト ストリームからライブラリーを作成する](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream)」をご覧ください。

## <a name="next-steps"></a>次の手順

Azure SQL Database Machine Learning Services と R (プレビュー) の詳細については、次の記事を参照してください。

- [Azure SQL Database Machine Learning Services と R (プレビュー)](sql-database-machine-learning-services-overview.md)
- [Machine Learning Services (プレビュー) を使用して Azure SQL Database に高度な R 関数を記述する](sql-database-machine-learning-services-functions.md)
- [Azure SQL Database Machine Learning Services (プレビュー) での R および SQL データの処理](sql-database-machine-learning-services-data-issues.md)