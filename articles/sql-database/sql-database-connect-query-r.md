---
title: Azure SQL Database で Machine Learning Services と R を使用するためのクイック スタート (プレビュー) | Microsoft Docs
description: このトピックでは、Azure SQL Database で Machine Learning Services を使用する方法や、R スクリプトを実行して高度な分析を大規模に実施する方法のほか、ネットワークを介してデータをプルしなくて済むように、データが置かれている場所で計算や処理を実行する機能について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 04/04/2019
ms.openlocfilehash: f5d43794ba80e4791c8bf24ad404069a8329518e
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051225"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>クイック スタート:Azure SQL Database で Machine Learning Services と R を使用する (プレビュー)

この記事では、[Azure SQL Database で Machine Learning Services と R](sql-database-machine-learning-services-overview.md) のパブリック プレビューを使用する方法について説明します。 SQL データベースと R の間でデータを移動する基本的な方法を紹介します。また、整形された R コードをストアド プロシージャ [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) にラップし、SQL データベースで機械学習モデルをビルド、トレーニング、使用する方法についても説明します。

R 言語の機能を使用して、データベース内で高度な分析と機械学習を提供します。 この機能を使用すると、データが存在する場所で計算と処理を行うことができ、ネットワーク経由でデータをプルする必要がありません。 また、エンタープライズ R パッケージの機能を利用して、高度な分析を大規模に提供できます。

Machine Learning Services には、R の基本ディストリビューションに、Microsoft 提供のエンタープライズ R パッケージがオーバーレイされたものが含まれます。 Microsoft の R 関数とアルゴリズムはスケールと実用性の両方を考慮して設計されており、予測分析、統計モデリング、データの視覚化、および最先端の機械学習アルゴリズムが提供されます。

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/)してください。

> [!IMPORTANT]
> Azure SQL Database の Machine Learning Services は、現在パブリック プレビュー期間です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> [プレビューにサインアップしてください](sql-database-machine-learning-services-overview.md#signup)。

## <a name="prerequisites"></a>前提条件

以降の演習のサンプル コードを実行するには、あらかじめ、Machine Learning Services と R が有効になった SQL データベースを用意しておく必要があります。 パブリック プレビュー期間中は、Microsoft がお客様のオンボードを行い、既存のデータベースまたは新しいデータベースに対して機械学習を有効にします。 「[Sign up for the preview (プレビューにサインアップする)](sql-database-machine-learning-services-overview.md#signup)」の手順に従ってください。

SQL Database に接続して T-SQL クエリまたはストアド プロシージャを実行できるものであれば、どのようなデータベース管理ツールやクエリ ツールからでも、SQL Database に接続して R スクリプトを実行することができます。 このクイック スタートでは、[SQL Server Management Studio](sql-database-connect-query-ssms.md) を使用します。

また、[パッケージの追加](#add-package)の演習では、お使いのローカル コンピューターに [R](https://www.r-project.org/) と [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) もインストールする必要があります。

このクイック スタートでは、サーバーレベルのファイアウォール規則を構成することも必要です。 これを行う方法を示すクイック スタートについては、「[サーバーレベルのファイアウォール規則を作成する](sql-database-server-level-firewall-rule.md)」を参照してください。

## <a name="verify-r-exists"></a>R の存在を確認する

ご利用の SQL データベースで Machine Learning Services と R が有効になっていることを確認できます。 次の手順に従ってください。

1. SQL Server Management Studio を開き、SQL データベースに接続します。 接続する方法について詳しくは、「[クイック スタート:SQL Server Management Studio を使用して Azure SQL Database に接続しクエリを実行する](sql-database-connect-query-ssms.md)」をご覧ください。

1. 次のコードを実行します。 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    すべて問題なければ、次のような結果メッセージが表示されます。

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. なんらかのエラーが発生した場合は、お使いの SQL データベースで Machine Learning Services のパブリック プレビューと R が有効になっていない可能性があります。 前出のパブリック プレビューにサインアップする方法を参照してください。

## <a name="grant-permissions"></a>アクセス許可を付与する

管理者の場合は、外部コードを自動的に実行できます。 他のすべてのユーザーにはアクセス許可を付与する必要があります。

コマンドを実行する前に、`<username>` を有効なデータベース ユーザー ログインに置き換えてください。

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

## <a name="basic-r-interaction"></a>R の基本的な対話式操作

SQL Database では、次の 2 とおりの方法で R コードを実行できます。

+ システム ストアド プロシージャ [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) の引数として R スクリプトを追加します。
+ [リモート R クライアント](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client)から SQL データベースに接続し、SQL Database をコンピューティング コンテキストとして使用してコードを実行します。

以下の演習では 1 つ目の対話モデルに焦点を絞り、R コードをストアド プロシージャに渡す方法を説明します。

1. 簡単なスクリプトを実行して、SQL データベースにおける R スクリプトの実行方法を確認します。

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

2. すべて正しく設定されていれば、適切な結果が計算され、R の `print` 関数から **[メッセージ]** ウィンドウに結果が返されます。

    **結果**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    **stdout** メッセージは、コードのテスト時には便利ですが、一般的には、結果を表形式で返す必要があります。それをアプリケーションで使用したり、テーブルに書き込んだりすることができます。 詳細については、「入力と出力」セクションを参照してください。

`@script` 引数内の情報はすべて、有効な R コードであることが必要ですので、ご注意ください。

## <a name="inputs-and-outputs"></a>入力と出力

[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) が既定で受け取るのは、単一の入力データセットです。これは通常、有効な SQL クエリの形式で提供します。 その他の種類の入力は、SQL の変数として渡すことができます。

このストアド プロシージャは単一の R データ フレームを出力として返しますが、スカラーやモデルを変数として出力することもできます。 たとえば、トレーニング済みのモデルをバイナリ変数として出力して、T-SQL INSERT ステートメントに渡し、そのモデルをテーブルに書き込むことができます。 また、プロットを (バイナリ形式で) 生成したり、スカラー (個別の値。日時、モデルをトレーニングする際の経過時間など) を生成したりすることもできます。

ここでは、sp_execute_external_script の既定の入力変数 (`InputDataSet`) と出力変数 (`OutputDataSet`) にのみ注目しましょう。

1. 次の T-SQL ステートメントを実行して、テスト データの小さなテーブルを作成します。

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    テーブルが作成されたら、次のステートメントを使用してテーブルを照会します。
  
    ```sql
    SELECT * FROM RTestData
    ```

    **結果**

    ![RTestData テーブルの内容](./media/sql-database-connect-query-r/select-rtestdata.png)

2. このテーブルからデータを R スクリプトへの入力として取得することができます。 以下のステートメントを実行してください。 テーブルからデータが取得され、R ランタイムとのラウンド トリップを経て、*NewColName* という列名で値が返されます。

    クエリから返されたデータは R ランタイムに渡され、R ランタイムはそのデータをデータ フレームとして SQL Database に返します。 WITH RESULT SETS 句は、SQL Database に対して返されるデータ テーブルのスキーマを定義します。

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **結果**

    ![テーブルのデータを返す R スクリプトからの出力](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. 入力変数または出力変数の名前を変更してみましょう。 前出のスクリプトでは、入力変数と出力変数に既定の名前である _InputDataSet_ と _OutputDataSet_ を使用しました。 _InputDatSet_ に関連付けられる入力データを定義するには、*\@input_data_1* 変数を使用します。

    このスクリプトでは、ストアド プロシージャの出力変数と入力変数の名前を *SQL_out* と *SQL_in* に変更しています。

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    R では、大文字と小文字が区別されることに注意してください。`@input_data_1_name` と `@output_data_1_name` における入力変数と出力変数の大文字と小文字の区別は、`@script` における R コード内の変数と一致していなければなりません。 

    加えて、パラメーターの順序も重要です。 オプション パラメーターの *\@input_data_1_name* と *\@output_data_1_name* を使用するには、まず必須パラメーターの *\@input_data_1* と *\@output_data_1* を指定する必要があります。

    パラメーターとして渡すことができる入力データセットは 1 つだけです。また、返すことのできるデータセットも 1 つだけです。 ただし、R コード内から他のデータセットを呼び出し、そのデータセットに加えて、他の種類の出力を返すことができます。 任意のパラメーターに OUTPUT キーワードを追加することもでき、その場合は、パラメーターに結果が返されます。 

    `WITH RESULT SETS` ステートメントでは、SQL Database で使用されるデータのスキーマを定義します。 R から取得する各列には、SQL 互換のデータ型を指定する必要があります。スキーマ定義を使用すれば、新しい列名を指定することもできます。R データ フレームの列名を使用する必要はありません。

4. R スクリプトを使用して値を生成し、_@input_data_1_ 内の入力クエリ文字列を空白のままにすることもできます。

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **結果**

    ![@script を入力として使用したクエリの結果](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>R のバージョンを確認する

お使いの SQL データベースにインストールされている R のバージョンを確認したい場合は、次の手順に従います。

1. お使いの SQL データベースで以下のスクリプトを実行します。

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. R の `print` 関数から **[メッセージ]** ウィンドウにバージョンが返されます。 下の出力例を見ると、この SQL Database には R バージョン 3.4.4 がインストールされていることがわかります。

    **結果**

    ```text
    STDOUT message(s) from external script:
                   _
    platform       x86_64-w64-mingw32
    arch           x86_64
    os             mingw32
    system         x86_64, mingw32
    status
    major          3
    minor          4.4
    year           2018
    month          03
    day            15
    svn rev        74408
    language       R
    version.string R version 3.4.4 (2018-03-15)
    nickname       Someone to Lean On
    ```

## <a name="list-r-packages"></a>R パッケージを一覧表示する

Microsoft では、SQL データベースに Machine Learning Services と共にプレインストールされる R パッケージを多数提供しています。 バージョン、依存関係、ライセンス、ライブラリのパス情報を含め、インストールされている R パッケージを一覧表示するには、以下の手順に従います。 新たにパッケージを追加する場合は、「[パッケージを追加する](#add-package)」セクションを参照してください。

1. お使いの SQL データベースで以下のスクリプトを実行します。

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. この出力は、R の `installed.packages()` から結果セットとして返されたものです。

    **結果**

    ![インストールされている R のパッケージ](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="create-a-predictive-model"></a>予測モデルを作成する

R を使用してモデルをトレーニングし、お使いの SQL データベース内のテーブルにそのモデルを保存することができます。 この演習では、速度に基づいて自動車の停止距離を予測する単純な回帰モデルをトレーニングします。 R に付属する `cars` データセットが小さくて理解しやすいため、このデータセットを使用することにします。

1. まず、トレーニング データを保存するためのテーブルを作成します。

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    R ランタイムには、大小多くのデータセットが付属しています。 R と共にインストールされているデータセットの一覧を取得するには、R のコマンド プロンプトで「`library(help="datasets")`」を入力します。

2. 回帰モデルを作成します。 自動車の速度データには、`dist` と `speed` という 2 つの数値の列が含まれます。 いくつかの速度に対する観測データが複数存在します。 このデータから、自動車の速度と自動車が停止するまでに必要な距離との間のなんらかの関係を表す線形回帰モデルを作成します。

    線形モデルの要件は単純です。

   - 従属変数 `speed` と独立変数 `distance` の間の関係を表す数式を定義します。

   - モデルのトレーニングに使用する入力データを提供します。

     > [!TIP]
     > 線形モデルについて復習する必要がある場合は、次のチュートリアルをお勧めします。rxLinMod を使用してモデルを当てはめるプロセスが説明されています。「[Fitting Linear Models (線形モデルの当てはめ)](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)」

     モデルをビルドするには、R コード内で式を定義し、そのデータを入力パラメーターとして渡します。

     ```sql
     DROP PROCEDURE IF EXISTS generate_linear_model;
     GO
     CREATE PROCEDURE generate_linear_model
     AS
     BEGIN
       EXEC sp_execute_external_script
       @language = N'R'
       , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
           trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
       , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
       , @input_data_1_name = N'CarsData'
       , @output_data_1_name = N'trained_model'
       WITH RESULT SETS ((model VARBINARY(max)));
     END;
     GO
     ```

     rxLinMod の第 1 引数は *formula* パラメーターです。このパラメーターで、distance を speed の従属変数として定義します。 入力データは `CarsData` 変数に格納されます。この変数は、SQL クエリによって入力されます。 入力データに具体的な名前を割り当てなかった場合、既定の変数名は _InputDataSet_ になります。

2. 次に、モデルを再トレーニングしたり予測に使用したりできるよう、モデルの格納先となるテーブルを作成します。 モデルを作成する R パッケージの出力は通常、**バイナリ オブジェクト**になります。 したがって、このテーブルには **VARBINARY(max)** 型の列が必要となります。

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. モデルを保存するには、次の Transact-SQL ステートメントを実行してストアド プロシージャを呼び出し、モデルを生成してテーブルに保存します。

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    このコードを 2 度実行すると次のエラーが返されることに注意してください。

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    このエラーを回避する方法の 1 つに、新しいモデルごとに名前を更新する方法があります。 たとえば、よりわかりやすい名前に変更したり、モデルの種類や作成日などを含めたりすることができます。

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. 一般に、ストアド プロシージャ [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) からの R の出力は、単一のデータ フレームに限られます。

    ただしデータ フレームに加えて、他の種類の出力 (スカラーなど) を返すことはできます。

    たとえば、モデルをトレーニングすると同時に、そのモデルの係数のテーブルをすぐに表示したいとします。 この場合は、メインの結果セットとして係数のテーブルを作成し、トレーニング済みのモデルを SQL 変数で出力することができます。 その変数を呼び出せばモデルをすぐに再利用できます。または、次に示すように、モデルをテーブルに保存することもできます。

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **結果**

    ![トレーニング済みのモデルと追加の出力](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>予測する

新しいデータに対し、前のセクションで作成したモデルを使用して予測のスコアリングを行います。 新しいデータを使用して "_スコアリング_" を実行するには、トレーニング済みのモデルのいずれかをテーブルから取得し、予測の基になる新しいデータ一式を呼び出します。 スコアリングは、予測や確率など、トレーニング済みのモデルに取り込まれた新しいデータに基づく値を生成することを意味する言葉として、データ サイエンスの分野で用いられることがあります。

1. まず、新しい速度データを含んだテーブルを作成します。 元のトレーニング データの速度が毎時 25 マイルまでしかないことにお気付きでしょうか。 これは、元のデータが 1920 年の実験をベースにしているためです。 1920 年代の自動車が仮に 60 mph や 100 mph もの速度で走行した場合、停止距離はどのぐらいになるのでしょうか。 この疑問に対する答えを得たければ、新たにいくつかの速度値を提供する必要があります。

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    この例で、モデルの基礎になっているのは、**RevoScaleR** パッケージの一部として提供されている **rxLinMod** アルゴリズムであるため、一般的な R の `predict` 関数ではなく、[rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) 関数を呼び出すことになります。

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
                current_model <- unserialize(as.raw(speedmodel));
                new <- data.frame(NewCarData);
                predicted.distance <- rxPredict(current_model, new);
                str(predicted.distance);
                OutputDataSet <- cbind(new, ceiling(predicted.distance));
                '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    上記のスクリプトは、次のステップを実行します。

   + SELECT ステートメントを使用してテーブルから単一のモデルを取得し、それを入力パラメーターとして渡します。

   + テーブルからモデルを取得した後、そのモデルに対して `unserialize` 関数を呼び出します。

       > [!TIP] 
       > RevoScaleR に用意されている新しい[シリアル化関数](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel)もご確認ください。リアルタイム スコアリングがサポートされています。
   + `rxPredict` 関数と適切な引数をモデルに適用し、新しい入力データを提供します。

   + この例では、テスト フェーズ中、`str` 関数を追加して、R から返されるデータのスキーマをチェックしています。このステートメントは後から削除することができます。

   + R スクリプトで使用される列名は、必ずしもストアド プロシージャの出力に渡されるとは限りません。 ここでは、WITH RESULTS 句を使用して、いくつかの新しい列名を定義しています。

     **結果**

     ![停止距離を予測するための結果セット](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

     [Transact-SQL の PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql) を使用して、保存されているモデルに基づいて、予測された値やスコアを生成することもできます。

<a name="add-package"></a>

## <a name="add-a-package"></a>パッケージを追加する

お使いの SQL データベースにまだインストールされていないパッケージを使用する必要がある場合は、[sqlmlutils](https://github.com/Microsoft/sqlmlutils) を使用してインストールすることができます。 パッケージをインストールするには、以下の手順に従います。

1. 最新の **sqlmlutils** ZIP ファイルを [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) からローカル コンピューターにダウンロードします。 ファイルを解凍する必要はありません。

1. R がインストールされていない場合は、[www.r-project.org](https://www.r-project.org/) から R をダウンロードしてローカル コンピューターにインストールしてください。 R は、Windows、MacOS、Linux 向けに提供されています。 この例では、Windows を使用しています。

1. まず、**sqlmlutils** の前提条件である **RODBCext** パッケージをインストールします。 **RODBCext** をインストールすると、その依存関係 (**RODBC** パッケージ) もインストールされます。 **コマンド プロンプト**を開いて、次のコマンドを実行します。

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    "'R' は、内部コマンドまたは外部コマンド、操作可能なプログラムまたはバッチ ファイルとして認識されていません" というエラーが発生した場合、おそらく R.exe のパスが Windows の **PATH** 環境変数に追加されていないものと思われます。 該当するディレクトリを環境変数に追加するか、コマンドを実行する前にコマンド プロンプトでそのディレクトリに移動してください (例: `cd C:\Program Files\R\R-3.5.1\bin`)。

1. **R CMD INSTALL** コマンドを使用して **sqlmlutils** をインストールします。 ZIP ファイルをダウンロードしたディレクトリのパスとその ZIP ファイルの名前を指定します。 例: 

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    返される出力は次のようになります。

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. この例では、RStudio Desktop を IDE として使用します。 必要に応じて、別の IDE を使用してもかまいません。 まだ RStudio をインストールしていない場合は、[www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/) から RStudio Desktop をダウンロードしてインストールします。

1. **RStudio** を開いて、新しい **R Script** ファイルを作成します。 次の R コードを使用し、sqlmlutils を使ってパッケージをインストールします。 以下の例では、文字列の書式設定と補間の機能を備えた [glue](https://cran.r-project.org/web/packages/glue/) パッケージをインストールします。

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > **scope** には、**PUBLIC** または **PRIVATE** を指定できます。 パブリック スコープは、全ユーザーが使用できるパッケージをデータベース管理者がインストールする場合に適しています。 プライベート スコープを指定した場合、パッケージを利用できるのは、それをインストールしたユーザーに限られます。 スコープを指定しなかった場合の既定のスコープは **PRIVATE** です。

1. ここで、**glue** パッケージがインストールされていることを確認します。

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **結果**

    ![RTestData テーブルの内容](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. パッケージがインストールされたら、**sp_execute_external_script** を介して R スクリプトで使用することができます。 **SQL Server Management Studio** を開き、SQL データベースに接続します。 次のスクリプトを実行します。

    ```sql
    EXEC sp_execute_external_script @language = N'R'
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

    [メッセージ] タブに次の結果が表示されます。

    **結果**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. パッケージを削除したい場合は、ローカル コンピューター上の **RStudio** で次の R スクリプトを実行します。

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> R パッケージを SQL データベースにインストールする方法として、[CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) でバイト ストリームから R パッケージをアップロードする方法もあります。

## <a name="next-steps"></a>次の手順

Machine Learning Services について詳しくは、以下の記事をご覧ください。 これらの記事の一部は SQL Server 向けですが、大半の情報は、Azure SQL Database における Machine Learning Services と R にも当てはまります。

- [Azure SQL Database の Machine Learning Services と R](sql-database-machine-learning-services-overview.md)
- [SQL Server Machine Learning サービス](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [チュートリアル:SQL Server における R を使用したデータベース内分析について学習する](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [R と SQL Server に関するエンド ツー エンドのデータ サイエンス チュートリアル](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [チュートリアル:SQL Server データで RevoScaleR R 関数を使用する](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
